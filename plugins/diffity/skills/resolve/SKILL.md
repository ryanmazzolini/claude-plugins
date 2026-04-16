---
description: Resolve open Diffity threads by making the requested code changes
argument-hint: "[optional thread id]"
disable-model-invocation: true
allowed-tools:
  - Read
  - Edit
  - Write
  - Glob
  - Bash(which diffity)
  - Bash(diffity *)
---

# Resolve

Resolve open Diffity feedback by working back and forth with the user. Your goal is to make the most correct, simple and maintainable fixes.

1. Run `which diffity`.
   - If it is missing, stop and tell the user to install it with `npm install -g diffity`.
2. Load open threads with `diffity agent list --status open --json`.
   - If `$ARGUMENTS` contains a thread id, narrow to that thread.
   - If there are no actionable threads, tell the user there is nothing to resolve.
3. Skip general comments (`filePath == "__general__"`).
4. For each actionable thread:
   - read the latest comment and the surrounding file context
   - work back and forth with the user until the fix is clear
   - when the change is done, resolve it with `diffity agent resolve <id> --summary "Fixed: ..."`
5. If the thread is really a question instead of a change request, answer it with a reply `diffity agent reply <id> --body "..."` or resolve summary.
6. After processing threads, run `diffity agent list --json` to confirm state.
7. Reply briefly and tell the user to check Diffity in the browser.
