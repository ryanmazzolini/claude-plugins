---
description: Create implementation plan with options analysis
argument-hint: "[feature description or issue URL]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:research-analyst)
  - Task(subagent_type:codebase-explore)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(gh issue view:*)
  - Bash(date +*)
  - Bash(mkdir *)
  - Write
  - Read
---

# Plan Creation

<rules>
- **OPTIONS-FIRST**: Present 2-4 approaches, user picks
- **MVP FOCUS**: 80/20 task prioritization (must-have vs nice-to-have)
- **CONTEXT MINIMAL**: High-signal codebase pointers only
- **MAX 150 LINES**: Plan doc stays lean
- **NO SPECULATION**: Ask clarifying questions when unclear
</rules>

## Summary

Interactive planning workflow that converges on WHAT before exploring HOW:

1. **Parse & Research** → Extract feature from URL/text, delegate heavy research to subagents
2. **Define Iteratively** → Clarify problem/solution/scope with `AskUserQuestion` until locked
3. **Options Analysis** → Present 3-6 key decisions with 2-4 options each, user picks progressively
4. **Task Breakdown** → Create 2-7 vertical slices, prioritize MVP (80/20)
5. **Confirm & Document** → Summary review, create plan doc at `~/projects/[package]/YYYY-MM-DD-[slug].md`

**Key**: Component-level thinking (modules/services), not file-level. Plans stay architectural.

## Process

### 1. Parse Input & Research

```bash
date +%Y-%m-%d  # Get today's date for filename
```

**Extract feature details:**
- Shortcut URL: `mcp__shortcut__stories-get-by-id <id>` or `short story <id>`
- GitHub URL: `gh issue view <number>`
- Plain text: Use as feature description

**Delegate research** (keep main context lean):
- External APIs/patterns: `Task(subagent_type:research-analyst)`
- Codebase patterns: `Task(subagent_type:codebase-explore)`

Skip if context already provided in conversation history.

### 2. Define Feature Iteratively

**Goal**: Converge on WHAT before exploring HOW.

Present initial understanding:
```markdown
## Feature: [Name]

**Problem**: [What pain point does this solve?]
**Solution**: [What does the feature do?]
**MVP Scope**: [What's essential vs nice-to-have?]
```

**Use `AskUserQuestion`** for clarification (1-4 questions):
- Problem/solution fit
- Scope boundaries (what's in/out of MVP)
- Success criteria
- Technical constraints or preferences

**Iterate** until locked:
- Clear answers → proceed
- Need more clarity → ask follow-ups
- User adjusts scope → refine and re-present

**Lock definition** before technical decisions.

### 3. Options Analysis

Identify 3-6 key decisions:
- User experience (what user sees/does)
- Technical approach (architecture, integration)
- Data/state management
- Error handling & edge cases
- Testing approach

**Use `AskUserQuestion` for each decision** (present 1-4 at a time):
- 2-4 options per question
- Each option includes: description, pros/cons, components affected
- Provide recommendation with reasoning
- Use multiSelect if choices aren't mutually exclusive

Example question structure:
```
header: "Auth method"
question: "How should users authenticate API requests?"
options:
  - label: "JWT tokens"
    description: "Stateless, scales horizontally. Changes auth module + middleware.
                  Can't revoke before expiry (con)."
  - label: "Session cookies"
    description: "Revocable, httpOnly prevents XSS. Changes auth + session store.
                  Requires Redis (con)."
```

Record choices → move to next decision(s).

### 4. Task Breakdown

Create 2-7 vertical slices (feature + tests + docs):

**Must Have** (2-3 tasks delivering 80% value):
- [ ] Core task 1 - auth module, user service
- [ ] Core task 2 - API endpoints, middleware

**Nice to Have**:
- [ ] Enhancement 1 - notification system
- [ ] Polish task - error messages

### 5. Confirm & Document

Present summary:
```markdown
Ready to create your plan?

## [Feature Name]

**Goal**: [One sentence outcome]

**User Experience**:
  • [How user will experience the feature]

**Implementation**:
□ [MVP task 1] - auth module, user service
□ [MVP task 2] - API endpoints, middleware
□ [Nice to have] - notification system

**Components Affected**: authentication, user management, API layer

**Effort**: [time estimate] • [complexity/risk]
```

**Use `AskUserQuestion`** to confirm (if changes needed, iterate back).

**Create plan doc** at `~/projects/[package]/YYYY-MM-DD-[feature-slug].md`:

```markdown
# [Feature Name] - YYYY-MM-DD

**Status**: Planning | **External**: [Link] | **Owner**: [Name] | **Deadline**: [Date/N/A]

## Goal
[One sentence: what success looks like]

## Approach
[1-2 sentences describing selected strategy]

**Why This Way**: Rejected [option X] ([reason]) and [option Y] ([reason]).

## Tasks (80/20 Priority)

**MVP - Must Have**:
- [ ] [Core task 1]: [Description] - auth module, user service
- [ ] [Core task 2]: [Description] - API endpoints, middleware

**Nice to Have**:
- [ ] [Enhancement 1]: [Description] - notification system

## Architecture Context
- Similar pattern: [existing feature/component]
- Integrates with: [related systems/modules]
- Test approach: [unit/integration/e2e strategy]

## Notes
[Assumptions, blockers, key decisions]
```

Confirm creation:
```
✅ Created plan at ~/projects/[package]/YYYY-MM-DD-[feature].md

Ready to implement with `/plan:implement YYYY-MM-DD-[feature]`?
```

## Guidelines

**Component-level**: Think modules/services, not files ("auth module" not "auth/login.ts:45")
**Brevity**: Each section ≤50 words
**Signal**: Only context that changes decisions
**Concrete**: Specific components, clear interfaces

## Anti-Patterns

❌ All evaluated options in plan doc (selected approach only)
❌ Full requirements documents (brief pointers only)
❌ File-level implementation details (component-level design)
❌ Code samples in plan (architectural descriptions only)
❌ Every possible component (high-impact changes only)

✅ Selected approach + rejected alternatives with reasons
✅ Architectural patterns and similar features
✅ Vertical task slices with component scope
✅ Clear MVP prioritization
