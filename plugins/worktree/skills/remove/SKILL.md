---
description: Remove a git worktree safely after its branch is merged. Use when cleaning up completed worktrees.
argument-hint: "[branch-name]"
allowed-tools:
  - Bash(wtp:*)
  - Bash(git worktree list:*)
  - Bash(git branch:*)
  - Bash(gh pr view:*)
---

# Remove Worktree

Clean up a worktree without destroying branch state.

## Process

1. **Identify the worktree**
   - If `$ARGUMENTS` is empty, run `wtp list` and ask the user which to remove.
   - Otherwise use `$ARGUMENTS` as the branch name.

2. **Verify safe to remove**
   - `gh pr view <branch> --json state,mergedAt 2>/dev/null` — confirm merged or explicitly abandoned.
   - If the PR is still open or the branch has unpushed commits, stop and confirm with the user before removing.

3. **Remove via wtp**
   - `wtp remove <branch>`.

4. **Do NOT delete the branch**
   - This user's convention is worktree removal only — leave the local branch in place. The remote branch is typically cleaned up by the PR merge settings.
   - If the user explicitly asks to delete the branch, run `git branch -d <branch>` (safe delete, refuses on unmerged) — never `-D` without confirmation.
