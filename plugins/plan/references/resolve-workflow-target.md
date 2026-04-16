# Resolve Workflow Target

Consistent rules for turning `$ARGUMENTS` into a workflow target. Every plan skill (except `progress`, which is read-only and has its own simpler variant) should follow these.

## Shapes

- **Workflow directory**: `thoughts/ryan/plans/YYYY-MM-DD-[slug]/` containing zero or more of `question.md`, `research.md`, `design.md`, `structure.md`, `plan.md`. Name matches `^\d{4}-\d{2}-\d{2}-.+`.
- **Legacy flat plan**: `thoughts/ryan/plans/*.md` (single file, not inside a workflow dir). Supported read/write for back-compat.

## Resolution priority

Walk this list top-down. The first match wins.

1. **Arg is an existing workflow directory** (matches the pattern above and is a dir) → use that dir.
2. **Arg is an existing file named `question.md` / `research.md` / `design.md` / `structure.md` / `plan.md`** whose parent matches the workflow pattern → resolve to the parent dir.
3. **Arg is an existing `.md` file under `thoughts/ryan/plans/`** (flat, not in a subdir) → legacy plan.
4. **Arg is a slug or date fragment** → Glob `thoughts/ryan/plans/*[arg]*/` first, then `thoughts/ryan/plans/*[arg]*.md`. Multiple matches → AskUserQuestion with up to 5 most-recent candidates (by mtime).
5. **Arg is a free-text goal** *(only for skills that create artifacts: `question`, `task`, `next`)* → mkdir new workflow dir at `thoughts/ryan/plans/$(date +%Y-%m-%d)-[slug]/`. Generate slug per `slug-generation.md`.
6. **No arg** → Glob `thoughts/ryan/plans/*/plan.md` and `thoughts/ryan/plans/*.md`, sort by mtime desc, pick most recent. If nothing, prompt the user for a goal.

## Write rules

- Stage skills (`question`, `research`, `design`, `structure`) write their artifact inside the resolved workflow dir. Never write to legacy flat paths.
- `create` and `task` write `plan.md` inside the resolved workflow dir.
- `implement`, `save`, `verify` write back to the resolved target — either `[dir]/plan.md` (workflow) or the legacy flat file.
- Read-only skills (`progress`, `next` before invoking) never write.

## Sync

After writing any artifact, run from the repo root (where the `thoughts/` symlink lives):

```bash
humanlayer thoughts sync
```

## Ambiguity

If multiple workflow dirs or legacy files match, list up to 5 most-recent candidates with a one-line description (parsed from the artifact's `goal:` frontmatter or `# Title` header) and let the user pick via `AskUserQuestion`.

## Errors

- `design` / `structure` called with a raw goal → explain they need a workflow dir and suggest `/plan:question` or `/plan:next`.
- No candidates and no arg → print `Usage: /plan:<skill> <workflow-dir | slug | goal>` with 1-2 concrete examples.
