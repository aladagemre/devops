# GitHub Workflows Usage Guide

This guide explains how to use the centralized GitHub Actions workflows in your projects.

## Overview

The reusable workflows provide:
- **Fast CI** with aggressive caching
- **Concurrency controls** to cancel outdated runs
- **Conditional execution** to skip unnecessary steps
- **Path-based ignoring** for documentation changes
- **Security scanning** built-in
- **Parallel job execution** where possible

## Available Workflows

1. **Next.js CI** - Full build testing with fresh install simulation
2. **Django CI** - Django-specific testing with database
3. **FastAPI CI** - FastAPI testing with pytest
4. **Express.js CI** - Node.js API testing
5. **Rust CI** - Cargo build and test with caching
6. **Go CI** - Go modules with build and test

## Setup Instructions

### Step 1: Create Workflow File in Your Project

Create `.github/workflows/ci.yml` in your project repository.

### Step 2: Choose Your Workflow

#### Next.js/React Projects

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
      - '.vscode/**'
  push:
    branches: [main, develop]
    paths-ignore:
      - '**.md'
      - 'docs/**'
      - '.vscode/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
      skip-build-test: false
      skip-typecheck: false
      skip-lint: false
      skip-tests: false
```

#### Django Projects

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  push:
    branches: [main, develop]
    paths-ignore:
      - '**.md'
      - 'docs/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/python-django-ci.yml@main
    with:
      python-version: '3.11'
      requirements-file: 'requirements.txt'
      skip-tests: false
      skip-lint: false
      skip-migrations-check: false
```

#### FastAPI Projects

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  push:
    branches: [main, develop]
    paths-ignore:
      - '**.md'
      - 'docs/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/python-fastapi-ci.yml@main
    with:
      python-version: '3.11'
      requirements-file: 'requirements.txt'
      skip-tests: false
      skip-lint: false
```

#### Express.js Projects

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  push:
    branches: [main, develop]
    paths-ignore:
      - '**.md'
      - 'docs/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/expressjs-ci.yml@main
    with:
      node-version: '20'
      skip-typecheck: false
      skip-lint: false
      skip-tests: false
```

#### Rust Projects

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  push:
    branches: [main, develop]
    paths-ignore:
      - '**.md'
      - 'docs/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/rust-ci.yml@main
    with:
      rust-version: 'stable'
      skip-tests: false
      skip-clippy: false
      skip-fmt: false
```

#### Go Projects

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  push:
    branches: [main, develop]
    paths-ignore:
      - '**.md'
      - 'docs/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/go-ci.yml@main
    with:
      go-version: '1.21'
      skip-tests: false
      skip-lint: false
      skip-fmt: false
```

Replace `YOUR_USERNAME` with your GitHub username or organization name.

## Workflow Features

### Next.js CI Workflow

**Features:**
- Node.js setup with version selection
- npm dependency caching
- Next.js build cache (`.next/cache`)
- TypeScript type checking
- ESLint linting
- Test execution (if tests exist)
- Production build
- **Fresh build test** (simulates production deployment)

**Fresh Build Test:**
The workflow includes a separate job that:
1. Removes `node_modules` and `.next`
2. Performs clean install (`npm ci`)
3. Builds the application
4. Verifies build artifacts

This catches issues where:
- Dependencies are missing from `package.json`
- Build breaks in fresh environments
- Lock file is out of sync

**Inputs:**
- `node-version` (default: '20')
- `working-directory` (default: '.')
- `skip-build-test` (default: false)
- `skip-typecheck` (default: false)
- `skip-lint` (default: false)
- `skip-tests` (default: false)

### Django CI Workflow

**Features:**
- Python setup with version selection
- pip dependency caching
- PostgreSQL database service
- Black formatter check
- Flake8 linting
- MyPy type checking
- Django migrations check
- Django system checks (deployment mode)
- Test execution with coverage
- Static files collection
- Security scanning (safety, bandit)

**Inputs:**
- `python-version` (default: '3.11')
- `working-directory` (default: '.')
- `requirements-file` (default: 'requirements.txt')
- `skip-tests` (default: false)
- `skip-lint` (default: false)
- `skip-migrations-check` (default: false)

**Environment Variables Set:**
- `DATABASE_URL`: postgresql://postgres:postgres@localhost:5432/test_db
- `DJANGO_SETTINGS_MODULE`: config.settings
- `SECRET_KEY`: test-secret-key-for-ci
- `DEBUG`: 'False'

### FastAPI CI Workflow

**Features:**
- Python setup with version selection
- pip dependency caching
- PostgreSQL database service
- Black formatter check
- Ruff linting
- MyPy type checking
- pytest with coverage
- Coverage report upload
- Security scanning (safety, bandit)

**Inputs:**
- `python-version` (default: '3.11')
- `working-directory` (default: '.')
- `requirements-file` (default: 'requirements.txt')
- `skip-tests` (default: false)
- `skip-lint` (default: false)

### Express.js CI Workflow

**Features:**
- Node.js setup with version selection
- npm dependency caching
- PostgreSQL and Redis services
- TypeScript type checking
- ESLint linting
- Test execution
- Application build
- npm security audit

**Inputs:**
- `node-version` (default: '20')
- `working-directory` (default: '.')
- `skip-typecheck` (default: false)
- `skip-lint` (default: false)
- `skip-tests` (default: false)

### Rust CI Workflow

**Features:**
- Rust toolchain setup
- Cargo registry, index, and build caching
- rustfmt formatting check
- Clippy linting
- Project build
- Test execution
- Doc tests
- Security audit (cargo-audit)
- Outdated dependency check

**Inputs:**
- `rust-version` (default: 'stable')
- `working-directory` (default: '.')
- `skip-tests` (default: false)
- `skip-clippy` (default: false)
- `skip-fmt` (default: false)

### Go CI Workflow

**Features:**
- Go setup with version selection
- Go modules caching
- Dependency verification
- gofmt formatting check
- go vet analysis
- golangci-lint
- Project build
- Test execution with race detection and coverage
- Coverage report upload
- gosec security scanner
- govulncheck vulnerability scanning

**Inputs:**
- `go-version` (default: '1.21')
- `working-directory` (default: '.')
- `skip-tests` (default: false)
- `skip-lint` (default: false)
- `skip-fmt` (default: false)

## Advanced Usage

### Monorepo Support

For monorepos, specify the working directory:

```yaml
jobs:
  frontend-ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
      working-directory: './frontend'

  backend-ci:
    uses: YOUR_USERNAME/devops/.github/workflows/python-fastapi-ci.yml@main
    with:
      python-version: '3.11'
      working-directory: './backend'
```

### Multiple Node/Python Versions

Test against multiple versions:

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

### Conditional Workflows

Skip certain steps for specific branches:

```yaml
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
      skip-tests: ${{ github.ref == 'refs/heads/develop' }}
      skip-build-test: ${{ github.ref == 'refs/heads/develop' }}
```

### Custom Triggers

Run CI on different events:

```yaml
on:
  pull_request:
  push:
    branches: [main]
  schedule:
    - cron: '0 0 * * 0'  # Weekly on Sunday
  workflow_dispatch:  # Manual trigger
```

## Performance Optimization

### Caching Strategy

All workflows implement aggressive caching:

**Node.js Projects:**
- npm dependencies (`node_modules`)
- Next.js build cache (`.next/cache`)
- ESLint cache (`.eslintcache`)

**Python Projects:**
- pip packages
- pytest cache

**Rust Projects:**
- Cargo registry
- Cargo git index
- Build artifacts (`target/`)

**Go Projects:**
- Go modules
- Build cache

### Concurrency Control

All workflows include:

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

This automatically cancels outdated CI runs when new commits are pushed.

### Path Ignoring

Skip CI for documentation changes:

```yaml
on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
      - '.vscode/**'
      - '.github/**'
```

## Integration with Renovate

The workflows are designed to work seamlessly with Renovate auto-merge:

1. **Renovate creates PR** with dependency updates
2. **CI runs automatically** using these workflows
3. **If all checks pass**, Renovate auto-merges (for patch/minor)
4. **If checks fail**, PR requires manual review

Ensure your branch protection rules require these checks:
- Next.js: "CI" job
- Django/FastAPI: "CI" and "Security Checks" jobs
- Others: "CI" and "Security" jobs

## Troubleshooting

### CI Not Running

1. Verify the workflow file is in `.github/workflows/`
2. Check YAML syntax is valid
3. Ensure the devops repository is accessible
4. Verify branch name in `uses` statement (`@main`)

### Caching Issues

If caching causes problems:

1. Clear the cache in GitHub Actions settings
2. Update cache keys in the workflow
3. Check disk space isn't exhausted

### Permission Errors

Ensure your repository has permission to access the devops repository:

1. If private, grant access in repository settings
2. For organizations, verify org-wide settings
3. Check GitHub App permissions if using one

### Fresh Build Test Failing (Next.js)

If fresh build test fails but regular build passes:

1. **Missing dependency in package.json** - most common cause
2. **Relying on global packages** - should be in devDependencies
3. **Lock file out of sync** - run `npm install` locally
4. **Environment-specific code** - check for platform dependencies

Fix by:
```bash
# Locally test the fresh build
rm -rf node_modules .next
npm ci
npm run build
```

## Best Practices

1. **Use path-ignore** to skip CI for docs-only changes
2. **Enable branch protection** requiring CI to pass
3. **Keep workflows updated** by pinning to `@main` or specific version
4. **Monitor CI duration** and optimize slow steps
5. **Use matrix builds** sparingly (they multiply CI time)
6. **Skip optional checks** on feature branches if needed
7. **Review security scan results** regularly

## Migrating Existing CI

If you have existing CI workflows:

1. Back up your current `.github/workflows/` directory
2. Create new workflow using these reusable workflows
3. Test on a feature branch first
4. Compare CI duration and reliability
5. Migrate remaining projects once confident
6. Delete old workflow files

## Versioning

You can pin to specific versions of the workflows:

```yaml
# Pin to a specific commit
uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@abc123

# Pin to a tag
uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@v1.0.0

# Use latest from main (recommended for most projects)
uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
```

## Contributing

When updating workflows in the devops repository:

1. Test changes in a single project first
2. Document breaking changes in commit messages
3. Update this documentation
4. Consider backward compatibility
5. Communicate changes to teams using the workflows
