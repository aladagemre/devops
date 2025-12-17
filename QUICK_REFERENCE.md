# Quick Reference Guide

Fast reference for common tasks and configurations.

## Table of Contents
- [Setup New Project](#setup-new-project)
- [Configuration Snippets](#configuration-snippets)
- [Troubleshooting](#troubleshooting)
- [Common Customizations](#common-customizations)

## Setup New Project

### Next.js Project (3 minutes)

```bash
# 1. Create renovate.json
cat > renovate.json << 'EOF'
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ]
}
EOF

# 2. Create CI workflow
mkdir -p .github/workflows
cat > .github/workflows/ci.yml << 'EOF'
name: CI
on:
  pull_request:
    paths-ignore: ['**.md', 'docs/**']
  push:
    branches: [main]
    paths-ignore: ['**.md', 'docs/**']
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
EOF

# 3. Install Renovate App
# Visit: https://github.com/apps/renovate

# 4. Enable auto-merge in repository settings
# Settings → Pull Requests → Allow auto-merge

# 5. Configure branch protection
# Settings → Branches → Add rule for main → Require CI
```

### Python Django Project (3 minutes)

```bash
# 1. Create renovate.json
cat > renovate.json << 'EOF'
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/python"
  ]
}
EOF

# 2. Create CI workflow
mkdir -p .github/workflows
cat > .github/workflows/ci.yml << 'EOF'
name: CI
on:
  pull_request:
    paths-ignore: ['**.md', 'docs/**']
  push:
    branches: [main]
    paths-ignore: ['**.md', 'docs/**']
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/python-django-ci.yml@main
    with:
      python-version: '3.11'
      requirements-file: 'requirements.txt'
EOF
```

## Configuration Snippets

### Renovate

#### Change Update Schedule

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "schedule": ["after 10pm every sunday"]
}
```

#### Pin Specific Package

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "packageRules": [
    {
      "matchPackageNames": ["next"],
      "automerge": false
    }
  ]
}
```

#### Increase Stability Days

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "stabilityDays": 7
}
```

#### Reduce PR Limit

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "prConcurrentLimit": 2
}
```

#### Monthly Updates Only

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "schedule": ["on the first saturday of the month"]
}
```

### GitHub Actions

#### Skip Fresh Build Test (Next.js)

```yaml
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
      skip-build-test: true
```

#### Custom Python Requirements File

```yaml
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/python-django-ci.yml@main
    with:
      python-version: '3.11'
      requirements-file: 'requirements/production.txt'
```

#### Monorepo Setup

```yaml
jobs:
  frontend:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
      working-directory: './frontend'

  backend:
    uses: YOUR_USERNAME/devops/.github/workflows/python-fastapi-ci.yml@main
    with:
      python-version: '3.11'
      working-directory: './backend'
```

#### Multiple Node Versions

```yaml
jobs:
  ci:
    strategy:
      matrix:
        node-version: ['18', '20', '21']
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: ${{ matrix.node-version }}
```

## Troubleshooting

### Renovate Not Creating PRs

```bash
# Check Renovate Dashboard issue
# Look for: "Dependency Dashboard" issue in repository

# Validate renovate.json
cat renovate.json | jq .

# Check Renovate App is installed
# Visit: https://github.com/apps/renovate
```

### CI Not Running

```bash
# Validate workflow YAML
yamllint .github/workflows/ci.yml

# Check workflow file location
ls -la .github/workflows/

# View GitHub Actions tab in repository
# Check for error messages
```

### Auto-Merge Not Working

**Checklist:**
```
[ ] "Allow auto-merge" enabled (Settings → Pull Requests)
[ ] Branch protection configured (Settings → Branches)
[ ] CI check required in branch protection
[ ] Update is patch or minor (not major)
[ ] CI checks passing
[ ] Package not in 0.x.x range
```

### Fresh Build Test Failing

```bash
# Test locally
rm -rf node_modules .next
npm ci
npm run build

# If it fails, you have missing dependencies
# Check the error and add missing packages:
npm install --save-dev MISSING_PACKAGE
```

## Common Customizations

### Never Auto-Merge Certain Packages

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "packageRules": [
    {
      "matchPackageNames": ["react", "next", "django"],
      "automerge": false,
      "labels": ["needs-review"]
    }
  ]
}
```

### Separate Dev Dependencies

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "packageRules": [
    {
      "matchDepTypes": ["devDependencies"],
      "groupName": "dev dependencies",
      "automerge": true
    }
  ]
}
```

### Ignore Specific Packages

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "ignoreDeps": ["package-to-ignore"]
}
```

### Different Schedule for Security Updates

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "vulnerabilityAlerts": {
    "schedule": ["at any time"]
  }
}
```

### Custom Commit Message Format

```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "commitMessagePrefix": "build:",
  "commitMessageAction": "bump",
  "commitMessageTopic": "{{depName}}"
}
```

### Skip CI for Specific Paths

```yaml
on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
      - 'scripts/**'
      - '.vscode/**'
```

### Only Run CI on Specific Paths

```yaml
on:
  pull_request:
    paths:
      - 'src/**'
      - 'package.json'
      - 'package-lock.json'
```

### Add Deployment Step

```yaml
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'

  deploy:
    needs: ci
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to production
        run: |
          # Your deployment commands
```

## File Locations Reference

```
your-project/
├── renovate.json              # Root of repository
└── .github/
    └── workflows/
        └── ci.yml             # CI workflow
```

## Links

- [Getting Started Guide](GETTING_STARTED.md)
- [Renovate Usage](docs/renovate-usage.md)
- [Workflows Usage](docs/workflows-usage.md)
- [Implementation Notes](IMPLEMENTATION_NOTES.md)
- [Examples](examples/)

## Command Reference

### Copy Example Configs

```bash
# Next.js
cp /path/to/devops/examples/nextjs/* /path/to/your/project/

# Django
cp /path/to/devops/examples/django/* /path/to/your/project/

# FastAPI
cp /path/to/devops/examples/fastapi/* /path/to/your/project/
```

### Validate JSON

```bash
# Validate renovate.json
cat renovate.json | jq .

# Pretty print
cat renovate.json | jq . > renovate.json.tmp && mv renovate.json.tmp renovate.json
```

### Validate YAML

```bash
# Install yamllint
pip install yamllint

# Validate
yamllint .github/workflows/ci.yml
```

### Test CI Locally (Next.js)

```bash
# Install dependencies
npm ci --prefer-offline --no-audit

# Type check
npm run typecheck || npx tsc --noEmit

# Lint
npm run lint

# Test
npm run test

# Build
npm run build

# Fresh build test
rm -rf node_modules .next
npm ci
npm run build
```

### Test CI Locally (Django)

```bash
# Install dependencies
pip install -r requirements.txt

# Format check
black --check .

# Lint
flake8 .

# Type check
mypy .

# Check migrations
python manage.py makemigrations --check --dry-run

# System checks
python manage.py check --deploy

# Run tests
pytest --cov
```

## Time Savings

After implementing these configurations:

- **Setup time**: 3-5 minutes per project (vs 30-60 minutes custom setup)
- **PR review time**: 70-90% reduction (grouped updates)
- **CI duration**: 2-5x faster (aggressive caching)
- **Maintenance**: Near-zero (auto-merge patch/minor)
- **Security response**: Immediate (automated security PRs)

## Support

Need help?
1. Check [GETTING_STARTED.md](GETTING_STARTED.md)
2. Review [Troubleshooting](#troubleshooting) above
3. Check Dependency Dashboard in your repository
4. Review GitHub Actions logs
5. Create issue in devops repository
