---
description: Open Diffity for the current repo so you can inspect changes in the browser
argument-hint: "[optional ref]"
disable-model-invocation: true
allowed-tools:
  - Bash(which diffity)
  - Bash(diffity *)
  - Bash(git rev-parse --show-toplevel)
---

# Diff

Use Diffity as a browser-first diff surface.

1. Run `which diffity`.
   - If it is missing, stop and tell the user to install it with `npm install -g diffity`.
2. Start Diffity for the requested ref.
   - If `$ARGUMENTS` is empty, run `diffity`.
   - Otherwise run `diffity $ARGUMENTS`. eg. `diffity HEAD~1` or `diffity main..feature` or `diffity work` (uncommitted changes).
   - Use the Bash tool's background support. Do not append `&`.
3. Wait briefly, then run:
   - `git rev-parse --show-toplevel`
   - `diffity list --json`
4. Match the running Diffity session for the current repo and report only the local URL.
5. Keep the reply short:
   - say the diff is ready
   - suggest leaving comments in the browser and then running `/diffity:resolve`
   - suggest `/diffity:review` for an AI review
