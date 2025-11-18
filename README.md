# RomM Docker Deployment for Synology NAS 🚀

**Easily self-host RomM (Retro Game Manager) on your Synology NAS (DS-918+ or similar) using Docker and MariaDB.**  
This repo provides a ready-to-use `docker-compose.yml` and safe example `.env`, plus comprehensive setup guidance for managing and enriching your retro game collection on your own hardware.

***

## 🚩 Executive Summary

RomM lets you organize, display, and enrich your retro game collection—featuring multi-platform support, rich metadata, and a modern user interface—all while owning your data.  
This project streamlines the process of deploying RomM on Synology NAS devices using Docker, with a prebuilt setup that leverages MariaDB for database storage and supports Synology’s Container Manager out-of-box.  
Start playing and organizing your ROMs with minimal fuss, robust privacy, and full open-source transparency.[11]

***

## ✨ Features

- **Self-hosted & private**: No offsite cloud or vendor lock-in—run everything on your own Synology server.
- **Rich metadata support**: Integrates with multiple metadata providers (e.g., ScreenScraper, SteamGridDB) to enrich your library.
- **Easy Docker deployment**: Focused on Synology NAS with clear volume mappings and environment config.
- **Open source**: Built on the RomM project (AGPLv3) for maximum freedom and community benefit.[11]
- **Flexible configuration**: Clean separation of sensitive secrets using `.env` files.

***

## ⚡️ Quick Start

1. **Clone or download** this repository to your Synology NAS.
2. **Fill in environment variables**:  
   - Rename `.env.example` to `.env`.
   - Edit `.env` to use strong passwords/secrets and provider API keys as needed.
3. **Review/Adjust volume mappings** in `docker-compose.yml` to match your local folder layout (ROMs, assets, BIOS, etc.).
4. **Create Docker network (if missing)**:  
   ```sh
   docker network create rommbridge
   ```
5. **Deploy with Docker Compose**:  
   ```sh
   docker compose up -d
   ```
6. **Visit** [`http://<your-nas-ip>:7676/`](http://<your-nas-ip>:7676/) to finish setup in the RomM Web UI.
   - Set up your admin account using the on-screen wizard.
   - Begin importing and tagging your ROMs per your preferences.

***

## 🏗️ Detailed Technical Instructions

### 🗝️ Environment Variables

All sensitive data (passwords, secrets, API keys) is stored in a `.env` file.  
An example with comments can be found in `.env.example`.  
**Never commit your real `.env` file to any public repo!**

### 📁 File Structure and Volumes

- **ROM library**: `/volume1/data/games/roms`
- **BIOS (optional)**: `/volume1/data/games/bios`
- **Assets & Config**:  
  - `/volume1/data/games/assets`
  - `/volume1/data/games/config`
- **MariaDB storage**: `/volume1/docker/mariadb-romm`

Customize the paths in `docker-compose.yml` to reflect your storage setup.

### 🗄️ Database Setup

The included configuration spins up a MariaDB container pre-configured for use by RomM.  
Credentials and DB name must remain consistent between `.env` and `docker-compose.yml`.

### 🌐 Networking

Containers use a bridge network called `rommbridge`.  
Create this Docker network if it does not already exist.

### 🏷️ Metadata Providers

You may obtain additional metadata by [registering for free accounts/API keys](https://docs.romm.app/latest/Getting-Started/Metadata-Providers/) with services such as:
- ScreenScraper
- RetroAchievements
- SteamGridDB

Enter these keys/usernames into your `.env` to enable richer scraping.

***

## 🛠️ Troubleshooting

- Check logs for the `romm` and `romm-db` containers using Synology’s Container Manager or with `docker logs <container_name>`.
- Review permissions for all mapped folders—ensure the Docker containers can read/write as expected.
- If updates are released, simply update the image tags and run `docker compose pull && docker compose up -d`.

***

## 🤝 Contributing

Pull requests, issues, and improvements are encouraged!  
Please see upstream RomM documentation and follow the AGPLv3 licensing requirements for contributions.[11]

***

## 📰 License

- Docker configs/scripts: Released under the terms of the repository MIT or GPL license (set your choice).
- RomM: Core application is [AGPLv3](https://github.com/rommapp/romm).[11]
- See individual source files for details.

***

This format provides executive clarity at a glance, with section icons for rapid orientation and an inviting user experience.[2][5]

[1](https://stackoverflow.com/questions/34538879/unicode-emojis-in-github-markdown)
[2](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
[3](https://docs.github.com/en/account-and-profile/how-tos/profile-customization/managing-your-profile-readme)
[4](https://markdown-all-in-one.github.io/docs/contributing/emoji.html)
[5](https://gist.github.com/rxaviers/7360908)
[6](https://gist.github.com/roachhd/1f029bd4b50b8a524f3c)
[7](https://www.reddit.com/r/github/comments/1kcci2h/do_you_like_a_readme_with_or_without_emojis/)
[8](https://github.com/ikatyang/emoji-cheat-sheet)
[9](https://gitmoji.dev)
[10](https://dev.to/nikolab/complete-list-of-github-markdown-emoji-markup-5aia)
[11](https://github.com/rommapp)
