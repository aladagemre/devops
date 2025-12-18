# DevOps Configuration Repository

Central repository for reusable CI/CD configurations, Renovate dependency management, and GitHub Actions workflows across multiple projects.

## Philosophy

This repository implements a **noise-reduction, efficiency-first** approach to DevOps:

- **Fewer, Meaningful PRs**: Dependencies grouped intelligently, scheduled for weekends
- **Fast CI**: Aggressive caching, parallel jobs, skip unnecessary steps
- **Auto-merge Minor Updates**: Patch and minor updates merge automatically after CI passes
- **Manual Review for Major**: Breaking changes require human review

## Repository Structure

```
devops/
├── renovate/                    # Renovate configuration presets
│   ├── base.json               # Shared settings (scheduling, stability, limits)
│   ├── nextjs.json             # Next.js/React specific grouping
│   ├── python.json             # Django/FastAPI grouping
│   ├── python-package.json     # Python packages/libraries
│   ├── expressjs.json          # Express.js grouping
│   ├── rust.json               # Rust dependencies
│   └── go.json                 # Go modules
├── .github/workflows/           # Reusable GitHub Actions workflows
│   ├── nextjs-ci.yml
│   ├── python-django-ci.yml
│   ├── python-fastapi-ci.yml
│   ├── python-package-ci.yml   # Python packages/libraries
│   ├── python-package-publish.yml  # PyPI publishing
│   ├── expressjs-ci.yml
│   ├── rust-ci.yml
│   └── go-ci.yml
└── docs/                        # Documentation
    ├── renovate-usage.md
    └── workflows-usage.md
```

## Quick Start

### Using Renovate Configurations

In your project's `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "github>YOUR_USERNAME/devops//renovate/base",
    "github>YOUR_USERNAME/devops//renovate/nextjs"
  ]
}
```

### Using Reusable Workflows

In your project's `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  ci:
    uses: YOUR_USERNAME/devops/.github/workflows/nextjs-ci.yml@main
    with:
      node-version: '20'
```

## Supported Tech Stacks

- **TypeScript + Next.js/React** - Full build testing with clean install simulation
- **Python Django** - Django-specific testing and checks
- **Python FastAPI** - FastAPI testing with pytest
- **Python Packages** - Reusable libraries with multi-version testing and PyPI publishing
- **Express.js** - Node.js API testing
- **Rust** - Cargo build and test with caching
- **Go** - Go modules with build and test

## Key Features

### Renovate
- Weekend-only PR creation (Saturday mornings)
- 3-day stability period for new releases
- Smart dependency grouping (linting, types, testing, frameworks)
- Auto-merge patch/minor updates
- Manual review for major updates
- PR limits to prevent overwhelming the team

### GitHub Actions
- Aggressive caching (dependencies, build artifacts)
- Concurrency controls with cancel-in-progress
- Path-based ignoring (skip CI for docs-only changes)
- Conditional steps (only run if scripts exist)
- Parallel job execution
- Fresh install testing for Next.js projects

## Documentation

- [Renovate Usage Guide](docs/renovate-usage.md)
- [GitHub Workflows Guide](docs/workflows-usage.md)

## Contributing

This repository is designed to evolve with your team's needs. When updating:

1. Test changes in a single project first
2. Document any breaking changes
3. Update version references in dependent projects
4. Consider backward compatibility

## License

Internal use - adapt as needed for your projects.
