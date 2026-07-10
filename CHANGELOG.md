# Changelog

All notable changes to helios-docker-bases are documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), SemVer.

## [Unreleased]

## [0.3.8] — 2026-07-10

### Fixed
- `python-api-base`: bump oprim `v3.10.31` → `v3.19.1`. The prior 0.3.7 commit ("bump oprim v3.10.31 (facade fix)") pinned the WRONG version — v3.10.31 never had the aggregate facades `oprim.signal_analysis` / `data_fetch` / `correlation_options` (2.x→3.x refactor gap). This left the baked oprim incompatible with the baked `oskill@v4.0.0`, which imports `oprim.signal_analysis` — so `from oprim.signal_analysis import ...` (oskill.signal_fusion_skills, downstream helios analytics/collectors) raised ModuleNotFoundError, silently 500-ing the counterfactual/factor-attribution endpoints. oprim v3.19.1 restores those 3 facades (oprim main commit e498f84). Downstream services bump their `FROM python-api-base` tag to 0.3.8 to pick this up.

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
