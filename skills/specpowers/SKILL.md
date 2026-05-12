---
name: specpowers
description: Use when starting any non-trivial feature, refactor, or change that needs both structured documentation and disciplined execution. Combines OpenSpec for thinking and documentation with Superpowers for execution discipline.
---

# SpecPowers

## Overview

OpenSpec owns thinking and documentation (what and why). Superpowers owns execution discipline (how and doing it right). Never chain their planning layers — avoids cross-tool state management.

## Activation Notice

**When this skill is triggered, you MUST immediately announce it to the user before doing anything else.**

Format: `Using SpecPowers [mode] mode` — include the selected mode name (Bugfix / Simple / Standard / Complex).

Example: `Using SpecPowers Standard mode` or `Using SpecPowers Bugfix mode`

This announcement is mandatory. Do not start any workflow step without it.

## Resource Map

- `commands/*.md` — installable Claude Code command resources for the `specpowers` namespace
- `references/flow.md` — complete ASCII lifecycle and mode flow
- `references/help.md` — user-facing command help and examples
- `references/install.md` — manual installation instructions for command resources
- `specs/specpowers-command-resources.md` — maintenance spec for future changes

Load resources when needed:

- Read the matching `commands/*.md` when a SpecPowers command is invoked.
- Read `references/flow.md` when lifecycle, finalize, commit, or archive order is unclear.
- Read `references/help.md` when the user asks how to use SpecPowers.
- Read `references/install.md` when the user asks how to install the command resources.
- Read `specs/specpowers-command-resources.md` before modifying this skill.

## Installed Commands

Command resources are stored inside this skill first. Use `references/install.md` for manual installation instructions.

- `/user:specpowers`
- `/user:specpowers:bugfix`
- `/user:specpowers:simple`
- `/user:specpowers:standard`
- `/user:specpowers:complex`
- `/user:specpowers:finalize`
- `/user:specpowers:commit`
- `/user:specpowers:archive`

## Prerequisites

**REQUIRED PLUGINS AND SKILLS:** This skill depends on these capabilities being installed:

1. **OpenSpec** — provides `opsx:explore`, `opsx:propose`, `opsx:apply`, `opsx:archive`
2. **Superpowers** — provides `superpowers:brainstorming`, `superpowers:test-driven-development`,
   `superpowers:subagent-driven-development`, `superpowers:dispatching-parallel-agents`,
   `superpowers:verification-before-completion`, etc.
3. **code-simplifier** — provides post-implementation simplification before verification

**OPTIONAL SKILLS:**

- **gitcommitmessage** — provides commit message discipline for commit and archive commands. If unavailable, use the repository's normal commit message style.

**Startup check:** Before starting the workflow, verify required plugins and skills are available.
If any required dependency is missing, stop and tell the user which dependency must be installed first. Do not block on missing optional skills.

## When to Use

All non-trivial development tasks. Pick a mode based on complexity.

## Modes

```
  Assess complexity
      │
      ├─ Single-point fix, known root cause ──────▶ Bugfix mode
      │
      ├─ Single file/module, clear boundaries ────▶ Simple mode
      │
      ├─ Multi-file/module, needs design decisions ▶ Standard mode
      │
      └─ Cross-module refactor / new subsystem ───▶ Complex mode
```

### Bugfix Mode

Known-root-cause bug fix. No structured documentation needed.

```
  /superpowers:systematic-debugging
       →  /superpowers:test-driven-development
       →  code-simplifier
       →  /superpowers:verification-before-completion
       →  READY TO COMMIT
```

1. `systematic-debugging` — reproduce, locate root cause, confirm fix approach
2. `test-driven-development` — write regression test first (captures the bug), then fix
3. `code-simplifier` — simplify/refine changed code without changing behavior
4. `verification-before-completion` — confirm tests pass

**Skip** all OpenSpec phases. Do not run finalize or archive. If the fix turns out more complex than expected, escalate to Simple or Standard mode.

### Simple Mode

Single file/module change, clear boundaries, no deep exploration needed.

```
  /opsx:propose (lightweight)  →  /superpowers:brainstorming  →  TDD  →  code-simplifier  →  verify  →  READY FOR FINALIZE
```

**Phase 1:** Skip explore, go straight to `/opsx:propose` — generate only proposal + tasks, no design or detailed specs.

**Phase 2:**
1. `/superpowers:brainstorming` — quickly confirm implementation details
2. `/superpowers:test-driven-development` — test-first implementation
3. `code-simplifier` — simplify/refine changed code without changing behavior
4. `/superpowers:verification-before-completion` — verify passing

**Phase 3:** Stop with `READY FOR FINALIZE`. Finalize is manual via `/user:specpowers:finalize`.

### Standard Mode

Multi-file/module change, needs design decisions, but complexity is manageable.

```
  explore → propose → brainstorming → TDD → code-simplifier → verify → READY FOR FINALIZE
```

**Phase 1:**
1. `/opsx:explore` — clarify problem boundaries and constraints
2. `/opsx:propose` — generate full docs: proposal + design + specs + tasks

**Phase 2:**
1. `/superpowers:brainstorming` — converge on implementation details from propose docs
2. `/superpowers:test-driven-development` — test-first implementation
3. `code-simplifier` — simplify/refine changed code without changing behavior
4. `/superpowers:verification-before-completion` — verify passing

**Phase 3:** Stop with `READY FOR FINALIZE`. Finalize is manual via `/user:specpowers:finalize`.

**DO NOT** enter `/opsx:apply`. It overlaps with Superpowers execution layer and causes dual state management conflicts.

### Complex Mode

Cross-module refactor, new subsystem, or architecture change. Needs full documentation and parallel execution.

```
  explore → propose → brainstorming → parallel/subagent → code-simplifier → verify → review → READY FOR FINALIZE
```

**Phase 1:**
1. `/opsx:explore` — thorough exploration, map architectural impact and dependencies
2. `/opsx:propose` — generate full docs, each capability with its own spec, tasks split into parallelizable units

**Phase 2:**
1. `/superpowers:brainstorming` — converge on module interfaces and execution order from propose docs
2. Execution (pick by task structure):

   | Task structure | Skill |
   |----------------|-------|
   | Independent tasks | superpowers:dispatching-parallel-agents |
   | Dependent tasks | superpowers:subagent-driven-development |

3. `code-simplifier` — simplify/refine changed code without changing behavior
4. `/superpowers:verification-before-completion` — full verification
5. `/superpowers:requesting-code-review` — request code review

**Phase 3:** Stop with `READY FOR FINALIZE`. Finalize is manual via `/user:specpowers:finalize`.

## Finalize, Commit, Archive

```
simplify → verify → READY FOR FINALIZE → finalize → implementation commit → archive → archive commit
```

- `code-simplifier` runs after implementation and before verification.
- `/user:specpowers:finalize` is manually triggered and decides whether commit is safe. It never commits or archives.
- `/user:specpowers:commit` commits implementation, tests, and finalized spec/task changes only.
- `/user:specpowers:archive` runs `opsx:archive` and commits archive-only changes.
- Development commands do not commit or archive automatically.

## Runtime State Machine

For lifecycle decisions, follow `references/flow.md`.

Primary states:

- `READY FOR FINALIZE`: implementation has been simplified and verified; manually run `/user:specpowers:finalize` next.
- `READY TO COMMIT`: implementation, tests, specs, and tasks are aligned.
- `RETURN TO IMPLEMENTATION`: spec, behavior, acceptance criteria, or implementation changed; return to implementation, simplify, verify, and finalize again.
- `BLOCKED`: pause. Resolve the blocker first, then resume from the stage that produced it.

`BLOCKED` does not automatically return to implementation.

Common BLOCKED recovery:

| Blocker | Resume after resolution |
|---|---|
| Verification missing or failing | `VERIFY`, or implementation if fixes are needed |
| Spec/design issue | `PROPOSE` |
| Implementation mismatch | implementation, then verify and finalize |
| Tasks inaccurate | update tasks, then finalize |
| Open question | ask user, then resume from propose or brainstorming |
| Code review feedback | `superpowers:receiving-code-review`, then implementation |
| Unsafe repo state | resolve repository state, then resume original stage |
| Scope expanded | escalate mode: Simple → Standard, Standard → Complex |

## Simplify Gate

Run `code-simplifier` after implementation and before verification.

Rules:

- It may simplify, refactor, or improve maintainability of changed code.
- It must not change behavior, requirements, acceptance criteria, or public API unless the current spec already requires that change.
- If simplification changes behavior or breaks verification, return to implementation, then simplify and verify again.
- Complex mode runs code review after simplification and verification so review sees the refined implementation.

## Installation

`references/install.md` explains how to manually copy this skill's command resources into Claude Code command directories.

Supported targets:

- user commands: `~/.claude/commands/specpowers.md` and `~/.claude/commands/specpowers/*.md`
- project commands: `.claude/commands/specpowers.md` and `.claude/commands/specpowers/*.md`

Do not install or overwrite command files without explicit user confirmation.

## Exception Handling

| Exception | Action |
|-----------|--------|
| Design issue found during execution | Stop, go back to propose, update design/spec, then continue |
| Bugfix more complex than expected | Escalate to Simple or Standard mode |
| Code review feedback received | superpowers:receiving-code-review — verify before implementing |
| Finalize changes behavior or acceptance criteria | Return to implementation, then verify and finalize again |

## Red Flags

- Skipping explore in Standard/Complex mode — may miss critical constraints
- Skipping brainstorming before execution — may cause rework from misunderstood scope
- Using opsx:apply instead of Superpowers execution — dual state management conflicts
- Archiving before implementation commit — mixes lifecycle state with implementation work
- Committing archive changes with implementation changes — makes review and rollback harder
- Skipping TDD in Bugfix mode — fix has no regression test, likely to recur
