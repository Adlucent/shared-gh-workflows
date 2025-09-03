# Shared GitHub Workflows

This repository contains reusable GitHub Actions workflows for Adlucent repositories.

## Quick Start

Ready-to-use workflow files are available in the `examples/` directory:
- `claude-pr-workflow.yml` - For Claude PR assistant
- `security-scan-workflow.yml` - For security scanning

Simply copy these files to your repository's `.github/workflows/` directory and you're ready to go!

## Available Workflows

1. **Claude PR Handler** - AI-powered PR assistant
2. **Security Scan** - Comprehensive security and dependency scanning

## Claude PR Handler

The Claude PR handler workflow enables Claude to respond to comments in issues and pull requests when mentioned with `@claude`.

### Usage

Copy `examples/claude-pr-workflow.yml` to your repository's `.github/workflows/` directory (you can rename it if desired).

Or create the file manually with this content:

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

#### File Operations
- Edit, View, Create, GlobTool, GrepTool

#### Git Operations  
- git diff, git status, git add, git commit

#### JavaScript/Node.js
- npm install, npm run build, npm run test, npm run lint

#### Python
- **Execution**: python, python3
- **Package Management**: pip install, pip list, pip freeze
- **Testing**: pytest, python -m pytest, python -m unittest
- **Code Quality**: black, ruff, flake8, mypy, pylint
- **Environment**: python -m venv, source, which python
- **Django**: python manage.py, django-admin

#### General Build Tools
- make, docker, docker-compose

#### API Testing
- curl, wget

#### Basic Diagnostics
- ls -la, cat

**Note**: Individual repositories can request customization of allowed tools based on their specific technology stack and requirements.

---

## Security Scan Workflow

Comprehensive security scanning for Python, Node.js, and Docker applications using free, open-source tools.

### Features

#### Python Security
- **pip-audit**: Scans for known vulnerabilities in Python packages
- **Bandit**: Static security analysis for Python code
- **Safety**: Checks dependencies against Safety vulnerability database
- **Outdated packages**: Identifies packages that need updates
- **License checking**: Reviews package licenses for compliance

#### Node.js Security  
- **npm audit**: Built-in Node.js vulnerability scanning
- **better-npm-audit**: Enhanced npm audit with better reporting
- **Outdated packages**: Identifies packages that need updates
- **License checking**: Reviews package licenses for compliance

#### Docker Security
- **Hadolint**: Dockerfile best practices and security linting
- **Trivy**: Comprehensive vulnerability scanning for containers

### Usage

Copy `examples/security-scan-workflow.yml` to your repository's `.github/workflows/` directory (you can rename it if desired).

Or create the file manually with this content:

```yaml
name: Security Scan

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
  schedule:
    - cron: '0 9 * * 1'  # Weekly on Monday at 9 AM
  workflow_dispatch:

jobs:
  security:
    uses: adlucent/shared-gh-workflows/.github/workflows/security-scan.yml@main
    with:
      python-version: '3.11'     # Optional: default is 3.11
      node-version: '18'         # Optional: default is 18
      enable-python-scan: true   # Optional: default is true
      enable-node-scan: true     # Optional: default is true
      enable-docker-scan: false  # Optional: default is false
```

### Configuration Options

| Input | Description | Required | Default |
|-------|------------|----------|---------|
| `python-version` | Python version to use | No | `3.11` |
| `node-version` | Node.js version to use | No | `18` |
| `enable-python-scan` | Enable Python security scanning | No | `true` |
| `enable-node-scan` | Enable Node.js security scanning | No | `true` |
| `enable-docker-scan` | Enable Docker security scanning | No | `false` |

### Reports

The workflow generates the following reports (available as artifacts):
- **Python**: `pip-audit-report.json`, `bandit-report.json`, `safety-report.json`
- **Node.js**: `npm-audit-report.json`
- **Docker**: `hadolint-report.json`, `trivy-results.sarif`

### Recommendations

1. **Schedule regular scans**: Run weekly to catch new vulnerabilities
2. **PR scanning**: Always scan pull requests before merging
3. **Fix priority**: Address critical and high severity issues immediately
4. **Update regularly**: Keep dependencies up-to-date to minimize vulnerabilities
5. **Use lock files**: Ensure reproducible builds with `requirements.txt`, `package-lock.json`

### No GitHub Enterprise Required

This workflow uses only free, open-source tools and doesn't require:
- GitHub Advanced Security
- GitHub Enterprise
- Dependabot (though it's recommended if available)
- Any paid GitHub features

All vulnerability databases are publicly available and updated regularly by the open-source community.