# Versi Python bisa diganti dari .env: PYTHON_VERSION=3.10 / 3.11 / 3.12
ARG PYTHON_VERSION=3.12
FROM python:${PYTHON_VERSION}-slim

# paket sistem untuk mysqlclient, Pillow, dan sejenisnya
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential pkg-config git curl \
        default-libmysqlclient-dev \
        libjpeg-dev zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# dependency dipasang saat build agar container cepat menyala
COPY requirements.txt* ./
RUN if [ -f requirements.txt ]; then pip install --no-cache-dir -r requirements.txt; fi
