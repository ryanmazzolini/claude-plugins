---
description: Align current/target state and key design decisions. Third stage of the planning workflow.
argument-hint: "[workflow dir]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:general-purpose)
  - AskUserQuestion
  - Read
  - Edit(thoughts/*/plans/**)
  - Glob
  - Bash(humanlayer *)
---

# Plan: Design

Converge on 2-5 design decisions that materially shape implementation. Reads workflow artifacts, surfaces options with tradeoffs, asks the user to choose, then writes `design.md`.

<rules>
- **ARTIFACT-FIRST**: Use workflow dir artifacts as inputs, not the chat transcript alone
- **DECISION SCOPE**: 2-5 material decisions (architecture, sequencing, dependencies) — not minor choices
- **EVIDENCE-GROUNDED**: Connect each choice to research findings or codebase constraints
- **TRADEOFFS NOT EDICTS**: Surface alternatives with tradeoffs; ask the user when direction isn't clear
- **DURABLE ARTIFACT**: Output is `design.md`, not a task plan
</rules>

## Summary

Convergent design workflow that locks key choices before structuring work:

1. **Resolve target** → Require workflow dir; error helpfully if given a raw goal
2. **Load artifacts** → Read `question.md`, `research.md`; summarize state aloud
3. **Surface decisions** → 2-5 material decisions, comparison then `AskUserQuestion`
4. **Write `design.md`** → Per `stage-doc-schemas.md` schema
5. **Sync and handoff** → `humanlayer thoughts sync` + handoff template

## Process

### 1. Resolve Target

Follow `references/resolve-workflow-target.md` resolution rules. `$ARGUMENTS` must resolve to a workflow directory.

**If `$ARGUMENTS` is a raw goal** (free text, not a workflow dir): stop with a helpful error.

```
/plan:design requires an existing workflow directory.
Try:
  /plan:next [workflow-dir]   — let the orchestrator pick the right stage
  /plan:question [goal]       — start from scratch
```

### 2. Load Artifacts

Read existing artifacts from the resolved workflow dir — `question.md`, `research.md` — following `references/workflow-artifact-loading.md`.

Before deciding anything, **summarize out loud**:
- The problem (from `question.md` or `research.md`)
- Key findings and approaches identified (from `research.md`)
- Current system/workflow state
- Target state being aimed for

### 3. Surface Design Decisions

Identify 2-5 decisions that materially change the implementation shape. Focus on architecture, sequencing, and external dependencies — not naming or minor details.

For each decision:

**First**, output a detailed comparison as normal text — code snippets, tradeoff tables, architecture notes. Reference specific research findings or codebase constraints. Give the user enough context to make an informed choice.

**Then**, use `AskUserQuestion` with concise options that point back to the comparison:

Example:
```
header: "State management"
question: "Where should draft state live? (see comparison above)"
options:
  - label: "Server-side (Recommended)"
    description: "Matches existing session pattern; survives page refresh"
  - label: "Client-side (localStorage)"
    description: "Zero backend changes; lost on clear"
  - label: "Hybrid"
    description: "More complex; defers the tradeoff rather than resolving it"
```

Record each choice before moving to the next decision.

### 4. Write `design.md`

Write `[workflow-dir]/design.md` using the schema from `references/stage-doc-schemas.md` (`design.md` section). Don't duplicate the schema here.

### 5. Sync and Handoff

From the repo root (where the `thoughts/` symlink lives):

```bash
humanlayer thoughts sync
```

Then print the handoff from the "After `design.md`" section of `references/handoff-templates.md`.
