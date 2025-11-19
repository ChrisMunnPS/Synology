```markdown
# 🚀 RomM on Synology NAS (Docker + Portainer)

## 📌 Executive Summary
Self-hosted ROM manager with IGDB metadata, built-in scanner, and sleek web UI. Runs perfectly on Synology DS918+ via Portainer. MariaDB backend + Redis cache.

## 📁 Required Folders (create in File Station)
Create these folders (read/write for docker):

```
📦 /volume1/docker/mariadb-romm          # MariaDB data
📦 /volume1/docker/romm/resources        # RomM resources
📦 /volume1/docker/romm/redis-data       # Redis data
📦 /volume1/data/media/games/library     # ← Your ROMs go here
📦 /volume1/data/media/games/assets      # Covers & metadata
📦 /volume1/data/media/games/config      # RomM config
```

## ⚙️ docker-compose.yml (Portainer → Web editor)
```yaml
version: "3"
networks:
  synobridge:
    driver: bridge

services:
  romm:
    image: rommapp/romm:latest
    container_name: romm
    restart: unless-stopped
    environment:
      - DB_HOST=romm-db
      - DB_NAME=romm
      - DB_USER=romm-user
      - DB_PASSWD=${DB_PASSWORD}
      - ROMM_AUTH_SECRET_KEY=${ROMM_SECRET_KEY}
      - IGDB_CLIENT_ID=${IGDB_CLIENT_ID}
      - IGDB_CLIENT_SECRET=${IGDB_CLIENT_SECRET}
    volumes:
      - /volume1/docker/romm/resources:/romm/resources
      - /volume1/docker/romm/redis-data:/redis-data
      - /volume1/data/media/games/library:/romm/library
      - /volume1/data/media/games/assets:/romm/assets
      - /volume1/data/media/games/config:/romm/config
    ports:
      - 7676:8080
    networks:
      - synobridge
    depends_on:
      romm-db:
        condition: service_healthy

  romm-db:
    image: mariadb:latest
    container_name: romm-db
    restart: unless-stopped
    environment:
      - MARIADB_ROOT_PASSWORD=${DB_ROOT_PASSWORD}
      - MARIADB_DATABASE=romm
      - MARIADB_USER=romm-user
      - MARIADB_PASSWORD=${DB_PASSWORD}
    ports:
      - 3309:3306
    networks:
      - synobridge
    volumes:
      - /volume1/docker/mariadb-romm:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      start_period: 30s
      interval: 10s
      timeout: 5s
      retries: 5
```

## 🔑 Environment Variables (Portainer → Add manually)
```env
# 🔒 MariaDB root password
DB_ROOT_PASSWORD=your_very_strong_root_password

# 🔒 RomM database user password (same in both services)
DB_PASSWORD=your_strong_db_password

# 🔑 RomM auth secret (run: openssl rand -hex 32)
ROMM_SECRET_KEY=0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef

# 🎮 IGDB API (register at https://api.igdb.com)
IGDB_CLIENT_ID=your_igdb_client_id
IGDB_CLIENT_SECRET=your_igdb_client_secret
```

## 🚪 Access
http://YOUR_NAS_IP:7676  
Default login: `admin` / `admin` → ⚠️ Change immediately!

Enjoy your ROM library! 🕹️
```
