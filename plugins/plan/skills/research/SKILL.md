---
description: Research a problem space before planning. Explores prior art, codebase patterns, related implementations, and risks.
argument-hint: "[feature description, GitHub URL, or Shortcut URL]"
disable-model-invocation: true
allowed-tools:
  - Task(subagent_type:general-purpose)
  - mcp__shortcut__stories-get-by-id
  - mcp__shortcut__stories-get-branch-name
  - Bash(gh issue view:*)
  - Bash(date +*)
  - Bash(mkdir *)
  - Bash(humanlayer *)
  - Glob
  - Read
  - Edit(thoughts/ryan/research/**)
  - WebSearch
  - WebFetch
---

# Research

<rules>
- **DIVERGE FIRST**: Explore broadly before narrowing — surface options, patterns, and risks
- **WEB + CODEBASE**: Research both external prior art and internal patterns
- **DEVIL'S ADVOCATE**: Always include a contrarian agent that challenges assumptions
- **STRUCTURED OUTPUT**: Produce a research doc that /plan:create can consume
- **TEACH**: Explain what you found and why it matters — don't just dump links
- **NO DECISIONS**: Surface tradeoffs, don't make choices — that's /plan:create's job
</rules>

## Summary

Divergent research workflow that explores a problem space before planning:

1. **Parse Input** → Extract feature from description, issue, or ticket
2. **Choose Lens** → How to organize the research team
3. **Confirm Agents** → User approves or customizes research team
4. **Run Research** → Parallel agents explore web, codebase, and risks
5. **Synthesize** → Combine findings into structured research doc
6. **Handoff** → Suggest `/plan:create` or `/plan:task`

## Process

### 1. Parse Input

```bash
DATE=$(date +%Y-%m-%d)
```

**Auto-detect source from `$ARGUMENTS`:**
- Contains `github.com` or `gh#` or `#NNN` → `gh issue view <number>`
- Contains `shortcut` or `sc-` → `mcp__shortcut__stories-get-by-id`
- Otherwise → use as plain text feature description

Summarize the problem space in 2-3 sentences. Identify what needs researching.

### 2. Choose Research Lens

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

### 3. Confirm Agents

Generate 2-4 agents based on the chosen lens, plus Devil's advocate (always included). Each agent has a clear research mandate.

**Agent types and what they do:**

- **Codebase agents** — explore internal patterns, conventions, existing implementations relevant to the feature. Use `general-purpose` subagent with Glob/Grep/Read access.
- **Web research agents** — search for prior art, OSS implementations, blog posts, library docs, and known pitfalls. Use `general-purpose` subagent with WebSearch/WebFetch.
- **Devil's advocate** — challenges assumptions, identifies risks, finds counterexamples. Explores both codebase and web for failure modes and cautionary tales. Always included.

Present the full list and let the user confirm or customize:

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

If "Customize": follow up with a multiSelect listing each agent so the user can deselect specific ones.

### 4. Run Research

**Spawn confirmed agents** in parallel (sonnet) using `general-purpose` subagent type.

Each agent prompt should include:
- The feature description and problem context
- Its specific research mandate (concern, domain, or perspective)
- What to look for: patterns, approaches, tradeoffs, risks, key files, doc links
- Instruction to explain WHY findings matter, not just list them

**Web research agents** should:
- Search for how other projects/libraries solve the same problem
- Look for known pitfalls, anti-patterns, and failure modes
- Find relevant documentation for libraries or APIs that might be used
- Cite sources with URLs

**Devil's advocate** should:
- Challenge the premise — is this the right problem to solve?
- Identify what could go wrong with likely approaches
- Find examples of projects that tried similar things and struggled
- Surface hidden complexity or scope creep risks
- Consider maintenance burden and operational concerns

### 5. Synthesize Findings

Present findings to the user with explanations. Then write a structured research doc.

**Present findings as narrative first** — organize by theme, not by agent. Explain what was found and why it matters. Highlight:
- Consensus across agents (strong signals)
- Contradictions between agents (areas needing decisions)
- Surprises or non-obvious findings
- Devil's advocate concerns that seem most legitimate

**Write research doc** to `thoughts/ryan/research/YYYY-MM-DD-[slug].md`:

```markdown
---
source: {repo basename}
date: YYYY-MM-DD
type: research
---

# [Feature Name] — Research

**Date**: YYYY-MM-DD
**Problem**: [1-2 sentence problem statement]
**External**: [Link if applicable]

## Key Findings

[3-5 bullet points — the most important things learned]

## Codebase Patterns

[Relevant existing patterns, conventions, and code that relates to this feature.
Include file paths and brief explanations of why each matters.]

## Prior Art

[How other projects/libraries solve this. Include links and brief assessments.
Note what worked, what didn't, and why.]

## Approaches Identified

[2-4 possible approaches surfaced during research. For each:]

### [Approach name]
- **How**: [Brief description]
- **Pros**: [What's good about it]
- **Cons**: [What's risky or costly]
- **Evidence**: [What supports this — codebase patterns, external examples, etc.]

## Risks & Concerns

[Devil's advocate findings and other risks. Be specific:]
- [Risk]: [Why it matters] — [Evidence or reasoning]

## Open Questions

[Things that still need answers before planning. These feed into /plan:create's
definition and options steps.]

## Sources

- [URL or file path] — [What it contributed]
```

### 6. Handoff

Sync from the repo root and prompt next steps:

```bash
humanlayer thoughts sync
```

Assess complexity and suggest the appropriate next step:

```
Research saved to thoughts/ryan/research/YYYY-MM-DD-[slug].md

Next steps:
- /plan:create — distill research into an implementation plan
- /plan:task — if this turned out to be simpler than expected
```
