# Workflows
github reusable workflow

# Available workflows
## [sync-fork.yml](https://github.com/tenfyzhong/workflows/tree/main/.github/workflows/sync-fork.yml)
This workflow sync fork from the upstream. You can setup a schedule to sync automatically.
### Usage
```yaml
name: sync-fork
on:
  schedule:
    - cron: '0 0 * * *'
  workflow_dispatch: { }
jobs:
  sync:
    uses: tenfyzhong/workflows/.github/workflows/sync-fork.yml@main
```
