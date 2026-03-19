---
name: gh-pr-from-jira-ticket
description: Create and open GitHub pull requests from Jira tickets using `gh pr create`, defaulting the base branch to `dev` unless a base is explicitly provided. Use when a user asks to open a PR with Jira ticket title format, wants Jira context via MCP, or needs a fallback flow when Jira MCP is unavailable.
---

# GH PR From Jira Ticket

Open PRs with a consistent Jira-based format.

## Required Output Format

Use this PR title format:

`TICKET-123: Ticket description`

Use this PR body format:

```md
Description of the PR:

Link to the Jira ticket:
- <jira-ticket-url>

Summary of changes:
- <change 1>
- <change 2>
- <change 3>
```

## Workflow

1. Resolve base and head branches.
- Base branch defaults to `dev`.
- If user passes a base branch, use that.
- Head branch defaults to current branch from `git branch --show-current`.

2. Resolve ticket metadata.
- Preferred: use Jira MCP to fetch ticket summary from ticket key.
- Fallback (when Jira MCP is not configured):
  - Extract ticket key from branch name if possible (example: `ABC-123`).
  - If not available, ask user for ticket key.
  - Ask user for ticket description/title and Jira URL when they cannot be inferred.
- Never invent ticket details.

3. Gather change summary.
- Build 3-7 concise bullets from `git diff --stat`, staged diff, or user-provided summary.
- Keep bullets specific and outcome-focused.

4. Compose title and body.
- Title: `<TICKET_KEY>: <TICKET_DESCRIPTION>`
- Body: use the required template above.

5. Open the PR with GitHub CLI.
- Ensure branch is pushed before creating PR.
- Command pattern:
```bash
gh pr create --base "${BASE_BRANCH:-dev}" --head "${HEAD_BRANCH}" --title "$PR_TITLE" --body "$PR_BODY"
```

6. Report result.
- Return PR URL, base branch, head branch, title, and final body.

## No-Jira-MCP Fallback Details

When Jira MCP is unavailable, proceed with manual ticket data and still create a compliant PR:

1. Ticket key (required): `ABC-123`
2. Ticket description (required): short sentence for title
3. Jira URL (required): full link to ticket
4. Summary bullets (required): 3-7 bullets

Then run `gh pr create` with the same format.

## Reference

For a copy/paste template, use:
- `references/pr-body-template.md`
