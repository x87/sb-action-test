# SannyBuilder GitHub Action

A GitHub Action that downloads SannyBuilder and compiles script files on Windows runners.

## Features

- Downloads SannyBuilder from official releases
- Compiles script files using `sanny.exe`
- Checks for compilation errors via `compile.log`
- Uploads successful compilation output as artifacts

## Usage

### As a reusable action

```yaml
name: Compile Script

on:
  push:
    branches: [ main ]

jobs:
  compile:
    runs-on: windows-latest
    
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        
      - name: Compile with SannyBuilder
        uses: x87/sb-action-test@main
        with:
          input-file: 'path/to/your/script.txt'
          output-file: 'output.scm'
          sanny-version: 'v4.2.0'  # optional, defaults to v4.2.0
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `input-file` | Path to the input script file to compile | Yes | - |
| `output-file` | Path to the output compiled file | No | `output.scm` |
| `sanny-version` | SannyBuilder version to download | No | `v4.2.0` |

## Outputs

| Output | Description |
|--------|-------------|
| `success` | Whether compilation was successful (true/false) |
| `error-log` | Content of error log if compilation failed |

## How it works

1. Downloads SannyBuilder from `https://github.com/sannybuilder/dev/releases/download/{version}/SannyBuilder-{version}.zip`
2. Unpacks the archive
3. Runs `sanny.exe --compile <input-file> <output-file>`
4. Checks if `compile.log` exists and is not empty (indicates error)
5. If successful, uploads the output file as an artifact
6. If failed, provides error log content in outputs

## Example with error handling

```yaml
jobs:
  compile:
    runs-on: windows-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Compile Script
        id: compile
        uses: x87/sb-action-test@main
        with:
          input-file: 'script.txt'
          output-file: 'compiled.scm'
        continue-on-error: true
        
      - name: Handle compilation failure
        if: steps.compile.outputs.success == 'false'
        run: |
          echo "Compilation failed!"
          echo "Error log: ${{ steps.compile.outputs.error-log }}"
```

## Requirements

- Must run on a Windows runner (`runs-on: windows-latest`)
- Input file must exist in the repository or be created before compilation