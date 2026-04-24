# syntax=docker/dockerfile:1.7
# helios-plat/python-ml-base
# For ML/quant research workers: numpy + pandas (pinned <3.0 for nautilus compat) + sklearn + lightgbm.
# Inherits: python-os (NOT worker-base — ML services may expose HTTP)
#
# CRITICAL: pandas<3.0 is pinned at THIS layer because nautilus-trader (which inherits via quant-base)
# requires pandas<3.0 as of 2026-04. Upgrading will break quant-base silently.

ARG BASE_TAG=latest
FROM ghcr.io/helios-plat/helios/python-os:${BASE_TAG}

USER root

# ML libs need build tooling for a few wheels on arm64; install temporarily then purge
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        libgomp1 \
    && rm -rf /var/lib/apt/lists/*

COPY --chown=app:app requirements.txt /tmp/requirements.txt
RUN uv pip install --system --no-cache -r /tmp/requirements.txt && rm /tmp/requirements.txt

# Strip build tools after install (libgomp1 stays — runtime dep for lightgbm)
RUN apt-get purge -y --auto-remove build-essential \
    && rm -rf /var/lib/apt/lists/*

USER app

CMD ["python", "-c", "import numpy, pandas, sklearn, lightgbm; print(f'ml-base OK: numpy={numpy.__version__} pandas={pandas.__version__} sklearn={sklearn.__version__} lightgbm={lightgbm.__version__}')"]
