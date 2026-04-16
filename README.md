# claude-plugins

A personal marketplace of [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) plugins that I use day-to-day. Opinionated, not intended to be universally applicable — fork it, keep what's useful, and adapt the rest.

## Plugins

| Plugin | Summary |
|---|---|
| `plan` | Staged implementation planning workflow (QRSPI): question → research → design → structure → create → implement → verify. Each stage writes a durable artifact into a per-plan workflow directory. Autodetects `.plans/`, `thoughts/`, `docs/plans/`, or `PRPs/`. Walkthrough: [YouTube — QRSPI in action](https://www.youtube.com/watch?v=YwZR6tc7qYg). |
| `commit` | Git commit, PR, and workflow automation. |
| `review` | Code review workflows with progressive loading. |
| `research` | Technical research with structured output. |
| `explain` | Code explanation with visual diagrams and analogies. |
| `diffity` | Browser-first diff review via [Diffity](https://www.npmjs.com/package/diffity). |
| `humanlayer` | [HumanLayer](https://humanlayer.dev) thoughts integration — auto-persists plans, research, and reviews under `thoughts/`. |
| `ai-authoring` | Context guidelines for AI configuration work. |
| `typescript-nextjs` | TypeScript and Next.js patterns and conventions. |
| `ux-accessibility` | Usability heuristics, accessibility standards, and responsive design. |

## Install

Add the marketplace, then install any plugins you want:

```
/plugin marketplace add ryan-mazzolini/claude-plugins
/plugin install plan@ryan-personal
```

## Caveats before you fork

These plugins are **calibrated to my workflow**. In particular:

- **`humanlayer` requires [HumanLayer](https://humanlayer.dev).** It assumes a `thoughts/` symlink farm exists at the repo root (`humanlayer thoughts init`). Skip this plugin if you don't use HumanLayer. `plan` works fine without it — it'll autodetect `.plans/`, `docs/plans/`, or `PRPs/` instead.
- **Some plugins are fairly bare.** `typescript-nextjs`, `ux-accessibility`, and `ai-authoring` are mostly rule sets I iterate on — treat them as examples more than products.

## Contributing

This is a personal repo; PRs welcome but expect opinionated pushback. If you've built something similar, I'd rather link to your marketplace than merge a generalization that dilutes the intent here.

## License

MIT — see [LICENSE](./LICENSE).
