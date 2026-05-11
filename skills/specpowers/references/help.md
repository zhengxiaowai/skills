# SpecPowers Help

SpecPowers combines OpenSpec for thinking/documentation with Superpowers for execution
discipline. OpenSpec owns what and why; Superpowers owns how and doing it right.

## Prerequisites

SpecPowers requires OpenSpec, Superpowers, and code-simplifier to be available.
`gitcommitmessage` is optional for commit message discipline; if unavailable, use the repository's normal commit message style.
If any required dependency is missing, stop before starting the workflow and tell the user what to install.

## Command Summary

| Command | Purpose | Commits? | Archives? |
|---|---|---:|---:|
| `/user:specpowers` | Route a task or show help | no | no |
| `/user:specpowers:bugfix` | Known-root-cause single-point fix | no | no |
| `/user:specpowers:simple` | Single-file/module bounded change | no | no |
| `/user:specpowers:standard` | Multi-file/module change needing design | no | no |
| `/user:specpowers:complex` | Architecture, cross-module, or subsystem work | no | no |
| `/user:specpowers:finalize` | Check readiness before commit | no | no |
| `/user:specpowers:commit` | Create implementation commit | yes | no |
| `/user:specpowers:archive` | Archive OpenSpec change and commit archive diff | yes | yes |

## Which Mode Should I Use?

| Situation | Command |
|---|---|
| Known root cause, localized bug | `/user:specpowers:bugfix` |
| Single file/module, clear boundary | `/user:specpowers:simple` |
| Multi-file/module, design decisions | `/user:specpowers:standard` |
| Architecture change, subsystem, cross-module refactor | `/user:specpowers:complex` |
| Unsure | `/user:specpowers` |

When uncertain, choose Standard mode.

## Lifecycle

```text
Bugfix:
  debug → regression test → fix → simplify → verify → READY TO COMMIT

Simple:
  lightweight propose → brainstorm → TDD → simplify → verify → READY FOR FINALIZE

Standard:
  explore → propose → brainstorm → TDD → simplify → verify → READY FOR FINALIZE

Complex:
  explore → propose → brainstorm → parallel/subagent → simplify → verify → review → READY FOR FINALIZE

Finish:
  READY FOR FINALIZE → finalize → READY TO COMMIT → commit → archive → archive commit
```

## Finalize States

| State | Meaning | Next step |
|---|---|---|
| `READY FOR FINALIZE` | implementation has been simplified and verified | `/user:specpowers:finalize` |
| `READY TO COMMIT` | code, tests, spec, and tasks are aligned | `/user:specpowers:commit` |
| `RETURN TO IMPLEMENTATION` | spec/requirements changed or implementation is incomplete | return to TDD/execution |
| `BLOCKED` | unresolved question, failed verification, or unsafe repo state | resolve blocker first |

If finalize updates only documentation, commit may proceed. If finalize changes behavior,
requirements, acceptance criteria, or test expectations, return to implementation.

## Simplify Gate

Run `code-simplifier` after implementation and before verification. It may simplify or refactor
changed code, but it must not change behavior, requirements, acceptance criteria, or public API
unless the current spec already requires that change.

## Commit and Archive Separation

Use two commits by default:

```text
feat(scope): implement feature
docs(openspec): archive <change-name>
```

`/user:specpowers:commit` commits implementation, tests, and finalized spec/task changes.
`/user:specpowers:archive` runs `opsx:archive` and commits archive-only changes.

## Installing Commands

Commands are installed manually from `references/install.md`. There is no installed init
command, because an init command would have to be installed before it could install
anything.

Install targets:

```text
~/.claude/commands/specpowers.md
~/.claude/commands/specpowers/*.md

.claude/commands/specpowers.md
.claude/commands/specpowers/*.md
```

Installation must not overwrite existing command files without confirmation.

## Safety Rules

- Never use `opsx:apply`.
- Development commands do not commit.
- Development commands do not archive.
- Finalize does not commit or archive.
- Commit does not archive.
- Archive does not commit implementation code.
- Do not skip hooks.
- Do not commit unrelated files or secrets.

## Examples

```text
/user:specpowers add remember-me support to login
/user:specpowers:bugfix fix null response handling in checkout
/user:specpowers:standard add API and UI support for saved filters
/user:specpowers:finalize saved-filters
/user:specpowers:commit filters
/user:specpowers:archive saved-filters
```
