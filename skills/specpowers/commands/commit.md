---
description: Commit a finalized SpecPowers change using the project commit convention
argument-hint: <optional commit scope or message context>
---

Commit the current finalized SpecPowers change.

Flow:

```text
check status -> inspect diff -> confirm ready state -> commit -> COMMITTED
```

Workflow:

1. Confirm the work is ready to commit:
   - Bugfix mode: verification passed and status is `READY TO COMMIT`.
   - Simple / Standard / Complex modes: finalize returned `READY TO COMMIT`.
2. Check git status, including untracked files.
3. Inspect staged and unstaged diffs.
4. Stage only relevant files.
5. Use `gitcommitmessage` if available. If unavailable, follow the repository's normal commit message style.
6. Create the commit.
7. Report `COMMITTED` and the commit hash.

Rules:

- Do not commit if the latest status is `RETURN TO IMPLEMENTATION` or `BLOCKED`.
- Do not commit unrelated files.
- Do not commit secrets or local environment files.
- Do not use `git add -A` unless the user explicitly asks.
- Do not amend commits unless the user explicitly asks.
- Do not skip hooks.
- Do not archive after committing.
- If there are unrelated or suspicious changes, stop and ask.

User request:

$ARGUMENTS
