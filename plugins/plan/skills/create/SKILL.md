---
description: Create implementation plan with options analysis and research team
argument-hint: "[feature description, GitHub URL, or Shortcut URL]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:Explore)
  - Task(subagent_type:general-purpose)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(gh issue view:*)
  - Bash(date +*)
  - Bash(mkdir *)
  - Bash(humanlayer *)
  - Glob
  - Write
  - Read
---

# Plan Creation

<rules>
- **OPTIONS-FIRST**: Present 2-4 approaches with detailed context, user picks
- **GOALS NOT TASKS**: Remaining Intent describes outcomes, not checkbox items
- **RESEARCH TEAM**: Spawn parallel Explore agents + devil's advocate for research
- **TEACH**: Explain WHY at every step — patterns, tradeoffs, reasoning
- **LEAN PLANS**: Plan doc stays under 150 lines
- **ASK WHEN UNCLEAR**: Clarify via AskUserQuestion before assuming
</rules>

## Summary

Interactive planning workflow that converges on WHAT before exploring HOW:

1. **Parse & Research** → Extract feature, spawn research team with devil's advocate
2. **Define Iteratively** → Clarify problem/solution/scope via `AskUserQuestion`
3. **Options Analysis** → Present decisions with detailed context, user picks
4. **Shape Intent** → Define goal, remaining intent, verification criteria
5. **Confirm & Document** → Create plan doc at `thoughts/ryan/plans/YYYY-MM-DD-[slug].md`

## Process

### 1. Parse Input & Research

**Ensure thoughts directory exists:**
```bash
REPO=$(basename "$(pwd)")
if [ ! -d "thoughts/ryan/plans" ]; then
  mkdir -p ~/thoughts/repos/$REPO && humanlayer thoughts init --directory $REPO
fi
mkdir -p thoughts/ryan/plans
DATE=$(date +%Y-%m-%d)
# Use $DATE when constructing thoughts/ryan/plans/$DATE-[slug].md
```

**Auto-detect source from `$ARGUMENTS`:**
- Contains `github.com` or `gh#` or `#NNN` → `gh issue view <number>`
- Contains `shortcut` or `sc-` → `mcp__shortcut__stories-get-by-id`
- Otherwise → use as plain text feature description

**Spawn research team** (2-3 parallel sonnet agents):
- `Task(subagent_type:Explore, model:sonnet)` — "What existing patterns in this codebase handle [X]? Show key files and explain the approach."
- `Task(subagent_type:general-purpose, model:sonnet)` — "What are standard approaches for [X]? Pros/cons with deep links to docs."
- `Task(subagent_type:general-purpose, model:sonnet)` — **Devil's advocate**: "What are the risks, limitations, and failure modes of [X]? What could go wrong? What assumptions might be wrong?"

Present findings with explanations. Share devil's advocate concerns transparently.

Skip research if context already provided.

### 2. Define Feature Iteratively

Converge on WHAT before exploring HOW.

Present initial understanding, then **`AskUserQuestion`** (1-4 questions):
- Problem/solution fit
- Scope boundaries (in/out)
- Success criteria
- Technical constraints

Iterate until locked. Explain codebase patterns found during research.

### 3. Options Analysis

Identify 3-6 key decisions. For each decision:

**First**, output the detailed comparison as normal text — code snippets, architecture
diagrams, tradeoff tables. Give the user full context they can scroll through.

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

**Remaining Intent** — describe outcomes, not rigid tasks:
- "API needs login/logout/refresh endpoints" not "Implement login endpoint"
- LLM decides concrete steps during implementation

**Verification** — split into automated + manual:
- Automated: test commands (`npm test`, `pytest`, etc.)
- Manual: step-by-step actions ("Load /login, enter creds, verify redirect to /dashboard")

### 5. Confirm & Document

Present summary, then confirm via `AskUserQuestion`. Write plan doc to `thoughts/ryan/plans/YYYY-MM-DD-[slug].md`.

```markdown
# [Feature Name] - YYYY-MM-DD

**Status**: Planning | **Goal**: [one sentence outcome]
**External**: [Link if applicable]

## Approach
[Selected strategy]
**Why**: [reasoning, rejected alternatives]

## Decisions
- [Decision]: [choice] — [why this fits]

## Done
[Empty — filled during implementation]

## Remaining Intent
- [Goal-oriented outcomes, not rigid tasks]

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

**After writing the plan doc**, prompt the user with next steps:

```
Plan saved to thoughts/ryan/plans/YYYY-MM-DD-[slug].md

Next steps:
1. Run /clear to free up context
2. Run /plan:implement YYYY-MM-DD-[slug]
```

