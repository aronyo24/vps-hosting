
```bash
Mem: 1.8Gi total → (your 2GB RAM)
Swap: 2.0Gi total → (newly added swap)
```

That means:

* You now have **about 4GB total usable memory** (2GB RAM + 2GB swap).
* No risk of "out of memory" crashes during iRedMail install.

**Perfect for running iRedMail without antivirus!**

---

## 🔥 NEXT STEP: Start iRedMail installer

Make sure you are in the iRedMail-1.7.3 folder:

```bash
cd /root/iRedMail-1.7.3
bash iRedMail.sh
```

👉 When the installer asks:

| Question      | Answer                                                            |
| ------------- | ----------------------------------------------------------------- |
| Mail storage  | /var/vmail                                                        |
| Domain        | technoheaven.org                                                  |
| Backend       | MariaDB                                                           |
| Web server    | nginx                                                             |
| Webmail       | Roundcube                                                         |
| DKIM          | Yes                                                               |
| Fail2Ban      | Yes                                                               |
| Amavis/ClamAV | **No (skip antivirus)**                                           |
| Admin         | [postmaster@technoheaven.org](mailto:postmaster@technoheaven.org) |

✅ After installer completes → reboot the server:

```bash
reboot
```

---

## 🔥 REMEMBER:

* **SSL (Let’s Encrypt)** → we will apply right *after* install.
* **DKIM** → add after install.
* **No spam/antivirus** → mail sending will be super fast and light.

---

## 📝 Summary of your progress

| Step            | Status           |
| --------------- | ---------------- |
| Swap added      | ✅ Yes            |
| RAM OK          | ✅ Yes            |
| iRedMail ready  | 🔄 Starting now  |
| DNS ready       | ✅ Yes            |
| SSL             | 🔄 After install |
| Webmail & Admin | 🔄 After install |

---

If you want, I can **stay ready here** and tell you what to choose at each installer step.

👉 Start the installer now (`bash iRedMail.sh`), and tell me if it begins properly.
**Shall we proceed?** 👇 Yes / No?
