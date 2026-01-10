---
description: Scaffold a new full-stack project with dev, test, and prod environments
argument-hint: <project-name>
---

# Scaffold New Project

Create a complete project structure with Docker Compose environments for local development, local prod-like testing, and remote production.

## Arguments

- `$1` (required): Project name in kebab-case (e.g., `my-app`)

## Gather Project Information

Before scaffolding, ask the user these questions using AskUserQuestion:

1. **What services do you need?** (multiSelect: true)
   - Web app (frontend)
   - API backend
   - PostgreSQL database
   - Redis cache
   - Background worker

2. **What's your production domain?** (free text via "Other")
   - example.com
   - Will decide later

3. **What local domain do you want?**
   - `<project-name>.localhost` (Recommended - works without /etc/hosts)
   - `<project-name>.test` (Requires /etc/hosts entry)

## Directory Structure to Create

```
<project-name>/
├── docker/
│   ├── traefik/
│   │   ├── traefik.dev.yml        # Dev config (HTTP)
│   │   ├── traefik.test.yml       # Test config (HTTPS with mkcert)
│   │   └── traefik.prod.yml       # Prod config (HTTPS with Let's Encrypt)
│   └── certs/                     # For local mkcert certificates
│       └── .gitkeep
├── envs/
│   ├── .env.example               # Template with all variables
│   ├── .env.dev                   # Local development values
│   ├── .env.test                  # Local prod-like test values
│   └── .env.prod.example          # Production template (never commit real values)
├── scripts/
│   ├── dev.sh                     # Start dev environment
│   ├── test.sh                    # Start test environment
│   ├── setup-certs.sh             # Generate local HTTPS certs with mkcert
│   └── deploy.sh                  # Deploy to production
├── docker-compose.yml             # Base compose file
├── docker-compose.dev.yml         # Dev overrides
├── docker-compose.test.yml        # Test overrides (prod-like)
├── docker-compose.prod.yml        # Production overrides
├── .gitignore
├── Makefile                       # Convenience commands
└── README.md
```

## File Contents

### docker-compose.yml (base)

```yaml
services:
  traefik:
    image: traefik:v3.0
    container_name: ${PROJECT_NAME}-traefik
    restart: unless-stopped
    networks:
      - web
      - internal

  # Add services based on user selection:
  # - app: for web frontend
  # - api: for backend
  # - db: for PostgreSQL
  # - redis: for Redis
  # - worker: for background jobs

networks:
  web:
    name: ${PROJECT_NAME}-web
  internal:
    name: ${PROJECT_NAME}-internal
```

### docker-compose.dev.yml

```yaml
services:
  traefik:
    command:
      - "--configFile=/etc/traefik/traefik.dev.yml"
    ports:
      - "80:80"
      - "8080:8080"  # Traefik dashboard
    volumes:
      - ./docker/traefik/traefik.dev.yml:/etc/traefik/traefik.dev.yml:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro

  # Services use development settings:
  # - Hot reload enabled
  # - Debug logging
  # - Local database
```

### docker-compose.test.yml

```yaml
services:
  traefik:
    command:
      - "--configFile=/etc/traefik/traefik.test.yml"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./docker/traefik/traefik.test.yml:/etc/traefik/traefik.test.yml:ro
      - ./docker/certs:/certs:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro

  # Services use production-like settings:
  # - HTTPS enabled
  # - Production build
  # - Separate database
```

### docker-compose.prod.yml

```yaml
services:
  traefik:
    command:
      - "--configFile=/etc/traefik/traefik.prod.yml"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./docker/traefik/traefik.prod.yml:/etc/traefik/traefik.prod.yml:ro
      - traefik-certs:/certs
      - /var/run/docker.sock:/var/run/docker.sock:ro

volumes:
  traefik-certs:
```

### docker/traefik/traefik.dev.yml

```yaml
api:
  dashboard: true
  insecure: true

entryPoints:
  web:
    address: ":80"

providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
    network: ${PROJECT_NAME}-web

log:
  level: DEBUG
```

### docker/traefik/traefik.test.yml

```yaml
api:
  dashboard: true
  insecure: true

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
  websecure:
    address: ":443"

providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
    network: ${PROJECT_NAME}-web
  file:
    filename: /etc/traefik/traefik.test.yml

tls:
  certificates:
    - certFile: /certs/${LOCAL_DOMAIN}.pem
      keyFile: /certs/${LOCAL_DOMAIN}-key.pem

log:
  level: INFO
```

### docker/traefik/traefik.prod.yml

```yaml
api:
  dashboard: true

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
  websecure:
    address: ":443"

providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false

certificatesResolvers:
  letsencrypt:
    acme:
      email: ${ACME_EMAIL}
      storage: /certs/acme.json
      httpChallenge:
        entryPoint: web

log:
  level: WARN
```

### envs/.env.example

```bash
# Project
PROJECT_NAME=<project-name>

# Domains
LOCAL_DOMAIN=<project-name>.localhost
PROD_DOMAIN=example.com

# Database
DB_HOST=db
DB_PORT=5432
DB_NAME=<project-name>
DB_USER=postgres
DB_PASSWORD=changeme

# Redis
REDIS_URL=redis://redis:6379

# API Keys (use different values per environment!)
SECRET_KEY=generate-a-secure-key

# SSL/ACME (production only)
ACME_EMAIL=admin@example.com

# Environment
ENVIRONMENT=development
DEBUG=true
```

### scripts/dev.sh

```bash
#!/usr/bin/env bash
set -euo pipefail

cd "$(dirname "$0")/.."

# Load dev environment
set -a
source envs/.env.dev
set +a

docker compose -f docker-compose.yml -f docker-compose.dev.yml up "$@"
```

### scripts/test.sh

```bash
#!/usr/bin/env bash
set -euo pipefail

cd "$(dirname "$0")/.."

# Check for certs
if [ ! -f "docker/certs/${LOCAL_DOMAIN}.pem" ]; then
    echo "Local certs not found. Run ./scripts/setup-certs.sh first"
    exit 1
fi

# Load test environment
set -a
source envs/.env.test
set +a

docker compose -f docker-compose.yml -f docker-compose.test.yml up "$@"
```

### scripts/setup-certs.sh

```bash
#!/usr/bin/env bash
set -euo pipefail

cd "$(dirname "$0")/.."

# Load environment for domain
source envs/.env.dev

# Check for mkcert
if ! command -v mkcert &> /dev/null; then
    echo "mkcert not found. Install it:"
    echo "  - macOS: brew install mkcert"
    echo "  - Linux: https://github.com/FiloSottile/mkcert#installation"
    exit 1
fi

# Install local CA if needed
mkcert -install

# Generate certs
mkdir -p docker/certs
mkcert -cert-file docker/certs/${LOCAL_DOMAIN}.pem \
       -key-file docker/certs/${LOCAL_DOMAIN}-key.pem \
       ${LOCAL_DOMAIN} "*.${LOCAL_DOMAIN}"

echo "Certificates created for ${LOCAL_DOMAIN}"
```

### Makefile

```makefile
.PHONY: dev test prod setup-certs clean

dev:
	./scripts/dev.sh

test: setup-certs
	./scripts/test.sh

prod:
	./scripts/deploy.sh

setup-certs:
	./scripts/setup-certs.sh

clean:
	docker compose down -v --remove-orphans
```

### .gitignore

```
# Environment files with secrets
envs/.env.dev
envs/.env.test
envs/.env.prod

# Keep examples
!envs/.env.example
!envs/.env.prod.example

# Local certificates
docker/certs/*.pem

# Docker
.docker/

# IDE
.idea/
.vscode/
*.swp
```

## After Scaffolding

Provide these next steps:

```
Project scaffolded at <project-name>/

Next steps:
1. cd <project-name>
2. Copy and edit environment files:
   cp envs/.env.example envs/.env.dev
   cp envs/.env.example envs/.env.test
3. Generate local HTTPS certificates:
   make setup-certs
4. Start development:
   make dev
5. Test production-like setup:
   make test

Your local site will be available at:
  - Dev:  http://<project-name>.localhost
  - Test: https://<project-name>.localhost
```

## Notes

- All scripts should be created with executable permissions (chmod +x)
- The .localhost TLD works without /etc/hosts on most systems
- mkcert is required for local HTTPS - provide install instructions if missing
- Database passwords in .env files should be unique per environment
