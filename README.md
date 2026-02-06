# Setup Makeself Action

A GitHub Action to set up [makeself](https://github.com/megastep/makeself) - a tool to create self-extracting archives on Unix.

## Features

- Installs makeself.sh and adds it to PATH
- Supports specifying a version or using latest
- Works on Linux and macOS runners
- Outputs installed version and path for downstream use

## Usage

### Basic Usage

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup Makeself
    uses: henry-hsieh/setup-makeself@v1
  
  - name: Create self-extracting archive
    run: |
      makeself.sh ./my-app ./installer.run "My Application Installer" ./install.sh
```

### Specify Version

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup Makeself
    uses: henry-hsieh/setup-makeself@v1
    with:
      version: '2.5.0'
```

### Use Outputs

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup Makeself
    id: makeself
    uses: henry-hsieh/setup-makeself@v1
  
  - name: Show version
    run: |
      echo "Installed version: ${{ steps.makeself.outputs.version }}"
      echo "Makeself path: ${{ steps.makeself.outputs.makeself-path }}"
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `version` | Version of makeself to install (e.g., `2.7.1`). Use `latest` for most recent. | No | `latest` |

## Outputs

| Output | Description |
|--------|-------------|
| `version` | The installed version of makeself |
| `makeself-path` | Full path to the makeself.sh script |

## Complete Example

Here's a complete workflow that builds an installer:

```yaml
name: Build Installer

on:
  push:
    branches: [main]
  release:
    types: [created]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Makeself
        uses: henry-hsieh/setup-makeself@v1
      
      - name: Prepare application
        run: |
          mkdir -p dist
          cp -r src/* dist/
          cp install.sh dist/
      
      - name: Create installer
        run: |
          makeself.sh --gzip dist/ myapp-installer.run "MyApp Installer" ./install.sh
      
      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: installer
          path: myapp-installer.run
```

## Makeself Options

Once installed, you can use all makeself.sh options. Common ones include:

| Option | Description |
|--------|-------------|
| `--gzip` | Use gzip compression (default) |
| `--bzip2` | Use bzip2 compression |
| `--xz` | Use xz compression |
| `--zstd` | Use zstd compression |
| `--notemp` | Extract in place instead of temp directory |
| `--current` | Extract to current directory |
| `--follow` | Follow symlinks |
| `--nox11` | Disable X11 GUI |

See the [makeself documentation](https://github.com/megastep/makeself#usage) for full details.

## License

MIT License - see [LICENSE](LICENSE) file.
