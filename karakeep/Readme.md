# 🧠 KaraKeep Stack — Self-Hosted Bookmark Intelligence

## 🚀 Executive Summary

KaraKeep is a powerful, privacy-first bookmarking and content enrichment platform. This stack deploys KaraKeep with full crawler, search, and inference capabilities using Docker Compose — ideal for self-hosted environments like Synology NAS or Portainer.

This setup includes:

- 🔍 **MeiliSearch** for fast, local search indexing
- 🧠 **OpenAI integration** for intelligent tagging (optional)
- 🕵️‍♂️ **Crawler with cookie support** for authenticated scraping
- 🧱 **Named volumes** for Portainer compatibility
- 🔐 **Secure `.env.template`** for secrets and API keys

---

## 🛠️ Files to Create

| File | Purpose |
|------|--------|
| `docker-compose.yml` | Defines the full stack: web, crawler, search, browser |
| `.env.template` | Stores secrets and configuration variables |
| `volumes/data/cookies.json` | Required for crawler authentication (must be a valid JSON array) |

---

## 📦 Installation Instructions

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/karakeep-stack.git
cd karakeep-stack


2. Create required folders and files
mkdir -p volumes/data
echo '[]' > volumes/data/cookies.json


Ensure cookies.json contains a valid JSON array: []


3. Configure .env.template
Rename and edit:
cp .env.template .env


Fill in your secrets:
KARAKEEP_VERSION=release
OPENAI_API_KEY=sk-...           # Optional
MEILI_MASTER_KEY=your_meili_key
NEXTAUTH_SECRET=your_stable_secret
BROWSER_COOKIE_PATH=/data/cookies.json



4. Deploy with Docker Compose
docker compose up -d


Or via Portainer:
- Paste docker-compose.yml into the stack editor
- Manually add .env variables
- Ensure volumes/data/cookies.json is copied into the data volume

⚙️ docker-compose.yml Overview
version: '3.8'

services:
  web:
    image: ghcr.io/karakeep-app/karakeep:${KARAKEEP_VERSION:-release}
    ports:
      - 3000:3000
    volumes:
      - data:/data
    environment:
      MEILI_ADDR: http://meilisearch:7700
      MEILI_MASTER_KEY: ${MEILI_MASTER_KEY}
      OPENAI_API_KEY: ${OPENAI_API_KEY}
      NEXTAUTH_SECRET: ${NEXTAUTH_SECRET}
      BROWSER_COOKIE_PATH: /data/cookies.json
      DATA_DIR: /data

  chrome:
    image: gcr.io/zenika-hub/alpine-chrome:124
    command:
      - --no-sandbox
      - --remote-debugging-port=9222

  meilisearch:
    image: getmeili/meilisearch:v1.13.3
    volumes:
      - meilisearch:/meili_data
    environment:
      MEILI_MASTER_KEY: ${MEILI_MASTER_KEY}
      MEILI_NO_ANALYTICS: "true"

volumes:
  data:
  meilisearch:



🧪 Healthchecks & Debugging
- Check logs:
docker logs karakeep-web-1
- Validate cookies file:
docker exec -it karakeep-web-1 sh
cat /data/cookies.json



🧩 Optional Enhancements
- 🛡️ Add healthchecks to all services
- 🧬 Auto-create cookies.json on startup
- 📜 Document crawler schema for cookie injection
- 🔄 Add Watchtower for auto-updates

🤝 Credits
- KaraKeep
- MeiliSearch
- Zenika Alpine Chrome

🧭 License
MIT — feel free to fork, adapt, and improve.
