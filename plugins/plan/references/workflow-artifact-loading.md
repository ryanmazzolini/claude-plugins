# Workflow Artifact Loading

How downstream stage skills (`design`, `structure`, `create`) consume upstream artifacts.

## Load order

Glob the resolved workflow dir, then read any of these that exist:

1. `question.md` — problem framing, unknowns, constraints, success criteria.
2. `research.md` — key findings, codebase patterns, prior art, approaches, risks, open questions.
3. `design.md` — current state, target state, key decisions, chosen approach.
4. `structure.md` — proposed milestones, dependencies, verification notes.

## Synthesize before moving on

Before asking the user anything, produce a 3-6 line internal summary covering:

- The problem (from `question.md` or the goal in frontmatter).
- Key findings that shape the stage you're about to run (from `research.md`).
- Locked decisions so far (from `design.md`).
- Existing milestone shape (from `structure.md`).

Present the summary to the user as the first message of the stage — it confirms the handoff is understood and gives the user a chance to correct misinterpretations before work proceeds.

## Don't re-do prior stages

If `research.md` already answers a question, don't research it again. If `design.md` locks a decision, don't re-open it without explicit user consent. Upstream artifacts are authoritative; the current stage builds on them.

## Missing artifacts

- `design` called without `research.md` — warn and ask: "Proceed without research?" (via `AskUserQuestion`).
- `structure` called without `design.md` — warn and ask: "Proceed without a design?"
- `create` called without any upstream — suggest running `/plan:question` first, but allow "Continue anyway".

## After writing

The current stage's artifact takes its place in the dir.
