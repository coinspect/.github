# Shared Workflows

Reusable GitHub Actions workflows for Coinspect organization repositories.

## Workflows

### `org-policy.yml`

General policy workflow. Detects the project type and runs the appropriate policy checks.

Repos opt in by calling this workflow:

```yaml
# .github/workflows/org-policy.yml
name: org-policy

on:
  pull_request:
  push:
    branches: [main]

jobs:
  policy:
    uses: coinspect/.github/.github/workflows/org-policy.yml@main
```

Add a status badge to the repo README:

```markdown
![Org Policy](https://github.com/coinspect/REPO_NAME/actions/workflows/org-policy.yml/badge.svg)
```

---

### `npm-policy.yml`

Node.js supply chain policy. Called automatically by `org-policy.yml` for Node projects.

Checks:

- npm >= 11.10.0
- `.npmrc` contains `min-release-age=14`
- `.npmrc` contains `allow-git=none`
- `.npmrc` contains `engine-strict=true`
- `package.json` declares `engines.npm`
- Dependencies install cleanly via `npm ci`

Requires a `.npmrc` in the repo root:

```ini
min-release-age=14
allow-git=none
engine-strict=true
```

Requires `package.json` to declare the npm version requirement:

```json
{
  "engines": {
    "npm": ">=11.10.0"
  }
}
```

`engine-strict=true` combined with `engines.npm` causes npm to hard-error locally if a developer's npm version does not satisfy the declared requirement.

The workflow sets `NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}` at the job level to allow `npm ci` to resolve packages from private GitHub npm registries. If the repo uses only public packages this has no effect, but it is required for any repo that pulls from `npm.pkg.github.com`.
