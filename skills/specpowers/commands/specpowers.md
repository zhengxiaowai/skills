---
description: Route a development task to the appropriate SpecPowers mode
argument-hint: <task>
---

Assess the task and choose the appropriate SpecPowers mode.

Flow:

```text
task
  -> assess complexity
  -> known root-cause bugfix: Bugfix
  -> single module, clear boundary: Simple
  -> multi-file, design decisions: Standard
  -> architecture / cross-module: Complex

if uncertain -> Standard
```

Modes:

- Bugfix mode: known root-cause, single-point bugfix.
- Simple mode: single file/module, clear boundaries.
- Standard mode: multi-file/module change needing design decisions.
- Complex mode: cross-module refactor, new subsystem, or architecture change.

Immediately announce:

```text
Using SpecPowers <mode> mode
```

Then follow that mode's workflow.

Rules:

- Do not use `opsx:apply`.
- Do not archive automatically.
- Do not commit automatically unless the user explicitly asks.
- For Bugfix mode, stop at `READY TO COMMIT` after verification.
- For Simple, Standard, and Complex modes, stop after verification with `READY FOR FINALIZE`.
- Finalize is manual and handled by `/user:specpowers:finalize`.
- Archive is handled only by `/user:specpowers:archive`.
- Commit is handled only by `/user:specpowers:commit` or an explicit user request.
- If the user asks for help, use `references/help.md`.
- If the user asks about the workflow, use `references/flow.md`.
- If the user asks to install commands, use `references/install.md`.

User request:

$ARGUMENTS
