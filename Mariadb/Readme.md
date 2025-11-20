Ah, I see what’s happening. GitHub treats `Synology/MariaDB` as a **repository name**, not a folder path. So when you create a repo called `Synology/MariaDB`, GitHub automatically converts it to `synology-mariadb` (lowercase, hyphenated) because **slashes aren’t allowed in repository names**.

---

### 🧭 What You Actually Want

If you're trying to organize your local project like this:

```
Synology/
└── MariaDB/
    ├── docker-compose.yml
    ├── .env
    └── README.md
```

Then you should:

1. Create a **local folder** structure like above.
2. Initialize Git **inside `MariaDB/`**, not `Synology/`.

```bash
mkdir -p Synology/MariaDB
cd Synology/MariaDB
git init
```

3. Create a GitHub repo called **`MariaDB`** (or `synology-mariadb` if you prefer).
4. Link it:

```bash
git remote add origin https://github.com/yourusername/synology-mariadb.git
git push -u origin main
```

---

### ✅ Summary

- GitHub repo names **cannot** contain slashes (`/`)
- To keep `MariaDB` as a subfolder of `Synology`, treat `Synology` as a local parent folder, not part of the repo name
- Your GitHub repo will be named something like `synology-mariadb`, but your local structure can be nested however you like

---

Let me know if you want help setting up `.gitignore`, GitHub Actions, or organizing multiple stacks under `Synology/`.
