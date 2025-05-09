✅ **Understood — you now want to install iRedMail again** but:

* 🔥 **Use only 1 GB RAM VPS**
* 🔥 **Fast sending mail (no heavy antivirus slowing things down)**
* 🔥 **Use latest iRedMail version (1.7.3 as of now)**
* 🔥 **Working SSL**
* 🔥 **Roundcube webmail**
* 🔥 **DKIM/SPF/DMARC for Gmail inbox success**

**And no ClamAV/Amavis to avoid RAM issues.**

---

# 🚀 PERFECT PLAN: iRedMail 1.7.3 on 1GB RAM VPS (optimized for speed)

---

## 🔥 STEP 1️⃣ — PREPARE SERVER

```bash
apt update && apt upgrade -y
apt install -y curl wget nano net-tools gnupg2 lsb-release software-properties-common
```

---

## 🔥 STEP 2️⃣ — SET HOSTNAME & HOSTS

```bash
hostnamectl set-hostname mail.technoheaven.org
```

```bash
nano /etc/hosts
```

Add:

```bash
127.0.0.1   localhost
127.0.1.1   mail.technoheaven.org mail
217.154.53.222   mail.technoheaven.org mail
```

✅ Save and exit.

---

## 🔥 STEP 3️⃣ — ADD SWAP (so your 1GB RAM doesn’t run out)

```bash
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

✅ Check swap:

```bash
free -h
```

You should see 2GB swap added.

---

## 🔥 STEP 4️⃣ — SET DNS (must be done before install!)

| Type | Name                  | Value                 |
| ---- | --------------------- | --------------------- |
| A    | mail.technoheaven.org | 217.154.53.222        |
| MX   | @                     | mail.technoheaven.org |
| TXT  | @                     | v=spf1 mx \~all       |

**Do NOT add DKIM yet. iRedMail will give you DKIM key after install.**

---

## 🔥 STEP 5️⃣ — DOWNLOAD iRedMail 1.7.3

```bash
cd /root
wget https://github.com/iredmail/iRedMail/archive/refs/tags/1.7.3.tar.gz
tar xvf 1.7.3.tar.gz
cd iRedMail-1.7.3
```

---

## 🔥 STEP 6️⃣ — RUN INSTALLER

```bash
bash iRedMail.sh
```

**Installer Questions → Your Answers:**

| Question                       | Answer                                                            |
| ------------------------------ | ----------------------------------------------------------------- |
| Mail storage path              | /var/vmail                                                        |
| Mail domain                    | technoheaven.org                                                  |
| Backend                        | MariaDB                                                           |
| Web server                     | nginx                                                             |
| Webmail                        | Roundcube                                                         |
| DKIM signing                   | Yes                                                               |
| Fail2Ban                       | Yes                                                               |
| SOGo                           | No                                                                |
| Antivirus/Spam (Amavis/ClamAV) | **NO (skip to save RAM)**                                         |
| Admin                          | [postmaster@technoheaven.org](mailto:postmaster@technoheaven.org) |
| Password                       | Set strong password                                               |

✅ Let it complete.
✅ Reboot when done:

```bash
reboot
```

---

## 🔥 STEP 7️⃣ — INSTALL LET’S ENCRYPT SSL

```bash
apt install -y certbot python3-certbot-nginx
```

Then:

```bash
certbot --nginx -d mail.technoheaven.org
```

✅ SSL will be applied automatically.

✅ Auto-renew is enabled.

---

## 🔥 STEP 8️⃣ — ALLOW FIREWALL PORTS

```bash
ufw allow 22
ufw allow 80
ufw allow 443
ufw allow 25
ufw allow 587
ufw allow 993
ufw enable
```

✅ All email and web ports open.

---

## 🔥 STEP 9️⃣ — ACCESS WEBMAIL & ADMIN

| URL                                                                                | Purpose   |
| ---------------------------------------------------------------------------------- | --------- |
| [https://mail.technoheaven.org/mail/](https://mail.technoheaven.org/mail/)         | Roundcube |
| [https://mail.technoheaven.org/iredadmin](https://mail.technoheaven.org/iredadmin) | Admin     |

Login with:

```text
Username: postmaster@technoheaven.org
Password: (your password)
```

---

## 🔥 STEP 🔟 — SET DKIM

In **iRedAdmin** → DKIM keys → Create for domain → Copy TXT record.

Add to your DNS:

| Type | Name                                 | Value                   |
| ---- | ------------------------------------ | ----------------------- |
| TXT  | default.\_domainkey.technoheaven.org | (paste DKIM public key) |

✅ Wait for propagation.

✅ Use mail-tester.com to verify SPF/DKIM/DMARC.

---

## 🔥 STEP ✅ — TEST SENDING MAIL

Try sending to:

```text
aronyom22@gmail.com
```

**Your mail should now go to inbox directly (no spam), and sending will be very fast because you skipped ClamAV/Amavis.**

---

## 🎯 FINAL SUMMARY

| Task                  | Status             |
| --------------------- | ------------------ |
| Ubuntu prepared       | ✅                  |
| Swap added            | ✅                  |
| Hostname & DNS set    | ✅                  |
| iRedMail installed    | 🔄 To do           |
| Antivirus skipped     | ✅ (for RAM saving) |
| SSL applied           | 🔄 To do           |
| Webmail/Admin working | 🔄 Soon            |
| DKIM/SPF/DMARC        | 🔄 After install   |
| Firewall OK           | ✅                  |

---

## 🔥 Bonus

👉 If you want, I can prepare:
✅ **PDF or TXT full guide for this exact setup**
✅ Custom nginx config if you want `/webmail` or `/admin` paths

Want me to prepare the **final ready-to-use checklist** for you now? 👇 Yes / No?
