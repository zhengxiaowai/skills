---
description: Use SpecPowers Complex mode for architecture changes or cross-module refactors
argument-hint: <large change request>
---

Using SpecPowers Complex mode

Use this mode for cross-module refactors, new subsystems, architecture changes, or large
changes that need coordinated execution.

Flow:

```text
explore
-> propose
-> brainstorm
-> parallel/subagent execution
-> simplify
-> verify
-> code review
-> READY FOR FINALIZE
```

Workflow:

1. Use `opsx:explore` to map architectural impact, dependencies, constraints, and risks.
2. Use `opsx:propose` to generate full proposal, design, specs, and tasks.
   - Split each capability into its own spec where appropriate.
   - Split tasks into independent or dependency-ordered execution units.
3. Use `superpowers:brainstorming` to converge on module interfaces and execution order.
4. Choose execution strategy:
   - Use `superpowers:dispatching-parallel-agents` for independent tasks.
   - Use `superpowers:subagent-driven-development` for dependent tasks.
5. Run `code-simplifier` to simplify and refine the changed code without changing behavior.
6. Use `superpowers:verification-before-completion` for full verification.
7. Use `superpowers:requesting-code-review` for code review.
8. Stop with `READY FOR FINALIZE`. Finalize is manual and handled by `/user:specpowers:finalize`.

Rules:

- Do not use `opsx:apply`.
- Do not archive automatically.
- Do not commit automatically unless the user explicitly asks.
- If the task is smaller than expected, you may downgrade to Standard mode before execution.
- `code-simplifier` must not change behavior or acceptance criteria.
- If verification and code review pass, stop with `READY FOR FINALIZE`.
- If verification fails or review finds unresolved issues, stop with `BLOCKED`.
- If simplification changes behavior or acceptance criteria, return to implementation and verify again.

User request:

$ARGUMENTS
