---
description: Resolve open comments from the Diffity tree browser by making code changes
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

# Resolve Tree

Resolve comments left in the Diffity tree browser by working back and forth with the user. Your goal is to make the most correct, simple and maintainable fixes. Same workflow as `/diffity:resolve` but for tree-mode comments.

1. Run `which diffity`.
   - If missing, stop and tell the user to install it with `npm install -g diffity`.
2. Verify a tree session exists: `diffity agent list`.
   - If it fails with "No active review session", tell the user to start one with `/diffity:tree`.
3. Load open threads with `diffity agent list --status open --json`.
   - If `$ARGUMENTS` contains a thread id, narrow to that thread.
   - If there are no actionable threads, say there is nothing to resolve.
4. Skip general comments (`filePath == "__general__"`).
5. For each actionable thread:
   - read the latest comment and the surrounding file context
   - work back and forth with the user until the fix is clear
   - when done, resolve with `diffity agent resolve <id> --summary "Fixed: ..."`
6. For question threads, answer with a reply `diffity agent reply <id> --body "..."` or resolve summary.
7. After processing, run `diffity agent list --json` to confirm state.
8. Reply briefly and tell the user to check the browser.
