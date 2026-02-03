---
description: Review staged changes with progressive loading - pre-commit feedback
argument-hint: "[optional: prior review ID for comparison]"
disable-model-invocation: true
allowed-tools:
  - Read
  - Grep
  - Glob
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(git status)
  - Bash(git diff --cached:*)
  - Bash(git diff:*)
  - Bash(git show:*)
---

# Staged Changes Review

> **DIRECTIVE**: Review ONLY staged changes. Provide fast, actionable feedback before commit.
> This is the final quality gate before changes enter history.

Previous context: $ARGUMENTS
*(Ignore if empty - may contain prior review ID for comparison)*

## Phase 1: Establish Scope

**Goal**: Verify staging area and load minimal context

### Step 1.1: Check Staging Area

```bash
# What's staged? This is our entire review scope
git diff --cached --name-status --diff-filter=ACMR
```

**Decision Point**: If empty, stop immediately:
```
No staged changes to review. Stage files with `git add` and rerun.
```

### Step 1.2: Get File Stats

```bash
# Quick stats: insertions/deletions per file
git diff --cached --stat
```

**Output boundary statement**:
```
Reviewing X staged files (Y insertions, Z deletions)
Ready to commit: [yes/no - will determine after review]
```

## Phase 2: Progressive Review

**Goal**: Analyze staged hunks efficiently without loading unnecessary context

### Step 2.1: File-Level Overview

For each staged file, determine:
- **A**dded (new file - full review)
- **M**odified (review only staged hunks)
- **R**enamed (check if content changed)
- **D**eleted (validate removal safety)

**Token optimization**: Work from file list first, then load diffs

### Step 2.2: Load Diffs (Targeted)

```bash
# Get actual staged changes with minimal context
git diff --cached --unified=3 --no-color
```

**Review boundaries**:
- Only comment on lines in staged diff hunks (green + / red -)
- Reference format: `file:line` from diff output
- For new files, review all content but still reference specific lines
- Ignore unstaged changes completely

### Step 2.3: Partial Staging Detection

```bash
# Are there unstaged changes to same files?
git diff --name-only
```

**If overlap exists**, warn:
```
⚠️ Partial staging detected in: [files]
Review covers only staged hunks. Unstaged changes ignored.
```

## Phase 3: Targeted Feedback

**Goal**: Fast, actionable issues for immediate fixing

### Review Criteria (Selective Application)

**Apply ONLY to staged changes**. Don't lecture about unchanged code.

1. **Code Quality** (if logic changed):
   - Style consistency with surrounding code
   - Magic numbers → named constants
   - Error handling patterns
   - Debug artifacts (console.log, debugger, TODO comments)
   - Naming clarity

2. **Architecture** (if structure changed):
   - Data model integrity
   - Separation of concerns
   - Migration safety (if DB changes)
   - Breaking changes to APIs

3. **Security** (always check, non-negotiable):
   - Input validation at boundaries
   - SQL/command/template injection risks
   - Secrets in code (API keys, passwords, tokens)
   - Auth/authz enforcement
   - Principle of least privilege
   - OWASP top 10

4. **Performance** (if queries/algorithms changed):
   - N+1 query patterns
   - Missing indexes
   - Algorithm complexity (O(n²) opportunities)
   - Async/batching opportunities

5. **Testing** (if feature code changed):
   - New logic covered
   - Edge cases tested
   - Fixtures vs hardcoded data

6. **Documentation** (if public APIs changed):
   - README accuracy
   - API docs updated
   - Breaking change warnings

### Output Format

```markdown
# Pre-Commit Review #[number]

## Scope
**Staged**: [X files] ([Y insertions, Z deletions])
**Partial**: [Yes - files with unstaged changes / No]

## Intent
[Plain English: what is this commit trying to accomplish?
1-2 sentences based on staged diffs]

## Strengths
[Call out 1-2 patterns done well. Specific, not generic praise.
Reference file:line where you see good practices.]

## Issues

### 🔴 Critical - Fix Before Commit
[Showstoppers: security holes, syntax errors, breaking changes]

**[Title]** in `file:line`
[WHY this is critical] → [WHAT to do instead]
[Code suggestion if helpful]

### 🟡 Important - Consider Fixing
[Violates patterns, tech debt, performance concerns]

**[Title]** in `file:line`
[What's happening] → [Better approach] because [reason]

### 🟢 Suggestions - Optional
[Minor improvements, style preferences]

In `file:line`: [suggestion] to [benefit]

## Missing Coverage
[Only if applicable]

- **Tests**: `function_name` in `file:line` lacks coverage for [specific scenario]
- **Docs**: New public function `name` needs JSDoc/docstring

## Quick Checks
[Run these on staged files only]

**Lint**: ✅ Clean / ❌ [X issues - see below]
**Types**: ✅ Sound / ❌ [X errors - see below]
**Tests**: ✅ Pass / ⚠️ [X failing - see below]
**Security**: ✅ No issues / 🔴 [Critical: see above]

[If failures, show relevant excerpts only]

## Verdict

[One of:]
- ✅ **Ready to commit** - No blocking issues found
- ⚠️ **Commit with caution** - Fix yellow issues soon
- 🔴 **Do not commit** - Critical issues must be resolved

## Next Steps
[If ready to commit:]
Suggested commit message structure:
```
type: imperative subject (≤72 chars)

Why this change is needed (2-3 lines)

- Specific change in component/file
- Another change with impact
```

## Scope Confirmation
✓ Reviewed staged changes in: [X files]
✓ All line references are from staged diff
✓ Unstaged changes ignored
```

## Token Efficiency Patterns

**Progressive Disclosure**:
1. Load file names + stats first (cheap)
2. Load diffs only for staged files (moderate)
3. Skip historical context (not needed pre-commit)
4. No PR/branch context (not relevant yet)

**Avoid**:
- Reading entire files when diffs available
- Loading commit history
- Analyzing unstaged changes
- Checking branch state (not relevant for staging review)

**Minimal High-Signal**:
- Reference specific lines from staged diffs only
- Mention only violated patterns, not all criteria
- Keep examples concise (3-5 lines max)
- Fast feedback loop (this is pre-commit, not pre-push)

## Partial Staging Awareness

When files have both staged and unstaged changes:
```
In `file.ts:45` (staged hunk):
[Issue with staged code]

Note: This file has unstaged changes. They are not reviewed here.
```

This prevents confusion about review scope.

## Review Storage

Save to: `.claude/reviews/staged_[YYYY-MM-DD]_[#].md`

Check for existing reviews today:
```bash
ls -1 .claude/reviews/staged_$(date +%Y-%m-%d)_*.md 2>/dev/null | sort -V | tail -1
```

If found, increment number. If user provided $ARGUMENTS with prior review path, compare and note:
```
Δ since review #[N]: [X files changed, Y lines added]
```

## When to Use

**Use staged review for**:
- Final check before commit
- Atomic commit validation
- Quick feedback loop during development
- Ensuring commit quality standards

**Use branch review for**:
- Pre-push/pre-PR validation
- Multi-commit review
- Understanding change evolution
- Architectural review across commits
