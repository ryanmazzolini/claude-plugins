---
name: rpi
description: Durable implementation planning workflow router. Use for RPI/QRSPI planning, research, plan creation, implementation, verification, progress checks, saves, and focused task planning. Invoke as /rpi [stage|mode] [workflow dir, slug, plan file, or goal].
argument-hint: "[next|question|research|design|structure|plan|create|implement|verify|progress|save|task|rpi|qrspi] [target or goal]"
allowed-tools:
  - Task(subagent_type:general-purpose)
  - AskUserQuestion
  - Read
  - Glob
  - Edit(thoughts/*/plans/**)
  - Edit(docs/plans/**)
  - Edit(.plans/**)
  - Edit(PRPs/**)
  - Bash(date +*)
  - Bash(mkdir *)
  - Bash(ls thoughts/*/plans/*)
  - Bash(ls docs/plans/*)
  - Bash(ls .plans/*)
  - Bash(ls PRPs/*)
  - Bash(gh issue view:*)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - WebSearch
  - WebFetch
---

# RPI Planning Workflow

Router for durable planning workflows. `/rpi` is the primary entrypoint; the first argument may be a stage or mode.

## Command forms

```text
/rpi                              # pick the next stage for the current/latest workflow
/rpi next [target-or-goal]         # same as /rpi
/rpi research [goal-or-target]     # jump directly to research; infer RPI if no question.md exists
/rpi plan [target-or-goal]         # create/write plan.md (alias: create)
/rpi implement [target-or-plan]    # execute plan.md
/rpi verify [target-or-plan]       # verify plan.md
/rpi task [task]                   # focused single-concern path
/rpi qrspi [goal-or-target]        # force question → research → design → structure → plan → implement → verify
/rpi rpi [goal-or-target]          # force research → plan → implement → verify
```

Claude Code may display this plugin skill as `/rpi` and internally expand it to `/rpi:rpi`; treat the remaining text as the arguments above.

## Workflow modes

- **RPI**: `research → plan → implement → verify`. Use when the user jumps straight to `research`, explicitly says `rpi`, or the work is clear enough that a question artifact would be ceremony.
- **QRSPI**: `question → research → design → structure → plan → implement → verify`. Use when the user explicitly says `qrspi`, starts with `question`, or the work is fuzzy/cross-cutting and needs durable framing before research.
- **Task**: focused single-concern planning and execution. Use when the first argument is `task` or the work is clearly small and local.

## Routing rules

1. Parse the first argument.
   - Stage aliases: `plan` = `create`; `next` = route automatically.
   - Mode prefixes: `rpi` and `qrspi` set the stage ladder, then route automatically unless another stage follows.
2. Resolve the workflow target using `references/resolve-workflow-target.md`.
3. If a stage was explicitly requested, read the matching `stages/[stage].md` and execute it. Do not require missing upstream artifacts just because the full QRSPI ladder would have included them.
   - Example: `/rpi research "Add SSO"` creates or resolves a workflow and writes `research.md`; it does not ask for `question.md` first.
4. If no stage was requested, choose the next stage using `references/stage-ladder.md` and the selected/default mode.
5. Load only the stage file and references needed for that stage.
6. After the stage completes, stop. Do not automatically chain into the next stage.

## Stage files

Read the relevant file only when that stage is selected:

- `stages/question.md` — frame the problem and write `question.md`
- `stages/research.md` — explore prior art, codebase patterns, options, and risks; write `research.md`
- `stages/design.md` — align key design choices; write `design.md`
- `stages/structure.md` — decompose into milestones; write `structure.md`
- `stages/create.md` — write `plan.md` (this is `/rpi plan`)
- `stages/implement.md` — implement against `plan.md`
- `stages/verify.md` — verify against `plan.md`
- `stages/progress.md` — read-only progress report
- `stages/save.md` — checkpoint session state into `plan.md`
- `stages/task.md` — focused single-concern planning path

## Reference files

Read these only as needed:

- `references/resolve-workflow-target.md` — target/root/slug resolution
- `references/stage-ladder.md` — RPI/QRSPI next-stage rules
- `references/slug-generation.md` — slug rules
- `references/workflow-artifact-loading.md` — artifact loading semantics
- `references/stage-doc-schemas.md` — `question.md`, `research.md`, `design.md`, `structure.md` schemas
- `references/plan-doc-schema.md` — `plan.md` schema
- `references/milestone-format.md` — milestone syntax
- `references/handoff-templates.md` — stage completion messages
