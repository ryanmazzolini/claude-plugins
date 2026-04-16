# Stage Ladder

Deterministic rules for `/plan:next` to pick the recommended next stage. Walk top-down; first match wins.

## Workflow-dir targets

Inspect the workflow dir for these artifacts: `question.md`, `research.md`, `design.md`, `structure.md`, `plan.md`.

| Artifacts present | Recommended stage | Why |
|---|---|---|
| *(none)* | `question` | Frame the problem before researching. |
| `question` | `research` | Now that unknowns are framed, explore the space. |
| `question`, `research` | `design` | Research done — align on key technical choices. |
| `question`, `research`, `design` | `structure` | Design locked — decompose into milestones. |
| `question`, `research`, `design`, `structure` | `create` | Milestones ready — distill into a plan. |
| *(any subset)* + `plan.md` with non-empty Remaining Intent | `implement` | Plan exists with work left. |
| *(any subset)* + `plan.md` with empty Remaining Intent | `verify` | Plan exists and all intents addressed. |

*"Non-empty Remaining Intent"* = the `## Remaining Intent` section contains at least one `-` bullet (grep `^\s*-\s+` between the section header and the next `^## `).

Any of the upstream artifacts can be missing — the ladder treats `question → research → design → structure` as in-order; if `design.md` exists without `research.md`, still recommend the earliest missing upstream, unless the user already jumped ahead intentionally.

## Legacy flat plan targets

Legacy flat files (`thoughts/ryan/plans/*.md`) have no upstream artifacts. Ladder:

| Remaining Intent | Recommended stage |
|---|---|
| Non-empty | `implement` |
| Empty | `verify` |

## Alternatives

When presenting via `AskUserQuestion`, always offer the recommended stage first and include these alternatives:

- For fresh dirs / early stages: `{question, research, design, structure, create, task}` (filtered to stages that make sense given current artifacts).
- For dirs with `plan.md`: `{implement, verify, save, progress}`.
- Always include a "Stay in current session" option so the user can invoke a skill manually.

## Invocation

Use the `Skill` tool: `skill: "plan:<chosen>"`, `args: "<workflow-dir-or-plan-path>"`. Do not chain stages — after the invoked skill returns, `/plan:next` exits and the user can re-run it to advance.
