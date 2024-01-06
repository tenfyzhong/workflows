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

## [sync-fork.yml](https://github.com/tenfyzhong/workflows/tree/main/.github/workflows/release-go.yml)
This workflow create release for go tools when push a tag.
### Usage
Example: 
```yaml
jobs:
  tag:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.tag.outputs.version }}
    steps:
      - name: Get the version
        id: tag
        run: echo "version=$(echo $GITHUB_REF | cut -d / -f 3)" >> $GITHUB_OUTPUT
      - name: Echo tag
        run: echo ${{ steps.tag.outputs.version }}
  release:
    uses: tenfyzhong/workflows/.github/workflows/release-go.yml@main
    needs: tag
    name: release
    with:
      bin-path: "./cmd/st2"
      command: "st2"
      build-option: "-ldflags \"-X 'github.com/tenfyzhong/st2/cmd/st2/config.Version=${{ needs.tag.outputs.version }}'\""

``````

### inputs
- `go-version`: The go version which to build go source code.
- `bin-path`: The bin-path relative to the project root.
- `output`: The directory to build bin files, default `output`.
- `command`: command name to build.
- `build-option`: option passed to `go build`.
