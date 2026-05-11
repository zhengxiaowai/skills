---
description: Use SpecPowers Standard mode for multi-file changes needing design decisions
argument-hint: <feature or change request>
---

Using SpecPowers Standard mode

Use this mode for multi-file or multi-module changes that need design decisions.

Flow:

```text
explore -> propose -> brainstorm -> TDD -> simplify -> verify -> READY FOR FINALIZE
```

Workflow:

1. Use `opsx:explore` to clarify boundaries, constraints, and impact.
2. Use `opsx:propose` to generate proposal, design, specs, and tasks.
3. Use `superpowers:brainstorming` to converge on implementation details from the proposal.
4. Use `superpowers:test-driven-development` for test-first implementation.
5. Run `code-simplifier` to simplify and refine the changed code without changing behavior.
6. Use `superpowers:verification-before-completion` to verify the change.
7. Stop with `READY FOR FINALIZE`. Finalize is manual and handled by `/user:specpowers:finalize`.

Rules:

- Do not use `opsx:apply`.
- Do not archive automatically.
- Do not commit automatically unless the user explicitly asks.
- If the task becomes cross-module architecture work or a new subsystem, stop and escalate to
  SpecPowers Complex mode.
- `code-simplifier` must not change behavior or acceptance criteria.
- If verification passes, stop with `READY FOR FINALIZE`.
- If verification fails, stop with `BLOCKED`.
- If simplification changes behavior or acceptance criteria, return to implementation and verify again.

User request:

$ARGUMENTS
