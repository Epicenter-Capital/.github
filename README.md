# Epicenter shared CI workflows

Central home for reusable GitHub Actions workflows used across Epicenter service repos.
This repo is **public** so that repos under both `Epicenter-Capital` and personal accounts
can reference the workflows — keep it free of secrets and anything company-specific.

## python-ci.yml

Lint (ruff) + test (pytest) for Python services. Call it from a repo like this:

```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
  push:
    branches: [main]
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: ${{ github.ref != 'refs/heads/main' }}
jobs:
  ci:
    uses: Epicenter-Capital/.github/.github/workflows/python-ci.yml@v1
    with:
      python-version: "3.13"
      install: uv        # "uv" (uv.lock) or "pip" (requirements.txt)
      lint: true          # runs `ruff check .`
      # pytest-args: -q -m "not integration"   # the default
```

Conventions:

- Tests that hit the network or need credentials are marked
  `@pytest.mark.integration`; CI runs `pytest -m "not integration"`.
- Installs are frozen (`uv sync --frozen`) so a stale lockfile fails loudly.
- Pin callers to the `v1` tag; breaking changes get a new major tag.
