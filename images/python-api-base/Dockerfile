# syntax=docker/dockerfile:1.7
# helios-plat/python-api-base:0.3.4
# For FastAPI services: web stack + asyncpg + uvicorn + oprim/oskill/omodul.
# Inherits: python-os

ARG BASE_TAG=latest
FROM ghcr.io/helios-plat/helios/python-os:${BASE_TAG}

USER root

# Build tools for uvloop + scipy/numpy wheels
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential git \
    && rm -rf /var/lib/apt/lists/*

# Install requirements (oprim/oskill/omodul from private GitHub repos).
# PAT is delivered via BuildKit secret (id=gh_token) — never baked into image history.
COPY --chown=app:app requirements.txt /tmp/requirements.txt
RUN --mount=type=secret,id=gh_token \
    set -eu; \
    GH_TOKEN="$(cat /run/secrets/gh_token 2>/dev/null || true)"; \
    if [ -n "$GH_TOKEN" ]; then \
        git config --global url."https://${GH_TOKEN}@github.com/".insteadOf "https://github.com/"; \
    fi; \
    uv pip install --system --no-cache -r /tmp/requirements.txt; \
    rm /tmp/requirements.txt; \
    git config --global --unset-all url."https://github.com/".insteadOf 2>/dev/null || true

# Fail-fast: the 3O stack MUST be importable, else fail the build instead of
# silently publishing a broken image (root cause of 0.3.0–0.3.3).
RUN python -c "import oprim, oskill, omodul; print(f'3O baked: oprim={oprim.__version__} oskill={oskill.__version__} omodul={omodul.__version__}')"

RUN apt-get purge -y --auto-remove build-essential \
    && rm -rf /var/lib/apt/lists/*

USER app

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD curl -fsS http://localhost:8000/health || exit 1

CMD ["python", "-c", "import fastapi, asyncpg, uvicorn, pydantic, oprim, oskill, omodul; print(f'api-base OK: oprim={oprim.__version__} oskill={oskill.__version__} omodul={omodul.__version__}')"]
