---
description: Run automated tests and walk through manual verification against a plan. Use after implementation or to re-verify.
argument-hint: "[plan filename or date]"
allowed-tools:
  - Read
  - Edit(thoughts/ryan/plans/**)
  - Glob
---

# Plan Verification

<rules>
- **AUTOMATED FIRST**: Run test commands before manual steps
- **STEP BY STEP**: Present manual tests one at a time, record results
- **DEVIATIONS REVIEWED**: Surface all off-script changes for decision
- **EXPLAIN FAILURES**: Diagnose what went wrong and suggest fixes
</rules>

## Summary

Three-phase verification against a plan doc:

1. **Automated** → Run test commands, report pass/fail
2. **Manual** → Walk through manual test steps with user
3. **Deviation review** → Surface off-script changes for keep/revert/adapt

## Process

### 1. Load Plan

Find `thoughts/ryan/plans/YYYY-MM-DD-[slug].md`. Read Verification and Deviations sections.

### 2. Automated Tests

Run each command from **Verification > Automated**.

For each:
- **Pass**: report briefly
- **Fail**: explain what failed, likely cause, suggested fix

Summary: "N/M automated checks passing."

### 3. Manual Testing Guide

Present **Verification > Manual** steps one at a time via `AskUserQuestion`:

```
header: "Step 1"
question: "Load /login, enter test@example.com / password123, click Sign In. Does it redirect to /dashboard with your username in the header?"
options:
  - label: "Pass"
    description: "Works as expected"
  - label: "Fail"
    description: "Something went wrong — describe what happened"
  - label: "Skip"
    description: "Can't test right now"
```

For failures: ask what happened, diagnose, suggest fix.

Summary: "N/M manual checks passing, K skipped."

### 4. Deviation Review

If **Deviations** section has entries, present each via `AskUserQuestion`:

```
header: "Deviation 1"
question: "[Description of off-script change]. What should we do?"
options:
  - label: "Keep"
    description: "Accept — update plan to reflect it"
  - label: "Revert"
    description: "Undo this change"
  - label: "Adapt plan"
    description: "Rethink approach to account for this"
```

For "adapt": update plan doc Approach/Decisions/Remaining Intent.

### 5. Report

Summary:
- Automated: pass/fail
- Manual: pass/fail/skip
- Deviations: kept/reverted/adapted
- Overall: ready to ship or needs work

Update plan doc **Status** if all verification passes. Run `humanlayer thoughts sync` after updating the plan doc.

Check if **Remaining Intent** still has unaddressed items.

**If remaining work exists:**
```
Verification complete. Remaining intents to address.

Next steps:
1. Run /commit:simple to commit verified work
2. Run /clear
3. Run /plan:implement [slug] to continue with next intent
```

**If all intents addressed:**
```
Verification complete — all intents addressed.

Next steps:
  Run /commit:simple to commit your changes
```
