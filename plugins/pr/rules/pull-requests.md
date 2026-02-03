# Pull Request Standards

## Title

≤72 chars, imperative, no period, action-first

## Template

```markdown
## Summary
[2-3 lines: what and why, active voice]

## Changes
- [Component]: [Specific change]

## Related
https://app.shortcut.com/odeko/story/XXXXX
```

## Anti-Patterns

- AI/Claude attribution
- Weasel words ("comprehensive", "various", "improved")
- Passive voice ("was added", "has been updated")
- Numeric counts ("added 5 tests")
- Implementation details (the diff shows that)

## Shortcut CLI

### Create Story

```bash
short create \
  --type bug \                    # bug | feature | chore
  --project 1099 \                # Core Services (use `short projects` to list)
  --state 500000006 \             # Ready for Dev (use `short workflows` to list)
  --title "Title here" \
  --description "## Problem\n..."
```

### Common Commands

```bash
short search "is:started owner:%self%"   # Your in-progress stories
short story 12345                         # View story
short story --from-git                    # Get story from branch name
short story 12345 --git-branch            # Create branch from story
```

### Reference IDs

Projects: `short projects` | Workflows/States: `short workflows`