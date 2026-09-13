# FlashForgeWebUI Docker

Docker image for FlashForgeWebUI, built from [Parallel-7/FlashForgeWebUI](https://github.com/Parallel-7/FlashForgeWebUI) releases.

[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue)](https://github.com/<owner>/flashforge-webui-docker)
[![ghcr.io](https://img.shields.io/badge/ghcr.io-Images-green)](https://ghcr.io/<owner>/flashforge-webui-docker)

This is a custom Docker image that wraps the FlashForgeWebUI binary from the upstream `Parallel-7/FlashForgeWebUI` repository. It automatically syncs with new upstream releases and builds Docker images pushed to `ghcr.io`.

## How It Works

1. The Dockerfile downloads the release binary from GitHub releases at a specific tag
2. GitHub Actions monitors upstream releases and auto-updates the `.tag` file
3. When `.tag` changes, the image is automatically rebuilt and pushed to `ghcr.io`
4. The container runs the FlashForgeWebUI web interface on port 3000

## Pre-built Images

- `ghcr.io/<owner>/flashforge-webui-docker:v1.2.0-alpha.7` - Pin to specific version
- `ghcr.io/<owner>/flashforge-webui-docker:latest` - Always latest release

## Quick Start

### Docker Run

```bash
docker run -d \
  --name flashforge-webui \
  -p 3000:3000 \
  -v ./data:/data \
  ghcr.io/<owner>/flashforge-webui-docker:v1.2.0-alpha.7 \
  --all-saved-printers --webui-port=3000 --webui-password=changeme
```

### Docker Compose

```yaml
version: "3.8"
services:
  flashforge-webui:
    image: ghcr.io/<owner>/flashforge-webui-docker:v1.2.0-alpha.7
    container_name: flashforge-webui
    ports:
      - "3000:3000"
    volumes:
      - ./data:/data
    restart: unless-stopped
    command: ["--all-saved-printers", "--webui-port=3000", "--webui-password=changeme"]
    healthcheck:
      test: ["CMD", "curl", "-sfS", "http://127.0.0.1:3000/"]
      interval: 1m
      timeout: 5s
      retries: 3
      start_period: 30s
```

```bash
docker compose up -d
```

### Change password:

```bash
docker run -d --name flashforge-webui -p 3000:3000 -v ./data:/data ghcr.io/<owner>/flashforge-webui-docker:v1.2.0-alpha.7 --webui-port=3000 --webui-password=yourpassword
```

## Project Structure

```
flashforge-webui-docker/
├── Dockerfile              # Build definition
├── .tag                    # Current upstream release tag
├── .dockerignore           # Build context exclusions
├── docker-compose.yml      # Example compose file
├── README.md               # This file
├── .github/
│   └── workflows/
│       ├── build-and-push.yml     # Build & push to ghcr.io
│       └── monitor-releases.yml   # Watch upstream for new releases
```

## Auto-Update

The project has two GitHub Actions workflows:

| Workflow | Purpose | Trigger |
|----------|---------|---------|
| `build-and-push` | Build Docker image and push to ghcr.io | Push to `Dockerfile`/`.tag`, manual |
| `monitor-releases` | Check upstream for new releases daily | Cron (7am UTC), manual |

When `monitor-releases` finds a new upstream version, it updates `.tag` and pushes, which triggers `build-and-push`.

## CLI Options

| Option | Description |
|--------|-------------|
| `--webui-port=PORT` | Set webUI port |
| `--webui-password=PASSWORD` | Set login password |
| `--all-saved-printers` | Connect to all saved printers |
| `--last-used` | Connect to last used printer |
| `--printers="IP:NAME:KEY"` | Connect to specific printer(s) |

## Upstream

- [Parallel-7/FlashForgeWebUI](https://github.com/Parallel-7/FlashForgeWebUI) - Source project
- [FlashForgeWebUI v1.2.0-alpha.7](https://github.com/Parallel-7/FlashForgeWebUI/releases/tag/v1.2.0-alpha.7) - Current release

## License

MIT
