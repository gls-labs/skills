# Setup Code Reviewer Agents

This skill sets up the Codex code reviewer agents in a repository.

It creates or replaces `.github/workflows/pr-reviewer.yml` with the canonical workflow, ensures `.github/codex/prompts/` exists, and creates an empty `.github/codex/prompts/consolidate.md` if it is missing.

## Recommended Scope

`Global/personal skill`

Recommendation: install this globally if you bootstrap or repair the same Codex reviewer workflow across multiple repositories. The skill applies the same scaffold repeatedly instead of encoding project-specific product logic.

Repository requirements:

- Define `OPENAI_API_KEY` as `secrets.OPENAI_API_KEY` in the repository settings.
- Store review prompts in `.github/codex/prompts/`.

Prompt layout:

- `consolidate.md` is required and acts as the final consolidation prompt.
- Optional parallel review prompts should be named `review-*.md`, such as `review-code-style.md` or `review-security.md`.
- The reviewer can run up to 7 review agents in parallel before the consolidate step runs.

Invoke it with `$setup-code-reviewer-agents`.
