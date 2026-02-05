# Git Workflow Standards

## Branch Naming

- With issue: `{type}-gh{number}-{description}`
- Without: `{type}-{description}`

Examples: `feat-gh123-user-export`, `fix-stuck-modal`

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`, `build`, `ci`, `revert`

## Commits

- Split if >10 files OR >300 LOC/file
- One logical change per commit
- Run lint/typecheck/test before commit

### Format
```
type: subject (≤50 chars, imperative, no period)

Why this change matters and what problem it solves.
Focus on intent and business value, not implementation.
Wrap at 72 chars.

- component: specific change (optional, for multi-file commits)

Closes #123 (if applicable)
```

### Rules

- ≤50 char subject line, no period
- No scoped format: `feat:` not `feat(api):`

## Pull Requests

### Title

≤72 chars, imperative, no period, action-first

### Template

```markdown
## Summary
[2-3 lines: what and why, active voice]

## Changes
- [Component]: [Specific change]

Closes #123
```

## Writing Style

- **Active voice**: "Fix bug" not "Bug was fixed"
- **Imperative mood**: "Add feature" not "Added/Adding"
- **Strong verbs**: Fix, Add, Remove, Update (not modify, change, adjust)
- **No hedge words**: maybe, perhaps, might, probably
- **No weasel words**: actually, basically, comprehensive, various, improved
- **No AI/Claude references or test counts**
- **No implementation details** (the diff shows that)

## GitHub CLI

```bash
gh issue list                    # List issues
gh issue view 123                # View issue
gh pr list                       # List PRs
gh pr view                       # View current PR
gh pr create --draft             # Create draft PR
gh pr ready                      # Mark PR ready for review
```
