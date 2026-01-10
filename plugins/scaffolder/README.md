# Scaffolder

Scaffold full-stack projects with proper environment separation: local dev, local prod-like test, and remote production.

## The Problem

Setting up a new project with multiple environments is tedious:
- Local dev needs hot reload and debug tools
- Testing prod-like locally needs HTTPS and production builds
- Production needs Let's Encrypt, different domains, secrets management
- Config keeps drifting between environments

## The Solution

Scaffolder generates a complete project structure with Docker Compose files for each environment, Traefik for routing/SSL, and scripts to switch between them.

## Installation

```bash
/plugin install scaffolder@claude-bazaar
```

## Usage

```bash
/scaffolder:new my-app
```

This will ask you about:
- What services you need (web, API, database, Redis, workers)
- Your production domain
- Local domain preference

Then generate a complete project structure.

## What You Get

```
my-app/
├── docker-compose.yml          # Base services
├── docker-compose.dev.yml      # Dev: hot reload, HTTP
├── docker-compose.test.yml     # Test: prod build, local HTTPS
├── docker-compose.prod.yml     # Prod: Let's Encrypt HTTPS
├── docker/traefik/             # Traefik configs per environment
├── envs/                       # Environment-specific variables
├── scripts/                    # dev.sh, test.sh, setup-certs.sh
└── Makefile                    # make dev, make test, make prod
```

## Environments

| Environment | URL | HTTPS | Build | Use Case |
|-------------|-----|-------|-------|----------|
| Dev | http://app.localhost | No | Hot reload | Daily development |
| Test | https://app.localhost | mkcert | Production | Pre-deploy testing |
| Prod | https://yourdomain.com | Let's Encrypt | Production | Live |

## Commands

| Command | Description |
|---------|-------------|
| `make dev` | Start development environment |
| `make test` | Start prod-like test environment |
| `make setup-certs` | Generate local HTTPS certs with mkcert |
| `make clean` | Stop everything and remove volumes |

## Requirements

- Docker & Docker Compose
- mkcert (for local HTTPS) - [installation](https://github.com/FiloSottile/mkcert#installation)
