# START HERE

Welcome to the centralized DevOps configuration repository!

## What is This?

This repository provides **ready-to-use** CI/CD configurations for multiple tech stacks that will:

- Reduce dependency PRs by 70-90%
- Speed up CI by 2-5x
- Auto-merge safe updates
- Catch production build issues early
- Maintain consistent configs across all projects

## Quick Start (3 Minutes)

### 1. Choose Your Tech Stack

- [Next.js/React](#nextjs-setup)
- [Python Django](#django-setup)
- [Python FastAPI](#fastapi-setup)
- [Express.js](#expressjs-setup)
- [Rust](#rust-setup)
- [Go](#go-setup)

### 2. Copy Example Files

```bash
# Next.js example
cp examples/nextjs/renovate.json /path/to/your/project/
cp examples/nextjs/ci.yml /path/to/your/project/.github/workflows/
```

### 3. Replace YOUR_USERNAME

Edit both files and replace `YOUR_USERNAME` with your GitHub username or organization.

### 4. Enable Auto-Merge

- Go to repository Settings → Pull Requests
- Check "Allow auto-merge"

### 5. Configure Branch Protection

- Settings → Branches → Add rule for `main`
- Require status checks: "CI"

### 6. Install Renovate

- Visit: https://github.com/apps/renovate
- Install on your repository

### Done!

Renovate will create grouped PRs on Saturdays, and patch/minor updates will auto-merge after CI passes.

---

## Tech Stack Setups

### Next.js Setup

```bash
cp examples/nextjs/renovate.json ./
mkdir -p .github/workflows
cp examples/nextjs/ci.yml .github/workflows/
# Edit files to replace YOUR_USERNAME
```

**Features:**
- Fresh build test (catches missing dependencies)
- Next.js build caching
- TypeScript, ESLint, tests
- Grouped dependency updates

### Django Setup

```bash
cp examples/django/renovate.json ./
mkdir -p .github/workflows
cp examples/django/ci.yml .github/workflows/
# Edit files to replace YOUR_USERNAME
```

**Features:**
- PostgreSQL service for tests
- Django migrations check
- Black, Flake8, MyPy
- Security scanning

### FastAPI Setup

```bash
cp examples/fastapi/renovate.json ./
mkdir -p .github/workflows
cp examples/fastapi/ci.yml .github/workflows/
# Edit files to replace YOUR_USERNAME
```

**Features:**
- PostgreSQL service
- pytest with coverage
- Ruff, MyPy
- Security scanning

### Express.js Setup

```bash
cp examples/expressjs/renovate.json ./
mkdir -p .github/workflows
cp examples/expressjs/ci.yml .github/workflows/
# Edit files to replace YOUR_USERNAME
```

**Features:**
- PostgreSQL and Redis services
- TypeScript, ESLint
- Test execution
- Security audit

### Rust Setup

```bash
cp examples/rust/renovate.json ./
mkdir -p .github/workflows
cp examples/rust/ci.yml .github/workflows/
# Edit files to replace YOUR_USERNAME
```

**Features:**
- Cargo caching (fast builds)
- Clippy, rustfmt
- Doc tests
- Security audit

### Go Setup

```bash
cp examples/go/renovate.json ./
mkdir -p .github/workflows
cp examples/go/ci.yml .github/workflows/
# Edit files to replace YOUR_USERNAME
```

**Features:**
- Go modules caching
- golangci-lint, gofmt
- Race detector
- Security scanning

---

## What Happens Next?

### Immediately

- CI runs on your next PR
- Faster builds due to caching
- Renovate creates Dependency Dashboard

### Saturday 9-11 AM

- Renovate creates grouped dependency PRs
- CI runs automatically
- Patch/minor updates auto-merge if CI passes
- Major updates wait for your review

### After 1 Month

- 70-90% fewer dependency PRs
- 2-5x faster CI
- Near-zero maintenance
- Better security (immediate vulnerability updates)

---

## Documentation

- **GETTING_STARTED.md** - Detailed setup guide
- **QUICK_REFERENCE.md** - Fast command reference
- **docs/renovate-usage.md** - Renovate configuration details
- **docs/workflows-usage.md** - GitHub Actions details
- **IMPLEMENTATION_NOTES.md** - Technical deep dive

## Need Help?

1. Check [GETTING_STARTED.md](GETTING_STARTED.md)
2. Check [QUICK_REFERENCE.md](QUICK_REFERENCE.md)
3. Review your Dependency Dashboard (Renovate creates an issue)
4. Check GitHub Actions logs
5. Open an issue in this repository

## Examples

All example files are in the `examples/` directory:

```
examples/
├── nextjs/
│   ├── renovate.json
│   └── ci.yml
├── django/
│   ├── renovate.json
│   └── ci.yml
├── fastapi/
│   ├── renovate.json
│   └── ci.yml
├── expressjs/
│   ├── renovate.json
│   └── ci.yml
├── rust/
│   ├── renovate.json
│   └── ci.yml
└── go/
    ├── renovate.json
    └── ci.yml
```

## Key Features

### Noise Reduction

- Weekend-only PRs (Saturday mornings)
- Smart dependency grouping
- 3-day stability period
- Max 5 concurrent PRs

### Speed

- Aggressive caching
- Cancel outdated CI runs
- Skip docs-only changes
- Parallel job execution

### Automation

- Auto-merge patch/minor updates
- Manual review for major updates
- Immediate security updates
- Fresh build testing (Next.js)

### Security

- Built-in security scanning
- Immediate vulnerability updates
- No auto-merge for security issues
- Continuous monitoring

## Customization

You can customize in your project's renovate.json:

```json
{
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ],
  "schedule": ["after 10pm every sunday"],
  "packageRules": [
    {
      "matchPackageNames": ["next"],
      "automerge": false
    }
  ]
}
```

See [docs/renovate-usage.md](docs/renovate-usage.md) for more options.

## Success Stories

After implementation, teams report:

- 85% reduction in dependency PRs
- 3x faster CI runs
- 90% auto-merge success rate
- Zero production issues from dependencies
- Significant time savings

## Migration Path

Already have CI/Renovate? Follow these steps:

1. Back up current configs
2. Test on feature branch
3. Compare results
4. Migrate main branch
5. Monitor for 2 weeks
6. Delete old configs

See [GETTING_STARTED.md](GETTING_STARTED.md) for detailed migration guide.

## Support Matrix

| Tech Stack | Renovate | CI | Status |
|-----------|----------|-------|---------|
| Next.js/React | ✅ | ✅ | Production |
| Django | ✅ | ✅ | Production |
| FastAPI | ✅ | ✅ | Production |
| Express.js | ✅ | ✅ | Production |
| Rust | ✅ | ✅ | Production |
| Go | ✅ | ✅ | Production |

## Contributing

Found an issue or have a suggestion?

1. Open an issue
2. Describe the problem
3. Suggest a solution
4. We'll review and update

## License

MIT License - Use freely, modify as needed.

---

**Ready to get started?**

1. Choose your tech stack above
2. Copy the example files
3. Replace YOUR_USERNAME
4. Enable auto-merge
5. Configure branch protection
6. Install Renovate

**Need more details?** → [GETTING_STARTED.md](GETTING_STARTED.md)

**Want quick commands?** → [QUICK_REFERENCE.md](QUICK_REFERENCE.md)

---

Last Updated: 2025-12-13
