---
name: semantic-commits
description: Create git commits using Conventional Commit semantics (feat/fix/chore/refactor/docs/test/build/ci/perf/revert/style). Use when the user asks to commit changes, make a semantic/conventional commit, or write commit messages; stage all changed files by default unless the user explicitly names specific files to include.
---

# Semantic Commits

## Workflow

1. Inspect repo state with `git status --short` and `git diff --stat`.
2. Determine commit scope:
- If user names files, stage only those files.
- If user does not name files, stage all changed files (`git add -A`).
3. Review staged content (`git diff --cached --stat` and `git diff --cached`).
4. Choose Conventional Commit type and scope from actual changes.
5. Commit with a semantic subject line:
- Format: `<type>(<scope>): <imperative summary>`
- Keep subject concise and specific.
- Add body bullets when the change spans multiple concerns.
6. Report the commit hash, message, and staged files.

## Commit Type Guide

- `feat`: new behavior or capability
- `fix`: bug fix or regression fix
- `refactor`: code restructuring without behavior change
- `docs`: documentation-only changes
- `test`: tests added or updated
- `build`: build tooling or dependencies
- `ci`: CI/CD pipeline updates
- `perf`: measurable performance improvement
- `style`: formatting/style-only changes
- `chore`: maintenance tasks not covered above
- `revert`: revert a previous commit

## Guardrails

- Never include unrelated files when user specifies a file list.
- Never amend commits unless the user explicitly asks.
- Do not run destructive git operations.
- If there are no staged changes, stop and report that nothing is commit-ready.
- If unsure about type/scope, infer from changed paths and behavior, then keep message conservative.

## Command Pattern

Use this non-interactive sequence:

```bash
git status --short
git diff --stat
# Stage scope chosen from user intent
git add -A
# or: git add <file1> <file2> ...
git diff --cached --stat
git commit -m "<type>(<scope>): <summary>" [-m "<details>"]
```
