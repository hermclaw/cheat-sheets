---
title: "Docker Cheat Sheet"
description: "Comprehensive Docker reference covering containers, images, Dockerfiles, Compose, networking, volumes, and multi-stage builds."
layout: default
---

## Container Lifecycle

```bash
docker run <image>                    # Run container (default: foreground)
docker run -d <image>                 # Run in detached mode
docker run -it <image> bash           # Interactive with TTY
docker run --rm <image>               # Auto-remove on exit
docker run --name myapp <image>       # Named container
docker run -p 8080:80 <image>         # Port mapping: host:container
docker run -p 127.0.0.1:8080:80 <image>  # Bind to localhost only
docker run -v /host/path:/container/path <image>  # Bind mount
docker run -e KEY=value <image>       # Environment variable
docker run -e KEY <image>             # Env from host var
docker run --env-file .env <image>    # Env from file
docker run --restart unless-stopped <image>  # Restart policy
docker run --network mynet <image>    # Specific network
docker run --memory 512m <image>      # Memory limit
docker run --cpus 2 <image>           # CPU limit
docker run --user 1000:1000 <image>   # Run as specific user

docker start <container>              # Start stopped container
docker stop <container>               # Graceful stop (SIGTERM, then SIGKILL)
docker kill <container>               # Immediate stop (SIGKILL)
docker restart <container>            # Restart container
docker pause <container>              # Pause all processes
docker unpause <container>            # Resume paused container
docker rm <container>                 # Remove stopped container
docker rm -f <container>              # Force remove (running too)
docker rm -v <container>              # Remove associated anonymous volumes
docker container prune                # Remove all stopped containers
```

## Container Inspection & Interaction

```bash
docker ps                             # Running containers
docker ps -a                          # All containers (including stopped)
docker ps -q                          # Only container IDs
docker ps --format '{{.Names}}: {{.Status}}'  # Custom format

docker logs <container>               # View logs
docker logs -f <container>            # Follow logs
docker logs --tail 100 <container>    # Last 100 lines
docker logs --since 1h <container>    # Since time

docker exec -it <container> bash      # Shell into running container
docker exec -u root <container> cmd   # Run as different user
docker top <container>                # Process list in container
docker stats                          # Resource usage (live)
docker stats --no-stream              # Single snapshot

docker inspect <container>            # Low-level info (JSON)
docker inspect -f '{{.NetworkSettings.IPAddress}}' <container>

docker cp <container>:/path /host     # Copy from container
docker cp /host <container>:/path     # Copy to container
docker diff <container>               # Filesystem changes
```

## Images

```bash
docker images                         # List local images
docker image ls                       # Same as above
docker pull <image>                   # Pull from registry
docker pull <image>:<tag>             # Specific tag
docker pull --all-tags <image>        # All tags
docker push <image>:<tag>             # Push to registry
docker push registry.example.com/myimage:tag  # Push to custom registry

docker tag <image> <new-name>:<tag>   # Tag an image
docker rmi <image>                    # Remove image
docker rmi -f <image>                 # Force remove
docker image prune                    # Remove dangling images
docker image prune -a                 # Remove all unused images

docker build -t myapp .               # Build from Dockerfile
docker build -t myapp:1.0 .           # Build with tag
docker build -f Dockerfile.prod .     # Specific Dockerfile
docker build --no-cache .             # No cache
docker build --build-arg VAR=val .    # Build argument

docker history <image>                # Image layers
docker save <image> -o file.tar       # Export to tar
docker load -i file.tar               # Import from tar
```

## Dockerfile Syntax

```dockerfile
# Base image
FROM python:3.12-slim
FROM node:20 AS builder          # Named build stage

# Metadata
LABEL maintainer="dev@example.com"
LABEL version="1.0"

# Arguments & environment
ARG VERSION=latest
ENV NODE_ENV=production
ENV PATH="/app/bin:${PATH}"

# Working directory
WORKDIR /app

# Copy files
COPY package.json package-lock.json ./
COPY src/ /app/src/               # Copy directory
COPY --chown=1000:1000 src/ .    # Set ownership
COPY --from=builder /dist .      # Copy from another stage

# Add (supports URLs and tar extraction)
ADD https://example.com/file.tar.gz /tmp/
ADD archive.tar.gz /app/          # Auto-extracts tar

# Run commands
RUN apt-get update && apt-get install -y curl
RUN pip install -r requirements.txt
# Use && to reduce layers (single RUN = single layer)

# Expose port (documentation, not enforcement)
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://localhost:8080/ || exit 1

# User
USER 1000:1000

# Volume
VOLUME /data

# Entrypoint (executable, always runs)
ENTRYPOINT ["python", "app.py"]

# Default arguments (can be overridden)
CMD ["--host", "0.0.0.0", "--port", "8080"]
```

### ENTRYPOINT vs CMD

| Scenario | ENTRYPOINT | CMD |
|----------|-----------|-----|
| `ENTRYPOINT ["python"]` `CMD ["app.py"]` | Runs: `python app.py` | Default args |
| Override with `docker run myimg script.py` | Runs: `python script.py` | CMD replaced |
| CMD only (no ENTRYPOINT) | — | Fully replaceable |
| ENTRYPOINT only (no CMD) | Requires args via CLI or `docker run` | — |

**Best practice**: Use `ENTRYPOINT` for the executable, `CMD` for default arguments.

## Multi-Stage Builds

```dockerfile
# Stage 1: Build
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-slim AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

Benefits: smaller final image, no build tools in production.

## Docker Compose

```bash
docker compose up                   # Start services (foreground)
docker compose up -d                # Start in detached mode
docker compose up --build           # Rebuild and start
docker compose up --force-recreate  # Recreate containers
docker compose up <service>         # Start specific service

docker compose down                 # Stop and remove containers, networks
docker compose down -v              # Also remove volumes
docker compose down --rmi local     # Also remove images

docker compose ps                   # Running services
docker compose logs                 # All logs
docker compose logs -f <service>    # Follow service logs
docker compose logs --tail 50

docker compose exec <service> bash  # Shell into service
docker compose run <service> cmd    # Run one-off command
docker compose run --rm <service>   # Run and auto-remove

docker compose build                # Build/rebuild services
docker compose build --no-cache     # Build without cache
docker compose pull                 # Pull latest images

docker compose config               # Validate and view config
docker compose config --services    # List service names
docker compose top                  # Running processes
```

### Compose File Example

```yaml
# docker-compose.yml
version: "3.9"

services:
  web:
    build: .
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/mydb
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy
    volumes:
      - .:/app
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - pgdata:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  pgdata:

networks:
  default:
    name: myapp-network
```

## Networking

```bash
docker network ls                    # List networks
docker network create mynet          # Create bridge network
docker network create -d overlay mynet  # Overlay (Swarm)
docker network inspect mynet         # Network details
docker network rm mynet              # Remove network
docker network connect mynet <container>   # Connect container
docker network disconnect mynet <container>  # Disconnect
docker network prune                 # Remove unused networks
```

### DNS Resolution

- Containers on the same custom network can reach each other by **service name**
- `web` container can reach `db` container via hostname `db`
- Default bridge network does **not** support DNS resolution — use links or IP

## Volumes

```bash
docker volume ls                     # List volumes
docker volume create myvol           # Create volume
docker volume inspect myvol          # Volume details
docker volume rm myvol               # Remove volume
docker volume prune                  # Remove unused volumes
```

### Volume Types

- **Named volumes**: `docker run -v myvol:/data` — persisted by Docker
- **Bind mounts**: `docker run -v /host/path:/container/path` — host filesystem
- **Tmpfs**: `docker run --tmpfs /tmp` — in-memory, not persisted

## Cleanup Commands

```bash
docker system prune                  # Remove unused containers, networks, images (not volumes)
docker system prune -a               # Remove ALL unused images
docker system prune --volumes        # Also remove unused volumes
docker system df                     # Disk usage overview

docker container prune               # Stopped containers
docker image prune                   # Dangling images
docker image prune -a                # All unused images
docker volume prune                  # Unused volumes
docker network prune                 # Unused networks
docker builder prune                 # Build cache
```

## Useful Patterns

### Minimal Dockerfile (Python)

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### Minimal Dockerfile (Node)

```dockerfile
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

### Run as Non-Root

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
RUN useradd -m appuser
USER appuser
EXPOSE 8080
CMD ["python", "app.py"]
```

## Common Gotchas

- **Layers are cached**: Order Dockerfile commands from least to most frequently changed
- **`.` in COPY context**: The build context (the `.` in `docker build .`) is sent to the daemon — use `.dockerignore` to exclude files
- **PID 1**: In containers, your app runs as PID 1 and must handle signals — use `exec` in shell scripts or `ENTRYPOINT`
- **Not a VM**: Containers share the host kernel; no virtualization overhead
- **`latest` tag**: Not a version guarantee — always pin tags in production
- **Dangling images**: `<none>:<none>` images left after rebuilds — `docker image prune` to clean
- **`--restart: always`** persists across Docker daemon restarts (except `docker compose down`)
