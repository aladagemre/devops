# Branch Strategy Guide: Choosing Between High-Risk and Low-Risk Workflows

This guide helps you decide which dependency update strategy to use for each project.

## TL;DR: Quick Decision

**High-Risk Projects** (customer-facing, revenue-critical):
- Use `renovate-high-risk.json` → targets `develop` branch
- Dependencies tested in staging before production
- Manual promotion: `develop` → `main`

**Low-Risk Projects** (internal tools, side projects):
- Use `renovate-low-risk.json` → targets `main` branch
- Dependencies go straight to production after CI passes
- Faster updates, less overhead

---

## High-Risk Strategy (Develop Branch)

### When to Use

✅ **Use this strategy if your project has ANY of these:**
- Customer-facing production application
- Revenue-generating service
- Payment processing or financial transactions
- Healthcare, legal, or regulated industry
- Large user base (>1000 active users)
- Complex integration with third-party services
- Requires manual QA/testing before production
- Has staging/development environment
- Team wants to review updates before production
- Breaking a dependency could cost money/reputation

### How It Works

```
1. Renovate creates PR → develop branch
2. CI runs on PR
3. Automerge (patch/minor only) → develop
4. You test in staging environment
5. Manual PR: develop → main (when ready)
6. CI runs again
7. Deploy to production
```

### Configuration

**File:** `renovate-high-risk.json`
```json
{
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ],
  "baseBranches": ["develop"]
}
```

### Branch Setup

Required branches:
- `develop` - Development/staging branch (default branch)
- `main` - Production branch

### Pros & Cons

**Pros:**
- ✅ Extra safety layer before production
- ✅ Test in staging environment first
- ✅ Catch integration issues early
- ✅ Control when updates reach production
- ✅ Can rollback easily (just don't promote to main)

**Cons:**
- ❌ More manual work (promoting develop → main)
- ❌ Production dependencies lag behind
- ❌ Need to maintain two branches
- ❌ Slower update cycle

---

## Low-Risk Strategy (Main Branch)

### When to Use

✅ **Use this strategy if your project has ALL of these:**
- Internal tool or side project
- Small/no user base
- Non-revenue generating
- Good test coverage (>70%)
- Good monitoring/alerting
- Can tolerate brief downtime
- Fast rollback capability
- Team comfortable with direct-to-prod updates
- Breaking a dependency is low-impact

### How It Works

```
1. Renovate creates PR → main branch
2. CI runs on PR
3. Automerge (patch/minor only) → main
4. Auto-deploy to production
5. Monitor for issues
```

### Configuration

**File:** `renovate-low-risk.json`
```json
{
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ],
  "baseBranches": ["main"]
}
```

### Branch Setup

Required branches:
- `main` - Production branch (only branch needed)

Optional:
- Feature branches for new development

### Pros & Cons

**Pros:**
- ✅ Simpler workflow
- ✅ Dependencies always up-to-date
- ✅ Less maintenance overhead
- ✅ Faster iteration
- ✅ No manual promotion needed

**Cons:**
- ❌ Updates go straight to production
- ❌ Need excellent test coverage
- ❌ Need good monitoring
- ❌ Breaking changes hit production immediately

---

## Decision Tree

```
Is this a production application with users?
├─ NO  → Use LOW-RISK (main branch)
└─ YES → Does it generate revenue or handle sensitive data?
    ├─ NO  → Use LOW-RISK (main branch)
    └─ YES → Use HIGH-RISK (develop branch)

Alternative path:

Do you have >70% test coverage AND monitoring?
├─ NO  → Use HIGH-RISK (develop branch)
└─ YES → Can you tolerate 5 minutes of downtime?
    ├─ NO  → Use HIGH-RISK (develop branch)
    └─ YES → How many active users?
        ├─ <100   → Use LOW-RISK (main branch)
        ├─ <1000  → Use LOW-RISK (main branch)
        └─ >1000  → Use HIGH-RISK (develop branch)
```

---

## Examples by Project Type

### High-Risk Projects (Use develop branch)

- E-commerce website
- SaaS application
- Mobile app backend
- Payment processing service
- Healthcare/HIPAA application
- Banking/financial application
- Public API with SLA
- Multi-tenant application
- Customer-facing dashboard
- Revenue-tracking analytics

### Low-Risk Projects (Use main branch)

- Internal admin tool
- Personal blog/portfolio
- Developer documentation site
- Proof of concept / MVP
- Side project with <10 users
- Internal monitoring dashboard
- Company wiki/knowledge base
- Development playground
- Learning/tutorial project
- Open source library (no users in production)

---

## Hybrid Approach Recommendations

**Use different strategies for different projects:**

### Portfolio Example

| Project | Type | Users | Strategy | Reason |
|---------|------|-------|----------|---------|
| E-commerce Site | Next.js | 10,000 | **HIGH-RISK** | Revenue-critical |
| Admin Dashboard | React | 5 | **LOW-RISK** | Internal only |
| Blog API | FastAPI | 500 | **HIGH-RISK** | Public-facing |
| CLI Tool | Rust | 50 | **LOW-RISK** | Low impact |
| Payment Service | Go | 10,000 | **HIGH-RISK** | Financial data |
| Dev Docs | Next.js | 200 | **LOW-RISK** | Non-critical |

---

## Setup Instructions

### For High-Risk Projects

1. **Ensure branches exist:**
   ```bash
   git checkout -b develop
   git push -u origin develop
   ```

2. **Set develop as default branch** in GitHub repo settings

3. **Copy the renovate config:**
   ```bash
   cp devops/examples/nextjs/renovate-high-risk.json renovate.json
   ```

4. **Update YOUR_USERNAME** in renovate.json

5. **Copy the CI workflow:**
   ```bash
   cp devops/examples/nextjs/ci.yml .github/workflows/ci.yml
   ```

6. **Create promotion workflow** (optional automation):
   ```yaml
   # .github/workflows/promote-to-main.yml
   name: Promote to Production
   on:
     workflow_dispatch:

   jobs:
     promote:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
           with:
             ref: develop
         - name: Create PR to main
           run: gh pr create --base main --head develop --title "Promote to Production"
   ```

### For Low-Risk Projects

1. **Copy the renovate config:**
   ```bash
   cp devops/examples/nextjs/renovate-low-risk.json renovate.json
   ```

2. **Update YOUR_USERNAME** in renovate.json

3. **Copy the CI workflow:**
   ```bash
   cp devops/examples/nextjs/ci.yml .github/workflows/ci.yml
   ```

4. **Done!** Dependencies will automerge to main after CI passes.

---

## Switching Strategies

### From Low-Risk to High-Risk

1. Create develop branch:
   ```bash
   git checkout main
   git checkout -b develop
   git push -u origin develop
   ```

2. Update renovate.json:
   ```json
   {
     "baseBranches": ["develop"]
   }
   ```

3. Set develop as default branch in GitHub settings

### From High-Risk to Low-Risk

1. Update renovate.json:
   ```json
   {
     "baseBranches": ["main"]
   }
   ```

2. Optionally delete develop branch after merging all changes

---

## Monitoring & Rollback

### High-Risk Strategy

**Rollback:** Just don't promote to main
```bash
# If develop breaks, just fix it there
git checkout develop
git revert <bad-commit>
git push
```

**Production stays safe** until you manually promote.

### Low-Risk Strategy

**Rollback:** Revert main branch
```bash
git checkout main
git revert <bad-commit>
git push
```

**Important:** Need good monitoring to catch issues quickly!

Recommended monitoring:
- Error tracking (Sentry, Rollbar)
- Uptime monitoring (UptimeRobot, Better Uptime)
- Performance monitoring (New Relic, DataDog)
- Log aggregation (LogTail, Papertrail)

---

## FAQ

### Q: Can I use both strategies in the same repo?

**A:** Not recommended. Pick one strategy per repository. However, you can have different strategies for different repositories in your portfolio.

### Q: What if I'm unsure?

**A:** Default to **HIGH-RISK** (develop branch). It's safer. You can always switch to LOW-RISK later when you gain confidence.

### Q: Do I need a staging environment for HIGH-RISK?

**A:** Highly recommended but not required. At minimum, manually test in develop branch before promoting to main.

### Q: How do I handle major updates in LOW-RISK?

**A:** Major updates NEVER automerge (in both strategies). You'll always get a PR to review manually.

### Q: Can automerge break my production?

**A:**
- **HIGH-RISK:** No, automerge only affects develop. You control promotion to main.
- **LOW-RISK:** Potentially yes, but only patch/minor updates after CI passes. Major updates always require manual review.

### Q: What if CI is flaky?

**A:** Fix your CI first! Automerge requires reliable CI. With `requiredStatusChecks: null`, PRs won't merge until ALL checks pass.

---

## Summary

**Choose HIGH-RISK if:**
- Production application
- Has users or revenue
- Low tolerance for breakage
- Want staging layer

**Choose LOW-RISK if:**
- Internal tool
- Side project
- High test coverage
- Can tolerate brief issues

**When in doubt:** Start with HIGH-RISK, switch to LOW-RISK when confident.
