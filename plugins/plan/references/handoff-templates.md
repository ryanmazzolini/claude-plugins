# Handoff Templates

Closing-message shapes for each stage. Each skill picks the template matching its stage and fills the placeholders.

## After `question.md`

```
Question captured at [workflow-dir]/question.md

Next steps:
- /plan:next [workflow-dir] — let the orchestrator recommend the next stage
- /plan:research [workflow-dir] — jump to research
— or —
Stay here and refine question.md first
```

## After `research.md`

```
Research saved to [workflow-dir]/research.md

Next steps:
- /plan:next [workflow-dir]
- /plan:design [workflow-dir]
— or —
Stay here and expand the research first
```

## After `design.md`

```
Design locked at [workflow-dir]/design.md

Next steps:
- /plan:next [workflow-dir]
- /plan:structure [workflow-dir]
— or —
Stay here and refine design decisions first
```

## After `structure.md`

```
Structure drafted at [workflow-dir]/structure.md

Next steps:
- /plan:next [workflow-dir]
- /plan:create [workflow-dir]
— or —
Stay here and adjust milestone boundaries first
```

## After `plan.md` (from `/plan:create`)

```
Plan saved to [workflow-dir]/plan.md

Next steps:
1. Run /clear to free up context
2. Run /plan:implement [workflow-dir]
— or —
Run /plan:next [workflow-dir] to let the orchestrator pick
```

## After `plan.md` (from `/plan:task`)

```
Task plan saved to [workflow-dir]/plan.md

Next steps:
- Continue implementing inline (task skill executes directly)
- /plan:verify [workflow-dir] when work is done
- /commit:simple to commit
```
