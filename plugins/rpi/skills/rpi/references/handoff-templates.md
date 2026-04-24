# Handoff Templates

Closing-message shapes for each stage. The selected stage picks the matching template and fills the placeholders.

## After `question.md`

```
Question captured at [workflow-dir]/question.md

Next steps:
- /rpi [workflow-dir] — let the orchestrator recommend the next stage
- /rpi research [workflow-dir] — jump to research
— or —
Stay here and refine question.md first
```

## After `research.md`

```
Research saved to [workflow-dir]/research.md

Next steps:
- /rpi [workflow-dir]
- /rpi design [workflow-dir]
— or —
Stay here and expand the research first
```

## After `design.md`

```
Design locked at [workflow-dir]/design.md

Next steps:
- /rpi [workflow-dir]
- /rpi structure [workflow-dir]
— or —
Stay here and refine design decisions first
```

## After `structure.md`

```
Structure drafted at [workflow-dir]/structure.md

Next steps:
- /rpi [workflow-dir]
- /rpi plan [workflow-dir]
— or —
Stay here and adjust milestone boundaries first
```

## After `plan.md` (from `/rpi plan`)

```
Plan saved to [workflow-dir]/plan.md

Next steps:
1. Run /clear to free up context
2. Run /rpi implement [workflow-dir]
— or —
Run /rpi [workflow-dir] to let the orchestrator pick
```

## After `plan.md` (from `/rpi task`)

```
Task plan saved to [workflow-dir]/plan.md

Next steps:
- Continue implementing inline (task skill executes directly)
- /rpi verify [workflow-dir] when work is done
- /commit:simple to commit
```
