<!--ts-->
* [Workflows](#workflows)
* [Available workflows](#available-workflows)
   * [sync-fork](#sync-fork)
      * [Usage](#usage)
   * [release-go](#release-go)
      * [Usage](#usage-1)
      * [inputs](#inputs)
   * [fishtape](#fishtape)
      * [Usage](#usage-2)
      * [inputs](#inputs-1)
<!--te-->

# Workflows
github reusable workflow

# Available workflows
## sync-fork
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

## release-go
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

```

### inputs
- `go-version`: The go version which to build go source code.
- `bin-path`: The bin-path relative to the project root.
- `output`: The directory to build bin files, default `output`.
- `command`: command name to build.
- `build-option`: option passed to `go build`.

## fishtape
This workflow run [fishtape](https://github.com/jorgebucaran/fishtape) test.
### Usage
Example:
```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    name: test
    uses: tenfyzhong/workflows/.github/workflows/fishtape.yml@main
    with:
      test-glob: "tests/*.fish"
      dependencies: "curl pigz pbzip2 xz-utils lzma zstd lzip lz4 lrzip 7zip bzip2 lrzip cpio rar unrar zpaq"
```

### inputs
- `test-glob`: The fish test file glob.
- `dependencies`: Package dependencies which use `apt` to install. 
