---
description: Quick implementation options for short tasks (<2hr)
argument-hint: "[task description]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:simple-code-task)
---

# Quick Task Planning

<rules>
- **SPEED**: Max 5 min total (options + execution)
- **OPTIONS**: 2-3 approaches only
- **NO DOCS**: Don't create plan files
- **EXECUTE**: Implement directly or delegate strategically
</rules>

## Summary

Fast options-first workflow for short tasks (<2hr):

1. **Clarify** → Ask 1-2 quick questions if needed
2. **Present Options** → 2-3 approaches with tradeoffs, use `AskUserQuestion`
3. **Confirm** → Quick summary, get approval
4. **Execute** → Implement directly, ship it

**Key**: Speed over perfection. All in chat, no plan docs.

## Process

### 1. Clarify Task

Parse task description and ask 1-2 questions:
- What needs to be done?
- Any constraints or preferences?
- Expected scope/behavior?

Use `AskUserQuestion` for clarifying questions.

### 2. Present Options

Identify 1-3 key decisions (if task has choices):
- Technical approach
- Scope (minimal vs complete)
- Implementation strategy

**Use `AskUserQuestion`** for each decision:
```
header: "Approach"
question: "How should we implement [task]?"
options:
  - label: "Quick fix"
    description: "Fast, minimal changes. Pros: [X]. Cons: [Y]."
  - label: "Proper solution"
    description: "Thorough approach. Pros: [X]. Cons: [Y]."
```

**If task is straightforward** with one obvious approach: Skip to step 3.

### 3. Confirm & Execute

Present quick summary:
```
Ready to implement?

Approach: [Selected approach]
Changes: [Component/module affected]
Impact: [What user sees]
```

Use `AskUserQuestion` to confirm, then **execute directly**:
- Read relevant code
- Implement changes
- Test/verify
- Report completion

**Delegate strategically** only if valuable:
- Research: `Task(subagent_type:research-analyst)`
- Complex codebase exploration: `Task(subagent_type:codebase-explore)`

### 4. Report Completion

```
✅ Completed: [Brief summary]
Changed: [Components/files affected]
```

## When to Use

✅ **Good for `/plan:task`**:
- Bug fixes
- Small features (<100 LOC)
- Single component changes
- Adding tests
- Config changes
- Quick iterations (<2hr)

❌ **Use `/plan:create` instead**:
- Multi-component features
- Architecture changes
- >2 hours work
- Needs external tracking
- Team coordination required

## Anti-Patterns

❌ Creating plan docs for quick tasks (all in chat)
❌ Overthinking simple tasks (speed over perfection)
❌ Presenting >3 options (keep it simple)
❌ Skipping user confirmation (always confirm approach)
❌ Spending >5 min on options (bias toward action)

✅ Fast, focused options with clear tradeoffs
✅ Component-level thinking
✅ Skip options if approach is obvious
✅ Ship it
