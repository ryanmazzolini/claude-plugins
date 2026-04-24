# Stage Ladder

Deterministic rules for `/rpi` to pick the recommended next stage. Walk top-down; first match wins.

## Modes

- **RPI**: `research → plan(create) → implement → verify`
- **QRSPI**: `question → research → design → structure → plan(create) → implement → verify`
- **Task**: `task → verify`

Explicit stage requests bypass the ladder. For example, `/rpi research BLAH` starts at research and does not require `question.md`.

## Workflow-dir targets

Inspect the workflow dir for these artifacts: `question.md`, `research.md`, `design.md`, `structure.md`, `plan.md`.

### RPI ladder

Use RPI when the user explicitly says `rpi`, jumps directly to `research`, or the workflow has `research.md` without `question.md`.

| Artifacts present | Recommended stage | Why |
|---|---|---|
| *(none)* | `research` | Start by gathering evidence and approaches. |
| `research` | `create` | Research exists — distill it into `plan.md`. |
| `research`, optional `design`/`structure` | `create` | Optional upstream artifacts can inform the plan, but are not required. |
| *(any subset)* + `plan.md` with non-empty Remaining Intent | `implement` | Plan exists with work left. |
| *(any subset)* + `plan.md` with empty Remaining Intent | `verify` | Plan exists and all intents are addressed. |

### QRSPI ladder

Use QRSPI when the user explicitly says `qrspi`, starts with `question`, or the task is ambiguous/cross-cutting enough to need durable framing before research.

| Artifacts present | Recommended stage | Why |
|---|---|---|
| *(none)* | `question` | Frame the problem before researching. |
| `question` | `research` | Now that unknowns are framed, explore the space. |
| `question`, `research` | `design` | Research done — align on key technical choices. |
| `question`, `research`, `design` | `structure` | Design locked — decompose into milestones. |
| `question`, `research`, `design`, `structure` | `create` | Milestones ready — distill into `plan.md`. |
| *(any subset)* + `plan.md` with non-empty Remaining Intent | `implement` | Plan exists with work left. |
| *(any subset)* + `plan.md` with empty Remaining Intent | `verify` | Plan exists and all intents are addressed. |

*"Non-empty Remaining Intent"* = the `## Remaining Intent` section contains at least one `-` bullet (grep `^\s*-\s+` between the section header and the next `^## `).

Any upstream artifact can be missing if the user intentionally jumped ahead. Do not force backfilling earlier stages unless the user asks or the missing artifact blocks the selected stage.

## Legacy flat plan targets

Legacy flat files (`thoughts/*/plans/*.md`) have no upstream artifacts. Ladder:

| Remaining Intent | Recommended stage |
|---|---|
| Non-empty | `implement` |
| Empty | `verify` |

## Alternatives

When presenting via `AskUserQuestion`, always offer the recommended stage first and include reasonable alternatives:

- Fresh / early RPI: `{research, plan, task, question}`.
- Fresh / early QRSPI: `{question, research, design, structure, plan, task}`.
- Dirs with `plan.md`: `{implement, verify, save, progress}`.
- Always include a "Stay in current session" option so the user can invoke `/rpi [stage]` manually.

## Invocation

Read and execute the selected stage file from `stages/[stage].md`. Do not chain stages — after the selected stage returns, stop and let the user re-run `/rpi` to advance.
