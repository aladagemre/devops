# Example Configurations

This directory contains example configurations for different tech stacks with support for **hybrid branch strategies**.

## 🎯 Quick Start: Choose Your Strategy

Each tech stack has **THREE** renovate config files:

1. **`renovate.json`** - Default (targets main, backward compatible)
2. **`renovate-high-risk.json`** - For production apps (targets develop branch)
3. **`renovate-low-risk.json`** - For side projects (targets main branch)

**Not sure which to use?** Read [BRANCH_STRATEGY.md](../BRANCH_STRATEGY.md) for detailed guidance.

---

## 📋 Usage

### High-Risk Projects (Production Apps)

**When:** Customer-facing apps, revenue-generating services, >1000 users

```bash
# Copy high-risk Renovate config
cp examples/nextjs/renovate-high-risk.json /path/to/your/project/renovate.json

# Copy CI workflow (supports both main and develop branches)
mkdir -p /path/to/your/project/.github/workflows
cp examples/nextjs/ci.yml /path/to/your/project/.github/workflows/ci.yml

# Ensure develop branch exists
cd /path/to/your/project
git checkout -b develop
git push -u origin develop
```

**Workflow:**
- Renovate creates PRs → `develop` branch
- Test in staging
- Manually promote: `develop` → `main`

---

### Low-Risk Projects (Side Projects, Internal Tools)

**When:** Internal tools, <100 users, non-revenue, good test coverage

```bash
# Copy low-risk Renovate config
cp examples/nextjs/renovate-low-risk.json /path/to/your/project/renovate.json

# Copy CI workflow
mkdir -p /path/to/your/project/.github/workflows
cp examples/nextjs/ci.yml /path/to/your/project/.github/workflows/ci.yml
```

**Workflow:**
- Renovate creates PRs → `main` branch
- Automerge after CI passes
- Direct to production

---

## 🗂️ Available Tech Stacks

### Next.js / React

```bash
# High-risk (production app)
cp examples/nextjs/renovate-high-risk.json yourproject/renovate.json
cp examples/nextjs/ci.yml yourproject/.github/workflows/ci.yml

# Low-risk (side project)
cp examples/nextjs/renovate-low-risk.json yourproject/renovate.json
cp examples/nextjs/ci.yml yourproject/.github/workflows/ci.yml
```

### Django

```bash
# High-risk (production app)
cp examples/django/renovate-high-risk.json yourproject/renovate.json
cp examples/django/ci.yml yourproject/.github/workflows/ci.yml

# Low-risk (side project)
cp examples/django/renovate-low-risk.json yourproject/renovate.json
cp examples/django/ci.yml yourproject/.github/workflows/ci.yml
```

### FastAPI

```bash
# High-risk (production API)
cp examples/fastapi/renovate-high-risk.json yourproject/renovate.json
cp examples/fastapi/ci.yml yourproject/.github/workflows/ci.yml

# Low-risk (internal API)
cp examples/fastapi/renovate-low-risk.json yourproject/renovate.json
cp examples/fastapi/ci.yml yourproject/.github/workflows/ci.yml
```

### Express.js

```bash
# High-risk (production backend)
cp examples/expressjs/renovate-high-risk.json yourproject/renovate.json
cp examples/expressjs/ci.yml yourproject/.github/workflows/ci.yml

# Low-risk (dev backend)
cp examples/expressjs/renovate-low-risk.json yourproject/renovate.json
cp examples/expressjs/ci.yml yourproject/.github/workflows/ci.yml
```

### Rust

```bash
# High-risk (production service)
cp examples/rust/renovate-high-risk.json yourproject/renovate.json
cp examples/rust/ci.yml yourproject/.github/workflows/ci.yml

# Low-risk (CLI tool)
cp examples/rust/renovate-low-risk.json yourproject/renovate.json
cp examples/rust/ci.yml yourproject/.github/workflows/ci.yml
```

### Go

```bash
# High-risk (production service)
cp examples/go/renovate-high-risk.json yourproject/renovate.json
cp examples/go/ci.yml yourproject/.github/workflows/ci.yml

# Low-risk (internal service)
cp examples/go/renovate-low-risk.json yourproject/renovate.json
cp examples/go/ci.yml yourproject/.github/workflows/ci.yml
```

---

## ⚙️ Post-Setup Steps

### 1. Replace Placeholder

In `renovate.json`, update `YOUR_USERNAME` with your actual GitHub username:
```json
"github>YOUR_USERNAME/devops//renovate/base"
```

Change to:
```json
"github>your-github-username/devops//renovate/base"
```

For example, if your username is `emre`:
```json
"github>emre/devops//renovate/base"
```

### 2. For High-Risk Projects Only

Set `develop` as the default branch in GitHub repo settings:
1. Go to repository Settings
2. Navigate to Branches
3. Change default branch to `develop`

### 3. Verify CI Triggers

The CI workflow runs on both `main` and `develop` branches by default:
```yaml
on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]
```

---

## 📊 File Comparison

| File | Target Branch | Use Case | Automerge |
|------|---------------|----------|-----------|
| `renovate.json` | `main` | Default/backward compatible | Patch/Minor |
| `renovate-high-risk.json` | `develop` | Production apps | Patch/Minor (to develop) |
| `renovate-low-risk.json` | `main` | Side projects | Patch/Minor (to main) |

All three files extend the same base configuration and tech-stack rules.

---

## 🔄 Switching Strategies

### From Low-Risk to High-Risk

```bash
# 1. Create develop branch
git checkout main
git pull
git checkout -b develop
git push -u origin develop

# 2. Update renovate.json
# Change "baseBranches": ["main"] to "baseBranches": ["develop"]

# 3. Set develop as default branch in GitHub settings
```

### From High-Risk to Low-Risk

```bash
# 1. Merge all develop changes to main
# 2. Update renovate.json
# Change "baseBranches": ["develop"] to "baseBranches": ["main"]

# 3. Optionally delete develop branch
```

---

## 📚 Additional Resources

- **[BRANCH_STRATEGY.md](../BRANCH_STRATEGY.md)** - Comprehensive guide for choosing your strategy
- **[Renovate Usage Guide](../docs/renovate-usage.md)** - Detailed Renovate configuration
- **[Workflows Usage Guide](../docs/workflows-usage.md)** - GitHub Actions workflow details
- **[Getting Started](../GETTING_STARTED.md)** - Complete setup walkthrough

---

## 🤔 Decision Tree

```
Is this app in production with users?
├─ NO  → Use renovate-low-risk.json
└─ YES → Does it handle payments or sensitive data?
    ├─ NO  → Use renovate-low-risk.json
    └─ YES → Use renovate-high-risk.json

Alternative:
How many active users?
├─ <100   → renovate-low-risk.json
├─ <1000  → renovate-low-risk.json (if good tests)
└─ >1000  → renovate-high-risk.json
```

---

## 🔍 Example Portfolios

### Developer with Mixed Projects

| Project | Type | Users | Config | Why |
|---------|------|-------|--------|-----|
| E-commerce | Next.js | 5,000 | `renovate-high-risk.json` | Revenue-critical |
| Personal Blog | Next.js | 50 | `renovate-low-risk.json` | Low impact |
| Company API | FastAPI | 2,000 | `renovate-high-risk.json` | B2B customers |
| CLI Tool | Rust | 30 | `renovate-low-risk.json` | Internal use |

**Result:** 2 projects with safety (develop branch), 2 with speed (main branch)

---

## ❓ FAQ

**Q: Can I use the old `renovate.json` file?**
A: Yes! `renovate.json` is still valid and targets `main` branch by default.

**Q: What's the difference between `renovate.json` and `renovate-low-risk.json`?**
A: They're identical! `renovate-low-risk.json` just makes the strategy explicit.

**Q: Do I need both main and develop branches?**
A: Only if using `renovate-high-risk.json`. Low-risk strategy only needs `main`.

**Q: Will automerge break my production?**
A: **High-risk:** No, automerge only affects develop. You control promotion to main.
A: **Low-risk:** Only patch/minor updates after CI passes. Major updates always manual.

---

## 🎯 Recommendation

**When in doubt:** Start with `renovate-high-risk.json`. You can always switch to low-risk later once you gain confidence in your CI and test coverage.

**Best practice:** Use high-risk for customer-facing apps, low-risk for everything else.
