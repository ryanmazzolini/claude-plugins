---
description: Check progress on a plan — what's done, what remains, and suggested next action. Use anytime to get bearings.
argument-hint: "[plan filename or date]"
allowed-tools:
  - Read
  - Glob
---

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

Find `thoughts/ryan/plans/YYYY-MM-DD-[slug].md` by date pattern or argument. List available if ambiguous.

Read and extract: Status, Goal, Done, Remaining Intent, Deviations, Notes.

### 2. Assess Progress

Count and categorize:
- **Done items**: Completed intents from the Done section
- **Remaining items**: Outstanding intents from Remaining Intent
- **Deviations**: Off-script changes tracked
- **Blockers**: Any blockers mentioned in Notes

Estimate completion: done / (done + remaining) as a rough fraction.

### 3. Report

Present:

```
# [Plan Name] — Progress

**Status**: [from plan doc]
**Goal**: [one-liner from plan]
**Progress**: [N of M intents addressed]

## Done
- [completed intent — 1 line each]

## Remaining
- [outstanding intent — 1 line each]

## Deviations
- [off-script changes, if any]
[or "None"]

## Blockers
- [from Notes, if any]
[or "None"]

## Suggested Next
[One of:]
- "Continue implementing — next intent is [X]. Run `/plan:implement [slug]`."
- "All intents addressed — verify with `/plan:verify [slug]`."
- "Blockers need resolution before continuing."
- "Save progress first — run `/plan:save [slug]`."
```
