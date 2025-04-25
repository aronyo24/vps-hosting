#!/bin/bash

# --------------------------------------
# 🔄 Full VPS Backup Script
# --------------------------------------
# Backs up website files, Apache config,
# phpMyAdmin config, and all MySQL databases.
# Outputs a single compressed archive.
# --------------------------------------

# 📍 Set output directory and timestamp
BACKUP_DIR=~/full_backup_$(date +%F_%H-%M-%S)
ARCHIVE_NAME=full_server_backup_$(date +%F_%H-%M-%S).tar.gz

# 🔐 MySQL root user (you’ll be prompted for password)
MYSQL_USER="root"

# 🚀 Start
echo "🔄 Starting full backup..."
mkdir -p "$BACKUP_DIR"

# 1️⃣ Backup Website Files
echo "📦 Backing up website files..."
sudo tar -czvf "$BACKUP_DIR/website_files_backup.tar.gz" /var/www/html/

# 2️⃣ Backup Apache Config
echo "📦 Backing up Apache configuration..."
sudo tar -czvf "$BACKUP_DIR/apache_conf_backup.tar.gz" /etc/apache2/

# 3️⃣ Backup phpMyAdmin Config (optional)
echo "📦 Backing up phpMyAdmin configuration..."
if [ -d "/etc/phpmyadmin" ]; then
  sudo tar -czvf "$BACKUP_DIR/phpmyadmin_conf_backup.tar.gz" /etc/phpmyadmin/
else
  echo "⚠️  /etc/phpmyadmin not found, skipping..."
fi

# 4️⃣ Backup MySQL Databases
echo "🗃️  Backing up all MySQL databases..."
mysqldump -u "$MYSQL_USER" -p --all-databases > "$BACKUP_DIR/all_databases_backup.sql"

# 5️⃣ Compress Full Backup
echo "📦 Compressing everything to $ARCHIVE_NAME..."
tar -czvf "$ARCHIVE_NAME" -C "$(dirname "$BACKUP_DIR")" "$(basename "$BACKUP_DIR")"

# ✅ Done
echo "✅ Full backup completed!"
echo "📁 Backup folder: $BACKUP_DIR"
echo "🗜️  Archive: $(pwd)/$ARCHIVE_NAME"
