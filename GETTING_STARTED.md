# Getting Started Guide

This guide walks you through setting up your projects to use the centralized DevOps configurations.

## Prerequisites

- GitHub repository with admin access
- Renovate GitHub App installed (for dependency management)
- Branch protection enabled on main branch

## Quick Start (5 Minutes)

### For a Next.js Project

1. **Add Renovate configuration**

Create `renovate.json` in your project root:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ]
}
```

2. **Add CI workflow**

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  pull_request:
    paths-ignore:
      - '**.md'
      - 'docs/**'
  push:
    branches: [main]
    paths-ignore:
      - '**.md'
      - 'docs/**'

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
```

3. **Enable auto-merge in repository settings**

- Go to Settings → General → Pull Requests
- Check "Allow auto-merge"

4. **Configure branch protection**

- Go to Settings → Branches
- Add rule for `main`
- Require status checks to pass: select "CI"

5. **Install Renovate GitHub App**

- Visit: https://github.com/apps/renovate
- Install on your repository

**Done!** Renovate will create PRs on Saturdays, and patch/minor updates will auto-merge after CI passes.

## What You Get

### Noise Reduction
- **Before**: 20+ dependency PRs per week
- **After**: 2-5 grouped PRs on weekends

### Faster CI
- Aggressive caching (dependencies, builds)
- Concurrency controls (cancel outdated runs)
- Skip docs-only changes
- Parallel job execution

### Automated Updates
- Patch/minor updates auto-merge after CI passes
- Major updates flagged for manual review
- Security updates prioritized
- 3-day stability period to avoid broken releases

### Fresh Build Testing (Next.js Only)
Simulates production deployment:
1. Removes node_modules
2. Clean install
3. Build from scratch
4. Verify artifacts

This catches missing dependencies before production.

## Complete Setup Guide

### Step 1: Choose Your Tech Stack

Identify which stack(s) your project uses:

- TypeScript + Next.js/React → `nextjs.json`
- Python + Django → `python.json`
- Python + FastAPI → `python.json`
- Express.js → `expressjs.json`
- Rust → `rust.json`
- Go → `go.json`

### Step 2: Configure Renovate

Create `renovate.json` in project root:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/STACK_CONFIG"
  ]
}
```

Replace:
- `YOUR_USERNAME` with your GitHub username/org
- `STACK_CONFIG` with your tech stack config name

**Examples:**

Next.js:
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>emre/devops//renovate/base",
    "github>emre/devops//renovate/nextjs"
  ]
}
```

Django:
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>emre/devops//renovate/base",
    "github>emre/devops//renovate/python"
  ]
}
```

### Step 3: Add GitHub Actions Workflow

Create `.github/workflows/ci.yml`:

Choose the template for your stack from the examples below:

**Next.js:**
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
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
```

**Django:**
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
```

**FastAPI:**
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
```

**Express.js:**
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
```

**Rust:**
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
```

**Go:**
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
```

### Step 4: Configure Repository Settings

**Enable Auto-Merge:**

1. Go to repository Settings
2. General → Pull Requests
3. Check "Allow auto-merge"
4. Check "Automatically delete head branches"

**Configure Branch Protection:**

1. Settings → Branches
2. Add rule for `main`
3. Enable:
   - "Require status checks to pass before merging"
   - Select your CI check (usually "CI")
   - "Require branches to be up to date before merging"
   - "Require linear history" (optional but recommended)

### Step 5: Install Renovate

1. Visit: https://github.com/apps/renovate
2. Click "Configure"
3. Select your repository
4. Grant permissions

### Step 6: Verify Setup

1. **Check Renovate Dashboard**
   - Renovate will create a "Dependency Dashboard" issue
   - Review detected dependencies

2. **Test CI**
   - Make a small change
   - Create a PR
   - Verify CI runs successfully

3. **Wait for First Renovate PR**
   - Will arrive on Saturday 9-11 AM
   - Should be a grouped PR
   - Should auto-merge if CI passes (for patch/minor)

## Customization

### Adjust Update Schedule

In your `renovate.json`:

```json
{
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ],
  "schedule": ["after 10pm every sunday"]
}
```

### Pin Specific Packages

Never auto-merge certain packages:

```json
{
  "extends": ["..."],
  "packageRules": [
    {
      "matchPackageNames": ["next", "react"],
      "automerge": false
    }
  ]
}
```

### Disable Fresh Build Test (Next.js)

If you want to skip the fresh build test:

```yaml
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
      skip-build-test: true
```

### Different Python Requirements File

```yaml
jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/python-django-ci.yml@main
    with:
      python-version: '3.11'
      requirements-file: 'requirements/production.txt'
```

## Monorepo Setup

For monorepos with multiple tech stacks:

**Repository structure:**
```
my-monorepo/
├── frontend/          # Next.js
├── backend/           # FastAPI
├── mobile-api/        # Express.js
└── .github/
    └── workflows/
        └── ci.yml
```

**Root `renovate.json`:**
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base"
  ],
  "packageRules": [
    {
      "matchPaths": ["frontend/**"],
      "extends": ["github>YOUR_USERNAME/devops//renovate/nextjs"]
    },
    {
      "matchPaths": ["backend/**"],
      "extends": ["github>YOUR_USERNAME/devops//renovate/python"]
    },
    {
      "matchPaths": ["mobile-api/**"],
      "extends": ["github>YOUR_USERNAME/devops//renovate/expressjs"]
    }
  ]
}
```

**CI Workflow `.github/workflows/ci.yml`:**
```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

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

  mobile-api-ci:
    uses: YOUR_USERNAME/devops/.github/workflows/expressjs-ci.yml@main
    with:
      node-version: '20'
      working-directory: './mobile-api'
```

## Troubleshooting

### Renovate Not Creating PRs

**Check the Dependency Dashboard:**
- Renovate creates an issue titled "Dependency Dashboard"
- Lists all pending updates
- Shows configuration errors

**Common issues:**
- Invalid JSON in `renovate.json`
- Wrong repository reference in `extends`
- Schedule hasn't been reached yet (wait until Saturday)
- Renovate App not installed

### CI Failing

**Check workflow syntax:**
```bash
# Validate YAML locally
yamllint .github/workflows/ci.yml
```

**Common issues:**
- Wrong repository reference in `uses`
- Incorrect input parameters
- Workflow file not in `.github/workflows/`
- Branch name mismatch (`@main` vs `@master`)

### Auto-Merge Not Working

**Checklist:**
- [ ] "Allow auto-merge" enabled in repository settings
- [ ] Branch protection configured with required checks
- [ ] CI checks passing
- [ ] Update is patch or minor (not major)
- [ ] Package is not in 0.x.x version range

**Debug:**
1. Check PR labels - should have `dependencies`
2. Review Renovate Dashboard for automerge status
3. Verify branch protection rules match CI job names

### Fresh Build Test Failing (Next.js)

This usually means dependencies are missing from `package.json`:

**Test locally:**
```bash
# Simulate the fresh build test
rm -rf node_modules .next
npm ci
npm run build
```

**Common causes:**
- Global package being used (should be in devDependencies)
- Missing peer dependency
- Lock file out of sync

**Fix:**
```bash
# Add missing dependency
npm install --save-dev missing-package

# Regenerate lock file
rm package-lock.json
npm install

# Test again
rm -rf node_modules .next
npm ci
npm run build
```

## Best Practices

### 1. Start with One Project

Don't migrate all projects at once:
- Choose a low-risk project first
- Monitor for 2-3 weeks
- Adjust configuration as needed
- Roll out to other projects

### 2. Review Auto-Merged PRs

Occasionally review what got auto-merged:
- Check the closed PRs with `dependencies` label
- Look for patterns of issues
- Adjust grouping or automerge rules if needed

### 3. Keep CI Fast

- Use caching aggressively
- Skip unnecessary checks
- Run expensive tests only on main branch
- Consider parallel test execution

### 4. Monitor Renovate Dashboard

Check the dashboard weekly:
- Pending updates
- Rate-limited updates
- Configuration warnings
- Dependency graph

### 5. Communicate with Team

- Share this guide with team members
- Document project-specific customizations
- Review major dependency updates together
- Celebrate the reduced PR noise

## Migration Checklist

Moving from existing CI/Renovate setup:

- [ ] Back up existing `.github/workflows/` directory
- [ ] Back up existing `renovate.json` or `.github/renovate.json`
- [ ] Create new `renovate.json` using these configs
- [ ] Create new CI workflow using reusable workflows
- [ ] Test on a feature branch
- [ ] Compare CI duration (before/after)
- [ ] Enable auto-merge in settings
- [ ] Configure branch protection
- [ ] Monitor first Renovate PR cycle
- [ ] Delete old workflow files once confident
- [ ] Document any customizations made

## Next Steps

1. **Review Documentation:**
   - [Renovate Usage Guide](docs/renovate-usage.md)
   - [Workflows Usage Guide](docs/workflows-usage.md)

2. **Join the Dashboard:**
   - Monitor the Dependency Dashboard issue
   - Check for security alerts
   - Review update schedule

3. **Optimize Over Time:**
   - Adjust schedules based on team capacity
   - Fine-tune grouping rules
   - Add custom package rules as needed

4. **Share Feedback:**
   - Report issues in the devops repository
   - Suggest improvements
   - Share success stories

## Support

For questions or issues:
1. Check the documentation in `docs/`
2. Review the Dependency Dashboard in your repository
3. Check GitHub Actions logs for CI issues
4. Create an issue in the devops repository

## Success Metrics

After 1 month, you should see:

- **70-90% reduction** in dependency PRs
- **Faster CI** (due to caching)
- **Higher auto-merge rate** (patch/minor updates)
- **Better security** (immediate vulnerability updates)
- **Less cognitive load** (weekend-only PRs)
- **Fewer production issues** (fresh build testing for Next.js)

Happy automating!
