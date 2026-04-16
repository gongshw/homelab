# Homelab Infrastructure Repository

## Repository Purpose

Portainer stack files (Docker Compose) for deploying self-hosted services on a homelab server.

## Environment Variables

Required environment variables used across stacks:
- `CONFIG_ROOT` - Base path for service configuration data
- `MEDIA_ROOT` - Base path for media files (movies, TV, music)
- `UPLOAD_LOCATION` - Upload directory for Immich photo service

Some stacks reference `../stack.env` files relative to the YAML location for additional configuration (e.g., Immich uses this for database credentials and API keys).

## Deployment Order

Deploy `portainer/nginx.yaml` first - it creates the `nginx_network` that most other stacks require.

## User/Group Permissions

Media services consistently use PUID=1005 and PGID=1005. Ensure these IDs exist on the host system and have appropriate permissions on mounted volumes.

## Service Categories

- **Media automation**: `servarr.yaml` (radarr, sonarr, jackett, bazarr, jellyseerr, prowlarr, lidarr)
- **Photo management**: `immich.yaml`
- **Download clients**: `qbittorrent.yaml`, `transmission.yaml`, `aria2.yaml`
- **RSS**: `freshrss.yaml`, `rsshub.yaml`
- **Monitoring**: `monitoring.yaml` (prometheus, grafana, exporters)
- **Network**: `nginx.yaml`, `ddns-updater.yaml`, `ddns-go.yaml`

## Timezone

All services configured for `TZ=Asia/Shanghai`.
