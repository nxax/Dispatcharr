# Dispatcharr - Antigravity Agent Guidelines & Repository Context (`AGENTS.md`)

Welcome! This file provides essential repository context, architectural layout, tech stack details, and execution guidelines for **Antigravity** and AI agents working on **Dispatcharr**.

---

## 📖 Project Overview
**Dispatcharr** is an open-source IPTV, EPG (Electronic Program Guide), and VOD (Video on Demand) management dashboard/proxy.
It consolidates multiple IPTV M3U sources, schedules DVR recordings, emulates HDHomeRun devices for Plex/Jellyfin/Emby, generates XMLTV guides, and proxies streams with real-time bandwidth monitoring, failover support, and FFmpeg/VLC transcoding.

---

## 🛠️ Tech Stack & Dependencies
- **Backend Framework**: Django 6.0.5 (REST APIs built with Django REST Framework).
- **Asynchronous / Realtime**: Django Channels + Daphne (ASGI), Redis (caching and channel layer backend).
- **Background Tasks**: Celery 5.6.3 with Redis broker & Django Celery Beat.
- **Python Version**: `>=3.13` (managed via `uv` package manager).
- **Frontend Stack**: React 19, Mantine UI v8, Vite, React Router v7.
- **Database**: PostgreSQL (default production) / SQLite (flexible fallback for testing/local setups).
- **External Tools**: FFmpeg, VLC (`python-vlc`), Streamlink, and `yt-dlp` for stream proxying & transcoding.

---

## 📂 Project Architecture Map
Use this directory guide to locate files directly:

### 🐍 Backend Structure
- [**`dispatcharr/`**](file:///c:/Users/neis7/github/Dispatcharr/dispatcharr): Root project configuration.
  - [`settings.py`](file:///c:/Users/neis7/github/Dispatcharr/dispatcharr/settings.py): Application configuration, third-party settings, database setups, and logger configs.
  - [`celery.py`](file:///c:/Users/neis7/github/Dispatcharr/dispatcharr/celery.py): Celery instance setup and autodiscover.
  - [`asgi.py`](file:///c:/Users/neis7/github/Dispatcharr/dispatcharr/asgi.py) & [`wsgi.py`](file:///c:/Users/neis7/github/Dispatcharr/dispatcharr/wsgi.py): Production server entry points.
- [**`core/`**](file:///c:/Users/neis7/github/Dispatcharr/core): Project-wide utilities and core abstract logic.
  - Models, serializers, tasks, signals, and base scheduler engines that are globally referenced.
- [**`apps/`**](file:///c:/Users/neis7/github/Dispatcharr/apps): Domain-specific Django applications.
  - [**`accounts/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/accounts): Custom user model (`accounts.User`), JWT, API Key permissions, authentication filters.
  - [**`api/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/api): Central API endpoint router (`api_urls.py`, `views.py`), OpenAPI specification configurations (`drf-spectacular`).
  - [**`backups/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/backups): Logic for automated and manual file and database backups.
  - [**`channels/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/channels): DVR scheduler rules, stream associations, recording loops, and recording deduplication filters.
  - [**`connect/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/connect): Safely run custom user automation scripts within defined subfolders.
  - [**`dashboard/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/dashboard): General views.
  - [**`epg/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/epg): Ingesting, downloading, parsing XMLTV files; matching programmes to channels.
  - [**`hdhr/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/hdhr): Emulates HDHomeRun tuner endpoints (`discover.json`, `lineup.json`) to integrate virtual TV tuners with Media Centers like Plex, Emby, and Jellyfin.
  - [**`m3u/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/m3u): Import, download, parser, and synchronize external M3U playlists.
  - [**`output/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/output): Formats channels, VOD lists, and EPG data back into client-consumable outputs (M3U playlists, Xtream Codes APIs, XMLTV feeds).
  - [**`plugins/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/plugins): Plugin registry, manager, and custom plugin lifecycle hooks.
  - [**`proxy/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/proxy): High-performance stream proxier and proxy relay (buffers streams, applies output profiles / ffmpeg transcoding, measures real-time usage stats, switches to backups on failover).
  - [**`vod/`**](file:///c:/Users/neis7/github/Dispatcharr/apps/vod): Imports VOD profiles, crawls media structure, fetches TMDB/IMDb movie/show metadata, manages VOD streams.

### 🎨 Frontend Structure
- [**`frontend/`**](file:///c:/Users/neis7/github/Dispatcharr/frontend): React frontend.
  - [`package.json`](file:///c:/Users/neis7/github/Dispatcharr/frontend/package.json): Vite configs and frontend libraries (Mantine UI v8, Zustand, React 19).
  - [`src/`](file:///c:/Users/neis7/github/Dispatcharr/frontend/src): Contains the components, pages, hooks, state store, and route configurations.

---

## ⚡ Agent Guidelines & Efficiency
1. **Targeted Searches**: Avoid workspace-wide searches if the target app is known (e.g. search `apps/epg/` for EPG parsing).
2. **Environment & Config**: Reference [`dispatcharr/settings.py`](file:///c:/Users/neis7/github/Dispatcharr/dispatcharr/settings.py) for backend configs.
3. **Use UV**: Always execute Python scripts and Django management commands with `uv run`.

---

## 🖥️ Commands Reference

### 🐍 Backend
```bash
# Server & Services
uv run python manage.py runserver
uv run daphne -p 8000 dispatcharr.asgi:application
uv run celery -A dispatcharr worker -l info
uv run celery -A dispatcharr beat -l info

# Database & Migrations
uv run python manage.py makemigrations
uv run python manage.py migrate

# Tests
uv run python manage.py test
uv run python manage.py test apps.channels.tests
```

### 🎨 Frontend (`frontend/`)
```bash
npm install
npm run dev
npm run build
npm run test
```

### 🐳 Docker & Unraid Build (`https://github.com/nxax/Dispatcharr.git`)
```bash
# Build custom image on Unraid server terminal:
cd /mnt/user/appdata/dispatcharr-custom
git pull origin main
docker build -f docker/Dockerfile -t nxax/dispatcharr:custom .

# Set Unraid container Repository field to:
# nxax/dispatcharr:custom
```

---

## 📌 Coding Conventions
- **API Endpoints**: Use Django REST Framework `ModelViewSet` and annotate with `drf-spectacular`.
- **Type Annotations**: Python 3.13+ typing syntax is required on new methods/functions.
- **Async & Celery Tasks**: Implement concurrency safe locks where applicable for shared state or scheduled updates.

