---
description: Research a problem space before planning. Explores prior art, codebase patterns, related implementations, and risks.
argument-hint: "[workflow dir, feature description, GitHub URL, or Shortcut URL]"
allowed-tools:
  - Task(subagent_type:general-purpose)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(gh issue view:*)
  - Bash(date +*)
  - Bash(mkdir *)
  - Glob
  - Read
  - Edit(thoughts/*/plans/**)
  - Edit(docs/plans/**)
  - Edit(.plans/**)
  - Edit(PRPs/**)
  - WebSearch
  - WebFetch
---

# Research

<rules>
- **DIVERGE FIRST**: Explore broadly before narrowing — surface options, patterns, and risks
- **WEB + CODEBASE**: Research both external prior art and internal patterns
- **DEVIL'S ADVOCATE**: Always include a contrarian agent that challenges assumptions
- **STRUCTURED OUTPUT**: Produce a `research.md` that downstream stages can consume
- **TEACH**: Explain what you found and why it matters — don't just dump links
- **NO DECISIONS**: Surface tradeoffs, don't make choices — that's `/plan:design`'s job
</rules>

## Summary

Divergent research workflow that explores a problem space before planning:

1. **Resolve target** → Find or create workflow dir from arg
2. **Parse input** → Extract problem from description, issue, or ticket
3. **Choose lens** → How to organize the research team
4. **Confirm agents** → User approves or customizes research team
5. **Run research** → Parallel agents explore web, codebase, and risks
6. **Synthesize** → Write `research.md` inside the workflow dir
7. **Handoff** → Suggest `/plan:next` or `/plan:design`

## Process

### 1. Resolve Target

Read `references/resolve-workflow-target.md` and follow its resolution priority.

```bash
DATE=$(date +%Y-%m-%d)
```

- Arg is a workflow dir → use it.
- Arg is a slug matching an existing workflow dir → use it.
- Arg is a raw goal, GitHub URL, or Shortcut ticket → generate a slug per `references/slug-generation.md` and mkdir `thoughts/*/plans/$(date +%Y-%m-%d)-[slug]/`.

If a `question.md` already exists in the workflow dir, Read it — the Research Brief and Unknowns inform what to explore.

### 2. Parse Input

**Auto-detect source from `$ARGUMENTS`:**
- Contains `github.com` or `gh#` or `#NNN` → `gh issue view <number>`
- Contains `shortcut` or `sc-` → `mcp__shortcut__stories-get-by-id`
- Otherwise → use as plain text feature description

Summarize the problem space in 2-3 sentences. Identify what needs researching.

### 3. Choose Research Lens

Analyze the feature and propose 2-4 agents for each lens. Ask the user to pick via `AskUserQuestion`:

```
header: "Research lens"
question: "How should the research team be organized?"
options:
  - label: "By problem concern (Recommended)"
    description: "e.g. Security, User experience, Integration, Performance"
  - label: "By implementation domain"
    description: "e.g. Frontend UI, API layer, Database, Auth middleware"
  - label: "By perspective"
    description: "e.g. Codebase patterns, External prior art, Devil's advocate"
```

### 4. Confirm Agents

Generate 2-4 agents based on the chosen lens, plus Devil's advocate (always included). Each agent has a clear research mandate.

**Agent types:**
- **Codebase agents** — explore internal patterns, conventions, existing implementations. Use `general-purpose` subagent with Glob/Grep/Read.
- **Web research agents** — search for prior art, OSS implementations, blog posts, library docs, known pitfalls. Use `general-purpose` subagent with WebSearch/WebFetch.
- **Devil's advocate** — challenges assumptions, identifies risks, finds counterexamples. Always included.

Present the full list and let the user confirm:

```
header: "Agents"
question: "Research team: [Agent 1], [Agent 2], ..., Devil's advocate. Run all?"
multiSelect: false
options:
  - label: "Run all (Recommended)"
    description: "Spawn all research agents in parallel"
  - label: "Customize"
    description: "Choose which agents to run"
```

If "Customize": follow up with a multiSelect listing each agent.

### 5. Run Research

**Spawn confirmed agents** in parallel (sonnet) using `general-purpose` subagent type.

Each agent prompt should include:
- The feature description and problem context
- Its specific research mandate (concern, domain, or perspective)
- What to look for: patterns, approaches, tradeoffs, risks, key files, doc links
- Instruction to explain WHY findings matter, not just list them

**Web research agents** should: search how others solve the same problem; find known pitfalls and failure modes; cite sources.

**Devil's advocate** should: challenge the premise; identify what could go wrong; find examples of projects that struggled; surface hidden complexity and operational concerns.

### 6. Synthesize Findings

Present findings to the user as narrative first — organized by theme, not by agent. Highlight consensus, contradictions, surprises, and devil's advocate concerns that seem most legitimate.

Then write `research.md` to `[workflow-dir]/research.md` using the schema from `references/stage-doc-schemas.md` (the `research.md` section). Frontmatter uses `type: workflow-stage`, `stage: research`, and `goal: [one sentence goal]`.

### 7. Handoff

Print the handoff using the "After `research.md`" template from `references/handoff-templates.md`.
