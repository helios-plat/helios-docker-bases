# Changelog

All notable changes to helios-docker-bases are documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), SemVer.

## [Unreleased]

## [0.1.0] — 2026-04-24

### Added
- Initial 5-layer base image stack:
  - `python-os` (Python 3.14 + uv + tzdata + non-root user)
  - `python-api-base` (FastAPI + asyncpg)
  - `python-worker-base` (aio-pika + redis + asyncpg)
  - `python-ml-base` (numpy + pandas<3.0 + sklearn + lightgbm)
  - `python-quant-base` (nautilus-trader)
- Multi-arch builds: linux/amd64 + linux/arm64
- GitHub Actions workflow with GHA cache
- SemVer + rolling tag (`:0.1.0`, `:0.1`, `:latest`)

### Constraints established
- `pandas<3.0` pinned at `python-ml-base` (required by nautilus-trader)
- Non-root user `app` (uid=1000) in `python-os`
- Timezone `Asia/Shanghai` set in `python-os`
