# syntax=docker/dockerfile:1.7
# helios-plat/python-worker-base
# For background workers: asyncpg + aio-pika + redis, NO web stack.
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

CMD ["python", "-c", "import asyncpg, aio_pika, redis, pydantic; print(f'worker-base OK: asyncpg={asyncpg.__version__} aio-pika={aio_pika.__version__} redis={redis.__version__}')"]
