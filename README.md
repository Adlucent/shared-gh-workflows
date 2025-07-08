# Shared GitHub Workflows

This repository contains reusable GitHub Actions workflows for Adlucent repositories.

## Claude PR Handler

The Claude PR handler workflow enables Claude to respond to comments in issues and pull requests when mentioned with `@claude`.

### Usage

In your repository, create `.github/workflows/claude.yml`:

```yaml
name: Claude PR Assistant
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  claude:
    uses: adlucent/shared-gh-workflows/.github/workflows/claude-pr-handler.yml@main
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

### Requirements

1. Set `ANTHROPIC_API_KEY` as an organization or repository secret
2. Enable GitHub Actions for your repository
3. Allow Adlucent actions in your repository settings

### Branch Behavior

- **The workflow file only needs to exist on your main branch** - it will work for PRs from any branch
- When Claude responds to a PR comment, it automatically checks out and works on the PR's branch, not the main branch
- For issue comments (not on PRs), Claude operates on the main branch
- This means you can safely add the workflow to main and Claude will handle comments on all PRs regardless of their source branch

### Allowed Tools

The workflow is configured with a specific set of allowed tools for security:
- File operations: Edit, View, Create, GlobTool, GrepTool
- Git operations: git diff, git status, git add, git commit
- Build/test commands: npm install, npm run build, npm run test, npm run lint
- Basic diagnostics: ls -la, cat