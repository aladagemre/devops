# Renovate Configuration Usage Guide

This guide explains how to use the centralized Renovate configurations in your projects.

## Overview

The Renovate configurations are designed to:
- **Reduce PR noise** by grouping related dependencies
- **Schedule updates** for weekends only (Saturday mornings)
- **Auto-merge** patch and minor updates after CI passes
- **Require manual review** for major version updates
- **Add stability days** (3 days) to avoid immediately adopting broken releases

## Available Configurations

### Base Configuration (`base.json`)
Shared settings for all projects:
- Weekend scheduling (Saturday 9-11 AM)
- 3-day stability period
- Auto-merge for patch/minor updates
- Manual review for major updates
- PR limits (max 5 concurrent)
- Semantic commit messages
- Security vulnerability alerts (immediate)

### Tech Stack Specific Configurations

1. **Next.js/React** (`nextjs.json`)
2. **Python Django/FastAPI** (`python.json`)
3. **Express.js** (`expressjs.json`)
4. **Rust** (`rust.json`)
5. **Go** (`go.json`)

## Setup Instructions

### Step 1: Create `renovate.json` in Your Project Root

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ]
}
```

Replace `YOUR_USERNAME` with your GitHub username or organization name.

### Step 2: Choose Your Tech Stack Configuration

#### For Next.js/React Projects
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ]
}
```

#### For Django Projects
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/python"
  ]
}
```

#### For FastAPI Projects
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/python"
  ]
}
```

#### For Express.js Projects
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/expressjs"
  ]
}
```

#### For Rust Projects
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/rust"
  ]
}
```

#### For Go Projects
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/go"
  ]
}
```

### Step 3: Enable Renovate on GitHub

1. Install the Renovate GitHub App on your repository
2. Go to: https://github.com/apps/renovate
3. Click "Configure"
4. Select your repository
5. Grant necessary permissions

### Step 4: Enable Auto-Merge

For auto-merge to work, you need to enable it in your repository settings:

1. Go to your repository Settings
2. Navigate to "General" → "Pull Requests"
3. Enable "Allow auto-merge"
4. Optionally enable "Automatically delete head branches"

Then configure branch protection rules:

1. Go to Settings → Branches
2. Add a branch protection rule for `main` (or your default branch)
3. Enable "Require status checks to pass before merging"
4. Select the CI checks that must pass
5. Enable "Require branches to be up to date before merging"

## Dependency Grouping

### Next.js Projects

Dependencies are grouped as follows:

- **Next.js core**: `next`, `@next/*`
- **React core**: `react`, `react-dom`
- **TypeScript**: `typescript`, `@types/*`, `ts-*`
- **ESLint**: `eslint*`, `@typescript-eslint/*`
- **Testing**: `@testing-library/*`, `jest`, `vitest`
- **TailwindCSS**: `tailwindcss`, `@tailwindcss/*`, `postcss`, `autoprefixer`
- **Build tools**: `webpack`, `vite`, `turbo`, `esbuild`, `swc`
- **UI libraries**: `@radix-ui/*`, `@headlessui/*`, `lucide-react`
- **State management**: `zustand`, `jotai`, `redux`
- **Data fetching**: `swr`, `@tanstack/react-query`, `axios`

### Python Projects

- **Django core**: `Django`, `django`
- **Django extensions**: `django-*`, `djangorestframework`
- **FastAPI**: `fastapi`, `uvicorn`, `starlette`, `pydantic`
- **Database drivers**: `psycopg2`, `mysqlclient`, `pymongo`, `sqlalchemy`
- **Testing**: `pytest*`, `coverage`, `httpx`
- **Linting**: `black`, `flake8`, `mypy`, `ruff`, `isort`
- **Task queues**: `celery`, `kombu`, `amqp`
- **Authentication**: `PyJWT`, `python-jose`, `passlib`

### Express.js Projects

- **Express core**: `express`, `express-*`, middleware packages
- **Authentication**: `passport*`, `jsonwebtoken`, `bcrypt`
- **Database clients**: `mongoose`, `sequelize`, `typeorm`, `prisma`
- **Validation**: `joi`, `yup`, `zod`, `express-validator`
- **Testing**: `jest`, `mocha`, `chai`, `supertest`, `sinon`
- **TypeScript**: `typescript`, `@types/*`, `ts-node`
- **Linting**: `eslint*`, `@typescript-eslint/*`

### Rust Projects

- **Async runtime**: `tokio*`, `async-std`, `futures*`
- **Web frameworks**: `actix-web`, `actix-*`, `axum*`, `warp`, `rocket`
- **Serialization**: `serde*`, `toml`
- **Database clients**: `sqlx*`, `diesel*`, `sea-orm`, `mongodb`
- **HTTP clients**: `reqwest`, `hyper`, `ureq`
- **Testing**: `criterion`, `mockall`, `proptest`, `quickcheck`
- **Logging**: `tracing*`, `log`, `env_logger`

### Go Projects

- **Web frameworks**: `gin-gonic/gin`, `labstack/echo`, `gofiber/fiber`, `gorilla/mux`
- **Database drivers**: `lib/pq`, `go-sql-driver/mysql`, `gorm.io/*`
- **Testing**: `stretchr/testify`, `golang/mock`, `onsi/ginkgo`, `onsi/gomega`
- **Logging**: `sirupsen/logrus`, `uber.org/zap`, `rs/zerolog`
- **Validation**: `go-playground/validator`, `asaskevich/govalidator`
- **Configuration**: `spf13/viper`, `spf13/cobra`, `joho/godotenv`
- **Utilities**: `google/uuid`, `golang.org/x/crypto`, `golang.org/x/sync`

## Update Schedule

- **Regular updates**: Saturday 9-11 AM (your configured timezone)
- **Security updates**: Immediate (any time)
- **Lock file maintenance**: First day of each month at 3 AM

## Auto-Merge Behavior

### What Gets Auto-Merged
- **Patch updates** (1.2.3 → 1.2.4) - after CI passes
- **Minor updates** (1.2.3 → 1.3.0) - after CI passes
- **Lock file maintenance** - after CI passes

### What Requires Manual Review
- **Major updates** (1.x.x → 2.0.0)
- **Updates to 0.x.x versions** (considered unstable)
- **Security vulnerabilities** (flagged for immediate attention)

## Customization

You can override or extend the base configuration in your project:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ],
  "packageRules": [
    {
      "description": "Never auto-merge specific package",
      "matchPackageNames": ["critical-package"],
      "automerge": false
    },
    {
      "description": "Custom schedule for this project",
      "schedule": ["after 10pm on sunday"]
    }
  ]
}
```

## Troubleshooting

### PRs Not Being Created

1. Check the Renovate Dashboard issue in your repository
2. Verify the configuration is valid JSON
3. Check GitHub App permissions
4. Review Renovate logs in the Dashboard

### Auto-Merge Not Working

1. Ensure "Allow auto-merge" is enabled in repository settings
2. Verify branch protection rules are configured
3. Check that CI checks are passing
4. Confirm the dependency qualifies for auto-merge (patch/minor only)

### Too Many PRs

If you're getting too many PRs, you can:

1. Increase stability days in your config
2. Reduce `prConcurrentLimit`
3. Add more aggressive grouping rules
4. Adjust the schedule to less frequent updates

Example:
```json
{
  "extends": ["github>YOUR_USERNAME/devops//renovate/base"],
  "stabilityDays": 7,
  "prConcurrentLimit": 3,
  "schedule": ["after 9am on the first saturday of the month"]
}
```

## Best Practices

1. **Start with the base configuration** and add tech-stack specific configs as needed
2. **Monitor the Dependency Dashboard** for the first few weeks
3. **Review auto-merged PRs** occasionally to ensure quality
4. **Pin critical dependencies** that require careful review
5. **Use semantic versioning** properly in your own packages
6. **Keep CI fast** so auto-merge doesn't get delayed

## Security

- Security updates are processed immediately, bypassing the weekend schedule
- Vulnerabilities are labeled with `security` for visibility
- Security updates are NOT auto-merged by default (requires manual review)
- Use Dependabot or Snyk alongside Renovate for additional security scanning

## Migration from Dependabot

If you're currently using Dependabot:

1. Create `renovate.json` as described above
2. Disable Dependabot in repository settings
3. Let Renovate create its initial PRs
4. Review and adjust configuration as needed
5. Delete `.github/dependabot.yml` once satisfied

Renovate offers more features:
- Better grouping and scheduling
- Auto-merge capabilities
- Stability days
- More granular control
- Support for more package managers
