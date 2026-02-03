---
description: Execute implementation plan with progress tracking
argument-hint: "[plan filename or date]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:simple-code-task)
  - Task(subagent_type:typescript-pro)
  - Task(subagent_type:react-specialist)
  - Task(subagent_type:rails-expert)
  - Task(subagent_type:nextjs-developer)
  - Read
  - Edit
  - Write
  - Bash
  - TodoWrite
---

# Plan Implementation

<rules>
- **EXECUTE TASKS**: Complete implementation work, delegate strategically when valuable
- **MVP FIRST**: Complete "Must Have" before "Nice to Have"
- **UPDATE PROGRESS**: Check off tasks in plan doc as completed
- **ASK DON'T ASSUME**: Clarify ambiguities with user
- **STATUS SYNC**: Update external issues when done
</rules>

## Summary

Implementation workflow that executes planned tasks:

1. **Load Plan** → Find `~/projects/[package]/YYYY-MM-DD-[slug].md`, extract tasks & context
2. **Clarify** → Ask 1-4 questions about scope, constraints, priorities, ambiguities
3. **Implement MVP** → Execute each task, verify completion, update progress
4. **MVP Check** → Ask user to ship or continue with nice-to-haves
5. **Complete** → Update status, sync external issues, prompt for commit/PR

**Key**: Focus on task completion. Delegate to subagents for research/analysis/review when valuable. MVP before nice-to-haves.

## Process

### 1. Load Plan

Find `~/projects/[package]/YYYY-MM-DD-[feature-slug].md` by date pattern. List available if ambiguous.

Extract from plan:
- Selected approach and rationale
- MVP "Must Have" tasks (uncompleted: `- [ ]`)
- "Nice to Have" tasks
- Architecture context
- External issue links (Shortcut/GitHub)

### 2. Clarify Before Implementation

**Use `AskUserQuestion`** to clarify (1-4 questions):
- Any changes to scope or approach since plan created?
- Technical constraints or environment setup needed?
- Which MVP tasks to prioritize if multiple?
- Any ambiguities in task descriptions?

**Iterate** until clear:
- All answers clear → proceed to implementation
- Need more context → ask follow-ups
- Plan needs adjustment → update plan doc first

### 3. Implement Tasks Sequentially

**For each MVP task:**

1. **Execute the task**
   - Read relevant code and context
   - Implement changes following existing patterns
   - Write/update tests as needed
   - Run tests to verify

2. **Verify completion**
   - Tests pass
   - Changes align with plan approach
   - Update plan doc: `- [ ]` → `- [x]`, update Status count

3. **Check in with user**
   - After each MVP task: "MVP task N/M complete. Continue?"
   - If blocked or unclear: Ask for guidance

**Delegate strategically when valuable:**
- Research unfamiliar APIs/patterns: `Task(subagent_type:research-analyst)`
- Post-implementation review: `Task(subagent_type:senior-code-reviewer)`

### 4. MVP Completion Check

**Use `AskUserQuestion`** after all MVP tasks:
```
question: "MVP complete (delivers 80% of value). What's next?"
options:
  - label: "Ship MVP"
    description: "Mark done, update external issues, prepare commit/PR"
  - label: "Continue with nice-to-haves"
    description: "Implement remaining enhancement tasks"
```

**If ship:**
- Update Status → "Done (MVP)"
- Update external issue if linked
- Prompt: "Ready for `/commit:smart` or `/pr:create`?"

**If continue:** Implement nice-to-haves (same execution approach)

### 5. Final Completion

**When all tasks done:**
- Update Status → "Done"
- Update external issue if exists
- Use `AskUserQuestion` to confirm next step (commit/PR)

## Strategic Delegation

**Delegate for research/exploration:**
- `research-analyst`: External APIs, best practices, unfamiliar patterns
- `debugger`: Complex issue diagnosis, root cause analysis

**Delegate for post-implementation:**
- `senior-code-reviewer`: Security, performance, architecture review
- `qa-expert`: Test strategy, quality metrics

**Execute directly:**
- Core implementation work
- Following established patterns
- Test writing
- Bug fixes
- Progress tracking and plan updates

## PROGRESS TRACKING

Update plan document in place after each task:

```markdown
**Status**: In Progress (3/5 tasks)
...
## Tasks (80/20 Priority)

**MVP - Must Have**:
- [x] Task 1: Done
- [x] Task 2: Done
- [ ] Task 3: Current

**Nice to Have**:
- [ ] Task 4: Pending
```

**NEVER** create separate tracking docs.

## Anti-Patterns

❌ Nice-to-haves before MVP complete (MVP first, always)
❌ Skipping tests or verification (verify each task)
❌ Continuing when blocked or ambiguous (us `AskUserQuestion` for guidance)
❌ Creating separate tracking docs (update plan doc in place)
❌ Forgetting to update external issues (sync status when done)
