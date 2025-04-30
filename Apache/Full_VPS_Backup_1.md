
# 🔄 Full VPS Website Backup & Restore Guide

This guide covers how to **manually back up** and **fully restore** your websites hosted on an Apache VPS server with phpMyAdmin and MySQL.

---

## ✅ Backup Contents

- Website Files (`/var/www/html/` or custom paths)
- Apache Configuration (`/etc/apache2/`)
- phpMyAdmin Configuration (`/root/phpmyadmin`)
- All MySQL Databases
- Compressed into a single backup archive

---
## STEP 1: Create Backup Folder

```bash
mkdir -p ~/full_backup
cd ~/full_backup

```

## 📦 Step 2: Backup Everything

### 1. Backup Website Files

```bash
sudo tar -czvf website_files_backup.tar.gz /var/www/
```

> Replace `/var/www/` with your actual site directory if different.

---

### 2. Backup Apache Configuration

```bash
sudo tar -czvf apache_conf_backup.tar.gz /etc/apache2/
```

> Backup of all Apache settings and virtual host configurations.

---

### 3. Backup phpMyAdmin Configuration (Optional)

```bash
sudo tar -czvf phpmyadmin_conf_backup.tar.gz /root/phpmyadmin/
```

> Only needed if you made custom changes to phpMyAdmin settings.

---

### 4. Backup MySQL Databases

```bash
mysqldump -u root -p --all-databases > all_databases_backup.sql
```

> Enter the MySQL root password when prompted.

---


### 5.Backup SSL Certificates (Let's Encrypt)

```bash
tar -czvf ssl_letsencrypt.tar.gz /etc/letsencrypt/

```
### 6.Backup Cron Jobs

```bash
tar -czvf cron_jobs.tar.gz /etc/cron.d/


```
### 7.Backup Network Settings

```bash
tar -czvf network_conf.tar.gz /etc/hosts /etc/network/



```

### 8.Combine Everything into One Final Backup

```bash
cd ~
tar -czvf full_server_backup.tar.gz full_backup/


```



> Organize everything in one place before compression.

---

### 6. Compress the Final Backup

```bash
tar -czvf full_server_backup.tar.gz ~/full_backup/
```

> ✅ Your full backup archive is now `full_server_backup.tar.gz`.

---

## 💾 Step 2: Download Backup to Local System

```bash
scp user@your-vps-ip:/home/user/full_server_backup.tar.gz .
```

> Replace `user` and `your-vps-ip` with your actual VPS username and IP address.

---

## ♻️ Step 3: RESTORATION STEPS (On New VPS)

### 1. Upload the Backup to VPS

```bash
scp full_server_backup.tar.gz root@new-server-ip:/root/

```

> This uploads the backup archive back to the server.

---

### 2. Extract Backup Contents

```bash
tar -xzvf full_server_backup.tar.gz
cd full_backup

```

> Navigate to the folder after extraction.

---

### 3. Restore Website Files

```bash
sudo tar -xzvf website_files_backup.tar.gz -C /
```

> This restores all website data back to `/var/www/html/`.

---

### 4. Restore Apache Configuration

```bash
tar -xzvf apache2_conf.tar.gz -C /

```

> Restores your Apache server configuration.

---

### 5. Restore phpMyAdmin Configuration

```bash
tar -xzvf phpmyadmin_conf.tar.gz -C /

```



> Applies your phpMyAdmin settings if previously backed up.

---

### 6. Restore MySQL Databases

```bash
mysql -u root -p < all_databases_backup.sql
```

> Re-imports all databases.

---

### 7. Restart Services

```bash
sudo systemctl restart apache3
sudo systemctl restart mysql
```

> Make sure both web and database servers are running smoothly.

---

## ✅ Done!

Your VPS and all its services should now be restored and working as before.  
Visit your domain and phpMyAdmin panel to verify everything.

---

## 🛠️ Bonus Tips

- Schedule a cron job to automate the backup
- Use `rsync` or `rclone` to sync backups to cloud storage
- Consider also backing up:
  - `/etc/letsencrypt/` (SSL Certificates)
  - `/etc/cron.d/` (Scheduled Jobs)
  - `/etc/hosts`, `/etc/network/` for network settings

---

> 💡 **Pro Tip:** You can convert this guide into a shell script for faster recurring backups!
