_Documentation generated by chatGPT from the docker-compose.yml_

# Why ?

Ads pissed me off on Spotify so i made a stack to get musics & play it without ads.

__Jellyfin__ to play, 
__File Browser__ to manage files, 
__MeTube__ & __qBitTorrent__ to download medias in a very very legal way. 🤡

Some docker images are in old and/or specific version for it to work on my raspberry pi 2b.

# Spotimy Docker Compose Documentation

This documentation describes the setup & configuration of the **Spotimy** project using `docker-compose`. The project deploys multiple services for managing media content, downloads, & file browsing. Below is a breakdown of the services, their configurations, & their purposes.

---

## Table of Contents

1. [Overview](#overview)
2. [Services](#services)
   - [Jellyfin](#jellyfin)
   - [File Browser](#file-browser)
   - [MeTube](#metube)
   - [qBittorrent-nox](#qbittorrent-nox)
3. [Volumes](#volumes)
4. [Environment Variables](#environment-variables)

---

## Overview

**Spotimy** leverages Docker Compose to run a stack of services tailored for media management:
- **Jellyfin**: A media server for organizing & streaming content.
- **File Browser**: A web interface for managing files.
- **MeTube**: A downloader for video & audio content.
- **qBittorrent-nox**: A torrent client with a web interface.

---

## Services

### Jellyfin
A media server for organizing, managing, & streaming content.

#### Configuration:
- **Image**: `ghcr.io/jellyfin/jellyfin:10.10.3`
- **Container Name**: `jellyfin`
- **Ports**: `8041:8096`
- **Volumes**:
  - `jellyfin_config:/config`
  - `jellyfin_cache:/cache`
  - `/mnt/jellyfin:/media`
- **Environment**:
  - `TZ=Europe/Paris`
  - `UID` & `GID` should be set via environment variables.
- **Dependencies**:
  - Depends on the `filebrowser` service, with health check enabled.

---

### File Browser
A web-based file manager for managing media files.

#### Configuration:
- **Image**: `filebrowser/filebrowser:latest`
- **Container Name**: `filebrowser`
- **Ports**: `8031:80`
- **Volumes**:
  - `/mnt/jellyfin:/srv`
  - `filebrowser_data:/database`
- **Environment**:
  - `FB_BASEURL=/`

---

### MeTube
A downloader for video & audio content.

#### Configuration:
- **Image**: `ghcr.io/alexta69/metube:2024-04-10`
- **Container Name**: `metube_container`
- **Ports**: `8021:8081`
- **Volumes**:
  - `/mnt/jellyfin:/downloads`
- **Environment**:
  - `UID` & `GID` should be set via environment variables.

---

### qBittorrent-nox
A lightweight torrent client with a web UI.

#### Configuration:
- **Image**: `ghcr.io/qbittorrent/docker-qbittorrent-nox:5.0.2-1`
- **Container Name**: `qbittorrent-nox`
- **Ports**:
  - `6881:6881/tcp`
  - `6881:6881/udp`
  - `${QBT_WEBUI_PORT}:${QBT_WEBUI_PORT}/tcp`
- **Volumes**:
  - `${QBT_CONFIG_PATH}:/config`
  - `${QBT_DOWNLOADS_PATH}:/downloads`
- **Environment**:
  - `PUID` & `PGID` should match your user.
  - `TZ=Europe/Paris`
  - Additional environment variables:
    - `QBT_LEGAL_NOTICE`
    - `QBT_VERSION`
    - `QBT_WEBUI_PORT`
- **Security**:
  - Read-only filesystem.
  - Temporary file storage in `/tmp`.

---

## Volumes

The following volumes are used for persistent data storage:
- `jellyfin_config`: Stores Jellyfin configuration files.
- `jellyfin_cache`: Stores Jellyfin cache data.
- `filebrowser_data`: Stores File Browser configuration data.

---

## Environment Variables

Configure the following environment variables before deploying the stack:
- **UID**: User ID for file ownership.
- **GID**: Group ID for file ownership.
- **QBT_LEGAL_NOTICE**: Legal notice confirmation for qBittorrent.
- **QBT_VERSION**: Specify qBittorrent version.
- **QBT_WEBUI_PORT**: Port for the qBittorrent Web UI.
- **QBT_CONFIG_PATH**: Path to qBittorrent configuration directory.
- **QBT_DOWNLOADS_PATH**: Path for downloaded files.
