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

Collect all the information needed for subsequent steps. This is the single step responsible for all information gathering — later steps only consume what is collected here.

#### Determine the base branch

Identify the base branch using this priority:

1. User-specified base branch (if provided)
2. Repository default branch (via `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`)
3. Fall back to `main`

#### Collect change information

- Run `git log --oneline <base>..HEAD` to understand what commits are included.
- Run `git diff <base>..HEAD --stat` to see which files changed.
- Read the changed files as needed to understand the nature of the changes.
- If there are no commits ahead of the base branch, warn the user and confirm whether to proceed.

#### Collect user-provided inputs

Gather and record all inputs the user has provided. Do not re-ask for information the user has already stated:

- **Description**: Any summary or context the user provided about the changes.
- **Issue/ticket references**: Issue numbers, PR numbers, or external tracker URLs (e.g., JIRA tickets).
- **Screenshot path**: File path to a screenshot or image, if provided.
- **Draft preference**: Whether the PR should be created as a draft or regular PR. If the user has not specified, ask them.
- **Reviewers**: GitHub usernames for reviewer assignment, if specified.
- **Labels**: Labels to apply, if specified.
- **Milestone**: Milestone to assign, if specified.
- **Title**: An explicit PR title, if stated by the user.

### 2. Check for PR Template

Look for an existing PR template in the repository. Check these common locations in order:

1. `.github/PULL_REQUEST_TEMPLATE.md`
2. `.github/pull_request_template.md`
3. `docs/pull_request_template.md`
4. `PULL_REQUEST_TEMPLATE.md`
5. `.github/PULL_REQUEST_TEMPLATE/` directory (if multiple templates exist)

If a template is found, read its contents. It will be used as the base structure in the next step.

If no template is found, the PR body will be built from scratch in the next step.

### 3. Compose PR Title and Description

Using the context gathered in Step 1 and the optional template from Step 2, compose the PR title and body.

#### Title

- If the user provided an explicit title, use it verbatim.
- Otherwise, derive a concise title from the commit messages or branch name.
- Keep it under 72 characters.

#### Body

If a PR template was found in Step 2, use it as the base structure and fill in or merge the sections below. Preserve any existing sections from the template (e.g., checklists, additional headings).

If no template was found, build the body from scratch with the following sections:

##### Summary

Write a clear, concise summary of the changes:

- What was changed and why
- Key implementation details worth highlighting
- Any trade-offs or decisions made

##### Screenshots (conditionally included)

Include this section only if the repository appears to be front-end or UI-focused. Signals include:

- UI framework dependencies (e.g., `react`, `vue`, `angular`, `svelte`, `next`, `nuxt`, `remix`, `solid-js`, `astro`) in `package.json`
- Directories like `src/components/`, `src/pages/`, `src/views/`, `app/`, `public/`
- CSS/SCSS/Tailwind configuration files
- Storybook or similar UI tooling

If the repository is not front-end, skip this section entirely.

If including this section:

- If the user provided an image file path, add a placeholder: `![Screenshot](screenshot)` — the actual upload is handled in Step 5.
- If the user has NOT provided a screenshot, include the section with an empty placeholder:

```markdown
## Screenshots

<!-- Add screenshots here if applicable -->
```

- If the user gives other specific instructions about screenshots (e.g., "generate a table comparing before and after"), follow those instructions.

##### Refs

Include references to related issues, PRs, or external tracker tickets. Use GitHub keyword linking where appropriate:

```markdown
## Refs

- Closes #123
- Related to #456
- [JIRA-789](https://your-tracker.example.com/JIRA-789)
```

- Use the issue/ticket references collected in Step 1.
- If there are no known references, include the section with a placeholder:

```markdown
## Refs

<!-- Add related issues, PRs, or tickets here -->
```

### 4. Ensure Branch is Pushed

Before creating the PR, ensure the current branch exists on the remote:

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null
```

- **If no upstream exists**: Push with `git push -u origin HEAD`.
- **If upstream exists but local is ahead**: Push with `git push`.
- **If upstream exists but has diverged**: Warn the user and ask how to proceed (force-push, rebase, or abort).
- **If push fails**: Report the error to the user (e.g., permission denied, network error) and do not proceed to PR creation.

### 5. Create the Pull Request

Use the `gh` CLI to create the PR. Pass the PR body via a heredoc or temp file to avoid shell escaping issues with complex Markdown.

#### Build the command

Start with the base command and add flags based on what was collected in Step 1:

```bash
gh pr create \
  --title "<title>" \
  --body "<body>"
```

Add optional flags as applicable:

- `--draft` if the user chose draft mode
- `--base <branch>` with the base branch determined in Step 1
- `--reviewer <user1>,<user2>` if reviewers were specified
- `--label <label1>,<label2>` if labels were specified
- `--milestone <name>` if a milestone was specified

#### Handle screenshot upload

If the user provided a screenshot file path, create the PR first (as draft if not already), then upload the image by editing the PR body:

```bash
gh pr edit <pr-number> --body "$(updated body with uploaded image URL)"
```

If direct CLI upload is not feasible, inform the user that they need to manually drag-and-drop the image into the PR on GitHub's web UI.

#### Handle errors

- **PR already exists for this branch**: Inform the user and offer to open the existing PR URL or update it with `gh pr edit`.
- **Network error**: Retry once, then report the failure.
- **Other errors**: Report the error message to the user and suggest corrective action.

### 6. Confirm and Share

After the PR is created, present the result to the user:

- **PR URL** (as a clickable link)
- **PR title**
- **Short summary** of what the PR contains

If any follow-up actions are needed, list them:

- Screenshots left empty → remind the user to add them before requesting review
- No reviewers assigned → suggest assigning reviewers if appropriate
- Draft PR → remind the user to mark it as "Ready for review" when done
