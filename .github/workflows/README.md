# Codex PR Review Action

This folder contains a reusable GitHub Actions workflow that runs **automated PR reviews with Codex** and posts the result directly as a PR review.

File: `pr-review.yml`

## What It Does

When invoked by another workflow with a `pr_number`, this workflow:

1. Resolves PR metadata (`pr_number`, `base_ref`, `head_ref`).
2. Skips execution for **docs-only** changes (`docs/**` or `*.md`).
3. Discovers worker prompt files in `.github/codex/prompts`.
4. Runs one Codex worker per prompt in parallel.
5. Aggregates worker outputs with a consolidator prompt.
6. Posts a GitHub PR review (`APPROVE`, `COMMENT`, or `REQUEST_CHANGES`) with inline comments when possible.

## Required Repository Setup

### 1) Secret

- `OPENAI_API_KEY` (repository secret)

If this workflow is called from another workflow, pass that secret through `secrets:`.

### 2) Prompt files

Create these files in your repository:

- `.github/codex/prompts/consolidate.md` (required, non-empty)
- Worker prompts (at least one, non-empty):
  - `.github/codex/prompts/review.md` or
  - `.github/codex/prompts/review-*.md`

Limits:

- Maximum workers: `6`
- Empty worker files are skipped (reported as `skipped_workers`)

## Invocation Contract

This workflow is triggered via `workflow_call` only.

Required input:

- `pr_number` (number, required)

## Consolidator Output Contract

The consolidator should return JSON in `final-message` (plain JSON or fenced ` ```json ` block). Expected shape:

```json
{
  "event": "COMMENT",
  "summary": "Short summary",
  "blocking_findings": [
    {
      "severity": "high",
      "title": "Issue title",
      "body": "What is wrong and why",
      "path": "src/file.ts",
      "line": 42
    }
  ],
  "non_blocking_findings": [],
  "open_questions": ["Question for author"],
  "validation_checklist": [{ "item": "Tests updated", "checked": false }],
  "worker_coverage": [{ "worker": "review-security", "outcome": "success", "used": true, "notes": "" }]
}
```

Notes:

- `event` defaults to `COMMENT` if invalid.
- Inline comments are anchored to changed lines; non-anchorable findings are included in the review body.

## Quick Usage

1. Add `actions/pr-review.yml` to `.github/workflows/pr-review.yml` in your target repository.
2. Add the prompt files under `.github/codex/prompts`.
3. Add the `OPENAI_API_KEY` secret.
4. Create a caller workflow that passes `pr_number`.

Example caller (`.github/workflows/codex-pr-review-caller.yml`):

```yaml
name: Codex PR review caller

on:
  pull_request:
    branches: [dev]
    types: [opened, synchronize, reopened]
  workflow_dispatch:
    inputs:
      pr_number:
        description: Pull request number to review
        required: true
        type: number

jobs:
  codex_review_from_pr_event:
    if: github.event_name == 'pull_request'
    uses: ./.github/workflows/pr-review.yml
    with:
      pr_number: ${{ github.event.pull_request.number }}
    secrets:
      OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}

  codex_review_from_manual_dispatch:
    if: github.event_name == 'workflow_dispatch'
    uses: ./.github/workflows/pr-review.yml
    with:
      pr_number: ${{ github.event.inputs.pr_number }}
    secrets:
      OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```
