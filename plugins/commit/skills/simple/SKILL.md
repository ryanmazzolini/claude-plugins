---
description: Full git workflow - branch, commit, push, PR
argument-hint: "[message hint or sc-XXXXX]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:git-commit-expert)
  - Task(subagent_type:git-pr-expert)
  - Bash(git status)
  - Bash(git diff:*)
  - Bash(git log:*)
  - Bash(git branch:*)
  - Bash(git fetch:*)
  - Bash(git checkout:*)
  - Bash(git cherry-pick:*)
  - Bash(git push:*)
  - Bash(gh pr view:*)
---

# Commit

Unified git workflow handling branch creation, commits, and PRs.

## Process

1. **Gather context**
   - Parse $ARGUMENTS for Shortcut ticket (sc-XXXXX) or description
   - Check branch name for existing sc-XXXXX
   - Store for use in branch name, commit body, PR body

2. **Branch check**
   - `git branch --show-current`
   - If on main/master:
     - Propose branch name using Shortcut naming (see CLAUDE.md)
     - Ask: "Create branch `{name}`? (y/n)"
     - If yes: `git checkout main && git pull && git switch -c {name}`
   - If on feature branch: continue

3. **Changes check**
   - `git status`, `git diff --staged`, `git diff`
   - If has changes:
     - Call `git-commit-expert` agent for commit proposal
     - Present proposal, ask: "Proceed? (y/n)"
     - If yes: execute git add + commit
   - If no changes:
     - Check for unpushed commits: `git log origin/HEAD..HEAD`
     - If unpushed: skip to step 4
     - If nothing: ask user for ticket/description context, stop

4. **Push check**
   - `git log origin/HEAD..HEAD 2>/dev/null || git log HEAD~5..HEAD`
   - If unpushed commits exist:
     - Ask: "Push to remote? (y/n)"
     - If yes: `git push -u origin HEAD`

5. **PR check**
   - `gh pr view HEAD 2>/dev/null`
   - If no PR exists:
     - Call `git-pr-expert` agent for PR proposal
     - Present proposal, ask: "Create PR? (y/n)"
     - If yes: execute gh pr create --draft
   - If PR exists: show URL, offer to update/push

6. **Intg cherry-pick** (Odeko workflow)
   - Skip if: on main/master/intg, OR branch name has no `sc-`
   - Check: `git fetch origin intg 2>/dev/null` - skip if intg doesn't exist
   - Show commits to cherry-pick: `git log origin/intg..HEAD --oneline`
   - Ask: "Cherry-pick these commits to intg? (y/n)"
   - If yes:
     - `git checkout intg && git pull origin intg`
     - `git cherry-pick <commit-hashes>` (oldest to newest)
     - If conflict: `git cherry-pick --abort`, inform user, return to feature branch
     - `git push origin intg`
     - `git checkout <original-branch>` (return to feature branch)
