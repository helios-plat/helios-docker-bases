# syntax=docker/dockerfile:1.7
# helios-plat/python-os
# Base layer: OS-level dependencies, non-root user, timezone, uv binary.
# Upstream: python:3.14-slim-trixie (Debian trixie, 41MB)

ARG UV_VERSION=0.5.11
ARG PYTHON_IMAGE=python:3.14-slim-trixie

FROM ${PYTHON_IMAGE} AS base

ENV DEBIAN_FRONTEND=noninteractive \
    TZ=Asia/Shanghai \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    UV_COMPILE_BYTECODE=1 \
    UV_LINK_MODE=copy \
    UV_SYSTEM_PYTHON=1

# OS packages: minimum for building most Python wheels + ops tools
RUN apt-get update && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        tini \
        tzdata \
        libpq5 \
        && ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime \
        && echo ${TZ} > /etc/timezone \
        && rm -rf /var/lib/apt/lists/*

# Install uv binary from official distroless image (pinned version)
COPY --from=ghcr.io/astral-sh/uv:0.5.11 /uv /uvx /usr/local/bin/

# Non-root user (uid=1000, gid=1000)
RUN groupadd --system --gid 1000 app \
    && useradd --system --uid 1000 --gid app --create-home --shell /bin/bash app

WORKDIR /app
USER app

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["python", "-c", "print('helios python-os base ready')"]
