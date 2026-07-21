# Homelab

Portainer stack files (Docker Compose) for self-hosted services at `*.halfsugar.tech`.

## Two reverse proxies

| Proxy | Port | Auth | Network created |
|-------|------|------|----------------|
| **Traefik** (v3.6) — primary | 80, 443 | Cloudflare DNS challenge via `CF_DNS_API_TOKEN` | `traefik_network` |
| Nginx Proxy Manager | 9443 | Let's Encrypt | `nginx_network` |

Both share both networks. Most services connect to `nginx_network` and `traefik_network` (declared `external: true`).

## Deployment order

1. `nginx.yaml` — creates `nginx_network`
2. `traefik.yaml` — needs `nginx_network`, creates `traefik_network`
3. Everything else

## Environment variables

Required at deploy time (set in Portainer or docker-compose env):
- `CONFIG_ROOT` — base path for service config data
- `MEDIA_ROOT` — media files (movies, TV, music)
- `UPLOAD_LOCATION` — Immich upload directory
- `CF_DNS_API_TOKEN` — Cloudflare API token for Traefik TLS
- `ACME_EMAIL` — Let's Encrypt registration email
- `WATCHTOWER_HTTP_API_TOKEN`, `WATCHTOWER_NOTIFICATION_URL`

Immich uses `../stack.env` (relative to `portainer/` — sibling of repo root) for DB credentials and API keys. This file is **not in the repo**.

## User/group

Standard: `PUID=1005`, `PGID=1005`. Exceptions:
- `alist.yaml` — uses `PUID=0`/`PGID=0`
- `monitoring.yaml` — also sets `user: 1005:1005`
- `homepage.yaml` — PUID/PGID commented out

All services: `TZ=Asia/Shanghai`.

## Services

| File | Services |
|------|----------|
| `servarr.yaml` | radarr, sonarr, jackett, bazarr, jellyseerr, flaresolverr, prowlarr, lidarr |
| `immich.yaml` | immich-server, immich-microservices, immich-machine-learning, immich-web, typesense, redis, database, immich-proxy |
| `monitoring.yaml` | prometheus, grafana, node-exporter, cadvisor |
| Infrastructure | `traefik.yaml`, `nginx.yaml`, `watchtower.yaml`, `dockhand.yaml` |

## Services NOT in this repo

Deployed outside Portainer or elsewhere: Authentik, Jellyfin, PhotoPrism, Nextcloud, Cockpit, OpenWRT, AdGuard Home, WireGuard, Ollama.

## Notes

- These are Portainer stack definitions (valid docker-compose). Paste YAML into Portainer's "Add Stack" UI. Networks are `external: true` — create them first.
- No `.gitignore`, no lockfiles, no CI/CD config. Plain YAML only.
- `watchtower` auto-updates all containers and sends notification reports.
- Most Traefik-routed services use labels for `traefik.enable=true`, `websecure` entrypoint, `letsencrypt` certresolver.
