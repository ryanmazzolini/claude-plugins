---
description: Full git workflow handling branch creation, commits, and PRs. Use when committing changes, creating branches, or pushing code.
argument-hint: "[message hint]"
allowed-tools:
  - Bash(git status)
  - Bash(git diff:*)
  - Bash(git log:*)
  - Bash(git branch:*)
  - Bash(git fetch:*)
  - Bash(git checkout:*)
  - Bash(git add:*)
  - Bash(git commit:*)
  - Bash(git push:*)
  - Bash(gh pr view:*)
  - Bash(gh pr create:*)
---

# Commit

Unified git workflow handling branch creation, commits, and PRs.

## Process

1. **Gather context**
   - Parse $ARGUMENTS for description or hints
   - Check branch name for context
   - Store for use in branch name, commit body, PR body

2. **Branch check**
   - `git branch --show-current`
   - If on main/master:
     - Propose branch name following any active branch naming rules
     - Use `AskUserQuestion` tool to confirm branch creation
     - If confirmed: `git checkout main && git pull && git switch -c {name}`
   - If on feature branch: continue

3. **Changes check**
   - `git status`, `git diff --staged` (or `git diff` if nothing staged)
   - `git log -5 --oneline` to match existing commit style
   - If has changes:
     - Analyze the diff and write a commit message following active commit convention rules
     - If >10 files or distinct concerns, split into multiple commits
     - Use `AskUserQuestion` with `markdown` preview on the "Commit" option showing the full commit message (subject + body) so the user can read it inline
     - Options: "Commit as-is", "Edit message"
     - If confirmed: execute git add + commit
   - If no changes:
     - Check for unpushed commits: `git log origin/HEAD..HEAD`
     - If unpushed: skip to step 4
     - If nothing: ask user for context, stop

4. **Push check**
   - `git log origin/HEAD..HEAD 2>/dev/null || git log HEAD~5..HEAD`
   - If unpushed commits exist:
     - Use `AskUserQuestion` tool to confirm push
     - If confirmed: `git push -u origin HEAD`

5. **PR check**
   - `gh pr view HEAD 2>/dev/null`
   - If no PR exists:
     - Analyze commits with `git log origin/main..HEAD` and `git diff origin/main..HEAD --stat`
     - Write PR title and description following active PR convention rules
     - Use `AskUserQuestion` with `markdown` preview on the "Create PR" option showing the full PR title and body so the user can read it inline
     - Options: "Create draft PR", "Edit first"
     - If confirmed: execute gh pr create --draft
   - If PR exists: show URL, offer to update/push
