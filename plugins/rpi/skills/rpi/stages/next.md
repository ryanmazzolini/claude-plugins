# RPI: Next

Read-only router stage for `/rpi` with no explicit stage argument, or `/rpi next ...`.

## Rules

- **READ-ONLY UNTIL STAGE SELECTED**: Inspect workflow state before choosing; the selected stage owns writes.
- **ONE STAGE ONLY**: Recommend/invoke exactly one stage, then stop.
- **MODE-AWARE**: Use RPI unless QRSPI is explicit or clearly needed.
- **NO GUESSING**: If the workflow target is ambiguous, ask the user to choose.

## Process

### 1. Resolve Target

Read `references/resolve-workflow-target.md` and follow its resolution priority.

- Existing workflow dir/file/slug → resolve to that workflow.
- Raw goal → create a workflow dir using `references/slug-generation.md`.
- No arg → use the most recently touched workflow or legacy plan. If none exists, ask for a goal.

### 2. Determine Mode

- Explicit `qrspi` prefix or existing `question.md` → QRSPI.
- Explicit `rpi` prefix, existing `research.md` without `question.md`, or direct `research` start → RPI.
- If no artifacts exist and the work is fuzzy/cross-cutting, recommend QRSPI; otherwise recommend RPI.

### 3. Inspect Artifacts

Glob `[workflow-dir]/*.md`. Track whether these exist:

- `question.md`
- `research.md`
- `design.md`
- `structure.md`
- `plan.md`

For `plan.md`, read only `## Remaining Intent` to detect whether work remains.

### 4. Recommend Stage

Read `references/stage-ladder.md` and apply the selected mode. Present one recommended stage and a few reasonable alternatives via `AskUserQuestion`.

Use `plan` as the user-facing label for the `create` stage.

### 5. Execute Selected Stage

Read the selected `stages/[stage].md` file and follow it. Do not use the Skill tool to call another skill; this is a single bundled skill with stage files as Level 3 resources.

After the stage completes, stop. The user can run `/rpi` again to advance.
