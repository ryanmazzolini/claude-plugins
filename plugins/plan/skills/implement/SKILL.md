---
description: Execute plan via adaptive loop with guided learning and deviation tracking
argument-hint: "[plan filename or date]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:Explore)
  - Task(subagent_type:general-purpose)
  - Read
  - Edit
  - Write
  - Bash
---

# Plan Implementation

<rules>
- **ADAPTIVE LOOP**: Assess state → decide next step → explain → do → update → loop
- **IN-SESSION**: Implement all code changes synchronously — user sees every edit
- **USER COMMITS**: User decides when to stage and commit
- **HONOR DECISIONS**: Follow the Decisions section — ask user before changing a locked choice
- **TRACK DEVIATIONS**: Log and explain every off-script change in the Deviations section
- **TEACH**: Explain what will change, why, and how it connects to existing code
</rules>

## Summary

Adaptive implementation loop driven by plan doc state:

1. **Load & Resume** → Read plan doc, assess current state from Done/Remaining/Notes
2. **Adaptive Loop** → Decide highest-value next step, explain, implement, update
3. **Verify** → Run automated tests, present manual testing guide
4. **Complete** → Summary of done work, deviations, and next steps

## Process

### 1. Load & Resume

Find `thoughts/ryan/plans/YYYY-MM-DD-[slug].md` by date pattern. List available if ambiguous.

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

### 2. Adaptive Loop

**For each iteration:**

**a) Assess** — Read current plan state. What's the highest-value next step given:
- What's done
- What remains (respect milestone dependencies — skip milestones whose `_after` deps aren't in Done)
- What the goal needs
- Current deviations
- If a dependency seems wrong mid-implementation, deviate and log it

**b) Explain** — Before touching code, tell the user:
- What will change and which components are affected
- Why this is the right next step
- How it connects to existing code and patterns
- If pattern is unfamiliar, teach it inline

**c) Implement** — Make changes synchronously in-session. User sees every edit.

**d) Test** — Run automated test commands from Verification > Automated.
Report results. If failures, explain and fix before proceeding.

**e) Update plan doc:**
- Move completed work description to **Done** section
- Update **Remaining Intent** (remove or refine what was addressed)
- If anything went off-script: add numbered entry to **Deviations** with reasoning
  - Explain to user: "This wasn't in the plan — [reason]. Recording as deviation."
- Run `humanlayer thoughts sync` after updating the plan doc

**f) Check in** — `AskUserQuestion`:
```
header: "Next"
question: "Step complete. What now?"
options:
  - label: "Continue"
    description: "Move to next highest-value step"
  - label: "Verify this milestone"
    description: "Stop here, verify what we just built, then continue with next milestone"
  - label: "Commit"
    description: "Review and commit current progress via /commit:simple"
  - label: "Save & pause"
    description: "Checkpoint to Notes and free up context"
```

If "Verify this milestone": update plan doc (Done + Notes), then prompt:
```
Milestone complete. Next steps:
1. Run /clear
2. Run /plan:verify [slug] to verify this work
3. Then /plan:implement [slug] to continue with remaining milestones
```

If "Save & pause": update plan doc Notes, then prompt:
```
Progress saved. To resume later:
1. Run /clear
2. Run /plan:implement [slug]
```

**g) Loop** back to (a) until Remaining Intent is empty or user stops.

### 3. Verify

When Remaining Intent is addressed:

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
2. Run /plan:verify [slug] to run verification checks
   — or —
   Run /commit:simple to commit your changes
```

## Research During Implementation

If a step needs exploration, **ask user first** before spawning agents:
```
header: "Research"
question: "Need to investigate [topic]. Spin up a research team?"
options:
  - label: "Yes — research team"
    description: "2-3 Explore agents + devil's advocate"
  - label: "No — figure it out in-session"
    description: "Research synchronously here"
```

When using agent teams, always include a devil's advocate.

