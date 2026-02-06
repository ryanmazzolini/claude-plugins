---
description: Full git workflow handling branch creation, commits, and PRs. Use when committing changes, creating branches, or pushing code.
argument-hint: "[message hint]"
allowed-tools:
  - Task(subagent_type:commit:git-commit-expert)
  - Task(subagent_type:commit:git-pr-expert)
  - Bash(git status)
  - Bash(git diff:*)
  - Bash(git log:*)
  - Bash(git branch:*)
  - Bash(git fetch:*)
  - Bash(git checkout:*)
  - Bash(git add:*)
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
   - `git status`, `git diff --staged`, `git diff`
   - If has changes:
     - Call `commit:git-commit-expert` agent for commit proposal
     - Use `AskUserQuestion` tool to present proposal and get confirmation
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
     - Call `commit:git-pr-expert` agent for PR proposal
     - Use `AskUserQuestion` tool to confirm PR creation
     - If confirmed: execute gh pr create --draft
   - If PR exists: show URL, offer to update/push
