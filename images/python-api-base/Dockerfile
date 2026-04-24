# syntax=docker/dockerfile:1.7
# helios-plat/python-api-base
# For FastAPI services: web stack + asyncpg + uvicorn.
# Inherits: python-os

ARG BASE_TAG=latest
FROM ghcr.io/helios-plat/helios/python-os:${BASE_TAG}

USER root

# uvloop 0.21 has no Python 3.14 wheel — build from source, then purge tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY --chown=app:app requirements.txt /tmp/requirements.txt
RUN uv pip install --system --no-cache -r /tmp/requirements.txt && rm /tmp/requirements.txt

RUN apt-get purge -y --auto-remove build-essential \
    && rm -rf /var/lib/apt/lists/*

USER app

# Healthcheck assumes service exposes GET / on 8000 (override per service)
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD curl -fsS http://localhost:8000/health || exit 1

CMD ["python", "-c", "import fastapi, asyncpg, uvicorn, pydantic; print(f'api-base OK: fastapi={fastapi.__version__} asyncpg={asyncpg.__version__} pydantic={pydantic.VERSION}')"]
