# Plan Progress

<rules>
- **READ-ONLY**: Never modify the plan doc
- **CONCISE**: Summary fits in one screen
- **HONEST**: Surface blockers and deviations, don't sugarcoat
- **ACTIONABLE**: Always end with a suggested next step
</rules>

## Summary

Quick progress check against a plan doc:

1. **Load** → Find and read the plan doc
2. **Assess** → Compare Done vs Remaining Intent
3. **Report** → Progress summary with suggested next action

## Process

### 1. Load Plan

Read `references/resolve-workflow-target.md` and follow its resolution priority. Resolved target is either a workflow dir (read `[dir]/plan.md`) or a legacy flat file.

Read and extract: Status, Goal, Done, Remaining Intent, Deviations, Notes.

### 2. Assess Progress

Count and categorize at milestone level:
- **Done milestones**: Milestones fully completed (all intents in Done)
- **In-progress milestones**: Milestones with some intents done
- **Blocked milestones**: Milestones with unmet `_after:` dependencies
- **Remaining milestones**: Not yet started
- **Deviations**: Off-script changes tracked
- **Blockers**: Any blockers mentioned in Notes

Estimate completion: done milestones / total milestones as a rough fraction.

### 3. Report

Present:

```
# [Plan Name] — Progress

**Status**: [from plan doc]
**Goal**: [one-liner from plan]
**Progress**: [N of M milestones complete]

## Done
### [Concern area]
- **[Milestone]**: [1-line summary]

## In Progress
### [Concern area]
- **[Milestone]**: [what's done, what remains]

## Blocked
- **[Milestone]** — waiting on: [dependency milestone]

## Remaining
### [Concern area]
- **[Milestone]**: [intent summary]

## Deviations
- [off-script changes, if any]
[or "None"]

## Suggested Next
[One of:]
- "Continue implementing — next milestone is [X]. Run `/rpi implement [workflow-dir]` (or the plan file path for legacy plans)."
- "All milestones addressed — verify with `/rpi verify [workflow-dir]` (or the plan file path for legacy plans)."
- "[Milestone] is blocked by [dependency] — resolve that first."
- "Save progress first — run `/rpi save [workflow-dir]` (or the plan file path for legacy plans)."
```
