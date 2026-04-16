# Stage Document Schemas

Canonical shapes for the four upstream stage artifacts. Each is written into the workflow dir (`thoughts/*/plans/YYYY-MM-DD-[slug]/`).

## Shared frontmatter

```yaml
---
source: {repo basename}
date: YYYY-MM-DD
type: workflow-stage
stage: [question | research | design | structure]
goal: [one-sentence goal for the whole workflow]
---
```

## `question.md`

```markdown
# Question

## Problem
[1-2 sentence framing]

## Known Facts
- ...

## Unknowns
- ...

## Constraints
- ...

## Success Criteria
- ...

## Research Brief
- **Goal**: [what research should answer]
- **Unknowns to research**: ...
- **Evidence to gather**: ...
```

## `research.md`

```markdown
# Research

## Problem
[1-2 sentence problem statement]

## Key Findings
- [3-5 most important things learned]

## Codebase Patterns
- [file path] — [why it matters]

## Prior Art
- [URL] — [what it shows]

## Approaches Identified

### [Approach name]
- **How**: ...
- **Pros**: ...
- **Cons**: ...
- **Evidence**: ...

## Risks & Concerns
- [Risk]: [why it matters] — [evidence]

## Open Questions
- ...

## Sources
- [URL or file path] — [contribution]
```

## `design.md`

```markdown
# Design

## Current State
- ...

## Target State
- ...

## Key Decisions
- [Decision]: [choice] — [why]

## Chosen Approach
[Short narrative tying decisions into one strategy]

## Risks / Open Questions
- ...
```

## `structure.md`

Uses the milestone syntax from `references/milestone-format.md`.

```markdown
# Structure

## Proposed Milestones

### [Concern area]

#### [Milestone]
- [Outcome]
- [Outcome]

#### [Milestone]
_after: [Dependency]_
- [Outcome]

## Sequencing Notes
- ...

## Verification Notes
- Automated: [test commands]
- Manual: [step-by-step hints]
```
