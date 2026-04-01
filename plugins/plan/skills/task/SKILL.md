---
description: Focused planning and execution for single-concern tasks. Creates a slim plan doc compatible with save/progress/verify.
argument-hint: "[task description]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:general-purpose)
  - Read
  - Edit(thoughts/ryan/plans/**)
  - Glob
  - Bash(date +*)
  - Bash(humanlayer thoughts sync)
---

# Focused Task

<rules>
- **SINGLE CONCERN**: One component, one problem — suggest /plan:create for multi-component or architectural work
- **LIGHT RESEARCH**: Two parallel agents (research + devil's advocate) — not the full research team
- **SLIM DOC**: Same plan template as /plan:create but terser — flat intent list, no research summary
- **EXECUTE AFTER CONFIRM**: Implement directly after user confirms approach
- **TEACH**: Explain what will change, why, and how it connects to existing code
</rules>

## Summary

Options-first workflow for focused, single-concern tasks:

1. **Clarify & Research** → 1-2 questions, parallel research + devil's advocate
2. **Options** → 2-3 approaches with tradeoffs
3. **Document** → Write slim plan doc to `thoughts/ryan/plans/`
4. **Execute** → Implement, test, update plan doc
5. **Complete** → Sync thoughts, prompt next steps

## Process

### 1. Clarify Task

```bash
DATE=$(date +%Y-%m-%d)
```

Parse task description. Use `AskUserQuestion` (1-2 questions) if unclear:
- What needs to be done?
- Any constraints or preferences?

**Step A — Choose research lens.** Analyze the task and propose 1-2 agents for each lens below. Then ask the user to pick a lens via `AskUserQuestion`:

```
header: "Research lens"
question: "How should the research agents be organized?"
options:
  - label: "By problem concern (Recommended)"
    description: "e.g. Security, User experience, Performance"
  - label: "By implementation domain"
    description: "e.g. Component state, API validation, Schema"
  - label: "By perspective"
    description: "e.g. Codebase explorer, Devil's advocate"
```

**Step B — Confirm agents.** Generate 1-2 agents based on the chosen lens, plus Devil's advocate (always included). Present the full list and let the user confirm or customize:

```
header: "Agents"
question: "Research team: [Agent 1], ..., Devil's advocate. Run all?"
multiSelect: false
options:
  - label: "Run all (Recommended)"
    description: "Spawn all research agents in parallel"
  - label: "Customize"
    description: "Choose which agents to run"
```

If "Customize": follow up with a multiSelect listing each agent so the user can deselect specific ones.

**Spawn confirmed agents** in parallel (sonnet) using `general-purpose` subagent type.

Present findings and devil's advocate concerns before moving to options.

Skip research if the task is trivial (e.g. renaming, config tweak) or context is already provided.

### 2. Present Options

Identify 1-3 key decisions (if task has choices).

**Use `AskUserQuestion`** for each:
```
header: "Approach"
question: "How should we implement [task]?"
options:
  - label: "Minimal change"
    description: "Scope: [X]. Tradeoff: [Y]."
  - label: "Thorough approach"
    description: "Scope: [X]. Tradeoff: [Y]."
```

**If one obvious approach**: Skip options, present approach and confirm.

### 3. Document Plan

Write plan doc to `thoughts/ryan/plans/YYYY-MM-DD-[slug].md`.

Same template as `/plan:create` but terser — no research summary, brief sections, flat intent list:

```markdown
---
source: {repo basename}
date: YYYY-MM-DD
type: plan
---

# [Task Name] - YYYY-MM-DD

**Status**: In Progress | **Goal**: [one sentence]
**External**: [Link if applicable]

## Approach
[Selected approach in 1-2 sentences]

## Decisions
- [Any choices made during options step]

## Done
[Empty]

## Remaining Intent
- [Flat list of goal-oriented outcomes]

## Deviations
[Empty]

## Verification

### Automated
- [test commands]

### Manual
1. [Verification steps]

## Notes
[Empty]
```

Sync from the repo root (where the `thoughts/` symlink lives) after writing:
```bash
humanlayer thoughts sync
```

### 4. Execute

Implement directly — same adaptive loop as `/plan:implement`:

1. Pick highest-value intent
2. Explain what will change and why
3. Implement
4. Test (run automated verification)
5. Update plan doc (move to Done, update Remaining Intent, log Deviations)
6. Run `humanlayer thoughts sync` from the repo root

**Multi-step tasks** — check in between intents via `AskUserQuestion`:
```
header: "Next"
question: "Step complete. What now?"
options:
  - label: "Continue"
    description: "Move to next intent"
  - label: "Commit"
    description: "Commit current progress via /commit:simple"
  - label: "Save & pause"
    description: "Checkpoint to Notes and free up context"
```

**Single-intent tasks** — skip the check-in, complete and report.

### 5. Complete

Update plan doc Status. Report:
```
Done: [Brief summary]
Changed: [Files affected]

Next steps:
- /plan:verify [slug] — run verification
- /commit:simple — commit changes
```

## When to Use

**Good for `/plan:task`** — single-concern work:
- Bug fixes
- Small features
- Single component changes
- Adding tests
- Config changes

**Use `/plan:research` → `/plan:create` instead** — broader scope:
- Multi-component features
- Architecture changes
- Needs full research team (web, prior art, devil's advocate)
- Multiple stakeholders or external tracking
- Several major decisions to lock down
