---
name: setup-code-reviewer-agents
description: Set up the Codex code reviewer agents in a repository. Use when the user wants to install or repair the pull request review workflow, create the `.github/codex/prompts/` scaffold, or explain the required GitHub secret and prompt file layout for the multi-agent reviewer.
---

# Setup Code Reviewer Agents

## Workflow

1. Inspect the repository for `.github/workflows/pr-reviewer.yml` and `.github/codex/prompts/`.
2. Create missing directories with `mkdir -p .github/workflows .github/codex/prompts`.
3. Create or replace `.github/workflows/pr-reviewer.yml` with the exact contents from [assets/pr-reviewer.yml](assets/pr-reviewer.yml).
4. Ensure `.github/codex/prompts/consolidate.md` exists.
5. If `consolidate.md` is missing, create it as an empty file. Do not add placeholder text.
6. Do not create extra prompt files unless the user explicitly asks for them.
7. Keep any existing `review-*.md` prompt files unless the user explicitly asks to edit or remove them.
8. Explain the setup requirements and prompt conventions listed below.

## What To Explain

- The repository must define `secrets.OPENAI_API_KEY` in GitHub repository settings.
- Prompt files must live in `.github/codex/prompts/`.
- This reviewer uses multiple agents. It can run up to 7 review agents in parallel, depending on which `review-*.md` prompt files exist.
- Name the parallel review prompts `review-*.md`, for example `review-code-style.md`, `review-security.md`, or `review-testing.md`.
- `consolidate.md` is the final prompt. The main agent runs after the parallel review agents finish and consolidates the results into the final review.
- The minimum required scaffold consists of these two files:
- `.github/workflows/pr-reviewer.yml`
- `.github/codex/prompts/consolidate.md`
- Additional `review-*.md` files are optional and can be added later by the user.

## Guardrails

- Keep the workflow file content exact. Do not rename the workflow, branch filter, event types, permissions, job name, reusable workflow reference, or secret mapping.
- Use `.github/workflows/pr-reviewer.yml` as the workflow path.
- Use `.github/codex/prompts/` as the prompt directory path.
- If `consolidate.md` already exists, leave its contents unchanged unless the user explicitly asks to edit it.
- If the repository already has custom `review-*.md` files, leave them unchanged unless the user explicitly asks for edits.

## Canonical Workflow

Use [assets/pr-reviewer.yml](assets/pr-reviewer.yml) as the source of truth for the workflow contents.
