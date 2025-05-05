
## 🚀 FINAL (UPDATED) — A to Z Guide Without Reinstalling Apache/MySQL/phpMyAdmin

**Domain:** mail.technoheaven.org  
**Email addresses:** @technoheaven.org (like info@technoheaven.org)

---

## 🔧 STEP 1: Set Hostname

```bash
sudo hostnamectl set-hostname mail.technoheaven.org
sudo nano /etc/hosts
```

Add:
```bash
127.0.0.1 localhost
127.0.1.1 mail.technoheaven.org
```

---

## 📦 STEP 2: Install Only Mail Packages

```bash
sudo apt update
sudo apt install postfix dovecot-core dovecot-imapd dovecot-pop3d -y
```

**Postfix setup:**
- General type: **Internet Site**
- System mail name: **technoheaven.org**

*✅ No Apache or MySQL installation needed. Already installed.*

---

## ⚙️ STEP 3: Configure Postfix

```bash
sudo nano /etc/postfix/main.cf
```

Set:
```ini
myhostname = mail.technoheaven.org
myorigin = /etc/mailname
mydestination = technoheaven.org, mail.technoheaven.org, localhost.technoheaven.org, localhost
inet_interfaces = all
inet_protocols = ipv4
home_mailbox = Maildir/
```

**(TLS certs will be added after SSL step)**

```bash
sudo systemctl restart postfix
```

---

## 📥 STEP 4: Configure Dovecot

```bash
sudo nano /etc/dovecot/dovecot.conf
```

Set:
```ini
protocols = imap pop3
```

```bash
sudo nano /etc/dovecot/conf.d/10-mail.conf
```

Set:
```ini
mail_location = maildir:~/Maildir
```

```bash
sudo nano /etc/dovecot/conf.d/10-auth.conf
```

Uncomment:
```ini
disable_plaintext_auth = yes
```

```bash
sudo systemctl restart dovecot
```

---

## 👤 STEP 5: Create Mail User

```bash
sudo adduser info
sudo mkdir /home/info/Maildir
sudo chown -R info:info /home/info/Maildir
```

*This will be for info@technoheaven.org*

---

## 🌐 STEP 6: Install Roundcube (No Apache/MySQL install)

```bash
cd /var/www/
wget https://github.com/roundcube/roundcubemail/releases/download/1.6.6/roundcubemail-1.6.6-complete.tar.gz
tar -xvzf roundcubemail-1.6.6-complete.tar.gz
mv roundcubemail-1.6.6 roundcube
sudo chown -R www-data:www-data /var/www/roundcube
```

---
##Fix: Install the missing PHP mbstring package\

```bash
sudo apt install php-intl php-mbstring php-xml php-curl php-zip php-mysql -y
sudo systemctl restart apache2


```




## 🛠️ STEP 7: Apache Config for Roundcube

```bash
sudo nano /etc/apache2/sites-available/roundcube.conf
```

Paste:
```apache
<VirtualHost *:80>
    ServerName mail.technoheaven.org
    DocumentRoot /var/www/roundcube

    <Directory /var/www/roundcube/>
        Options +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/roundcube_error.log
    CustomLog ${APACHE_LOG_DIR}/roundcube_access.log combined
</VirtualHost>
```

```bash
sudo a2ensite roundcube.conf
sudo systemctl reload apache2
```

✅ No new Apache installation — just a new site config.

---

## 🔐 STEP 8: SSL for Roundcube

```bash
sudo apt install certbot python3-certbot-apache -y
sudo certbot --apache -d mail.technoheaven.org
```

✅ **After SSL is installed**, edit `/etc/postfix/main.cf` again:

```ini
smtpd_tls_cert_file=/etc/letsencrypt/live/mail.technoheaven.org/fullchain.pem
smtpd_tls_key_file=/etc/letsencrypt/live/mail.technoheaven.org/privkey.pem
smtpd_use_tls=yes
```

Restart Postfix:
```bash
sudo systemctl restart postfix
```

---

## 🧩 STEP 9: Create Roundcube Database in phpMyAdmin

**✅ Use your existing phpMyAdmin installation at:**
```text
https://www.technoheaven.org/phpmyadmin
```

1. **Create a database**: `roundcube`
2. **Create a user**: `roundcube_user`  
3. **Set password**: `roundcube_pass`  
4. **Grant all privileges** to `roundcube_user` on `roundcube` database.

---

## 🧱 STEP 10: Roundcube Web Installer

Visit:
```text
https://mail.technoheaven.org/installer
```

**Database Settings**:
- Database name: `roundcube`
- User: `roundcube_user`
- Password: `roundcube_pass`
- Host: `localhost`

**IMAP/SMTP Settings**:
- IMAP server: `localhost` port 143
- SMTP server: `localhost` port 587 (STARTTLS)

✅ After success:
```bash
sudo rm -rf /var/www/roundcube/installer
```

---

## ✅ STEP 11: Login to Webmail

Go to:
```text
https://mail.technoheaven.org
```

Login:
- Username: `info`
- Password: the one you set when creating the Linux user.

✅ **Your email address will be:** info@technoheaven.org

---

