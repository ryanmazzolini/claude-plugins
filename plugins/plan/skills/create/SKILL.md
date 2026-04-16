---
description: Distill workflow artifacts into an implementation plan with options analysis and intent shaping
argument-hint: "[workflow dir, research slug, feature description, GitHub URL, or Shortcut URL]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:general-purpose)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(gh issue view:*)
  - Bash(date +*)
  - Bash(mkdir *)
  - Bash(humanlayer *)
  - Glob
  - Edit(thoughts/ryan/plans/**)
  - Read
---

# Plan Creation

<rules>
- **ARTIFACT-FIRST**: Build from upstream workflow artifacts (question/research/design/structure), not memory
- **OPTIONS-FIRST**: Present 2-4 approaches with detailed context, user picks
- **GOALS NOT TASKS**: Remaining Intent describes outcomes, not checkbox items
- **TEACH**: Explain WHY at every step — patterns, tradeoffs, reasoning
- **LEAN PLANS**: Plan doc stays under 150 lines
- **ASK WHEN UNCLEAR**: Clarify via AskUserQuestion before assuming
</rules>

## Summary

Convergent planning workflow that distills workflow artifacts into decisions and intent:

1. **Resolve target** → Find or create workflow dir
2. **Load artifacts** → Read question/research/design/structure when present
3. **Define iteratively** → Clarify scope/success via `AskUserQuestion` (only for gaps artifacts don't cover)
4. **Options analysis** → Present decisions with detailed context, user picks
5. **Shape intent** → Define remaining intent per `milestone-format.md`, verification criteria
6. **Confirm & document** → Write `plan.md` inside the workflow dir per `plan-doc-schema.md`

## Process

### 1. Resolve Target

Read `references/resolve-workflow-target.md` and follow its resolution priority.

```bash
DATE=$(date +%Y-%m-%d)
```

**Auto-detect source from `$ARGUMENTS`:**
- Arg is a workflow dir → use it.
- Arg is a slug matching an existing workflow dir → use it.
- Contains `github.com` or `gh#` or `#NNN` → `gh issue view <number>`, then generate workflow dir.
- Contains `shortcut` or `sc-` → `mcp__shortcut__stories-get-by-id`, then generate workflow dir.
- Otherwise → raw goal; create workflow dir via `references/slug-generation.md`.

### 2. Load Artifacts

Read existing artifacts from the workflow dir per `references/workflow-artifact-loading.md`. Any of `question.md`, `research.md`, `design.md`, `structure.md` that exist are authoritative inputs.

**If no upstream artifacts exist**, prompt the user:

```
header: "No artifacts"
question: "No upstream artifacts found. /plan:create works best after /plan:research (or the full QRSPI pipeline)."
options:
  - label: "Run /plan:next first (Recommended)"
    description: "Let the orchestrator route you through framing/research"
  - label: "Use /plan:task instead"
    description: "For simple, single-concern tasks that skip research"
  - label: "Continue anyway"
    description: "Plan without upstream artifacts — you'll provide context directly"
```

If "Continue anyway": proceed from the raw goal.

Before asking any new questions, summarize what the artifacts already answer. Only ask about remaining gaps.

### 3. Define Feature Iteratively

Converge on WHAT before HOW.

If artifacts cover the problem/solution/scope, restate them and confirm. Otherwise, use `AskUserQuestion` (1-4 questions) for:
- Problem/solution fit
- Scope boundaries (in/out)
- Success criteria
- Technical constraints

Reference codebase patterns from `research.md` where relevant.

### 4. Options Analysis

Identify 3-6 key decisions. When `research.md` exists, use its **Approaches Identified** section as a starting point. When `design.md` exists, those decisions are already locked — don't re-open without explicit user consent.

For each remaining decision:

**First**, output the detailed comparison as normal text — code snippets, architecture diagrams, tradeoff tables. Reference research findings and devil's advocate concerns.

**Then**, use `AskUserQuestion` with concise options referencing the comparison above:

```
header: "Auth method"
question: "Which approach? (see comparison above)"
options:
  - label: "JWT tokens (Recommended)"
    description: "Stateless, matches existing middleware pattern"
  - label: "Session cookies"
    description: "Revocable, but adds Redis dependency"
```

Record each choice for the Decisions section.

### 5. Shape Intent

**Remaining Intent** — describe outcomes, not rigid tasks. Group by `###` concern and `####` milestone per `references/milestone-format.md`. If `structure.md` already exists, use its proposed milestones as the starting point and refine.

**Verification** — split into automated + manual:
- Automated: test commands (`npm test`, `pytest`, etc.)
- Manual: step-by-step actions ("Load /login, enter creds, verify redirect to /dashboard")

### 6. Confirm & Document

Present summary, then confirm via `AskUserQuestion`. Write `plan.md` to `[workflow-dir]/plan.md` using the schema from `references/plan-doc-schema.md`.

- Include the `**Workflow**:` header line pointing at the workflow dir.
- Include the `## Artifacts` section with `./question.md`, `./research.md`, `./design.md`, `./structure.md` bullets — omit any whose file doesn't exist. If none exist, omit the section entirely.
- Frontmatter uses `type: plan` and includes `goal: [one sentence]`.

**After writing**, sync from the repo root and print the handoff:

```bash
humanlayer thoughts sync
```

Use the "After `plan.md` (from `/plan:create`)" template from `references/handoff-templates.md`.
