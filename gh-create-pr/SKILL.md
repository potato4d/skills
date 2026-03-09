---
name: gh-create-pr
description: Create a GitHub Pull Request using the GitHub CLI (`gh`). Use when the user wants to create a PR, open a pull request, or submit changes for review on GitHub.
---

# GitHub Pull Request Creation Skill

Create well-structured GitHub Pull Requests using the `gh` CLI, respecting repository PR templates and producing clean, readable Markdown descriptions.

## Prerequisites

Before starting, verify that the GitHub CLI is installed and authenticated:

```bash
gh --version
```

If the `gh` command is not found, inform the user that the GitHub CLI is required and direct them to install it from https://cli.github.com/. Do NOT attempt to install it automatically.

Also verify authentication status:

```bash
gh auth status
```

If not authenticated, prompt the user to run `gh auth login` first.

## Workflow

Make a todo list for all the tasks in this workflow and work on them one after another.

### 1. Gather Context

Collect the information needed to build the PR description:

- Run `git log --oneline main..HEAD` (or the appropriate base branch) to understand what commits are included.
- Run `git diff main..HEAD --stat` to see which files changed.
- Read the changed files as needed to understand the nature of the changes.
- If the user has provided a description, issue link, or screenshot path, note them for later use.

### 2. Check for PR Template

Look for an existing PR template in the repository. Check these common locations in order:

1. `.github/PULL_REQUEST_TEMPLATE.md`
2. `.github/pull_request_template.md`
3. `docs/pull_request_template.md`
4. `PULL_REQUEST_TEMPLATE.md`
5. `.github/PULL_REQUEST_TEMPLATE/` directory (if multiple templates exist)

If a template is found, use it as the base structure for the PR body. Integrate the sections described below into the template, preserving any existing sections from the template (e.g., checklists, additional headings).

If no template is found, build the PR body from scratch using the sections below.

### 3. Determine if the Repository is Front-End

Check whether the project appears to be a front-end or UI-focused repository. Signals include:

- Presence of UI framework dependencies (e.g., `react`, `vue`, `angular`, `svelte`, `next`, `nuxt`, `remix`, `solid-js`, `astro`) in `package.json`
- Directories like `src/components/`, `src/pages/`, `src/views/`, `app/`, `public/`
- CSS/SCSS/Tailwind configuration files
- Storybook or similar UI tooling

This determines whether to include the Screenshots section.

### 4. Compose the PR Description

Build a well-formatted Markdown PR body with the following sections. If a PR template was found, merge these sections into it rather than replacing it.

#### Summary

Write a clear, concise summary of the changes. Include:

- What was changed and why
- Key implementation details worth highlighting
- Any trade-offs or decisions made

#### Screenshots (Front-End repositories only)

Include this section only if the repository is front-end oriented (as determined in Step 3).

- If the user provided an image file path, the image will be uploaded when creating the PR. Add a placeholder in the body: `![Screenshot](screenshot)` — the actual upload is handled in Step 5.
- If the user has NOT provided a screenshot, include the section with an empty placeholder so it can be filled in later:

```markdown
## Screenshots

<!-- Add screenshots here if applicable -->
```

- If the user gives other specific instructions about screenshots (e.g., "generate a table comparing before and after"), follow those instructions.

#### Refs

Include references to related issues, PRs, or external tracker tickets. Use GitHub keyword linking where appropriate:

```markdown
## Refs

- Closes #123
- Related to #456
- [JIRA-789](https://your-tracker.example.com/JIRA-789)
```

- If the user mentioned an issue number, PR number, or ticket URL, include it here.
- If there are no known references, include the section with a placeholder:

```markdown
## Refs

<!-- Add related issues, PRs, or tickets here -->
```

### 5. Create the Pull Request

Use the `gh` CLI to create the PR.

**If the user provided a screenshot file path**, use the following approach to upload it:

```bash
gh pr create --title "<title>" --body "<body>" --draft
```

Then edit the PR body to replace the screenshot placeholder by uploading the image via:

```bash
gh pr edit <pr-number> --body "$(gh pr view <pr-number> --json body -q .body | sed 's|!\[Screenshot\](screenshot)|![Screenshot](<uploaded-url>)|')"
```

IMPORTANT: To upload an image to GitHub, you can use the `gh` CLI issue/PR body editing flow, or inform the user that they need to manually drag-and-drop the image into the PR on GitHub's web UI if direct upload is not feasible via CLI.

**If no screenshot is needed**, create the PR directly:

```bash
gh pr create --title "<title>" --body "<body>"
```

**Important notes:**
- Pass the PR body via a heredoc or temp file to avoid shell escaping issues with complex Markdown.
- If the user has not specified whether to create a draft or regular PR, ask them.
- If the user has specified a target base branch, use `--base <branch>`.
- Make sure the current branch has been pushed to the remote before creating the PR.

### 6. Confirm and Share

After the PR is created:

- Display the PR URL to the user.
- Provide a brief summary of what was included in the PR.
- If screenshots were left empty, remind the user to add them.

## Wrap Up

Present the created PR to the user with:

- The PR URL (clickable link)
- The PR title
- A short summary of what the PR contains
- Any follow-up actions needed (e.g., "Remember to add screenshots before requesting review")
