# Thoughts Persistence

Proactively persist plans, research, and reviews to the thoughts directory.

## Directory

```
thoughts/ryan/
├── plans/       # implementation plans, design docs
├── research/    # investigations, evaluations, comparisons
└── reviews/     # code reviews, architectural reviews
```

Filename: `YYYY-MM-DD-{slug}.md` (lowercase, hyphenated).

Frontmatter:
```yaml
---
source: {repo basename or "global"}
date: YYYY-MM-DD
type: {plan|research|review}
---
```

## Detection

- `thoughts/ryan/` exists as symlink → initialized, persist automatically
- `thoughts/` missing → initialize: `mkdir -p ~/thoughts/repos/{repo} && humanlayer thoughts init --directory {repo}`
