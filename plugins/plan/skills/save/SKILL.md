---
description: Checkpoint current session progress to plan doc. Use before pausing, /clear, or ending a session.
argument-hint: "[plan filename or date]"
allowed-tools:
  - Read
  - Edit(thoughts/*/plans/**)
  - Glob
  - Bash(date +*)
  - Bash(humanlayer thoughts sync)
---

# Session Checkpoint

<rules>
- **PLAN DOC IS CHECKPOINT**: Write session state to the Notes section
- **CONCISE**: Summary, not transcript — enough to resume cold
- **PRESERVE SECTIONS**: Only update Notes, Done, Remaining Intent, Deviations
- **UPDATE ONLY**: Write to the plan doc, leave code and git untouched
</rules>

## Summary

Saves current session context to the plan doc so the next session can resume cold:

1. **Load** → Find plan doc, read current state
2. **Capture** → Summarize progress, findings, blockers
3. **Write** → Update Notes section with checkpoint

## Process

### 1. Load Plan

Read `references/resolve-workflow-target.md` and follow its resolution priority. Resolved target is either a workflow dir (write to `[dir]/plan.md`) or a legacy flat file.

### 2. Capture Session State

Assess and summarize:
- **Progress**: What was accomplished this session (update Done section if not already current)
- **Current work**: What was in progress when stopping
- **Findings**: Key things learned — patterns discovered, gotchas hit, decisions made
- **Blockers**: Open questions, things that need investigation
- **Next step**: What to tackle first next session and why

### 3. Write Checkpoint

Update the plan doc **Notes** section:

```markdown
## Notes
**Last session**: YYYY-MM-DD
**Status**: [milestone-level — e.g. "Token management done, Error handling in progress, Protected routes blocked"]

**In progress**: [what was being worked on when paused]

**Findings this session**:
- [Key learning or discovery]
- [Pattern or gotcha worth remembering]

**Blockers**:
- [Open question or issue, if any]

**Suggested next step**: [What to pick up first and why]
```

Also update **Done** and **Remaining Intent** sections if they're stale.

Write to `[workflow-dir]/plan.md` if the resolved target is a workflow directory, or to the legacy flat file path otherwise. The existing `Edit(thoughts/*/plans/**)` glob covers both shapes.

**Sync thoughts** from the repo root (where the `thoughts/` symlink lives) after writing the checkpoint:
```bash
humanlayer thoughts sync
```

Prompt next steps:
```
Checkpoint saved to [resolved target path]

To resume later:
1. Run /clear to free up context
2. Run /plan:implement [workflow-dir]  (legacy flat file: /plan:implement [slug])
```
