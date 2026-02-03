---
description: Context engineering principles for AI configuration. Auto-apply when editing CLAUDE.md, skills, commands, rules, agent prompts, or discussing AI configuration.
---

# Context Engineering Principles

Apply these principles when writing prompts, agent configurations, CLAUDE.md files, skills, commands, or AI documentation.

## 1. Signal-to-Noise Ratio

Concrete examples beat abstract rules.

- 2-3 canonical patterns > 15 edge cases
- Clear boundaries > exhaustive checklists
- Architecture decisions > task-specific specs
- Show patterns, not procedures

```markdown
❌ "Handle all error cases appropriately"
✅ `throw new AuthError('Invalid token', { code: 401 })`
```

## 2. Altitude Matching

Find the right level of specificity.

- **Too specific**: Hardcoded logic, rigid step-by-step
- **Too vague**: Assumed context, unclear goals
- **Right level**: Clear boundaries + flexible execution

Describe "what" and "why", not "how".

```markdown
❌ "Step 1: Open file. Step 2: Read line 5..."
✅ "Extract user preferences from config file"
```

## 3. Context Organization

Structure for both AI and human navigation.

- XML tags for AI parsing (`<role>`, `<constraints>`)
- Markdown headers for human scanning
- Max 2 levels of nesting
- Semantic section boundaries

```markdown
❌ Flat wall of text

✅ <role>Senior engineer...</role>
   ## Critical Constraints
   - Never...
```

## 4. Tool Design

Minimal, non-overlapping, self-contained.

- Fewer tools > more tools (reduce decision complexity)
- Non-overlapping functionality (clear boundaries)
- Self-contained descriptions (no cross-references)
- Token-conscious outputs (summarize when appropriate)

```markdown
❌ readFile(), getFileContents(), loadFile()
✅ Single Read tool
```

## 5. Memory Architecture

Separate persistent from ephemeral context.

| Type | Examples | Storage |
|------|----------|---------|
| Persistent | Principles, style guides, architecture | CLAUDE.md, skills |
| Ephemeral | File contents, debug traces, task specs | Session only |

- Throw away ephemeral after task completion
- Store reusable patterns, not one-time details

```markdown
❌ Storing entire file diffs in memory
✅ Storing "prefer composition over inheritance"
```

## 6. Examples Over Rules

Show, don't tell.

- Real code > pseudocode
- Anti-patterns alongside patterns
- Diverse scenarios, not similar variations
- Examples demonstrate edge cases implicitly

```markdown
❌ "Use descriptive names"
✅ `getUserById(id)` not `get(x)`
```

## 7. Iterative Development

Build based on observation, not speculation.

1. Start minimal (under-specify initially)
2. Add guidance based on observed failures
3. Deploy → Observe → Refine → Repeat
4. Resist premature optimization of prompts

```markdown
❌ 500-line prompt on day one
✅ 50 lines → test → add missing boundaries
```

---

## Application Patterns

### Writing CLAUDE.md Files

```markdown
# Project Name

<role>
[One sentence describing Claude's role and optimization goals]
</role>

<constraints>
- NEVER [critical constraint]
- ALWAYS [required behavior]
</constraints>

## [Topic]
[2-3 examples showing the pattern]

## Commands
[Available commands with brief descriptions]
```

### Writing Skills

```yaml
---
description: [What it does] + [When to trigger]. Include keywords users mention.
---

# Skill Name

[Core instructions - keep under 100 lines]
[Include examples, not exhaustive rules]
```

### Writing Agent Prompts

```markdown
<role>
You are [specific role] optimizing for [goal].
</role>

## When to [action]
- [Specific trigger]
- [Another trigger]

## Examples
[2-3 concrete examples]

## Constraints
- [Boundary]
- [Boundary]
```

---

## Anti-Patterns

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Wall of text | Hard to parse | Use headers, XML tags |
| Exhaustive rules | Token waste | 2-3 examples instead |
| Vague instructions | Inconsistent behavior | Concrete examples |
| Premature complexity | Over-engineered | Start minimal, iterate |
| Overlapping tools | Decision paralysis | Consolidate |
| Storing ephemeral data | Context bloat | Session-only |

---

## Quick Checklist

When writing AI configuration:

- [ ] Role and optimization goals clear (#2)
- [ ] Examples for critical behaviors (#6)
- [ ] XML + markdown structure (#3)
- [ ] Under 150 lines for skills/commands (#1)
- [ ] No overlapping functionality (#4)
- [ ] Persistent vs ephemeral separated (#5)

## Sources

- [Anthropic: Effective Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Martin Fowler: AI Development Observations](https://martinfowler.com/articles/202508-ai-thoughts.html)
