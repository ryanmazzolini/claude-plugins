---
description: Create well-structured pull request with proper title, summary, and changes. Use when creating or updating PRs.
argument-hint: "[title hint or issue URL]"
allowed-tools:
  - Bash(git status)
  - Bash(git diff:*)
  - Bash(git log:*)
  - Bash(git branch:*)
  - Bash(git fetch:*)
  - Bash(git push:*)
  - Bash(gh pr view:*)
  - Bash(gh pr list:*)
  - Bash(gh pr status:*)
  - Bash(gh pr create:*)
  - Bash(gh pr ready:*)
  - Bash(gh pr edit:*)
---

# Create PR

## Process

1. **Analyze Branch**:
   - `git log origin/main..HEAD --oneline` to see commits
   - `git diff origin/main..HEAD --stat` to see changed files
   - Extract issue reference from branch name or $ARGUMENTS

2. **Draft PR**:
   - Analyze commits and changes to identify main theme
   - Write PR title and description following any active PR convention rules
   - Include issue link if found

3. **Present Proposal to User**:
   - Use `AskUserQuestion` with `markdown` preview on the "Create PR" option showing the full PR title and body so the user can read it inline
   - Options: "Create draft PR", "Edit first"

4. **Execute if Confirmed**:
   - Push branch if needed
   - Create draft PR
   - Show PR URL after creation

5. **Post-Creation Actions**:
   - Use `AskUserQuestion` tool to offer: mark ready, add labels
   - Execute requested actions

## Notes

- Always create PRs as draft first, then offer to mark ready
- Follow any active PR convention rules for title and body format
