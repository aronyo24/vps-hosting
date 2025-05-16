Sure! Here is the **full A-to-Z guide** to convert **Mailu’s SQLite database** (`main.db`) to **MySQL**, so you can:

* Migrate all domains, users, aliases, etc.
* Continue using Mailu with **MySQL backend**
* Access the database via **phpMyAdmin**
* Access phpMyAdmin via `https://mail.technoheaven.org/phpmyadmin`

---

## ✅ GOAL

> Convert `/mailu/data/main.db` (SQLite) → MySQL
> And make Mailu 2024.06 run using MySQL instead of SQLite.

---

## 🧰 WHAT YOU NEED

| Requirement     | Example Value                              |
| --------------- | ------------------------------------------ |
| MySQL DB name   | `mailudb`                                  |
| MySQL user      | `mailu`                                    |
| MySQL password  | `mailupass123`                             |
| MySQL container | `mailu-mysql-1` *(adjust as needed)*       |
| Old SQLite DB   | `/mailu/data/main.db`                      |
| phpMyAdmin      | To view database via browser               |
| Public URL      | `https://mail.technoheaven.org/phpmyadmin` |

---

## 🧱 STEP-BY-STEP: CONVERT MAILU FROM SQLITE TO MYSQL

---

### ✅ Step 1: Install prerequisites (if not already)

```bash
apt update
apt install sqlite3 -y
```

---

### ✅ Step 2: Dump the old SQLite database

```bash
cd /mailu
sqlite3 data/main.db .dump > dump.sql
```

This creates a file `dump.sql` with all your Mailu data.

---

### ✅ Step 3: Clean and convert `dump.sql` for MySQL

Run these commands to fix most compatibility issues:

```bash
sed -i 's/AUTOINCREMENT/AUTO_INCREMENT/g' dump.sql
sed -i 's/PRAGMA.*;//g' dump.sql
sed -i 's/BEGIN TRANSACTION;/START TRANSACTION;/g' dump.sql
sed -i 's/COMMIT;/COMMIT;/g' dump.sql
sed -i '/sqlite_sequence/d' dump.sql
sed -i 's/"//g' dump.sql
```

---

### ✅ Step 4: Stop your Mailu stack

```bash
docker compose down
```

---

### ✅ Step 5: Remove the old SQLite database

```bash
rm -rf /mailu/data/main.db
```

> This forces Mailu to use MySQL when starting.

---

### ✅ Step 6: Edit `mailu.env` to use MySQL

Open `/mailu/mailu.env` and ensure:

```env
DB_FLAVOR=mysql
DB_HOST=mysql
DB_PORT=3306
DB_NAME=mailudb
DB_USER=mailu
DB_PW=mailupass123
```

---

### ✅ Step 7: Edit `docker-compose.yml` to include MySQL and phpMyAdmin

At the bottom of `services:` section, add:

```yaml
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass123
      MYSQL_DATABASE: mailudb
      MYSQL_USER: mailu
      MYSQL_PASSWORD: mailupass123
    volumes:
      - ./mysql-data:/var/lib/mysql
    networks:
      - default

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: always
    environment:
      PMA_HOST: mysql
      MYSQL_ROOT_PASSWORD: rootpass123
    networks:
      - default
```

---

### ✅ Step 8: Add reverse proxy to expose phpMyAdmin at `/phpmyadmin`

Create override file:

```bash
mkdir -p /mailu/overrides/nginx
nano /mailu/overrides/nginx/phpmyadmin.conf
```

Paste:

```nginx
location /phpmyadmin {
    proxy_pass http://phpmyadmin:80;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```

Ensure your `front` service includes:

```yaml
volumes:
  - "/mailu/overrides/nginx:/overrides:ro"
```

---

### ✅ Step 9: Start MySQL container first

```bash
docker compose up -d mysql
```

---

### ✅ Step 10: Import the cleaned SQL dump

```bash
docker exec -i mailu-mysql-1 mysql -u mailu -p'mailupass123' mailudb < dump.sql
```

---

### ✅ Step 11: Start the full Mailu stack

```bash
docker compose up -d
```

---

### ✅ Step 12: Confirm MySQL is active

```bash
docker compose logs admin | grep -i mysql
```

Look for:

```
INFO [alembic.runtime.migration] Context impl MySQLImpl
```

---

### ✅ Step 13: Access Admin and phpMyAdmin

* Mailu admin: `https://mail.technoheaven.org/admin`
* phpMyAdmin: `https://mail.technoheaven.org/phpmyadmin`

Login with:

* **User**: `mailu`
* **Password**: `mailupass123`

---

## 🎉 SUCCESS: Mailu Now Runs on MySQL with Web-Based Access to phpMyAdmin!

---

## 🧠 Summary Table

| Task                      | Status/Command                             |
| ------------------------- | ------------------------------------------ |
| Backup old DB             | `cp main.db main.db.bak`                   |
| Dump SQLite               | `sqlite3 main.db .dump > dump.sql`         |
| Clean SQL                 | `sed` or manual cleanup                    |
| Setup MySQL + phpMyAdmin  | Add services to `docker-compose.yml`       |
| Reverse proxy phpMyAdmin  | NGINX override at `/phpmyadmin`            |
| Configure Mailu for MySQL | Edit `mailu.env`                           |
| Import Data               | `docker exec -i ... mysql ... < dump.sql`  |
| Restart Mailu             | `docker compose up -d`                     |
| Access Admin & DB         | `https://mail.technoheaven.org/phpmyadmin` |
