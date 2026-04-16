# Hermes Agent Docker Deployment

This repository runs Hermes Agent in Docker with a dedicated gateway container and a separate dashboard container. Persistent state lives on the host at `${HOME}/.hermes`, so image upgrades do not remove Hermes configuration, sessions, or memories.

## Prerequisites

- Docker Engine with Docker Compose support
- At least 1 CPU / 1 GB RAM for light usage
- 2 CPU / 2-4 GB RAM recommended if you plan to use browser tooling

## 1. Initialize Hermes Once

Create the data directory and run the setup wizard once on the host:

```bash
mkdir -p ~/.hermes

docker run -it --rm \
  -v ~/.hermes:/opt/data \
  nousresearch/hermes-agent setup
```

This writes the initial Hermes files, including `~/.hermes/.env`.

## 2. Start Gateway And Dashboard

Optionally copy `.env.example` to `.env` and adjust image tags or host ports:

```bash
cp .env.example .env
```

Bring the stack up:

```bash
docker compose up -d
docker compose logs -f
```

The deployment exposes:

- Gateway on `http://localhost:8642`
- Dashboard on `http://localhost:9119`

## 3. Upgrade

```bash
docker compose pull
docker compose up -d
```

## Notes

- Do not run two Hermes gateway containers against the same `${HOME}/.hermes` directory.
- Sharing `${HOME}/.hermes` between the gateway and dashboard is safe.
- `shm_size: 1gb` is included to reduce browser automation issues.
- Exposing `8642` or `9119` to the public internet has security risk. Put them behind your own access controls if you need remote access.
- If you only want an interactive CLI session and do not need long-running services, you can run:

```bash
docker run -it --rm \
  -v ~/.hermes:/opt/data \
  nousresearch/hermes-agent
```
