# Rust Multi-Platform Release Action

A GitHub Action that automates creating releases and building multi-platform binaries for Rust projects.

## Features

- Automatically detects version changes in `Cargo.toml`
- Creates GitHub releases with configurable tags and names
- Builds binaries for multiple platforms:
  - Linux (x86_64, ARM64, i686, ARMv7)
  - Windows (x86_64, i686, ARM64)
  - macOS (x86_64, ARM64)
  - WebAssembly
- Supports both dynamic and static linking (musl)
- Generates SHA256 checksums for all binaries
- Handles cross-compilation with proper dependencies

## Usage

```yaml
name: Release

on:
  push:
    branches:
      - main  # or master, depending on your default branch

jobs:
  release:
    name: Create Release
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v3
        with:
          fetch-depth: 2

      - name: Create Release
        uses: tristanpoland/rust-multi-platform-release@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
```

## How It Works

1. The action checks if the version in `Cargo.toml` has changed compared to the previous commit
2. If the version has changed, it creates a new GitHub release
3. It then builds binaries for all specified target platforms
4. For each binary, it generates a SHA256 checksum file
5. Finally, it uploads all files to the GitHub release

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `token` | GitHub token | Yes | `${{ github.token }}` |
| `release_name` | Format for release name | No | `Release v{version}` |
| `tag_name` | Format for tag name | No | `v{version}` |
| `draft` | Create release as draft | No | `false` |
| `prerelease` | Create release as prerelease | No | `false` |
| `targets` | Comma-separated list of targets to build | No | *All supported targets* |

## Supported Targets

The following targets are supported by default:

- `x86_64-unknown-linux-gnu`
- `x86_64-unknown-linux-musl` (static)
- `aarch64-unknown-linux-gnu`
- `aarch64-unknown-linux-musl` (static)
- `i686-unknown-linux-gnu`
- `i686-unknown-linux-musl` (static)
- `armv7-unknown-linux-gnueabihf`
- `armv7-unknown-linux-musleabihf` (static)
- `x86_64-pc-windows-msvc`
- `i686-pc-windows-msvc`
- `aarch64-pc-windows-msvc`
- `x86_64-apple-darwin`
- `aarch64-apple-darwin`
- `wasm32-unknown-unknown`

## Asset Naming Convention

The action uses the following naming convention for the assets:

- Linux: `{package_name}-linux-{arch}[-static]`
- Windows: `{package_name}-windows-{arch}.exe`
- macOS: `{package_name}-macos-{arch}`
- WebAssembly: `{package_name}.wasm`

Where:
- `{package_name}` is the name from `Cargo.toml`
- `{arch}` is one of: `x86_64`, `arm64`, `i686`, `armv7`
- `-static` suffix is added for musl targets

## License

MIT
