# SpecPowers Workflow

```text
/user:specpowers
  -> assess task complexity

Modes:
  Bugfix: known root cause
    -> systematic debug
    -> regression test
    -> fix
    -> simplify
    -> verify
    -> READY TO COMMIT

  Simple: single file/module
    -> lightweight opsx:propose
    -> brainstorming
    -> TDD
    -> simplify
    -> verify
    -> READY FOR FINALIZE

  Standard: multi-file/module
    -> opsx:explore
    -> opsx:propose
    -> brainstorming
    -> TDD
    -> simplify
    -> verify
    -> READY FOR FINALIZE

  Complex: architecture/cross-module
    -> opsx:explore
    -> opsx:propose
    -> brainstorming
    -> parallel/subagent execution
    -> simplify
    -> verify
    -> code review
    -> READY FOR FINALIZE

READY FOR FINALIZE
  -> /user:specpowers:finalize

Finalize outcomes:
  aligned, no spec change
    -> READY TO COMMIT

  docs-only spec update
    -> READY TO COMMIT

  behavior / requirement change
    -> RETURN TO IMPLEMENTATION
    -> implement
    -> simplify
    -> verify
    -> READY FOR FINALIZE

  mismatch / open question / unsafe state
    -> BLOCKED
    -> resolve blocker
    -> resume from stage that produced blocker

READY TO COMMIT
  -> /user:specpowers:commit
  -> implementation commit created
  -> /user:specpowers:archive
  -> opsx:archive
  -> archive commit created
```

Complex mode joins the same finalize gate after its heavier execution path:

```text
Complex:
  opsx:explore
  -> opsx:propose
  -> brainstorming
  -> parallel/subagent execution
  -> code-simplifier
  -> verify
  -> code review
  -> READY FOR FINALIZE
```

## Runtime State Machine

```text
START
  -> ASSESS_MODE
  -> BUGFIX | SIMPLE | STANDARD | COMPLEX
  -> IMPLEMENT
  -> SIMPLIFY
  -> VERIFY

VERIFY outcomes:
  bugfix pass -> READY_TO_COMMIT
  simple/standard/complex pass -> READY_FOR_FINALIZE

READY_FOR_FINALIZE
  -> FINALIZE

FINALIZE outcomes:
  READY_TO_COMMIT -> COMMIT_IMPLEMENTATION -> ARCHIVE -> ARCHIVED_AND_COMMITTED
  RETURN_TO_IMPLEMENTATION -> IMPLEMENT -> SIMPLIFY -> VERIFY -> READY_FOR_FINALIZE
  BLOCKED -> resolve blocker -> resume from stage that produced blocker
```

## BLOCKED Recovery

`BLOCKED` pauses the workflow. It does not automatically return to implementation.
Resolve the blocker first, then resume from the stage that produced it.

| Blocker | Resume after resolution |
|---|---|
| Verification missing or failing | `VERIFY`, or implementation if fixes are needed |
| Spec/design issue | `PROPOSE` |
| Implementation mismatch | implementation, then verify and finalize |
| Tasks inaccurate | update tasks, then finalize |
| Open question | ask user, then resume from propose or brainstorming |
| Code review feedback | `superpowers:receiving-code-review`, then implementation |
| Unsafe repo state | resolve repository state, then resume original stage |
| Scope expanded | escalate mode: Simple -> Standard, Standard -> Complex |

Rules:

- NEVER use `opsx:apply`.
- Development commands stop before commit/archive.
- `finalize` decides whether commit is safe.
- `commit` creates the implementation commit only.
- `archive` runs `opsx:archive` and commits archive-only changes.
