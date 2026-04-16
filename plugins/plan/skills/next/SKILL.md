---
description: Advance the planning workflow to its next stage. Detects artifacts, recommends the next skill, and invokes it.
argument-hint: "[workflow dir, slug, or new goal]"
disable-model-invocation: true
allowed-tools:
  - Skill
  - AskUserQuestion
  - Read
  - Glob
  - Bash(date +*)
  - Bash(mkdir *)
  - Bash(ls thoughts/*/plans/*)
  - Bash(ls docs/plans/*)
  - Bash(ls .plans/*)
  - Bash(ls PRPs/*)
---

# Plan: Next

Read-only orchestrator that inspects a workflow directory, applies the stage ladder to pick the recommended next stage, confirms with the user, and invokes it via the Skill tool. Claude Code limitation: no fresh-session handoff — the selected stage skill is invoked in-session and `/plan:next` exits immediately after.

<rules>
- **READ-ONLY**: Never write, edit, or create any file. The invoked stage skill owns all writes.
- **DELEGATE**: Do all real work by invoking the chosen skill via the `Skill` tool. Do not attempt the stage work yourself.
- **NO CHAINING**: After the invoked skill returns, exit. The user re-runs `/plan:next` to advance further.
- **ONE RECOMMENDATION**: Recommend exactly one stage per run. Offer alternatives, but don't push them.
- **NO GUESSING**: If the workflow target is ambiguous after Glob, ask — don't assume.
</rules>

## Summary

- **Resolve** the workflow target from `$ARGUMENTS` (directory, slug, or new goal).
- **Inspect** artifacts present in the workflow dir.
- **Apply** the stage ladder to pick the recommended next stage.
- **Confirm** with the user via `AskUserQuestion`, offering alternatives.
- **Invoke** the chosen stage skill and exit.

## Process

### 1. Resolve Target

Read `references/resolve-workflow-target.md` and follow its resolution priority exactly.

```bash
date +%Y-%m-%d
```

- **Workflow dir / file / slug** → resolve per the reference.
- **Raw goal** → generate a slug per `references/slug-generation.md`, then `mkdir thoughts/*/plans/$(date +%Y-%m-%d)-[slug]/`.
- **No arg** → Glob `thoughts/*/plans/*/` and `thoughts/*/plans/*.md` sorted by mtime, pick most recent. If nothing found, print usage and exit (see edge cases below).

### 2. Inspect Artifacts

Glob `[workflow-dir]/*.md`. Build the set of which of `question.md`, `research.md`, `design.md`, `structure.md`, `plan.md` are present.

For dirs with `plan.md`, Read just the `## Remaining Intent` section to detect whether non-empty intent exists (at least one `- ` bullet).

### 3. Recommend Next Stage

Read `references/stage-ladder.md` and apply top-down. First match wins.

Do not duplicate the ladder rules inline — the reference is authoritative.

### 4. Confirm with User

Present an `AskUserQuestion`:

```
header: "Next stage"
question: |
  Goal: [goal from artifact frontmatter or dir name]
  Workflow: [dir path]
  Artifacts present: [comma-separated list, or "none"]
  Recommended: [stage] — [one-line reason from stage-ladder.md]
options:
  - "[Recommended stage] (Recommended)" — [reason]
  - "[Alternative 1]" — [description]
  - "[Alternative 2]" — [description]
  - "Stay in session" — I'll invoke a skill manually
```

Filter alternatives to stages that make sense given current artifacts (see stage-ladder.md § Alternatives).

Example for a dir with `question.md` and `research.md` present:

```
header: "Next stage"
question: |
  Goal: dark mode admin dashboard
  Workflow: thoughts/*/plans/2026-04-16-dark-mode-admin-dashboard/
  Artifacts present: question.md, research.md
  Recommended: design — research done, align on technical choices before structuring
options:
  - "design (Recommended)" — Align on current/target state and key choices
  - "structure" — Jump straight to milestones (skips design)
  - "question" — Revisit problem framing
  - "Stay in session" — I'll invoke a skill manually
```

### 5. Invoke

If the user picks a stage, call:

```
Skill tool:
  skill: "plan:[chosen-stage]"
  args: "[workflow-dir]"
```

Then exit. Do not continue or chain to another stage.

If the user picks "Stay in session", print a one-line acknowledgment (e.g. `Staying in session. Run /plan:<stage> <dir> when ready.`) and exit.

### 6. Edge Cases

**No arg + no existing workflows**: Print and exit:

```
Usage: /plan:next <workflow-dir | slug | goal>

Examples:
  /plan:next thoughts/*/plans/2026-04-16-dark-mode-admin-dashboard/
  /plan:next dark-mode-admin-dashboard
  /plan:next "Add dark mode toggle to admin dashboard"
```

**Ambiguous slug match** (multiple dirs match the query): Present an `AskUserQuestion` with up to 5 most-recent candidates by mtime. Each option shows the dir name and goal (parsed from `goal:` frontmatter or `# Title` header).

**Legacy flat plan target**: The stage ladder handles this — `references/stage-ladder.md` § Legacy flat plan targets. Pass the plan file path as `args` when invoking `plan:implement` or `plan:verify`.
