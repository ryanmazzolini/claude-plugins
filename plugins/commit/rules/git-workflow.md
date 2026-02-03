# Git Workflow Standards

## Branch Naming

- With Shortcut: `{type}-sc-{number}-{description}`
- Without: `{type}-{description}`

Examples: `feat-sc-12345-user-export`, `fix-stuck-modal`

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `perf`, `build`, `ci`, `revert`

## Commit Standards

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

sc: shortcut-link (if available)
```

### Rules

- ≤50 char subject line, no period
- No scoped format: `feat:` not `feat(api):`
- No AI/Claude references or test counts

### Writing Style

- **Active voice**: "Fix bug" not "Bug was fixed"
- **Imperative mood**: "Add feature" not "Added/Adding"
- **Strong verbs**: Fix, Add, Remove, Update (not modify, change, adjust)
- **No hedge words**: maybe, perhaps, might, probably
- **No weasel words**: actually, basically, comprehensive, various, improved

## Commands

| Command | Purpose |
|---------|---------|
| `/commit:simple` | Full workflow: branch → commit → push → PR |
| `/review:staged` | Pre-commit review of staged changes |
| `/review:branch` | Branch review before push/PR |

## Agent Delegation

Post-implementation: `senior-code-reviewer` + `qa-expert` (parallel)