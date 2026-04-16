---
description: Focused planning and execution for single-concern tasks. Creates a slim plan doc compatible with save/progress/verify.
argument-hint: "[workflow dir or task description]"
allowed-tools:
  - Task(subagent_type:general-purpose)
  - Read
  - Edit(thoughts/*/plans/**)
  - Edit(docs/plans/**)
  - Edit(.plans/**)
  - Edit(PRPs/**)
  - Glob
  - Bash(date +*)
  - Bash(mkdir *)
---

# Focused Task

<rules>
- **SINGLE CONCERN**: One component, one problem — suggest /plan:create for multi-component or architectural work
- **LIGHT RESEARCH**: Two parallel agents (research + devil's advocate) — not the full research team
- **SLIM DOC**: Same plan template as /plan:create but terser — flat intent list, no Artifacts section
- **EXECUTE AFTER CONFIRM**: Implement directly after user confirms approach
- **TEACH**: Explain what will change, why, and how it connects to existing code
</rules>

## Summary

Options-first workflow for focused, single-concern tasks:

1. **Resolve target** → Find or create workflow dir
2. **Clarify & research** → 1-2 questions, parallel research + devil's advocate
3. **Options** → 2-3 approaches with tradeoffs
4. **Document** → Write slim `plan.md` inside the workflow dir
5. **Execute** → Implement, test, update plan doc
6. **Complete** → Sync thoughts, prompt next steps

## Process

### 1. Resolve Target

Read `references/resolve-workflow-target.md` and follow its resolution priority.

```bash
DATE=$(date +%Y-%m-%d)
```

- Arg is a workflow dir → use it.
- Arg is a raw task description → create workflow dir via `references/slug-generation.md`.

### 2. Clarify Task

Parse task description. Use `AskUserQuestion` (1-2 questions) if unclear:
- What needs to be done?
- Any constraints or preferences?

**Step A — Choose research lens.** Analyze the task and propose 1-2 agents for each lens. Ask the user to pick via `AskUserQuestion`:

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

**Step B — Confirm agents.** Generate 1-2 agents based on the chosen lens, plus Devil's advocate (always included):

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

If "Customize": follow up with a multiSelect listing each agent.

**Spawn confirmed agents** in parallel (sonnet) using `general-purpose` subagent type. Present findings and devil's advocate concerns before moving to options.

Skip research if the task is trivial (renaming, config tweak) or context is already provided.

### 3. Present Options

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

### 4. Document Plan

Write `plan.md` to `[workflow-dir]/plan.md` using the schema from `references/plan-doc-schema.md`, with these adjustments for the task path:

- **Omit** the `## Artifacts` section (task path has no upstream artifacts).
- **Use a flat bullet list** under `## Remaining Intent` — no `###` / `####` grouping needed for single-concern work.
- Frontmatter uses `type: plan`, include `goal: [one sentence]`.
- Include the `**Workflow**:` header line pointing at the workflow dir.

### 5. Execute

Implement directly — same adaptive loop as `/plan:implement`:

1. Pick highest-value intent
2. Explain what will change and why
3. Implement
4. Test (run automated verification)
5. Update plan doc (move to Done, update Remaining Intent, log Deviations)

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

### 6. Complete

Update plan doc Status. Print handoff using the "After `plan.md` (from `/plan:task`)" template from `references/handoff-templates.md`.

## When to Use

**Good for `/plan:task`** — single-concern work:
- Bug fixes
- Small features
- Single component changes
- Adding tests
- Config changes

**Use `/plan:next` or `/plan:research` → `/plan:create` instead** — broader scope:
- Multi-component features
- Architecture changes
- Needs full research team
- Multiple stakeholders or external tracking
- Several major decisions to lock down
