# Sync Release to Develop

Reusable GitHub workflow that automatically merges release branches into `develop`. Designed to keep `develop` up-to-date with regression fixes and hotfixes flowing through release branches.

## How it works

When triggered by a push to a release branch (e.g. `release_0220`, `release_20260220`), this workflow:

1. Validates the branch name matches the release pattern (ignores branches like `release_0220_hotfix`)
2. Merges the release branch into `develop`
3. On failure — sends a notification to Microsoft Teams (optional)

## Usage

```yaml
name: "Synchronize release branches into develop"

on:
  push:
    branches:
      - 'release_[0-9][0-9][0-9][0-9]'
      - 'release_[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'
      - 'release-[0-9][0-9][0-9][0-9]'
      - 'release-[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'

concurrency:
  group: sync-develop
  cancel-in-progress: false

jobs:
  sync:
    uses: dejvidecz/sync_to_develop/.github/workflows/sync.yml@v1
    secrets: inherit
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `release_branch_pattern` | No | `^release[-_]([0-9]{4}\|[0-9]{8})$` | Regex pattern for valid release branch names |

## Secrets (via `secrets: inherit`)

| Secret | Required | Description |
|--------|----------|-------------|
| `SYNC_TEAMS_WEBHOOK_URL` | No | Teams Incoming Webhook URL for failure notifications |
| `SYNC_TEAMS_MENTION_EMAILS` | No | Comma-separated Teams emails to @mention, e.g. `novak@firma.cz,svoboda@firma.cz` |
