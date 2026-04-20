---
description: Create a new git worktree with ignored files (.env etc.) carried over. Use when the user wants to start work on a new branch in parallel without blocking the main checkout.
argument-hint: "[branch-name-or-shortcut-id]"
allowed-tools:
  - Bash(wtp:*)
  - Bash(git branch:*)
  - Bash(git fetch:*)
  - Bash(git worktree list:*)
  - Bash(ls:*)
---

# New Worktree

Create a new worktree via `wtp`, which runs the repo's `.wtp.yml` `post_create` hooks (env copy, `npm install`, etc.).

## Prerequisites

- `wtp` installed (`brew install satococoa/tap/wtp`)
- Repo has a `.wtp.yml` at its root defining `base_dir` and `post_create` hooks. If missing, run `wtp init` and configure it before proceeding.

## Process

1. **Parse arguments**
   - If `$ARGUMENTS` looks like a Shortcut ID (e.g. `sc-62821`, `62821`), ask the user for a short slug and build `feat-sc-<id>-<slug>` (matches this user's branch-naming convention for repos tied to Shortcut).
   - If `$ARGUMENTS` is a full branch name, use it as-is.
   - If empty, ask the user for the branch name or shortcut ID.

2. **Decide new vs existing branch**
   - `git fetch --quiet` then check `git branch --list <name>` and `git branch -r --list origin/<name>`.
   - If branch exists (local or remote): `wtp add <name>`.
   - If branch does not exist: `wtp add -b <name>` (creates from current HEAD of main by default; if the user wants a different base, pass it as the second arg).

3. **Run it**
   - Execute the `wtp add` command. The configured `post_create` hooks run automatically (e.g. copy `.env.local`, `npm install`).
   - Capture and surface the created path from wtp's output.

4. **Report**
   - Print the worktree path and any notable hook output (install warnings, copy confirmations).
   - Remind the user they can `cd $(wtp cd <branch>)` or rely on their configured shell hook.

## Notes

- If `.env.local` changes in the main repo after a worktree already exists, run `git worktreeinclude apply` inside the worktree to re-sync the files listed in `.worktreeinclude`.
- Do not manually `cp` env files — the hooks handle it. If copying fails, fix `.wtp.yml` rather than working around it.
