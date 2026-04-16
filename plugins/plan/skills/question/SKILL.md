---
description: Frame problem and unknowns before research. First stage of the planning workflow.
argument-hint: "[workflow dir, goal, GitHub URL, or Shortcut URL]"
disable-model-invocation: true
allowed-tools:
  - AskUserQuestion
  - Glob
  - Read
  - Edit(thoughts/*/plans/**)
  - Bash(date +*)
  - Bash(mkdir *)
  - Bash(humanlayer *)
  - Bash(gh issue view:*)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
---

# Plan: Question

Frame the problem and surface unknowns before research begins. Produces `question.md` inside the workflow dir.

<rules>
- **CLARIFY BEFORE SOLVING**: Problem framing is the output — not solution research or implementation advice
- **3-7 SHARP QUESTIONS**: Ask the minimum set needed to frame research well; never a long questionnaire
- **INFERRED CONTEXT**: If existing artifacts or a parsed ticket already answer something, summarize it rather than re-asking
- **DURABLE ARTIFACT**: End with a written `question.md` per `stage-doc-schemas.md`; that file is the deliverable
- **ARTIFACT-FIRST**: The workflow dir is the source of truth — always resolve it before doing anything else
</rules>

## Summary

1. **Resolve target** → workflow dir from arg (dir, slug, GitHub, Shortcut, or raw goal)
2. **Load context** → read any existing artifacts; if none, work from the raw goal
3. **Ask sharp questions** → 3-7 `AskUserQuestion` calls covering framing, scope, constraints, success criteria, risks
4. **Write `question.md`** → per `stage-doc-schemas.md` schema
5. **Sync and handoff** → `humanlayer thoughts sync` + handoff template

## Process

### 1. Resolve Target

Read `references/resolve-workflow-target.md` and follow it exactly.

```bash
DATE=$(date +%Y-%m-%d)
```

Auto-detect source from `$ARGUMENTS`:
- Contains `github.com`, `gh#`, or `#NNN` → `gh issue view <number>`
- Contains `shortcut` or `sc-` → `mcp__shortcut__stories-get-by-id`
- Matches workflow dir pattern → use as-is
- Otherwise → raw goal; create workflow dir via `slug-generation.md`

### 2. Load Context

Glob the resolved workflow dir for existing artifacts (`question.md`, `research.md`, `design.md`, `plan.md`). Read any that exist.

If artifacts are present, extract:
- The stated goal
- Already-answered questions (skip re-asking these)
- Open assumptions or missing constraints

If no artifacts exist, work from the raw goal text or parsed ticket description.

### 3. Ask Sharp Questions

Ask 3-7 questions using `AskUserQuestion`. Cover at least:

- **Problem framing** — what's actually broken or needed; who is affected and how
- **Scope** — what's explicitly in and out of scope
- **Constraints** — technical, team, timeline, or budget limits
- **Success criteria** — how we'll know we're done; what done looks like
- **Known risks / stakeholders** — who needs to be consulted; what could go wrong

If the context already answers some of these, summarize the inferred answers and only ask about remaining gaps.

Example — single open-ended framing question:

```
header: "Problem framing"
question: "What's broken or missing today, and who is most affected by it?"
```

Example — scoped multi-choice for constraints:

```
header: "Constraints"
question: "Which constraints should bound this work?"
multiSelect: true
options:
  - label: "Must ship within current sprint"
  - label: "No new external dependencies"
  - label: "Must be backwards-compatible"
  - label: "Other (explain below)"
```

### 4. Write `question.md`

Read `references/stage-doc-schemas.md` for the canonical `question.md` schema. Write the artifact to:

```
thoughts/*/plans/YYYY-MM-DD-[slug]/question.md
```

Populate every section from the conversation: Problem, Known Facts, Unknowns, Constraints, Success Criteria, and Research Brief.

### 5. Sync and Handoff

From the repo root:

```bash
humanlayer thoughts sync
```

Then print the handoff using the "After `question.md`" template from `references/handoff-templates.md`.
