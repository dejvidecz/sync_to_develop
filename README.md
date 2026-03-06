# Sync Release to Develop

GitHub Action that automatically merges release branches into `develop`. Designed to keep `develop` up-to-date with regression fixes and hotfixes flowing through release branches.

## How it works

When triggered by a push to a release branch (e.g. `release_0220`), this action:

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

concurrency:
  group: sync-develop
  cancel-in-progress: false

jobs:
  sync-release-to-develop:
    runs-on: ubuntu-latest
    steps:
      - name: Sync release to develop
        uses: dejvidecz/sync_to_develop@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          teams_webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
          teams_mention_emails: ${{ secrets.TEAMS_MENTION_EMAILS }}
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `token` | Yes | — | GitHub token with push permissions |
| `teams_webhook_url` | No | `""` | Microsoft Teams webhook URL for failure notifications |
| `teams_mention_emails` | No | `""` | Comma-separated emails to @mention in Teams on failure |
| `release_branch_pattern` | No | `^release_[0-9]{4}$` | Regex pattern for valid release branch names |

## Secrets setup

| Secret | Required | Description |
|--------|----------|-------------|
| `TEAMS_WEBHOOK_URL` | No | Teams Incoming Webhook URL (channel → Workflows → "Send webhook alerts to channel") |
| `TEAMS_MENTION_EMAILS` | No | Comma-separated Teams emails, e.g. `novak@firma.cz,svoboda@firma.cz` |
