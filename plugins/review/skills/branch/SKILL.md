---
description: Review branch changes with progressive context loading and actionable feedback
argument-hint: "[optional: prior review ID for comparison]"
disable-model-invocation: true
allowed-tools:
  - Read
  - Grep
  - Glob
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(git status)
  - Bash(git diff:*)
  - Bash(git show:*)
  - Bash(git log:*)
  - Bash(git merge-base:*)
  - Bash(gh pr view:*)
  - Bash(gh pr diff:*)
  - Bash(gh pr comments:*)
  - Bash(gh issue view:*)
---

# Branch Review

> **DIRECTIVE**: Review ONLY changes on the current branch using progressive context loading.
> Provide actionable, commit-aware feedback. Load PR context only when available.

Previous context: $ARGUMENTS
*(Ignore if empty - may contain prior review ID for comparison)*

## Phase 1: Establish Scope

**Goal**: Determine branch boundaries and load minimal context

### Step 1.1: Detect Branch Context

```bash
# Current branch and status
git status --short --branch

# Find common ancestor with main
BASE_BRANCH="main"
BASE_COMMIT=$(git merge-base $BASE_BRANCH HEAD)

# Commits on this branch
git log --oneline $BASE_COMMIT..HEAD
```

**Decision Point**: If no commits beyond base, stop immediately:
```
No branch commits to review. Current branch is even with main.
```

### Step 1.2: Check for PR (Just-in-Time)

```bash
# Only if gh is available and we're in a repo with PR
gh pr view --json number,title,url,isDraft 2>/dev/null || echo "No PR"
```

**Store PR context** (if exists): PR #, title, draft status, URL

### Step 1.3: Get File Scope

```bash
# Changed files only (names + stats)
git diff $BASE_COMMIT..HEAD --name-status --diff-filter=ACMR
git diff $BASE_COMMIT..HEAD --stat
```

**Output boundary statement**:
```
Reviewing X files across Y commits on branch [name]
Base: [base_branch] ([short_hash])
PR: #[number] ([title]) | None
```

## Phase 2: Progressive Review

**Goal**: Analyze changes efficiently without loading unnecessary context

### Step 2.1: File-Level Overview

For each changed file, determine:
- **A**dded (new file - full review)
- **M**odified (review only changed hunks)
- **R**enamed (check if content changed)
- **D**eleted (validate removal safety)

**Token optimization**: Don't load full diffs yet. Work from file list first.

### Step 2.2: Load Diffs (Targeted)

```bash
# Get actual changes with minimal context
git diff $BASE_COMMIT..HEAD --unified=3 --no-color
```

**Review boundaries**:
- Only comment on lines in diff hunks (green + / red -)
- Reference format: `file:line` from diff output
- For new files, review all content but still reference specific lines

### Step 2.3: Cross-Reference Commits

```bash
# Show which commits touched each file
git log $BASE_COMMIT..HEAD --name-only --oneline
```

**Use for context**: If file has multiple commits, understand evolution

### Step 2.4: PR Comments (If Applicable)

```bash
# Only if PR exists from Step 1.2
gh pr view --json comments --jq '.comments[] | "\(.author.login): \(.body)"'
```

**Integration**: Note existing feedback, avoid duplication

## Phase 3: Targeted Feedback

**Goal**: Provide actionable, commit-aware issues

### Review Criteria (Selective Application)

**Apply ONLY to changed code**. Don't lecture about unchanged areas.

1. **Code Quality** (if logic changed):
   - Style consistency with surrounding code
   - Magic numbers → named constants
   - Error handling patterns
   - Debug artifacts (console.log, debugger)
   - Naming clarity

2. **Architecture** (if structure changed):
   - Data model integrity
   - Separation of concerns
   - Migration safety (if DB changes)
   - Breaking changes to APIs

3. **Security** (always check, non-negotiable):
   - Input validation at boundaries
   - SQL/command/template injection risks
   - Secrets in code
   - Auth/authz enforcement
   - Principal of least privilege
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
# Branch Review #[number] | [branch_name]

## Context
**Branch**: [current] ← [base] ([X commits, Y files])
**PR**: #[number] "[title]" | None
**Review Scope**: [commit range]

## Intent
[Plain English: what are these commits trying to accomplish?
1-2 sentences based on commit messages and diffs]

## Commits
[commit_hash] [message]
[commit_hash] [message]
...

## Strengths
[Call out 2-3 patterns done well. Specific, not generic praise.
Reference file:line where you see good practices.]

## Issues

### 🔴 Critical - Block Before Push
[Showstoppers: security holes, data loss risks, breaking changes without migration]

**[Title]** in `file:line` (commit [short_hash])
[WHY this is critical] → [WHAT to do instead]
[Code suggestion if helpful]

### 🟡 Important - Fix Before Merge
[Violates patterns, tech debt, performance concerns]

**[Title]** in `file:line` (commit [short_hash])
[What's happening] → [Better approach] because [reason]

### 🟢 Suggestions - Consider
[Minor improvements, style preferences, optimizations]

In `file:line`: [suggestion] to [benefit]

## Missing Coverage
[Only if applicable]

- **Tests**: `function_name` in `file:line` lacks coverage for [specific scenario]
- **Docs**: New endpoint `path` needs documentation (commit [hash])

## Quick Checks
[Run these on branch HEAD, not individual files]

**Lint**: ✅ Clean / ❌ [X issues - see below]
**Types**: ✅ Sound / ❌ [X errors - see below]
**Tests**: ✅ Pass / ⚠️ [X failing - see below]
**Security**: ✅ No issues / 🔴 [Critical: see above]

[If failures, show relevant excerpts only]

## Verdict

[One of:]
- ✅ **Ready to push/merge** - No blocking issues
- ⚠️ **Ready to push, fix before merge** - Yellow issues need attention
- 🔴 **Fix critical issues before pushing** - Red issues must be resolved

## Scope Confirmation
✓ Reviewed commits: [hash]..[hash]
✓ Reviewed files: [X] ([list if ≤5, else "X files"])
✓ Base branch: [name] at [short_hash]
✓ All references are within branch diff
```

## Token Efficiency Patterns

**Progressive Disclosure**:
1. Load file names first (cheap)
2. Load diffs only for files needing review (moderate)
3. Load commit details only when context needed (moderate)
4. Load PR comments only if PR exists (conditional)

**Avoid**:
- Reading entire files when diffs available
- Loading historical context beyond base commit
- Analyzing unchanged code
- Redundant git commands

**Minimal High-Signal**:
- Reference specific lines from actual diffs
- Cite commit hashes for temporal context
- Mention only violated patterns, not all criteria
- Keep examples concise (3-5 lines max)

## Commit-Aware Feedback

When multiple commits touch same file:
```
In `file.ts:45` (commits [abc123], [def456]):
[Issue] was introduced in [abc123], then partially addressed in [def456].
Consider [complete fix].
```

This helps user understand change evolution vs one-time mistakes.

## Review Storage

Save to: `~/projects/[package]/reviews/branch_[YYYY-MM-DD]_[branch-name-slug]_[#].md`

Check for existing reviews of same branch:
```bash
ls -1 ~/projects/[package]/reviews/branch_*_[branch-name-slug]_*.md 2>/dev/null | sort -V | tail -1
```

If found, increment number. If user provided $ARGUMENTS with prior review path, compare and note:
```
Δ since review #[N]: [X new commits, Y new files]
```
