# Milestone Format

Canonical syntax for `Remaining Intent` in `plan.md` and `Proposed Milestones` in `structure.md`. Used by `/plan:structure` and `/plan:create`.

## Shape

```markdown
### [Concern area]

#### [Milestone — coherent sub-goal]
_after: [dependency milestone, if any]_
- [Goal-oriented outcome]
- [Goal-oriented outcome]

#### [Another milestone]
_after: [Milestone], [Other Milestone]_
- [Goal-oriented outcome]
```

## Semantics

- `###` — concern or component area (e.g. "Auth layer", "UI", "Data access"). Groups related milestones.
- `####` — milestone, a cluster of outcomes that deliver a coherent sub-goal. The implement skill treats each `####` as one testable unit.
- `_after: X_` — optional dependency. Don't start this milestone until X is in Done. Multiple dependencies: `_after: X, Y_`.
- `-` — individual intents. **Outcomes, not tasks.** ("API needs login/logout/refresh endpoints", not "Implement login endpoint".)

Simple plans with one concern can use a single `###` heading or skip grouping entirely.

## Minimize dependencies

Before adding `_after:`, ask:

> Does this milestone need the **output** of the dependency, or just **knowledge** of a decision already made?

- Needs output (files, schema, API to call) → legitimate `_after:`.
- Needs decision knowledge → no dependency. Milestones can run in parallel.

Deletion/cleanup milestones are typically the only ones that genuinely depend on everything else.

## Adaptive selection

The implement skill picks adaptively across milestones — headings organize work, they don't impose strict sequence. Dependencies are the only hard constraint.
