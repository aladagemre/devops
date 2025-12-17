# DevOps Repository Checklist

Use this checklist when setting up the repository and deploying to projects.

## Repository Setup Checklist

### Initial Setup

- [ ] Push repository to GitHub
  ```bash
  cd /Users/emre/projects/devops
  git init
  git add .
  git commit -m "Initial commit: centralized DevOps configurations"
  git remote add origin git@github.com:YOUR_USERNAME/devops.git
  git push -u origin main
  ```

- [ ] Update all `YOUR_USERNAME` placeholders in documentation
  - [ ] README.md
  - [ ] GETTING_STARTED.md
  - [ ] QUICK_REFERENCE.md
  - [ ] docs/renovate-usage.md
  - [ ] docs/workflows-usage.md
  - [ ] All example files in examples/

- [ ] Verify repository is accessible
  - [ ] Public repository OR
  - [ ] Organization members have access

- [ ] Create initial release/tag (optional)
  ```bash
  git tag -a v1.0.0 -m "Initial release"
  git push origin v1.0.0
  ```

## Project Setup Checklist

Use this checklist for each project you migrate to the centralized configs.

### Pre-Migration

- [ ] Back up existing configurations
  ```bash
  mkdir -p ~/backups/project-name-$(date +%Y%m%d)
  cp -r .github ~/backups/project-name-$(date +%Y%m%d)/
  cp renovate.json ~/backups/project-name-$(date +%Y%m%d)/ 2>/dev/null || true
  ```

- [ ] Document current PR volume (for comparison)
- [ ] Document current CI duration
- [ ] Review existing Renovate/Dependabot config

### Setup

- [ ] Copy appropriate example configuration
  ```bash
  # For Next.js
  cp /path/to/devops/examples/nextjs/renovate.json ./
  mkdir -p .github/workflows
  cp /path/to/devops/examples/nextjs/ci.yml ./.github/workflows/
  ```

- [ ] Update `YOUR_USERNAME` in both files
  - [ ] renovate.json
  - [ ] .github/workflows/ci.yml

- [ ] Customize if needed
  - [ ] Adjust Node.js/Python/Rust/Go version
  - [ ] Modify paths-ignore patterns
  - [ ] Set working-directory (for monorepos)
  - [ ] Configure skip-* flags

- [ ] Commit changes
  ```bash
  git add renovate.json .github/workflows/ci.yml
  git commit -m "chore: migrate to centralized DevOps configs"
  ```

### GitHub Configuration

- [ ] Enable auto-merge
  - Navigate to: Settings → General → Pull Requests
  - Check "Allow auto-merge"
  - Check "Automatically delete head branches"

- [ ] Configure branch protection for main branch
  - Navigate to: Settings → Branches → Add rule
  - Branch name pattern: `main` (or your default branch)
  - Required settings:
    - [ ] "Require status checks to pass before merging"
    - [ ] Select "CI" (or your workflow name)
    - [ ] "Require branches to be up to date before merging"
    - [ ] "Require linear history" (optional but recommended)

- [ ] Install Renovate App (if not already installed)
  - Visit: https://github.com/apps/renovate
  - Click "Configure"
  - Select your repository
  - Grant permissions

### Testing

- [ ] Create test PR to verify CI works
  ```bash
  git checkout -b test-ci
  # Make a small change
  echo "# Test" >> README.md
  git add README.md
  git commit -m "test: verify CI workflow"
  git push -u origin test-ci
  ```

- [ ] Verify CI runs successfully
  - [ ] Check GitHub Actions tab
  - [ ] Verify all jobs complete
  - [ ] Check duration (should be fast after cache builds)

- [ ] Check Renovate Dashboard
  - [ ] Look for "Dependency Dashboard" issue
  - [ ] Verify dependencies detected
  - [ ] Check for configuration errors

- [ ] Wait for first Renovate PR (Saturday 9-11 AM)
  - [ ] Verify PR is created
  - [ ] Check grouping is correct
  - [ ] Verify CI runs on PR
  - [ ] Check if auto-merge triggers (for patch/minor)

### Post-Migration

- [ ] Monitor for 2 weeks
  - [ ] Track PR volume (should decrease 70-90%)
  - [ ] Monitor CI duration (should be 2-5x faster)
  - [ ] Check auto-merge success rate (target >80%)
  - [ ] Review any failed auto-merges

- [ ] Adjust if needed
  - [ ] Modify grouping rules
  - [ ] Adjust schedule
  - [ ] Pin problematic packages
  - [ ] Tune stability days

- [ ] Document project-specific customizations
  - [ ] Add comments to renovate.json
  - [ ] Update project README if needed

- [ ] Clean up old configurations (if satisfied)
  - [ ] Remove old .github/workflows/ci.yml (if different name)
  - [ ] Remove .github/dependabot.yml (if migrating from Dependabot)
  - [ ] Clean up backup directory

## Validation Checklist

### Repository Validation

- [ ] All JSON files are valid
  ```bash
  find . -name "*.json" -exec python3 -m json.tool {} \; > /dev/null
  ```

- [ ] All YAML files are valid
  ```bash
  yamllint .github/workflows/
  ```

- [ ] Documentation links work
  - [ ] README.md links
  - [ ] GETTING_STARTED.md links
  - [ ] Cross-references between docs

- [ ] Examples are up to date
  - [ ] Match current configuration structure
  - [ ] Include all required fields
  - [ ] Reference correct repository

### Project Validation

- [ ] renovate.json is valid
  ```bash
  cat renovate.json | python3 -m json.tool
  ```

- [ ] CI workflow is valid
  ```bash
  yamllint .github/workflows/ci.yml
  ```

- [ ] Branch protection is configured
- [ ] Auto-merge is enabled
- [ ] Renovate App is installed
- [ ] CI passes on a test PR

## Success Metrics Checklist

Track these metrics after 1 month:

### Quantitative Metrics

- [ ] PR volume reduced by 70-90%
  - Before: _____ PRs/week
  - After: _____ PRs/week
  - Reduction: _____%

- [ ] CI duration improved 2-5x
  - Before: _____ minutes
  - After: _____ minutes
  - Improvement: _____x

- [ ] Auto-merge success rate >80%
  - Total auto-merge attempts: _____
  - Successful auto-merges: _____
  - Success rate: _____%

### Qualitative Metrics

- [ ] Team reports reduced cognitive load
- [ ] Fewer production issues from dependencies
- [ ] Security updates applied faster
- [ ] Consistent CI across projects
- [ ] Easier to onboard new projects

## Troubleshooting Checklist

### Renovate Issues

If Renovate isn't working:

- [ ] Check Dependency Dashboard for errors
- [ ] Validate renovate.json syntax
- [ ] Verify Renovate App is installed
- [ ] Check repository permissions
- [ ] Review Renovate logs in dashboard
- [ ] Verify extends references are correct
- [ ] Check if schedule has been reached

### CI Issues

If CI isn't running:

- [ ] Validate workflow YAML syntax
- [ ] Check workflow file location (.github/workflows/)
- [ ] Verify repository access to devops repo
- [ ] Check branch reference (@main)
- [ ] Review GitHub Actions logs
- [ ] Verify inputs are correct
- [ ] Check if paths-ignore is too aggressive

### Auto-Merge Issues

If auto-merge isn't working:

- [ ] Verify "Allow auto-merge" is enabled
- [ ] Check branch protection rules
- [ ] Verify CI checks are required
- [ ] Confirm update is patch/minor (not major)
- [ ] Check package is not 0.x.x
- [ ] Verify CI is passing
- [ ] Review Renovate configuration
- [ ] Check for conflicting branch protection rules

## Rollback Checklist

If you need to rollback:

- [ ] Restore from backup
  ```bash
  cp -r ~/backups/project-name-DATE/.github ./
  cp ~/backups/project-name-DATE/renovate.json ./
  ```

- [ ] Commit rollback
  ```bash
  git add .github renovate.json
  git commit -m "chore: rollback to previous DevOps configuration"
  git push
  ```

- [ ] Document issues encountered
- [ ] Report issues to devops repository
- [ ] Wait for fixes before retrying

## Maintenance Checklist

### Monthly

- [ ] Review auto-merged PRs
- [ ] Check for security updates
- [ ] Verify CI performance
- [ ] Review Dependency Dashboard

### Quarterly

- [ ] Update workflow versions
- [ ] Review and optimize grouping
- [ ] Check for new best practices
- [ ] Update documentation
- [ ] Review success metrics

### Annually

- [ ] Major version updates review
- [ ] Tech stack updates
- [ ] Configuration cleanup
- [ ] Documentation refresh

## Communication Checklist

### Before Migration

- [ ] Share GETTING_STARTED.md with team
- [ ] Explain benefits (fewer PRs, faster CI)
- [ ] Set expectations (auto-merge, weekend PRs)
- [ ] Answer questions

### During Migration

- [ ] Notify team of changes
- [ ] Share Dependency Dashboard link
- [ ] Explain how to customize
- [ ] Provide support channel

### After Migration

- [ ] Share success metrics
- [ ] Gather feedback
- [ ] Document lessons learned
- [ ] Plan additional rollouts

## Quick Start Checklist (5 Minutes)

Minimal steps to get started:

1. [ ] Copy example config for your tech stack
2. [ ] Replace `YOUR_USERNAME`
3. [ ] Commit and push
4. [ ] Enable auto-merge in settings
5. [ ] Add branch protection rule
6. [ ] Install Renovate App
7. [ ] Done!

## Documentation Update Checklist

When updating the devops repository:

- [ ] Update relevant configuration files
- [ ] Test in a sample project
- [ ] Update documentation
  - [ ] README.md
  - [ ] GETTING_STARTED.md
  - [ ] Relevant guides in docs/
  - [ ] IMPLEMENTATION_NOTES.md
  - [ ] QUICK_REFERENCE.md

- [ ] Update examples if needed
- [ ] Update version number (if using tags)
- [ ] Commit with clear message
- [ ] Notify teams using the configs

## Final Verification

Before marking complete:

- [ ] Repository pushed to GitHub
- [ ] All placeholders updated
- [ ] Documentation accurate
- [ ] Examples work
- [ ] JSON/YAML validated
- [ ] At least one project successfully migrated
- [ ] Auto-merge working
- [ ] CI running faster
- [ ] PR volume reduced
- [ ] Team is happy!

---

**Last Updated:** 2025-12-13
**Status:** Ready for deployment
