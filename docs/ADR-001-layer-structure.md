# ADR-001: 5-Layer Base Image Structure

Date: 2026-04-24
Status: Accepted

## Context

Helios platform has 4 projects (Helios / Helixa / Tide / Selene) each with
multiple services (FastAPI web, RabbitMQ workers, ML/quant workers). Each
service currently writes its own Dockerfile `FROM python:3.14-slim`, leading
to: version drift, duplicate apt-get blocks, forgotten non-root user, and
scattered pandas version pinning.

## Decision

Build 5 base images in an inheritance tree:

```
python-os → { python-api-base, python-worker-base, python-ml-base }
             └── python-ml-base → python-quant-base
```

**Layer rationale:**

- `python-os` owns OS-level concerns only (tzdata, certs, non-root user, uv).
  Upgrading Python = change one line here.
- `python-api-base` vs `python-worker-base` split: web stack (fastapi/uvicorn)
  is 30MB+ and unnecessary for pure workers. Workers need aio-pika/redis which
  web services don't. Separate images keep each minimal.
- `python-ml-base` inherits `python-os` (not `worker-base`): ML services
  sometimes need to expose HTTP (inference API), sometimes not. Starting
  fresh from `python-os` means they add exactly what they need.
- `python-quant-base` inherits `python-ml-base`: nautilus-trader needs pandas
  and numpy which are already in ml-base. Keeps it a thin layer adding only
  nautilus.

## Consequences

- 4 Helios projects `FROM` the appropriate image, no more `python:3.14-slim`
  directly.
- Cross-project upgrades are a `v0.1.x → v0.2.x` tag bump per project.
- One source of truth for pandas<3.0 pin, timezone, non-root user.
- Cost: slower CI on first build (~15 min for 5 layers × 2 arches). GHA
  cache amortizes subsequent runs.

## Alternatives considered

- **Single monolithic image**: rejected. Workers would carry FastAPI they never use.
- **Per-project bases**: rejected. Defeats the purpose of platform-level reuse.
- **Inherit ml-base → worker-base**: rejected. Workers don't need numpy/pandas.
