---
name: gitcommitmessage
description: Use when writing git commit messages, creating commits, or reviewing commit message quality. Enforces structured, machine-parseable commit convention with controlled type vocabulary.
---

# Git Commit Message Convention

## Overview

Follow a structured commit message format for searchable, changelog-friendly git history. Every commit message must conform to a fixed header-body-footer format with controlled vocabulary for types.

## Format

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

- Every line must be **100 characters or fewer**
- Header, body, and footer are separated by blank lines

## Header

Single line: `<type>(<scope>): <subject>`

### Allowed Types

| Type | Purpose |
|------|---------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation changes |
| `style` | Formatting, whitespace, semicolons — no logic change |
| `refactor` | Code restructuring — neither feature nor fix |
| `test` | Adding or correcting tests |
| `chore` | Build, tooling, or maintenance tasks |

### Scope

Optional but recommended. Identifies the module, component, or area of change (e.g. `auth`, `api`, `parser`, `cli`). Use `*` when no narrower scope fits.

### Subject Rules

- Imperative, present tense: "add" not "added" or "adds"
- Lowercase first letter
- No trailing period

```
feat(auth): add OAuth2 login flow
fix(api): handle null response from user endpoint
docs(readme): update installation instructions
```

## Body

- Imperative, present tense (same as subject)
- Explain **motivation** and contrast with previous behavior
- Omit if subject alone is sufficient

```
fix(parser): handle multi-line input correctly

Previous implementation split on newlines before
tokenizing, which broke string literals containing
line breaks. Now tokenize first, then split.
```

## Footer

### Breaking Changes

Start with `BREAKING CHANGE:` followed by description, reason, and migration notes.

```
BREAKING CHANGE: the auth middleware no longer accepts
query-string tokens. Pass tokens in the Authorization
header instead.

To migrate:
  Before: GET /api?token=abc
  After:  GET /api -H "Authorization: Bearer abc"
```

### Issue References

Close issues with `Closes`:

```
Closes #234
Closes #123, #245, #992
```

## Revert Commits

Prefix header with `revert:` and include the original header. Body must contain `This reverts commit <hash>.`

```
revert: feat(auth): add OAuth2 login flow

This reverts commit abc1234.
```

## Examples — Good vs Bad

### Bad: vague, no type, past tense

```
Fixed some stuff
```

```
updated things in the api
```

```
changes
```

### Good: typed, scoped, imperative

```
fix(api): handle null response from user endpoint
```

```
feat(auth): add OAuth2 login flow
```

```
chore(deps): bump eslint from 8.2 to 8.4
```

### Bad: body repeats the subject

```
fix(parser): fix parser bug

Fixed the parser bug where it crashed on empty input.
```

### Good: body explains why

```
fix(parser): handle empty input gracefully

Parser threw NullPointerException on empty strings
because tokenize() assumed at least one token.
Add early return for empty input.
```

### Bad: missing BREAKING CHANGE, vague migration

```
feat(auth): change token format

Closes #42
```

### Good: clear breaking change with migration path

```
feat(auth): switch from query-string to header tokens

BREAKING CHANGE: auth middleware no longer reads tokens
from the query string. Pass tokens in the Authorization
header instead.

To migrate:
  Before: GET /api?token=abc
  After:  GET /api -H "Authorization: Bearer abc"

Closes #42
```

### Bad: capitalized subject, trailing period

```
feat(UI): Add dark mode toggle.
```

### Good: lowercase, no period

```
feat(ui): add dark mode toggle
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Past tense in subject ("fixed bug") | Use imperative ("fix bug") |
| Capitalized subject ("Fix bug") | Lowercase first letter |
| Trailing period ("fix bug.") | Remove the period |
| Missing scope on large changes | Add scope for context |
| Vague subject ("update stuff") | Be specific ("update user schema") |
| Body repeats the subject | Body should explain WHY, not WHAT |
| Lines over 100 chars | Wrap at 100 characters |
