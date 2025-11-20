## 📘 README.md

```markdown
# MariaDB + phpMyAdmin Stack (Docker Compose on Synology)

## 🧭 Executive Summary

This repository provides a secure, modular Docker Compose stack for deploying **MariaDB 11.4 LTS** alongside **phpMyAdmin** on a Synology NAS or any Docker-compatible host. Designed for maintainability and public sharing, the stack externalizes all secrets to a `.env` file, uses a custom Docker network for internal resolution, and includes optional PHP configuration overrides.

Ideal for developers, sysadmins, and homelab enthusiasts seeking a clean, reproducible database management setup with minimal exposure and maximum control.

---

## ⚙️ Technical Overview

### 📦 Stack Components

- **MariaDB 11.4 (LTS)** — Long-term support until May 29, 2029
- **phpMyAdmin (latest)** — Web-based MySQL/MariaDB administration
- **Custom Docker network** — `MariaDB_Net` for internal container communication
- **Environment variables** — All sensitive values injected via `.env`
- **Persistent volumes** — MariaDB data stored on host
- **Optional PHP overrides** — via `uploads.ini` for phpMyAdmin

---

### 📁 Folder Structure

Ensure the following folders exist on your Synology NAS or Docker host:

```
/volume1/docker/mariadb/
└── phpmyadmin/
    └── uploads.ini
```

---

### 📄 uploads.ini (PHP configuration override)

Create this file at `/volume1/docker/mariadb/phpmyadmin/uploads.ini`:

```ini
file_uploads = On
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 300
```

---

### 🔐 Generating a Secure Blowfish Secret

phpMyAdmin requires a `PMA_BLOWFISH_SECRET` for cookie encryption. Generate a strong 32-byte base64 string using:

**PowerShell:**

```powershell
[Convert]::ToBase64String((1..32 | ForEach-Object {Get-Random -Minimum 0 -Maximum 256}) -as [byte[]])
```

**Linux/macOS:**

```bash
openssl rand -base64 32
```

Paste the result into your `.env` file.

---

### 📁 Example `.env` File

> ⚠️ **Do not commit this file to GitHub.** Add `.env` to your `.gitignore`.

```env
# MariaDB configuration
MYSQL_ROOT_PASSWORD=yourStrongRootPassword
TZ=Europe/London
PGID=100
PUID=1030

# phpMyAdmin configuration
PMA_HOST=mariadb
PMA_PORT=3306
PMA_BLOWFISH_SECRET=gX0CggmAAGuKemgfhqaX8tJ8W+VZ5vL1JmeqQ7c5YB4=
```

---

### 📦 docker-compose.yml Overview

- MariaDB exposed on host port `3307`
- phpMyAdmin exposed on host port `2500`
- Internal communication via `MariaDB_Net`
- All secrets injected from `.env`

---

## 🚀 Deployment Instructions

### 🖥️ CLI Method

1. SSH into your Synology NAS or Docker host.
2. Clone this repository and `cd` into it.
3. Create the required folders and `uploads.ini` file.
4. Create your `.env` file using the template above.
5. Deploy the stack:

```bash
docker compose up -d
```

6. Access phpMyAdmin at:  
   `http://<your-nas-ip>:2500`

---

### 🧭 Deploying with Portainer

If you're using **Portainer**, follow these steps:

#### 1. Prepare Your Environment

- Ensure folders and `uploads.ini` exist as described above.
- Create your `.env` file locally.

#### 2. Log into Portainer

- Open Portainer in your browser.
- Navigate to your Docker environment.
- Go to **Stacks**.

#### 3. Create a New Stack

- Click **Add Stack**.
- Name your stack (e.g., `mariadb-stack`).
- Paste the contents of `docker-compose.yml` into the editor.
- Scroll to **Environment variables** and add each key from `.env` manually:

  | Name                 | Value                                  |
  |----------------------|----------------------------------------|
  | MYSQL_ROOT_PASSWORD | yourStrongRootPassword                 |
  | TZ                  | Europe/London                          |
  | PGID                | 100                                    |
  | PUID                | 1030                                   |
  | PMA_HOST            | mariadb                                |
  | PMA_PORT            | 3306                                   |
  | PMA_BLOWFISH_SECRET | gX0CggmAAGuKemgfhqaX8tJ8W+VZ5vL1JmeqQ7c5YB4= |

- Click **Deploy the stack**.

#### 4. Access phpMyAdmin

- Visit: `http://<your-nas-ip>:2500`
- Log in with:
  - **Username:** `root`
  - **Password:** value from `MYSQL_ROOT_PASSWORD`

---

## 🧩 MariaDB Host Access Configuration

If you see an error like:

```
mysqli::real_connect(): (HY000/1130): Host '192.168.x.x' is not allowed to connect to this MariaDB server
```

MariaDB is rejecting the connection due to host-based access control. You need to grant access to the `root` user from any host.

### 🔧 Option 1: Manual Grant (after container starts)

1. Open a shell into the MariaDB container:

```bash
docker exec -it MariaDB mysql -u root -p
```

2. Run the following SQL:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourStrongRootPassword' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

3. Restart the container:

```bash
docker restart MariaDB
```

---

### 🧬 Option 2: Auto-Grant on First Initialization

Create a file called `init.sql` in your MariaDB volume folder:

```sql
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourStrongRootPassword' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

Then mount it in your `docker-compose.yml`:

```yaml
volumes:
  - /volume1/docker/mariadb:/var/lib/mysql:rw
  - /volume1/docker/mariadb/init.sql:/docker-entrypoint-initdb.d/init.sql:ro
```

> This script only runs on first-time database initialization.

---

## 🧼 Cleanup

To stop and remove the stack:

```bash
docker compose down
```

To remove unused networks and volumes:

```bash
docker system prune -af --volumes
```

---

## 🔒 Security Notes

- MariaDB is exposed on port `3307`. Restrict access via firewall or reverse proxy if needed.
- phpMyAdmin is exposed on port `2500`. Consider securing with HTTPS or IP whitelisting.
- All secrets are stored in `.env` and excluded from version control.
- Use strong passwords and rotate them periodically.

---

## 📚 Resources

- [MariaDB 11.4 LTS Documentation](https://mariadb.com/kb/en/mariadb-114/)
- [phpMyAdmin Configuration Guide](https://docs.phpmyadmin.net/)
- [Docker Compose Reference](https://docs.docker.com/compose/)
- [Portainer Documentation](https://docs.portainer.io/)

---

## 🧠 Author Notes

This stack is designed for clarity, modularity, and safe public sharing. Contributions, improvements, and feedback are welcome.
```
