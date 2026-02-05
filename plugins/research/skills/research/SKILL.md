---
name: research
description: Conduct technical research and produce structured findings. Use when doing spikes, evaluations, investigations, or when user asks to "research", "evaluate", "investigate", or "compare" technical options.
argument-hint: "[topic or gh#XXXXX]"
allowed-tools:
  - WebSearch
  - WebFetch
  - Bash(gh issue view:*)
  - Bash(gh issue comment:*)
---

# Research

Conduct technical research and produce structured findings with diagrams, options analysis, and references.

## GitHub Integration

### Tool Detection (in order of preference)

1. **gh CLI** (check with `which gh`):
   - `gh issue view <number>` - get issue context
   - `gh issue comment <number> --body "..."` - post findings as comment

2. **Manual fallback**:
   - Prompt for topic/scope directly

## Process

1. **Gather Context**
   - Parse $ARGUMENTS for GitHub issue (gh#XXXXX or #XXXXX) or topic
   - If issue found: fetch issue for problem context via `gh issue view`
   - Quick scan to understand scope

2. **Clarify** (AskUserQuestion)
   - **Scope**: What specific aspects need investigation?
   - **Constraints**: Requirements like cost, license, self-hosted?
   - **Depth**: Quick overview or deep-dive?
   - **Output destination** (infer from context, confirm with user):
     - GitHub issue comment (if issue in context)
     - Markdown file (specify path)
     - Inline response

3. **Research**
   - WebSearch for solutions, libraries, patterns
   - Explore codebase for existing patterns to integrate with
   - Analyze trade-offs between options

4. **Generate Output**

   Format findings using this template:

   <example>
   ## {Topic} Research Summary

   *Research conducted with Claude Code assistance*

   ### How {Domain} Works

   [Brief explanation of the core concept]

   ```mermaid
   flowchart LR
         A[Input] --> B[Process]
         B --> C[Output]
   ```

   ### Design Pattern

   - [Pattern Name](url) — explanation with inline deep-links

   ### Implementation Options

   | Solution | License | Key Feature | Cost  |
   |----------|---------|-------------|-------|
   | Option A | MIT     | Feature X   | Free  |
   | Option B | Apache  | Feature Y   | $X/mo |

   ### Recommended Approach: {Choice}

   - **Rationale**: Why this option fits best
   - **Integration**: How it fits with existing codebase
   - **Caveats**: Known limitations or concerns

   ### Next Steps

   1. Actionable item with owner if known
   2. Follow-up investigation if needed

   ### References

   - [Source Title](url) — brief description
   </example>

5. **Deliver Output**
   - If GitHub issue: post as comment via `gh issue comment`
   - If markdown file: write to specified path
   - If inline: display in conversation
