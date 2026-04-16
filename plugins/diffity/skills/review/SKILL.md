---
description: Review the current diff in Diffity and leave only high-signal comments
argument-hint: "[optional ref]"
disable-model-invocation: true
allowed-tools:
  - Read
  - Glob
  - Bash(which diffity)
  - Bash(diffity *)
  - Bash(git rev-parse --show-toplevel)
---

# Review

Keep reviews short, specific, and high signal.

1. Run `which diffity`.
   - If it is missing, stop and tell the user to install it with `npm install -g diffity`.
2. Start a fresh Diffity session for the requested ref so the review matches the diff exactly.
   - If `$ARGUMENTS` is empty, run `diffity --no-open --new`.
   - Otherwise run `diffity $ARGUMENTS --no-open --new`.
   - Use the Bash tool's background support. Do not append `&`.
3. Wait briefly, then gather context with:
   - `diffity agent diff`
   - `git rev-parse --show-toplevel`
   - `diffity list --json`
4. Review only changed code. Before commenting, read the full changed file for context.
5. Leave comments only for issues that matter:
   - correctness bugs
   - security issues
   - broken contracts or unsafe edge cases
   - missing tests for clearly new behavior
   Skip style nits and generic advice.
6. Use `diffity agent comment` for inline comments.
   - Prefix each comment with `[must-fix]`, `[suggestion]`, or `[question]`.
   - Prefer 0-3 strong comments over many weak ones.
7. Leave a general comment only when it adds value:
   - no issues found
   - several related issues need one summary
8. Open the browser with `diffity open` after commenting.
9. Reply with a short summary that includes the URL and the count of comments by severity.
