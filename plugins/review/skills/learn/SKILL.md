---
description: Extract architectural learning from changes - WHY over WHAT
argument-hint: "[PR number, commit SHA, or branch name]"
disable-model-invocation: true
allowed-tools:
  - Read
  - Grep
  - Glob
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(git diff:*)
  - Bash(git show:*)
  - Bash(git log:*)
  - Bash(git merge-base:*)
  - Bash(gh pr view:*)
  - Bash(gh pr diff:*)
  - Bash(gh pr comments:*)
  - Bash(gh issue view:*)
---

# Architectural Learning Review

> **DIRECTIVE**: Learn from changes. Extract patterns, principles, and design decisions.
> Teach through narrative, not checklist. Focus on WHY, not WHAT.

Previous context: $ARGUMENTS
*(Ignore if empty - may contain PR/commit reference)*

## Phase 1: Establish Learning Scope

**Goal**: Identify changes and architectural context

### Step 1.1: Determine Source

```bash
# If PR reference provided
gh pr view $PR_NUMBER --json number,title,body,files
gh pr diff $PR_NUMBER

# If commit SHA provided
git show $COMMIT_SHA --stat
git show $COMMIT_SHA

# If branch (use current or specified)
BASE_BRANCH="main"
BASE_COMMIT=$(git merge-base $BASE_BRANCH HEAD)
git diff $BASE_COMMIT..HEAD --stat
git log $BASE_COMMIT..HEAD --oneline
```

**Store context**: PR/commit/branch reference, file count, change magnitude

### Step 1.2: Load Architectural Context

Check for project context:
- Read `CLAUDE.md` or architecture docs if present
- Review related files using Grep/Glob for similar patterns

**Token optimization**: Only load context relevant to changed areas

## Phase 2: Semantic Analysis

**Goal**: Understand architectural decisions using minimal token budget

### Step 2.1: Symbol-Level Overview

For each changed file:
1. Read the file to see top-level structure
2. Identify changed/added symbols from diff
3. Map to design patterns (Factory, Strategy, Repository, etc.)

**Skip**: Loading full file contents initially when diff is sufficient

### Step 2.2: Dependency Mapping

Use Grep to find references to key symbols and understand impact.

**Focus on**: How changes ripple through the system

### Step 2.3: Pattern Recognition

Use Grep/Glob to find:
- Similar patterns in codebase
- Related implementations
- Evolution of approach over time

**Token efficiency**: Search only when pattern is unclear from context

### Step 2.4: Deep Dive (Selective)

Only read full file contents when:
- Pattern requires understanding implementation
- Design decision isn't clear from structure
- Teaching moment requires specific code example

## Phase 3: Extract Learning

**Goal**: Identify 2-4 high-signal learning moments

### Learning Lenses (Apply Selectively)

**Principles**:
- SOLID: Single responsibility, Open-closed, Liskov substitution, Interface segregation, Dependency inversion
- KISS: Simplest solution that works?
- YAGNI: Building what's needed now?
- DRY: Eliminating duplication?

**Patterns** (identify, don't list):
- Creational: Factory, Builder, Singleton
- Structural: Adapter, Decorator, Facade, Repository
- Behavioral: Strategy, Observer, Command, Template Method

**Idioms** (language/framework-specific):
- Python: Context managers, comprehensions, decorators
- JS/TS: Destructuring, async/await, type guards
- Framework: React hooks, Vue composition, Rails concerns

**Only discuss what's present in the changes.** Don't lecture about absent patterns.

### Output Format

```markdown
# Learning Review: [PR #X / Commit SHA / Branch]

## Context
**Scope**: [X files, Y commits]
**Area**: [What part of system - auth, API, data layer, etc.]
**Intent**: [What problem is being solved - 1 sentence]

## Key Learning Moments

### 1. [Pattern/Principle Name]
**Where**: `file:line`
**Concept**: [1-2 sentence explanation of the pattern/principle]

The code shows [specific decision made]. This demonstrates [principle/pattern] because [concise explanation of WHY].

**Alternative**: Could have used [different approach], but that would mean [trade-off]. This choice optimizes for [benefit].

**When to use**: Apply this when [specific scenario that would benefit].

---

### 2. [Design Decision]
**Where**: `file:line`
**Concept**: [What's being taught]

Notice [specific implementation detail]. This matters because [impact on system].

**The insight**: [Core lesson - what makes this approach effective/interesting]

**Watch for**: [Common pitfall or edge case this handles well]

---

[Repeat for 2-4 total learning moments - focus on quality over quantity]

## Architectural Impact

[How do these changes affect system design? 2-3 sentences on ripple effects, new capabilities, or constraints introduced]

## Testing Approach

[If tests are included, what do they teach about testing strategy? 1-2 sentences]
Example: "Tests use boundary value analysis for date handling, demonstrating defensive validation strategy"

## Connections

**Similar patterns**:
- `other_file:line` - [how it's similar/different]

**Evolved from**:
- Previous approach in `file:line` was [old way], now [new way] because [reason]

## Takeaways

1. **[Core concept]**: [One-sentence practical lesson]
2. **[Principle]**: [One-sentence recognition guide]
3. **[Decision-making]**: [One-sentence thought process]

[3-5 bullets max - each should be actionable/memorable]
```

## Token Efficiency Patterns

**Progressive Loading**:
1. Diff stats first (cheapest)
2. Symbol overviews (moderate)
3. Reference mapping only for key symbols (targeted)
4. Full symbol bodies only when teaching requires code (expensive)
5. Pattern search only when unclear (conditional)

**Avoid**:
- Loading all file contents
- Searching for patterns already visible in diff
- Reading symbols not relevant to learning points
- Over-analyzing test files (cover testing approach, not every test)

**Minimal High-Signal**:
- Extract 2-4 learning moments (not comprehensive catalog)
- One clear example per concept
- Concise explanations (3-5 sentences max)
- Actionable takeaways (not theoretical discussion)

## Narrative Voice

**Do**:
- Use conversational tone ("Notice how...", "This matters because...")
- Explain WHY, not just WHAT
- Connect to practical scenarios
- Use analogies when helpful

**Don't**:
- List all possible patterns (focus on what's present)
- Provide textbook definitions (assume basic knowledge)
- Over-explain obvious code
- Include placeholder sections (only write what adds value)

## When to Use

**Use learning review for**:
- Understanding architectural decisions in merged code
- Studying well-crafted PRs from senior engineers
- Documenting design patterns for team knowledge
- Post-mortem analysis of complex changes

**Don't use for**:
- Pre-commit/pre-merge reviews (use staged/branch review)
- Bug fixes without architectural lessons
- Trivial changes (<50 LOC, no design decisions)

## Review Storage

Save to: `~/projects/[package]/reviews/learn_[YYYY-MM-DD]_[topic-slug]_[#].md`

Topic slug should reflect the learning domain (e.g., `auth-strategy`, `caching-layer`, `api-design`)

Check for related learning reviews:
```bash
find ~/projects/[package]/reviews -name "learn_*_[topic-slug]_*.md" 2>/dev/null | sort -V
```

If found, reference in "Evolved from" section to show pattern progression.
