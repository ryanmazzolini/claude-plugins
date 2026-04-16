---
description: Execute plan via adaptive loop with guided learning and deviation tracking
argument-hint: "[workflow dir, plan file, or slug]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:general-purpose)
  - Read
  - Glob
  - Edit(thoughts/ryan/plans/**)
  - Bash(humanlayer thoughts sync)
---

# Plan Implementation

Guided implementation where the user learns and reviews every change.

## Summary

Adaptive implementation loop driven by plan doc state:

1. **Load & Resume** → Read plan doc, assess current state from Done/Remaining/Notes
2. **Adaptive Loop** → Decide highest-value next step, explain, implement, update
3. **Verify** → Run automated tests, present manual testing guide
4. **Complete** → Summary of done work, deviations, and next steps

<rules>
- **TEST-FIRST**: Write all milestone tests upfront before any implementation. Tests are the executable spec.
- **RATCHET**: Once a test goes green, it must stay green. Run the full test suite after every implementation step. Any regression is fixed immediately before proceeding.
- **SKIP-SAFE**: User can skip test generation for milestones that aren't unit-testable (UI, config, infra). Those milestones still get manual verification.
- **EVALUATOR**: After each milestone's tests pass, 4 parallel sonnet agents assess completeness, scope, direction, and simplicity. Findings are informational context for the user's check-in decision, never a gate. Agent failures are silent — the check-in proceeds without that agent's findings.
</rules>

## Process

### 1. Load & Resume

Read `references/resolve-workflow-target.md` and follow its resolution priority. The resolved target is either a **workflow dir** (read `[dir]/plan.md`) or a **legacy flat file** (`thoughts/ryan/plans/*.md`). Either shape works — the plan doc layout is identical.

Read and internalize:
- **Goal** — what we're building
- **Approach** — how we're building it
- **Decisions** — locked choices (ask user before changing any)
- **Done** — what's already completed
- **Remaining Intent** — what still needs to happen, grouped by concern (`###`) and milestone (`####`)
- **Milestone dependencies** — `_after: X_` annotations: don't start a milestone until its dependencies are in Done
- **Deviations** — what went off-script previously
- **Notes** — session context from last `/plan:save`

If Notes has context from a previous session, summarize: "Picking up where we left off — [context]."

**Use `AskUserQuestion`** (1-2 questions):
- Any changes to scope or approach since last session?
- Anything to prioritize or skip?

### 1.5 Write Test Suite

After loading the plan and before any implementation, write a failing test for each milestone in Remaining Intent.

**For each `####` milestone:**
- Derive test assertions from the milestone's outcome bullets (`-` items)
- Write the minimal test that would prove the milestone is complete
- Use the project's existing test framework and conventions

**Present the full test suite** via `AskUserQuestion`:

```
header: "Test suite"
question: "Here are the failing tests — one per milestone. These are the spec.\n\n[list each milestone → test name + what it asserts]\n\nDo these capture the right behaviors?"
options:
  - label: "Looks good — start implementing"
    description: "Tests capture intent, begin making them pass"
  - label: "Adjust"
    description: "Change what specific tests check"
  - label: "Skip tests"
    description: "Milestones aren't testable — use manual verification only"
```

If "Adjust": discuss which tests to change, update, re-confirm.
If "Skip tests": proceed to Adaptive Loop without tests (fall back to existing behavior).

**Run the full test suite** to confirm all new tests fail (red). Report the red count as the starting baseline:

```
Test ratchet: 0/N green — starting implementation.
```

Milestones the user marks as not testable get tagged `[manual]` and are excluded from the ratchet count.

### 2. Adaptive Loop

**For each iteration:**

**a) Assess** — Read current plan state. What's the highest-value next step given:
- What's done
- What remains (respect milestone dependencies — skip milestones whose `_after` deps aren't in Done)
- What the goal needs
- Current deviations
- If a dependency seems wrong mid-implementation, deviate and log it

**b) Propose** — `AskUserQuestion` before touching code. The question text teaches the change:

```
header: "Next step"
question: "[Component]: [what will change, why, and how it connects to existing code — 3-5 sentences]"
options:
  - label: "Go"
    description: "Implement this change"
  - label: "More detail"
    description: "Show affected code, patterns, and tradeoffs"
  - label: "Skip"
    description: "Move to a different step"
```

If "More detail": read and present the relevant existing code, explain the pattern, then re-propose.

**c) Implement** — State intent before each edit, then make the change.

**d) Test & Ratchet** — Run the full test suite (milestone tests + Verification > Automated).
- Confirm the target milestone test now passes (green)
- Confirm all previously-green tests still pass (ratchet check)
- If any previously-green test regressed: fix the regression before proceeding
- Report ratchet progress: `M/N green`

**e) Evaluate** — Spawn 4 parallel sonnet agents, each assessing one dimension of the milestone's work. Each agent receives scoped context (not the full codebase) and returns 1-3 focused bullets.

Spawn all 4 agents in a single message using `Agent` with `model: "sonnet"` and `run_in_background: true`. Wait for all to complete (or fail silently). Consolidate non-empty results into an `evaluator_findings` block for the check-in.

If an agent errors or times out, omit its section — do not surface the failure to the user. If all agents fail, proceed to the check-in with no evaluator section.

**Agent prompts** — each agent gets a focused question, scoped context, and a strict output format:

**1. Correctness** — "Does the implementation do what it claims to do?"
- Context: milestone outcome bullets (the `-` items under the `####`), git diff of changes made this step, test results from step 2d
- Focus: Logic errors, off-by-one mistakes, incorrect type handling, unhandled edge cases, broken invariants, race conditions, incorrect API usage. Verify that the code actually satisfies the milestone outcomes — not just that it runs.
- Output: 1-3 bullets. Each bullet identifies a specific correctness issue or confirms a tricky area is handled correctly. Tone: precise and evidence-based — cite the specific line or condition.

**2. Robustness / Maintainability** — "Will this hold up under real-world conditions and future changes?"
- Context: git diff, relevant existing files that the diff touches or imports, test results from step 2d
- Focus: Error handling at system boundaries (user input, network, file I/O, external APIs), graceful degradation, meaningful error messages, single responsibility, readable naming, testability, coupling between components. Flag missing error handling only where failure is plausible — don't add defensive code for impossible states.
- Output: 1-3 bullets. Flag brittle assumptions, missing boundary validation, or code that will be hard to modify. Tone: constructive — "this HTTP call should handle timeouts" not "error handling is missing." If the code is appropriately robust, return nothing.

**3. Simplicity** — "Is anything over-engineered, and are there reuse opportunities?"
- Context: git diff, relevant existing files that the diff touches or imports
- Focus: Unnecessary abstractions, premature generalization, dead code, redundant layers, missed reuse of existing utilities. Three similar lines are better than a premature abstraction. Flag feature flags or backwards-compatibility shims when the code could just be changed directly.
- Output: 1-3 bullets. Tone: constructive — "you could reuse X" or "this wrapper adds indirection without benefit" not "this is wrong." If the code is appropriately simple, return nothing.

**4. Security** — "Does this code introduce security vulnerabilities?"
- Context: git diff, relevant existing files that the diff touches or imports
- Focus: OWASP Top 10 — injection (SQL, command, XSS), broken authentication/authorization, sensitive data exposure, security misconfiguration, insecure deserialization, known vulnerable dependencies. Also check for secrets or credentials in code/config, overly permissive CORS/CSP, missing input sanitization at trust boundaries, path traversal, and unsafe dynamic code execution.
- Output: 1-3 bullets. Each bullet identifies a specific vulnerability or risk with a remediation suggestion. Tone: direct — "user input is interpolated into SQL without parameterization" not "consider security." If no security issues found, return nothing.

**f) Update plan doc:**
- Move completed work description to **Done** section
- Update **Remaining Intent** (remove or refine what was addressed)
- If anything went off-script: add numbered entry to **Deviations** with reasoning
  - Explain to user: "This wasn't in the plan — [reason]. Recording as deviation."
- Run `humanlayer thoughts sync` from the repo root (where the `thoughts/` symlink lives) after updating the plan doc

**g) Check in** — `AskUserQuestion` with evaluator findings:

Build the question text by consolidating evaluator results from step 2e. For each agent that returned findings, add a section. Omit agents that returned nothing or failed — do not show "no findings" placeholders.

```
header: "Next"
question: "Step complete. Ratchet: M/N green.\n\n**Evaluator findings:**\n- **Correctness**: [bullets from agent]\n- **Robustness**: [bullets from agent]\n- **Simplicity**: [bullets from agent]\n- **Security**: [bullets from agent]\n\nWhat now?"
options:
  - label: "Continue"
    description: "Move to next highest-value step"
  - label: "Fix findings"
    description: "Address evaluator findings before moving on"
  - label: "Verify this milestone"
    description: "Stop here, verify what we just built, then continue with next milestone"
  - label: "Commit"
    description: "Review and commit current progress via /commit:simple"
```

If any evaluator flags a security vulnerability or correctness bug, surface it prominently in the check-in question and recommend addressing it before continuing.

If "Verify this milestone": update plan doc (Done + Notes), then prompt:
```
Milestone complete. Next steps:
1. Run /clear
2. Run /plan:verify [workflow-dir] to verify this work
3. Then /plan:implement [workflow-dir] to continue with remaining milestones
```

(For legacy flat plans, pass the plan file path instead of a workflow dir.)

If "Save & pause": update plan doc Notes, then prompt:
```
Progress saved. To resume later:
1. Run /clear
2. Run /plan:implement [workflow-dir]
```

**h) Loop** back to (a) until Remaining Intent is empty or user stops.

### 3. Verify

When Remaining Intent is addressed:

**Ratchet** — Confirm all milestone tests are green (N/N). If any remain red, list them as incomplete milestones before proceeding to verification.

**Automated** — Run all commands from Verification > Automated.
Report pass/fail with explanations.

**Manual** — Present Verification > Manual steps:
```
Manual testing guide:

1. Load /login in your browser
2. Enter test@example.com / password123
3. Click "Sign In"
→ Expected: Redirect to /dashboard, username in header
```

**Deviations** — Surface all off-script changes for review.

Suggest running `/plan:verify` for thorough pass/fail tracking.

### 4. Complete

Update plan doc Status. Present summary:
- What was built and why
- Deviations and their reasoning
- Test results

Prompt next steps:
```
Implementation complete.

Next steps:
1. Run /clear to free up context
2. Run /plan:verify [workflow-dir] to run verification checks
   — or —
   Run /commit:simple to commit your changes
```

(For legacy flat plans, pass the plan file path instead of a workflow dir.)

## Research During Implementation

If a step needs exploration, **ask user first** before spawning agents:
```
header: "Research"
question: "Need to investigate [topic]. Spin up a research team?"
options:
  - label: "Yes — research team"
    description: "2-3 general-purpose agents + devil's advocate"
  - label: "No — figure it out in-session"
    description: "Research synchronously here"
```

When using agent teams, always include a devil's advocate.

