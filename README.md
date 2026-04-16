# claude-plugins

A personal marketplace of [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) plugins that I use day-to-day. Opinionated, not intended to be universally applicable — fork it, keep what's useful, and adapt the rest.

## Plugins

| Plugin | Summary |
|---|---|
| `plan` | Staged implementation planning workflow: question → research → design → structure → create → implement → verify. Each stage writes a durable artifact into a per-plan workflow directory. |
| `commit` | Git commit, PR, and workflow automation. |
| `review` | Code review workflows with progressive loading. |
| `research` | Technical research with structured output. |
| `explain` | Code explanation with visual diagrams and analogies. |
| `diffity` | Browser-first diff review via [Diffity](https://www.npmjs.com/package/diffity). |
| `humanlayer` | [HumanLayer](https://humanlayer.dev) thoughts integration — auto-persists plans, research, and reviews under `thoughts/`. |
| `persona` | Engineering personas for specialized contexts. |
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

- **HumanLayer profile name is hardcoded as `ryan`.** The `plan` and `humanlayer` plugins reference paths like `thoughts/ryan/plans/…` and include `Edit(thoughts/ryan/plans/**)` in their allowed-tools frontmatter. If you use HumanLayer with a different profile, search-and-replace `ryan` in `plugins/plan/` and `plugins/humanlayer/` to match your profile name.
- **HumanLayer is required for `plan` and `humanlayer`.** Those plugins assume a working `thoughts/` symlink. Install HumanLayer and run `humanlayer thoughts init` before using them, or skip those plugins.
- **Some plugins are fairly bare.** `persona`, `typescript-nextjs`, `ux-accessibility`, and `ai-authoring` are mostly rule sets I iterate on — treat them as examples more than products.

## Contributing

This is a personal repo; PRs welcome but expect opinionated pushback. If you've built something similar, I'd rather link to your marketplace than merge a generalization that dilutes the intent here.

## License

MIT — see [LICENSE](./LICENSE).
