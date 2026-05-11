# SpecPowers Command Resources Spec

## Purpose

Track the command-resource architecture for the SpecPowers skill.

## Goals

- Provide installable Claude Code command resources.
- Keep `SKILL.md` focused on activation, routing, and lifecycle contracts.
- Keep command-specific operational detail in `commands/*.md`.
- Provide reference documentation for users and agents.
- Preserve strict separation between implementation, simplification, finalize, commit, and archive.

## Non-Goals

- Do not implement a standalone binary installer.
- Do not install commands automatically when the skill is loaded.
- Do not use `opsx:apply`.
- Do not make development commands auto-commit.
- Do not make development commands auto-archive.
- Do not store project-specific conventions in this skill.

## Resource Layout

```text
skills/specpowers/
  SKILL.md
  commands/
    specpowers.md
    bugfix.md
    simple.md
    standard.md
    complex.md
    finalize.md
    commit.md
    archive.md
  references/
    flow.md
    help.md
    install.md
  specs/
    specpowers-command-resources.md
```

## Command Contract

| Resource | Installed command | Contract |
|---|---|---|
| `commands/specpowers.md` | `/user:specpowers` | Route task to a mode; do not commit/archive |
| `commands/bugfix.md` | `/user:specpowers:bugfix` | Known-root-cause fix; regression test; implement; simplify; verify; stop `READY TO COMMIT` |
| `commands/simple.md` | `/user:specpowers:simple` | Lightweight propose; brainstorm; TDD; simplify; verify; stop `READY FOR FINALIZE` |
| `commands/standard.md` | `/user:specpowers:standard` | Explore/propose; brainstorm; TDD; simplify; verify; stop `READY FOR FINALIZE` |
| `commands/complex.md` | `/user:specpowers:complex` | Explore/propose; brainstorm; subagent/parallel execution; simplify; verify; review; stop `READY FOR FINALIZE` |
| `commands/finalize.md` | `/user:specpowers:finalize` | Return readiness state; do not commit/archive |
| `commands/commit.md` | `/user:specpowers:commit` | Commit implementation only |
| `commands/archive.md` | `/user:specpowers:archive` | Archive and commit archive-only changes |

## Activation and Prerequisite Contract

When any SpecPowers workflow starts, the agent must immediately announce the selected mode using `Using SpecPowers [mode] mode` before workflow steps.

SpecPowers requires OpenSpec, Superpowers, and code-simplifier capabilities to be available. Before starting a workflow, the agent must verify required plugins and skills are available; if not, stop and tell the user which dependency is missing.

`gitcommitmessage` is optional. Commit and archive commands should use it when available; if unavailable, they should follow the repository's normal commit message style without blocking the workflow.

## Resource Loading Contract

- Read `commands/*.md` for command-specific behavior.
- Read `references/flow.md` for lifecycle, state machine, finalize, commit, or archive ordering.
- Read `references/help.md` when the user asks how to use SpecPowers.
- Read `references/install.md` when the user asks how to install command resources.
- Read this spec before modifying the SpecPowers skill or command contracts.

## Lifecycle Contract

```text
development command
→ simplification
→ verification
→ ready for finalize
→ finalize
→ implementation commit
→ archive
→ archive commit
```

Development commands for Simple, Standard, and Complex stop with `READY FOR FINALIZE` after simplification and verification. `finalize` must be triggered manually.

Development commands may return `READY FOR FINALIZE`; `finalize` does not return it.

`finalize` must return exactly one of:

- `READY TO COMMIT`
- `RETURN TO IMPLEMENTATION`
- `BLOCKED`

`code-simplifier` runs after implementation and before verification. It may simplify or refactor changed code, but must not change behavior, requirements, acceptance criteria, or public API unless the current spec already requires that change.

If a spec update is documentation-only, the lifecycle may continue to commit. If a spec
update changes behavior, requirements, acceptance criteria, or test expectations, the
workflow must return to implementation.

## State Machine

```text
START
  │
  ▼
ASSESS_MODE
  ├─ known root-cause bugfix ───────→ BUGFIX
  ├─ single module, clear boundary ─→ SIMPLE
  ├─ multi-file, design decisions ──→ STANDARD
  └─ architecture / cross-module ───→ COMPLEX

BUGFIX
  → DEBUG
  → REGRESSION_TEST
  → IMPLEMENT
  → SIMPLIFY
  → VERIFY
  ├─ pass → READY_TO_COMMIT
  └─ fail/unclear root cause → BLOCKED

SIMPLE
  → LIGHTWEIGHT_PROPOSE
  → BRAINSTORM
  → IMPLEMENT
  → SIMPLIFY
  → VERIFY
  → READY_FOR_FINALIZE

STANDARD
  → EXPLORE
  → PROPOSE
  → BRAINSTORM
  → IMPLEMENT
  → SIMPLIFY
  → VERIFY
  → READY_FOR_FINALIZE

COMPLEX
  → EXPLORE
  → PROPOSE
  → BRAINSTORM
  → PARALLEL_OR_SUBAGENT_EXECUTION
  → SIMPLIFY
  → VERIFY
  → CODE_REVIEW
  → READY_FOR_FINALIZE

READY_FOR_FINALIZE
  → FINALIZE

FINALIZE
  ├─ aligned or docs-only spec update → READY_TO_COMMIT
  ├─ behavior/requirement change ─────→ RETURN_TO_IMPLEMENTATION
  └─ mismatch/open question/unsafe state → BLOCKED

RETURN_TO_IMPLEMENTATION
  → IMPLEMENT
  → SIMPLIFY
  → VERIFY
  → READY_FOR_FINALIZE

READY_TO_COMMIT
  → COMMIT_IMPLEMENTATION
  → COMMITTED
  → ARCHIVE
  → ARCHIVED_AND_COMMITTED
```

## BLOCKED Recovery

`BLOCKED` pauses the workflow. It does not automatically return to implementation.
Resolve the blocker first, then resume from the stage that produced it.

| Blocker | Resume after resolution |
|---|---|
| Verification missing or failing | `VERIFY`, or `IMPLEMENT` if code/test changes are needed |
| Spec, code, tests, or tasks mismatch | `FINALIZE`, or `IMPLEMENT` if behavior must change |
| Tasks inaccurate | update tasks, then `FINALIZE` |
| Open question or unclear requirement | ask user, then `PROPOSE` or `BRAINSTORM` |
| Design/spec issue | `PROPOSE` |
| Unresolved code review feedback | `superpowers:receiving-code-review`, then `IMPLEMENT` |
| Unsafe git/worktree state | resolve repository state, then resume original stage |
| Missing plugin/tool/dependency | install or enable dependency, then resume original stage |
| Scope expanded beyond current mode | escalate mode: Simple → Standard, Standard → Complex |

## Install Contract

`references/install.md` documents manual installation of command resources into Claude Code command directories.

It must support:

- user scope: `~/.claude/commands`
- project scope: `.claude/commands`

It must not:

- define or install an init command
- overwrite existing command files without confirmation
- copy `references/` or `specs/` into command directories
- modify unrelated commands
- commit changes automatically

## Future Modification Rules

- Any new command must be documented in `references/help.md`.
- Any lifecycle change must update `references/flow.md`.
- Any command contract change must update this spec.
- Any commit behavior must use `gitcommitmessage` when available, with repository commit style as the fallback.
- Development commands must not auto-commit.
- Development commands must not auto-archive.
- `opsx:apply` remains prohibited.
- Keep command resources generic; project-specific conventions belong outside this skill.
