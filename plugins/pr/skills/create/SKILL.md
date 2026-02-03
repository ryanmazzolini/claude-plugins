---
description: Create well-structured pull request
argument-hint: "[title hint or shortcut URL]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:git-pr-expert)
  - Bash
---

# Create PR

## PROCESS

1. **Get Proposal from Agent**:
```
Task(subagent_type:git-pr-expert, prompt:"
Analyze branch and propose pull request.
$ARGUMENTS
")
```

2. **Present Proposal to User**:
   - Show PR title and description with clear delimiters
   - Show exact gh commands to execute
   - Ask: "Create this PR? (y/n)"
   - **STOP and wait for user confirmation**

3. **Execute if Confirmed**:
   - If user says "yes": run the exact commands
     - Push branch if needed
     - Create draft PR
     - Add OdekoTeam/engineering as reviewer
   - If user says "no" or wants changes: ask what to modify
   - Show PR URL after creation

4. **Post-Creation Actions**:
   - Ask: "Mark PR as ready? (y/n)"
   - If yes: `gh pr ready`
   - Ask: "Add labels? (feature/bugfix/etc)"
   - If provided: `gh pr edit --add-label "..."`
   - Ask: "Add additional reviewers?"
   - If provided: `gh pr edit --add-reviewer @username`

## PR STANDARDS (enforced by agent)

**Title**: ≤72 chars, imperative, no period, action-first
**Summary**: 2-3 lines max, active voice, explain "why"
**Changes**: 2-5 bullets, verb-first, specific components
**BLOCK**: AI refs | Weasel words | Passive voice | Numeric counts
**Draft First**: Always create as draft, then mark ready
