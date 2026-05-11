# Installing SpecPowers Commands

SpecPowers command files live as skill resources under `skills/specpowers/commands/`.
They are not installed automatically.

This file is a manual installation reference, not a slash command. Keeping install
instructions as a reference avoids the bootstrap problem where an init command would
need to be installed before it could install anything.

## Targets

User scope:

```text
~/.claude/commands/specpowers.md
~/.claude/commands/specpowers/*.md
```

Project scope:

```text
.claude/commands/specpowers.md
.claude/commands/specpowers/*.md
```

## Copy Map

```text
commands/specpowers.md → specpowers.md
commands/bugfix.md     → specpowers/bugfix.md
commands/simple.md     → specpowers/simple.md
commands/standard.md   → specpowers/standard.md
commands/complex.md    → specpowers/complex.md
commands/finalize.md   → specpowers/finalize.md
commands/commit.md     → specpowers/commit.md
commands/archive.md    → specpowers/archive.md
```

## Manual Install Workflow

1. Choose target scope: user or project.
2. Locate this skill's `commands/` directory.
3. Check whether destination files already exist.
4. Ask before overwriting existing files.
5. Create destination directories as needed.
6. Copy only the command files listed in the copy map.
7. Report installed paths.

## Rules

- Do not install without a clear target scope.
- Do not overwrite existing command files without user confirmation.
- Do not copy `references/` or `specs/` into Claude Code command directories.
- Do not install an `init` command.
- Do not modify unrelated commands.
- Do not commit automatically.
