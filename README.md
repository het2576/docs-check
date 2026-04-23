# Driftless Docs Check — GitHub Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Driftless%20Docs%20Check-blue?logo=github)](https://github.com/marketplace/actions/driftless-docs-check)

**Automatically checks if your README is in sync with your code on every PR or push.**

Driftless analyzes your repository's actual source code — dependencies, routes, env vars, scripts — and detects when your documentation may have become outdated.

## Usage

```yaml
name: Docs Check
on:
  push:
    branches: [main]
  pull_request:

jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
      - uses: driftless/docs-check@v1
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
          driftless-api-key: ${{ secrets.DRIFTLESS_API_KEY }}
```

Add `DRIFTLESS_API_KEY` to your repo secrets from **[driftless.vercel.app/settings](https://driftless.vercel.app/settings)**.

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `repo-token` | ✅ | — | `${{ secrets.GITHUB_TOKEN }}` — used to post PR comments |
| `driftless-api-key` | ✅ | — | Your Driftless API key from `/settings` |
| `fail-on-drift` | ❌ | `false` | Set to `true` to fail CI when drift is detected |
| `drift-threshold` | ❌ | `70` | Score below which to warn (0–100) |
| `post-comment` | ❌ | `true` | Set to `false` to disable PR comments |

## Outputs

| Output | Description |
|--------|-------------|
| `drift-score` | Documentation drift score (0–100). 100 = fully in sync. |
| `status` | `in-sync` \| `minor-drift` \| `moderate-drift` \| `major-drift` |
| `summary` | Human-readable summary of what changed |

## Example: Strict Mode (fail CI on drift)

```yaml
- uses: driftless/docs-check@v1
  with:
    repo-token: ${{ secrets.GITHUB_TOKEN }}
    driftless-api-key: ${{ secrets.DRIFTLESS_API_KEY }}
    fail-on-drift: 'true'
    drift-threshold: '80'
```

## How It Works

1. On every push/PR, this action calls the Driftless API with your repo name
2. Driftless checks your current README against the latest codebase snapshot
3. A drift score is calculated (100 = in sync, lower = more drift)
4. If drift is detected below your threshold:
   - A warning is posted to the CI logs
   - A comment is posted on the PR (if it's a PR event)
   - Optionally fails the CI check (`fail-on-drift: true`)

## Getting Your API Key

1. Sign in at [driftless.vercel.app](https://driftless.vercel.app) with GitHub
2. Go to **Settings** → **API Key**
3. Copy your key (starts with `rd_`)
4. Add it to your GitHub repo secrets as `DRIFTLESS_API_KEY`

## License

MIT © [Driftless](https://driftless.vercel.app)
