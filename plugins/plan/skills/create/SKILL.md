---
description: Distill research into an implementation plan with options analysis and intent shaping
argument-hint: "[research slug, feature description, GitHub URL, or Shortcut URL]"
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
- **OPTIONS-FIRST**: Present 2-4 approaches with detailed context, user picks
- **GOALS NOT TASKS**: Remaining Intent describes outcomes, not checkbox items
- **RESEARCH-INFORMED**: Build on research findings — don't re-research
- **TEACH**: Explain WHY at every step — patterns, tradeoffs, reasoning
- **LEAN PLANS**: Plan doc stays under 150 lines
- **ASK WHEN UNCLEAR**: Clarify via AskUserQuestion before assuming
</rules>

## Summary

Convergent planning workflow that distills research into decisions and intent:

1. **Load Context** → Find research doc or parse feature description
2. **Define Iteratively** → Clarify problem/solution/scope via `AskUserQuestion`
3. **Options Analysis** → Present decisions with detailed context, user picks
4. **Shape Intent** → Define goal, remaining intent, verification criteria
5. **Confirm & Document** → Create plan doc at `thoughts/ryan/plans/YYYY-MM-DD-[slug].md`

## Process

### 1. Load Context

```bash
DATE=$(date +%Y-%m-%d)
```

**Auto-detect source from `$ARGUMENTS`:**
- Matches a research slug → read `thoughts/ryan/research/*[slug].md`
- Contains `github.com` or `gh#` or `#NNN` → `gh issue view <number>`
- Contains `shortcut` or `sc-` → `mcp__shortcut__stories-get-by-id`
- Otherwise → use as plain text feature description

**If no research doc found**, check for recent research docs:

```
Glob: thoughts/ryan/research/*.md
```

If recent docs exist, ask the user which to use:

```
header: "Research"
question: "Found research docs. Use one as input?"
options:
  - label: "[most recent slug]"
    description: "[problem statement from doc]"
  - label: "[second most recent]"
    description: "[problem statement from doc]"
```

**If no research docs exist**, prompt the user to run research first:

```
header: "No research"
question: "No research doc found. /plan:create works best with research as input."
options:
  - label: "Run /plan:research first (Recommended)"
    description: "Research the problem space, then come back to create a plan"
  - label: "Use /plan:task instead"
    description: "For simple, single-concern tasks that don't need deep research"
  - label: "Continue anyway"
    description: "Plan without research — you'll provide context directly"
```

If "Run /plan:research first": stop and tell the user to run `/plan:research [feature]`.
If "Use /plan:task instead": stop and tell the user to run `/plan:task [feature]`.
If "Continue anyway": proceed with the feature description as-is.

**If research doc loaded**, summarize key findings and identified approaches as context for the definition step.

### 2. Define Feature Iteratively

Converge on WHAT before exploring HOW.

If research exists, present initial understanding drawn from the research doc's problem statement and key findings. Otherwise, form understanding from the feature description.

**`AskUserQuestion`** (1-4 questions):
- Problem/solution fit
- Scope boundaries (in/out)
- Success criteria
- Technical constraints

Iterate until locked. Reference codebase patterns from research where relevant.

### 3. Options Analysis

Identify 3-6 key decisions. When research exists, use the **Approaches Identified** section as a starting point — the research has already surfaced options with evidence.

For each decision:

**First**, output the detailed comparison as normal text — code snippets, architecture
diagrams, tradeoff tables. Give the user full context they can scroll through. Reference research findings and devil's advocate concerns where applicable.

**Then**, use `AskUserQuestion` with concise options that reference the output above:

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

### 4. Shape Intent

**Remaining Intent** — describe outcomes, not rigid tasks. Structure by concern and milestone:

```
### [Concern area]

#### [Milestone — coherent sub-goal]
_after: [dependency milestone, if any]_
- [Goal-oriented outcome]
- [Goal-oriented outcome]
```

- `###` — concern or component area (e.g. "Auth layer", "UI")
- `####` — milestone, a cluster of intents that deliver a coherent sub-goal
- `_after: X_` — optional dependency: don't start this milestone until X is in Done. Multiple deps: `_after: X, Y_`
- `-` — individual intents: outcomes not tasks ("API needs login/logout/refresh endpoints" not "Implement login endpoint")

Simple plans with one concern can use a single `###` heading or skip grouping entirely.

The implement skill picks adaptively across milestones — headings organize, they don't impose strict sequence. Dependencies are the only hard constraint.

**Minimize dependencies.** Before adding `_after:`, ask: "Does this milestone need the *output* of the dependency, or just knowledge of decisions already made?" If the latter, they can run in parallel. The only valid dependency is when one milestone produces artifacts (files, schema, API) that another milestone must read or call. Deletion/cleanup milestones are typically the only ones that genuinely depend on everything else.

**Verification** — split into automated + manual:
- Automated: test commands (`npm test`, `pytest`, etc.)
- Manual: step-by-step actions ("Load /login, enter creds, verify redirect to /dashboard")

### 5. Confirm & Document

Present summary, then confirm via `AskUserQuestion`. Write plan doc to `thoughts/ryan/plans/YYYY-MM-DD-[slug].md`.

```markdown
# [Feature Name] - YYYY-MM-DD

**Status**: Planning | **Goal**: [one sentence outcome]
**External**: [Link if applicable]
**Research**: [Link to research doc if applicable]

## Approach
[Selected strategy]
**Why**: [reasoning, rejected alternatives]

## Decisions
- [Decision]: [choice] — [why this fits]

## Done
[Empty — filled during implementation]

## Remaining Intent

### [Concern area]

#### [Milestone name]
- [Goal-oriented outcome]
- [Goal-oriented outcome]

#### [Another milestone]
_after: [Milestone name]_
- [Goal-oriented outcome]

## Deviations
[Empty — tracked when going off-script during implementation]

## Verification

### Automated
- [test commands]

### Manual
1. [Load X / Launch Y]
2. [Perform action]
3. [Verify outcome]

## Notes
[Empty — filled by /plan:save or during /plan:implement]
```

**After writing the plan doc**, sync thoughts from the repo root (where the `thoughts/` symlink lives) and prompt the user with next steps:

```bash
humanlayer thoughts sync
```

```
Plan saved to thoughts/ryan/plans/YYYY-MM-DD-[slug].md

Next steps:
1. Run /clear to free up context
2. Run /plan:implement YYYY-MM-DD-[slug]
```
