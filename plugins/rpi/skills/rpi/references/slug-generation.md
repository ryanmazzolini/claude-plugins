# Slug Generation

Deterministic rules for turning a free-text goal into a kebab-case slug. No LLM, no API — the skill reasons about this directly.

## Rules

- Lowercase.
- Kebab-case (words joined by `-`).
- 2-5 concrete domain words. Trim if longer.
- ≤48 characters total.
- ASCII only. Strip punctuation and non-alphanumerics (except `-` between words).

## Stopword list

Drop these words before picking the final 2-5:

```
a, an, and, are, as, at, be, by, for, from, has, have, in, is, it, of, on, or, that, the, to, was, were, will, with
add, adding, fix, fixing, implement, implementing, plan, planning, task, tasks, update, updating, workflow, support, new, create, creating, make, making, do, change, changing, refactor, refactoring
```

Keep domain-specific nouns, verbs, and adjectives. Prefer the most specific words.

## Examples

| Goal | Slug |
|---|---|
| "Add rate limiting to the API" | `rate-limiting-api` |
| "Implement dark mode toggle for admin dashboard" | `dark-mode-admin-dashboard` |
| "Fix auth session expiry bug" | `auth-session-expiry` |
| "Refactor the invoice export pipeline" | `invoice-export-pipeline` |
| "Migrate from Redis to Memcached" | `migrate-redis-memcached` |

## Full path

`thoughts/*/plans/$(date +%Y-%m-%d)-[slug]/` — e.g. `thoughts/*/plans/2026-04-16-rate-limiting-api/`.

## Collision

If the generated directory already exists, append `-2`, `-3`, etc. until free.
