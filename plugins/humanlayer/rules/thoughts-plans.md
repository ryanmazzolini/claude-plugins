# Thoughts: Plan Persistence

Auto-save plans to the thoughts directory for cross-session reference.

## When This Applies

After creating or completing an implementation plan — whether via a slash command, Claude's built-in plan mode (`ExitPlanMode`), or any workflow that produces a structured plan with tasks/steps.

## Detection

Check if HumanLayer thoughts is initialized:

```
thoughts/ryan/ exists and is a symlink → initialized
thoughts/ does not exist → not initialized
```

## Behavior

### If thoughts is initialized

After a plan is created or finalized, copy it to `thoughts/ryan/plans/`:

**Filename**: `YYYY-MM-DD-{feature-slug}.md` (derive slug from plan title, lowercase, hyphens)

**Format**: Copy the plan content as-is, prepend a short header:

```markdown
---
source: {repo name from current working directory basename}
date: YYYY-MM-DD
type: plan
---

{original plan content}
```

- Create the `thoughts/ryan/plans/` directory if it doesn't exist
- Do NOT ask for confirmation — just save it and mention where it was saved
- If a file with the same name exists, append a counter: `-2`, `-3`, etc.

### If thoughts is NOT initialized

Use `AskUserQuestion` **once per session** to suggest setup:

```
question: "HumanLayer thoughts isn't set up for this repo. Want to initialize it for persistent notes?"
options:
  - label: "Yes, set it up"
    description: "Run `humanlayer thoughts init` to create thoughts directory"
  - label: "Skip for now"
    description: "Don't ask again this session"
```

If "Yes": run `humanlayer thoughts init` and guide through the interactive prompt.
If "Skip": do not ask again during this session.
