---
description: Use SpecPowers Bugfix mode for known-root-cause single-point fixes
argument-hint: <bug description>
---

Using SpecPowers Bugfix mode

Use this mode only for known-root-cause, single-point bug fixes.

Flow:

```text
debug -> regression test -> fix -> simplify -> verify -> READY TO COMMIT
```

Workflow:

1. Use `superpowers:systematic-debugging` to reproduce the issue and confirm the root cause.
2. Use `superpowers:test-driven-development` to write a failing regression test first.
3. Implement the minimal fix.
4. Run `code-simplifier` to simplify and refine the changed code without changing behavior.
5. Use `superpowers:verification-before-completion` to verify the fix.

Rules:

- Do not use OpenSpec explore/propose/archive.
- Do not use `opsx:apply`.
- Do not run finalize.
- Do not commit automatically unless the user explicitly asks.
- `code-simplifier` must not change behavior or acceptance criteria.
- If the fix becomes multi-file/module or needs design decisions, stop and escalate to
  SpecPowers Standard mode.
- If verification passes, stop with `READY TO COMMIT`.
- If verification fails or the root cause is unclear, stop with `BLOCKED` and explain why.

User request:

$ARGUMENTS
