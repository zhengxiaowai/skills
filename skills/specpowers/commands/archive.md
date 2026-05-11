---
description: Archive a committed SpecPowers OpenSpec change and commit archive changes
argument-hint: <change name>
---

Archive the committed SpecPowers OpenSpec change and commit the archive changes.

Flow:

```text
confirm implementation committed
-> run opsx:archive
-> inspect archive diff
-> commit archive changes
-> ARCHIVED AND COMMITTED
```

Workflow:

1. Confirm the implementation has already been committed.
2. Check git status and ensure there are no uncommitted implementation changes.
3. Run `opsx:archive` for the requested change.
4. Inspect the archive diff.
5. Stage only archive-related OpenSpec changes.
6. Commit the archive changes using `gitcommitmessage` if available. If unavailable, follow the repository's normal commit message style.
   Suggested message:
   `docs(openspec): archive <change-name>`
7. Report `ARCHIVED AND COMMITTED` with the commit hash.

Rules:

- Do not archive unless the user explicitly requested archive.
- Do not archive before the implementation commit exists.
- Do not commit implementation code in this command.
- Do not commit unrelated files.
- Do not use `git add -A` unless the user explicitly asks.
- Do not amend commits unless the user explicitly asks.
- Do not skip hooks.
- If archive creates no changes, report `ARCHIVED` with no commit.
- If there are uncommitted implementation changes, stop with `BLOCKED`.
- If unrelated changes exist, stop and ask the user what to do.

User request:

$ARGUMENTS
