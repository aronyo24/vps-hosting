Sure! Here is the **full A-to-Z guide** to convert **Mailu’s SQLite database** (`main.db`) to **MySQL**, so you can:

* Migrate all domains, users, aliases, etc.
* Continue using Mailu with **MySQL backend**
* Access the database via **phpMyAdmin**

---

## ✅ GOAL

> Convert `/mailu/data/main.db` (SQLite) → MySQL
> And make Mailu 2024.06 run using MySQL instead of SQLite.

---

## 🧰 WHAT YOU NEED

| Requirement     | Example Value                        |
| --------------- | ------------------------------------ |
| MySQL DB name   | `mailudb`                            |
| MySQL user      | `mailu`                              |
| MySQL password  | `mailupass123`                       |
| MySQL container | `mailu-mysql-1` *(adjust as needed)* |
| Old SQLite DB   | `/mailu/data/main.db`                |
| phpMyAdmin      | Optional, to view database           |

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
sed -i 's/"//g' dump.sql  # Remove double quotes from table/column names
```

You can also open `dump.sql` with `nano` and manually clean it if needed.

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

Open `/mailu/mailu.env` and make sure these lines are present:

```env
DB_FLAVOR=mysql
DB_HOST=mysql
DB_PORT=3306
DB_NAME=mailudb
DB_USER=mailu
DB_PW=mailupass123
```

---

### ✅ Step 7: Edit `docker-compose.yml` to include MySQL

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
```

Also optionally add `phpmyadmin`:

```yaml
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    restart: always
    ports:
      - "8081:80"
    environment:
      PMA_HOST: mysql
      MYSQL_ROOT_PASSWORD: rootpass123
    networks:
      - default
```

---

### ✅ Step 8: Start the MySQL container first

```bash
docker compose up -d mysql
```

Wait \~10 seconds for the DB to initialize.

---

### ✅ Step 9: Import the cleaned SQL dump

```bash
docker exec -i mailu-mysql-1 mysql -u mailu -p'mailupass123' mailudb < dump.sql
```

> ⚠️ Adjust `mailu-mysql-1` if your container name is different.

---

### ✅ Step 10: Start the full Mailu stack

```bash
docker compose up -d
```

---

### ✅ Step 11: Confirm it works

Check the logs:

```bash
docker compose logs admin | grep -i mysql
```

Expected:

```
INFO [alembic.runtime.migration] Context impl MySQLImpl
```

---

### ✅ Step 12: Access Admin and phpMyAdmin

* Mailu admin: `https://mail.technoheaven.org/admin`
* phpMyAdmin: `http://your-vps-ip:8081`

Login with:

* **Username:** `mailu`
* **Password:** `mailupass123`

---

## 🎉 SUCCESS: Mailu Now Runs on MySQL!

---

## 🧠 Summary Table

| Task                      | Status/Command                            |
| ------------------------- | ----------------------------------------- |
| Backup old DB             | `cp main.db main.db.bak`                  |
| Dump SQLite               | `sqlite3 main.db .dump > dump.sql`        |
| Clean SQL                 | `sed` or manual cleanup                   |
| Setup MySQL               | Add service to `docker-compose.yml`       |
| Configure Mailu for MySQL | Edit `mailu.env`                          |
| Import Data               | `docker exec -i ... mysql ... < dump.sql` |
| Restart Mailu             | `docker compose up -d`                    |

---

Would you like a **script that automates this whole process**? I can generate that for you too.
