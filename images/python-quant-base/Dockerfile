# syntax=docker/dockerfile:1.7
# helios-plat/python-quant-base
# For live trading services: nautilus-trader on top of ml-base.
# Inherits: python-ml-base (pandas<3.0 already pinned there).

ARG BASE_TAG=latest
FROM ghcr.io/helios-plat/helios/python-ml-base:${BASE_TAG}

USER root

COPY --chown=app:app requirements.txt /tmp/requirements.txt
RUN uv pip install --system --no-cache -r /tmp/requirements.txt && rm /tmp/requirements.txt

USER app

CMD ["python", "-c", "import nautilus_trader; import pandas; print(f'quant-base OK: nautilus={nautilus_trader.__version__} pandas={pandas.__version__}')"]
