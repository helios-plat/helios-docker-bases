# helios-docker-bases

Unified Docker base images for the Helios platform.

## Layers

| Image | Inherits | Purpose |
|---|---|---|
| `python-os` | `python:3.14-slim-trixie` | OS deps, non-root user, tzdata, uv binary |
| `python-api-base` | `python-os` | FastAPI + asyncpg + uvicorn |
| `python-worker-base` | `python-os` | asyncpg + aio-pika + redis (no web stack) |
| `python-ml-base` | `python-os` | numpy + **pandas<3.0** + sklearn + lightgbm |
| `python-quant-base` | `python-ml-base` | nautilus-trader |

### Inheritance tree

```
python:3.14-slim-trixie
    └── python-os
            ├── python-api-base
            ├── python-worker-base
            └── python-ml-base
                    └── python-quant-base
```

## Usage in downstream services

Pin to an exact version in your Dockerfile:

```dockerfile
FROM ghcr.io/helios-plat/helios/python-api-base:0.1.0
```

Do NOT use `:latest` in production. Use it only for local experiments.

## Versioning

SemVer. Git tag `v0.1.0` triggers build that tags GHCR with:

- `0.1.0` (immutable, pin this in production)
- `0.1` (rolling minor)
- `latest` (rolling)

## Key constraints

- **pandas<3.0**: pinned at `python-ml-base` layer. Required by `nautilus-trader` as of 2026-04. Do not lift without compat testing.
- **Python 3.14**: upstream `python:3.14-slim-trixie`. All downstream services inherit this Python version.
- **Non-root user `app` (uid=1000)**: all containers run as this user by default.
- **Timezone `Asia/Shanghai`**: set in `python-os`. Override via `TZ` env if a service needs UTC.

## Local development

Cannot build locally for publish — only GitHub Actions has push permission to GHCR.

For local test build:

```bash
docker build -t helios/python-os:dev ./images/python-os
docker build -t helios/python-api-base:dev --build-arg BASE_TAG=dev ./images/python-api-base
```

## Release

```bash
git tag v0.1.0
git push origin v0.1.0
```

Watch Actions: https://github.com/helios-plat/helios-docker-bases/actions
