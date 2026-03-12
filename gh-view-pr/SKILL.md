---
name: gh-view-pr
description: View and inspect a GitHub Pull Request using the GitHub CLI (`gh`). Use when the user wants to view, read, or check the details of a pull request on GitHub.
---

# GitHub Pull Request View Skill

Retrieve and display GitHub Pull Request details using the `gh` CLI, presenting the information in a clear and readable format.

## Prerequisites

Verify `gh` is installed and authenticated before proceeding. Do NOT attempt to install it automatically — direct the user to https://cli.github.com/ if needed.

## Workflow

Make a todo list for all the tasks in this workflow and work on them one after another.

### 1. Identify the Pull Request

Determine which PR to view:

- **PR number or URL**: Use the user-specified PR number or URL if provided.
- **Current branch**: If no PR is specified, look up the PR associated with the current branch using `gh pr view`.
- **List PRs**: If the user wants to browse open PRs, list them with `gh pr list` and let the user choose.

### 2. Fetch PR Details

Retrieve the PR information using the `gh` CLI:

- **Basic info**: Title, number, state (open/closed/merged), author, base branch, head branch, creation date.
- **Description**: The full PR body.
- **Review status**: Requested reviewers, submitted reviews and their states (approved/changes requested/commented).
- **Checks**: CI/CD status from `gh pr checks` if the user wants to know about test results.
- **Comments**: Fetch review comments and general comments if relevant using `gh pr view --comments`.
- **Files changed**: List changed files with `gh pr diff` if the user wants to inspect the diff.

### 3. Present the Information

Display the PR details in a structured, readable format:

- **Header**: PR title, number, state, and URL.
- **Meta**: Author, base ← head branch, created/updated date, labels, milestone if set.
- **Description**: Render the PR body as-is.
- **Reviews**: Summarize review status — who approved, who requested changes, who is pending.
- **Checks**: Summarize CI status (passing/failing/pending) if fetched.
- **Comments**: Show relevant comments if requested or if they contain important context.

Highlight any action items that may require the user's attention (e.g., failing checks, requested changes, merge conflicts).

### 4. Handle Follow-up

Be ready to assist with common follow-up actions based on what the user sees:

- Checkout the PR branch locally with `gh pr checkout <number>`.
- View the full diff with `gh pr diff <number>`.
- Open the PR in the browser with `gh pr view <number> --web`.
- Summarize comments or reviews on request.

If the PR is not found or access is denied, diagnose the error and inform the user clearly.
