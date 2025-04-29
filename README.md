# Table of Contents
- [Workflows](#workflows)
- [Available workflows](#available-workflows)
  - [sync-fork](#sync-fork)
    - [Usage](#usage)
  - [go-build-test](#go-build-test)
    - [Usage](#usage)
    - [Inputs](#inputs)
  - [release-go](#release-go)
    - [Usage](#usage)
    - [Inputs](#inputs)
  - [fishtape](#fishtape)
    - [Usage](#usage)
    - [Inputs](#inputs)
  - [bats](#bats)
    - [Usage](#usage)
    - [Inputs](#inputs)
  - [vader](#vader)
    - [Usage](#usage)
    - [Inputs](#inputs)

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

## go-build-test
This workflow build and test go codes.
### Usage
```yaml
name: go
on:
  push:
  pull_request_target:
    branches: [ "main" ]

jobs:
  build-test:
    strategy:
      matrix:
        go-version: ['1.18', '1.19', '1.20', '1.21.x']
    uses: tenfyzhong/workflows/.github/workflows/go-build-test.yml@main
    with:
      go-version: ${{matrix.go-version}}
      path: "."
    secrets:
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
```

### Inputs
- `go-version`: The go version to run.
- `path`: The path to build and test.

### secrets
- `CODECOV_TOKEN`: get the token from codecov, and set it to github action secrets, and the call the workflow with the token

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
      goos: '["darwin"]'
      goarch: '["amd64", "arm64"]'

```

### Inputs
- `go-version`: The go version which to build go source code.
- `bin-path`: The bin-path relative to the project root.
- `output`: The directory to build bin files, default `output`.
- `command`: Command name to build.
- `build-option`: Option passed to `go build`.
- `goos`: The target os to build.
- `goarch`: The arget arch to build.

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

### Inputs
- `test-glob`: The fish test file glob.
- `dependencies`: Package dependencies which use `apt` to install.

## bats
This workflow run bash script test.
### Usage
Example:
```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    name: test
    uses: tenfyzhong/workflows/.github/workflows/bats.yml@main
    with:
      test-glob: "tests/*.bats"
```

### Inputs
- `test-glob`: The bats test file glob.
- `dependencies`: Package dependencies which use `apt` to install.

## vader
This workflow run vim plugin test with [vader](https://github.com/junegunn/vader.vim)
### Usage
Example:
```yaml
name: test

on: [push, pull_request]

jobs:
  test:
    name: test
    uses: tenfyzhong/workflows/.github/workflows/vader.yml@main
    with:
      test-pattern: 'vader/*.vader'
```

### Inputs
- `plugins`: A colon-separated list of plugins to install.
- `test-pattern`: A glob which points to you vader test files.
