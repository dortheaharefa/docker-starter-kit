# Versi Node bisa diganti dari .env: NODE_VERSION=18 / 20 / 22
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-alpine

# git dibutuhkan sebagian paket npm saat instalasi
RUN apk add --no-cache git

WORKDIR /app
