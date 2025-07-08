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

### Allowed Tools

The workflow is configured with a specific set of allowed tools for security:
- File operations: Edit, View, Create, GlobTool, GrepTool
- Git operations: git diff, git status, git add, git commit
- Build/test commands: npm install, npm run build, npm run test, npm run lint
- Basic diagnostics: ls -la, cat