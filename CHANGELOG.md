# Changelog

All notable changes to this project will be documented in this file.

## Attributors

- **Bert Berrevoets** — Project author
- **Claude Code** — AI-assisted development

## [1.2.3] - 2026-03-25

### Fixed

Author: *Claude Code*

- **list_devices fails on configs with !lambda** -- YAML parser only handled
  the !secret tag; configs using !lambda, !include, or other ESPHome tags caused
  a YAML parse error. Replaced single-tag handler with multi-constructor that
  passes through all ESPHome custom YAML tags.

## [1.2.2] - 2026-03-25

### Fixed

Author: *Claude Code*

- **ClosedResourceError log noise** — MCP SDK's stateless HTTP session handler
  crashes with `anyio.ClosedResourceError` when the client disconnects before
  the server finishes sending log notifications. Added ASGI middleware to catch
  this at the boundary and suppress the "[ERROR] Stateless session crashed"
  tracebacks. The error was non-fatal (server continued working) but produced
  confusing log output.

## [1.2.1] - 2026-03-25

### Fixed

Author: *Claude Code*

- **Compile timeout too short** — First compile downloads ~1GB of toolchains
  and takes 500-600s. Increased compile timeout from 300s to 900s.

## [1.2.0] - 2026-03-25

### Changed

Author: *Claude Code*

- **Rebased on ESPHome official image** — Changed base from Alpine Python to
  `ghcr.io/esphome/esphome-hassio:2026.3.1`. This provides all ESPHome
  dependencies, PlatformIO, and native toolchains (xtensa, ARM) pre-installed.
  Eliminates glibc/musl incompatibility that caused cmake ENOENT and 44GB mmap
  failures when PlatformIO tried to use glibc-linked binaries on Alpine.
- Dockerfile simplified to only install MCP server Python deps on top of base image.

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
