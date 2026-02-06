---
description: Create well-structured pull request with proper title, summary, and changes. Use when creating or updating PRs.
argument-hint: "[title hint or issue URL]"
allowed-tools:
  - Task(subagent_type:commit:git-pr-expert)
  - Bash(git status)
  - Bash(git diff:*)
  - Bash(git log:*)
  - Bash(git branch:*)
  - Bash(git fetch:*)
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
   - Call `commit:git-pr-expert` agent for proposal
   - Draft title and description following any active PR rules
   - Include issue link if found

3. **Present Proposal to User**:
   - Show PR title and description with clear delimiters
   - Show exact gh commands to execute
   - Use `AskUserQuestion` tool to confirm PR creation

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
