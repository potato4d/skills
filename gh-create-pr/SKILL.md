---
name: gh-create-pr
description: Create a GitHub Pull Request using the GitHub CLI (`gh`). Use when the user wants to create a PR, open a pull request, or submit changes for review on GitHub.
---

# GitHub Pull Request Creation Skill

Create well-structured GitHub Pull Requests using the `gh` CLI, respecting repository PR templates and producing clean, readable Markdown descriptions.

## Prerequisites

Verify `gh` is installed and authenticated before proceeding. Do NOT attempt to install it automatically — direct the user to https://cli.github.com/ if needed.

## Workflow

Make a todo list for all the tasks in this workflow and work on them one after another.

### 1. Understand the Changes

Gather the context needed to write a good PR:

- **Base branch**: Use the user-specified branch if provided, otherwise the repository's default branch.
- **Changes**: Review commits and file diffs between the base branch and HEAD. Read changed files as needed to understand the nature of the changes. If there are no commits ahead of the base, warn the user and confirm whether to proceed.
- **User inputs**: Note any information the user has already provided — description, issue/ticket references, screenshot paths, reviewers, labels, milestone, title, draft preference. Do not re-ask for information already stated.
- **Options**: For settings the user hasn't specified (e.g., draft vs. ready), infer a reasonable default from context. Only ask when genuinely ambiguous.

### 2. Compose the PR

#### Template

Check for a PR template in the repository (standard GitHub locations). If found, use it as the base structure and integrate the content below. If not found, build the body from scratch.

#### Title

Use the user's explicit title if provided. Otherwise, derive a concise title from the commit messages or branch name.

#### Body

At minimum, include:

- **Summary**: What changed and why. Highlight key implementation details and trade-offs.
- **Refs**: Related issues, PRs, or tickets using GitHub keyword linking where appropriate.

Add additional sections as the nature of the changes warrants:

- **Screenshots**: Include if the changes affect user-facing UI. If the user provided an image path, include it for upload. If not, leave a placeholder for the author to fill in.
- Other sections (e.g., migration notes, breaking changes, test plan) as appropriate for the changes.

### 3. Push and Create

Ensure the current branch is pushed to the remote. If the branch has diverged from its upstream, consult the user before force-pushing. Do not proceed if the push fails.

Create the PR with `gh pr create`, applying all relevant options (base, draft, reviewers, labels, milestone). Pass the body via a heredoc or temp file to avoid shell escaping issues with complex Markdown.

If the user provided a screenshot file, attempt to attach it to the PR. If CLI-based image upload is not feasible, inform the user how to add it manually.

If PR creation fails (e.g., a PR already exists for this branch, network error), diagnose the error and take appropriate action.

### 4. Confirm

Share the PR URL with the user. Mention any follow-up actions if relevant (e.g., screenshots still needed, draft status, reviewers not yet assigned).
