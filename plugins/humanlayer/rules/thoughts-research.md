# Thoughts: Research Persistence

Auto-save research findings to the thoughts directory.

## When This Applies

After completing substantial research — whether via a slash command, web searches, codebase exploration, technology evaluations, or any task that produces structured findings with references. Does NOT apply to single web searches or quick lookups.

## Detection

Same as thoughts-plans: check if `thoughts/ryan/` exists as a symlink.

## Behavior

### If thoughts is initialized

After research is complete and findings are delivered, write a summary to `thoughts/ryan/research/`:

**Filename**: `YYYY-MM-DD-{topic-slug}.md`

**Format**:

```markdown
---
source: {repo name or "global" if not repo-specific}
date: YYYY-MM-DD
type: research
---

# {Research Topic}

{2-3 sentence summary of what was investigated and the conclusion}

## Key Findings

- {Finding 1 with specific detail}
- {Finding 2 with specific detail}
- {Finding 3 if applicable}

## Decision / Recommendation

{What was decided or recommended, and why}

## References

- [{Title}]({URL}) — {one-line description}
- [{Title}]({URL}) — {one-line description}
```

- Create `thoughts/ryan/research/` if it doesn't exist
- Do NOT ask for confirmation — save and mention the path
- Keep it concise — this is a reference note, not the full research output
- Include ALL URLs that informed the findings

### If thoughts is NOT initialized

Defer to the thoughts-plans rule for the init prompt (only ask once per session across both rules).
