---
description: Open the Diffity file tree browser to browse and comment on any file
disable-model-invocation: true
allowed-tools:
  - Bash(which diffity)
  - Bash(diffity *)
  - Bash(git rev-parse --show-toplevel)
---

# Tree

Open a full file-tree browser for the current repo — no diff required.

1. Run `which diffity`.
   - If missing, stop and tell the user to install it with `npm install -g diffity`.
2. Run `diffity tree` using the Bash tool's background support. Do not append `&`.
3. Wait briefly, then run:
   - `git rev-parse --show-toplevel`
   - `diffity list --json`
4. Match the running session for the current repo and report the local URL.
5. Keep the reply short:
   - say the tree browser is ready
   - suggest leaving comments in the browser and then running `/diffity:resolve-tree`
