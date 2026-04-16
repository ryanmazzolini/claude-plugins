---
description: Decompose design into vertical-slice milestones and dependencies. Fourth stage of the planning workflow.
argument-hint: "[workflow dir]"
disable-model-invocation: true
allowed-tools:
  - AskUserQuestion
  - Read
  - Edit(thoughts/ryan/plans/**)
  - Glob
  - Bash(humanlayer *)
---

# Plan: Structure

Turn an aligned design into vertical-slice milestones with explicit dependencies. Output: `structure.md` in the workflow dir.

<rules>
- **VERTICAL SLICES**: Milestones deliver user or system outcomes end-to-end, not horizontal layers
- **INDEPENDENT VERIFICATION**: Each slice must be testable on its own before the next begins
- **MINIMIZE DEPENDENCIES**: Add `_after:` only when the milestone needs the **output** of another, not just decision knowledge
- **ARTIFACT-FIRST**: Build from `design.md`; do not reconstruct decisions from chat history
- **NO PLAN DOC**: Write `structure.md` only — `/plan:create` produces `plan.md`
</rules>

## Summary

- Requires a resolved workflow dir with `design.md`
- Loads all upstream artifacts and summarizes the design before proposing structure
- Groups outcomes into `###` concerns and `####` milestones per `references/milestone-format.md`
- Validates groupings with the user via `AskUserQuestion` before writing
- Writes `structure.md` and hands off to `/plan:create`

## Process

### 1. Resolve Target

Require a workflow dir. Follow `references/resolve-workflow-target.md` exactly.

If `$ARGUMENTS` is a raw goal (not a workflow dir or slug), do not create a new dir — explain that `/plan:structure` needs an existing workflow and suggest `/plan:question` or `/plan:next`.

### 2. Load Artifacts

Load upstream artifacts per `references/workflow-artifact-loading.md` (`question.md`, `research.md`, `design.md`).

If `design.md` is missing, warn via `AskUserQuestion`:

```
header: "No design.md found"
question: "Proceed without a design? Structure without locked decisions may need to be redone."
options:
  - label: "Proceed anyway"
  - label: "Stop — I'll run /plan:design first"
```

Before proposing anything, present a 3-6 line summary confirming:
- The goal and problem being solved
- Key findings from research that constrain the approach
- Decisions locked in `design.md` and the chosen approach

### 3. Derive Milestones

Group outcomes per `references/milestone-format.md` (`###` concern, `####` milestone, `-` intents).

Apply these checks before adding `_after:`:
- Does this milestone need a **file, schema, or API** from the dependency? → `_after:` is valid.
- Does it only need to know a decision already locked in `design.md`? → no dependency needed.

Aim for 3–8 milestones total. Each must describe an outcome, not a task.

### 4. Validate Boundaries

Before writing the file, confirm groupings with the user:

```
header: "Milestone groupings"
question: "Are these the right milestone boundaries?"
options:
  - label: "Looks good — write structure.md"
  - label: "Merge some milestones"
    description: "e.g. Auth + Session setup could be one milestone"
  - label: "Split a milestone further"
  - label: "Reorder or re-group concerns"
```

Incorporate feedback before proceeding.

### 5. Write `structure.md`

Write to `[workflow-dir]/structure.md` per `references/stage-doc-schemas.md` (see the `structure.md` schema) using milestone syntax from `references/milestone-format.md`.

### 6. Sync and Handoff

```bash
humanlayer thoughts sync
```

Then print the handoff message from the "After `structure.md`" section of `references/handoff-templates.md`.
