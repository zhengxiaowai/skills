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

## Prerequisites

**REQUIRED PLUGINS:** This skill depends on two plugins being installed:

1. **OpenSpec** — provides `opsx:explore`, `opsx:propose`, `opsx:apply`, `opsx:archive`
2. **Superpowers** — provides `superpowers:brainstorming`, `superpowers:test-driven-development`,
   `superpowers:subagent-driven-development`, `superpowers:dispatching-parallel-agents`,
   `superpowers:verification-before-completion`, etc.

**Startup check:** Before starting the workflow, verify both plugins are available by checking
that their skills appear in the skill list. If either is missing, stop and inform the user
that the plugin must be installed first.

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
       →  /superpowers:verification-before-completion
```

1. `systematic-debugging` — reproduce, locate root cause, confirm fix approach
2. `test-driven-development` — write regression test first (captures the bug), then fix
3. `verification-before-completion` — confirm tests pass

**Skip** all OpenSpec phases. If the fix turns out more complex than expected, escalate to Simple or Standard mode.

### Simple Mode

Single file/module change, clear boundaries, no deep exploration needed.

```
  /opsx:propose (lightweight)  →  /superpowers:brainstorming  →  TDD  →  archive
```

**Phase 1:** Skip explore, go straight to `/opsx:propose` — generate only proposal + tasks, no design or detailed specs

**Phase 2:**
1. `/superpowers:brainstorming` — quickly confirm implementation details
2. `/superpowers:test-driven-development` — test-first implementation
3. `/superpowers:verification-before-completion` — verify passing

**Phase 3:** `/opsx:archive`

### Standard Mode

Multi-file/module change, needs design decisions, but complexity is manageable.

```
  explore → propose  →  brainstorming → TDD  →  verify → update tasks → archive
```

**Phase 1:**
1. `/opsx:explore` — clarify problem boundaries and constraints
2. `/opsx:propose` — generate full docs: proposal + design + specs + tasks

**Phase 2:**
1. `/superpowers:brainstorming` — converge on implementation details from propose docs
2. `/superpowers:test-driven-development` — test-first implementation
3. `/superpowers:verification-before-completion` — verify passing

**Phase 3:**
1. Update `openspec/changes/<name>/tasks.md` — mark completed tasks
2. `/opsx:archive`

**DO NOT** enter `/opsx:apply`. It overlaps with Superpowers execution layer and causes dual state management conflicts.

### Complex Mode

Cross-module refactor, new subsystem, or architecture change. Needs full documentation and parallel execution.

```
  explore → propose  →  brainstorming → parallel/subagent → verify → review → update tasks → archive
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

3. `/superpowers:verification-before-completion` — full verification
4. `/superpowers:requesting-code-review` — request code review

**Phase 3:**
1. Update `openspec/changes/<name>/tasks.md` — mark completed tasks
2. `/opsx:archive`

## Exception Handling

| Exception | Action |
|-----------|--------|
| Design issue found during execution | Stop, go back to propose, update design/spec, then continue |
| Bugfix more complex than expected | Escalate to Simple or Standard mode |
| Code review feedback received | superpowers:receiving-code-review — verify before implementing |

## Red Flags

- Skipping explore in Standard/Complex mode — may miss critical constraints
- Skipping brainstorming before execution — may cause rework from misunderstood scope
- Using opsx:apply instead of Superpowers execution — dual state management conflicts
- Not updating tasks.md after Complex mode — incomplete archive, lost decision traceability
- Skipping TDD in Bugfix mode — fix has no regression test, likely to recur
