# Changelog

All notable changes to this project will be documented in this file.

## Attributors

- **Bert Berrevoets** — Project author
- **Claude Code** — AI-assisted development

## [1.1.0] - 2026-03-25

### Fixed

Author: *Claude Code*

- **PlatformIO penv bootstrap failure** — `esphome compile` and `esphome flash`
  failed with "Failed to install Python dependencies into penv" because:
  - `uv` package manager was missing (PlatformIO's bootstrapper requires it)
  - Alpine musl wheel index (`UV_EXTRA_INDEX_URL`) was not configured, causing
    pip to compile packages from source (which fails without full build deps)
  - PlatformIO was not pre-configured during Docker build, forcing first-run
    setup at compile time inside the running container

### Changed

- Dockerfile now matches ESPHome's official Docker image build approach:
  - Installs `uv==0.10.1` (same version as ESPHome official)
  - Sets `UV_EXTRA_INDEX_URL` to HA's musl wheel index for pre-compiled packages
  - Uses `build-base` meta-package instead of individual compiler packages
  - Pre-runs `platformio settings set` during build
  - Adds `curl`, `bash`, `patch`, `libmagic`, `cairo` (matching docker-base)
- Uses `uv pip install` instead of plain `pip3 install` for faster, reliable installs

## [1.0.0] - 2026-03-17

### Added

Author: *Bert Berrevoets, Claude Code*

- Initial release as Home Assistant add-on
- FastMCP server with streamable HTTP transport on port 8099
- Bearer token authentication (auto-generated or user-configured)
- Nine MCP tools: list_devices, validate, compile, flash, logs,
  push_files, pull_files, push_fonts, pull_fonts
- Direct filesystem access to `/config/esphome/` — no SSH required
- Alpine-based Docker image with ESPHome and PlatformIO pre-installed
- Multi-architecture support (aarch64, amd64)
- Add-on documentation (DOCS.md)
- secrets.yaml protection in push/pull operations
