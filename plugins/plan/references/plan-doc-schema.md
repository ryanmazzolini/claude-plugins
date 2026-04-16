# plan.md Schema

Canonical shape for the `plan.md` artifact written by `/plan:create` and `/plan:task`. Kept lean — stay under 150 lines.

## Full template

```markdown
---
source: {repo basename}
date: YYYY-MM-DD
type: plan
goal: [one sentence outcome]
---

# [Feature Name]

**Status**: Planning | **Goal**: [one sentence outcome]
**Workflow**: thoughts/ryan/plans/YYYY-MM-DD-[slug]/
**External**: [Link if applicable]

## Artifacts
- Question: ./question.md
- Research: ./research.md
- Design: ./design.md
- Structure: ./structure.md

## Approach
[Selected strategy in 1-3 sentences]
**Why**: [reasoning, rejected alternatives]

## Decisions
- [Decision]: [choice] — [why this fits]

## Done
[Empty — filled during implementation]

## Remaining Intent
[See references/milestone-format.md. Grouped by `###` concern and `####` milestone with optional `_after:` deps. `/plan:task` may use a flat bullet list instead of milestones.]

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

## Rules

- **`## Artifacts` section**: omit the bullet for any upstream artifact that doesn't exist in the workflow dir. If *no* upstream artifacts exist (pure `/plan:task` path), omit the whole `## Artifacts` section.
- **`**Workflow**:`** header line: always present. Points at the workflow dir (not the `plan.md` path).
- **`**External**:`** header: include only when a GitHub/Shortcut/Linear link is relevant. Omit otherwise.
- **`---` frontmatter**: `type: plan` (not `workflow-stage`). Include `goal:` for consistency with stage artifacts.
- **`## Status`**: `Planning` initially, `In Progress` once implementation starts, `Done` after verification passes.
