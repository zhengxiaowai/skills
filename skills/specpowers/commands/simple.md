---
description: Use SpecPowers Simple mode for bounded single-module changes
argument-hint: <change request>
---

Using SpecPowers Simple mode

Use this mode for single-file or single-module changes with clear boundaries.

Flow:

```text
lightweight propose -> brainstorm -> TDD -> simplify -> verify -> READY FOR FINALIZE
```

Workflow:

1. Use `opsx:propose` in lightweight form:
   - create proposal + tasks only
   - do not create full design/spec unless the task grows
2. Use `superpowers:brainstorming` to confirm implementation details.
3. Use `superpowers:test-driven-development` for test-first implementation.
4. Run `code-simplifier` to simplify and refine the changed code without changing behavior.
5. Use `superpowers:verification-before-completion` to verify the change.
6. Stop with `READY FOR FINALIZE`. Finalize is manual and handled by `/user:specpowers:finalize`.

Rules:

- Do not use `opsx:explore` by default.
- Do not use `opsx:apply`.
- Do not archive automatically.
- Do not commit automatically unless the user explicitly asks.
- If the task becomes multi-file/module or needs design decisions, stop and escalate to
  SpecPowers Standard mode.
- `code-simplifier` must not change behavior or acceptance criteria.
- If verification passes, stop with `READY FOR FINALIZE`.
- If verification fails, stop with `BLOCKED`.
- If simplification changes behavior or acceptance criteria, return to implementation and verify again.

User request:

$ARGUMENTS
