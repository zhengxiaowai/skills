---
description: Finalize a SpecPowers change before commit by checking spec, tasks, code, and tests
argument-hint: <change name or context>
---

Finalize the current SpecPowers change.

Flow:

```text
verify complete
  -> compare spec / tasks / code / tests
  -> mismatch or open questions: BLOCKED
  -> aligned, no spec change: READY TO COMMIT
  -> spec updated:
       docs-only change: READY TO COMMIT
       behavior / requirement change: RETURN TO IMPLEMENTATION
```

Workflow:

1. Confirm verification has passed.
2. Compare the final implementation and tests against:
   - proposal
   - design
   - specs
   - tasks
3. Update tasks accurately:
   - mark only truly completed tasks
   - leave unfinished or blocked tasks unchecked
4. If specs or proposal need updates, classify the update:
   - Documentation-only: records existing behavior, clarifies wording, or adds rationale.
   - Behavioral / requirement change: changes user-visible behavior, MUST/SHALL
     requirements, acceptance criteria, or test expectations.
5. Return exactly one final status:
   - `READY TO COMMIT`
   - `RETURN TO IMPLEMENTATION`
   - `BLOCKED`

Rules:

- Do not commit.
- Do not archive.
- Do not use `opsx:apply`.
- Do not mark tasks complete unless they are actually complete.
- If a spec update changes behavior or acceptance criteria, do not continue to commit.
  Return `RETURN TO IMPLEMENTATION`.
- If there are open questions, unresolved review comments, or mismatches, return `BLOCKED`.
- If verification has not clearly passed, return `BLOCKED`.
- If only documentation changed and implementation/tests already match, return `READY TO COMMIT`.

## BLOCKED Recovery

`BLOCKED` pauses the workflow. Do not guess the next step. Resolve the blocker first,
then resume from the stage that produced it.

| Blocker | Resume after resolution |
|---|---|
| Verification missing or failing | `VERIFY`, or implementation if fixes are needed |
| Spec/design issue | `PROPOSE` |
| Implementation mismatch | implementation, then verify and finalize |
| Tasks inaccurate | update tasks, then finalize |
| Open question | ask user, then resume from propose or brainstorming |
| Unresolved review feedback | `superpowers:receiving-code-review`, then implementation |
| Unsafe repo state | resolve repository state, then resume original stage |
| Scope expanded | escalate mode: Simple -> Standard, Standard -> Complex |

User request:

$ARGUMENTS
