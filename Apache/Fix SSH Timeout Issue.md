If you're getting a **timeout when trying to SSH** into your server, it means the SSH port (default `22`) is either:

* 🔒 Blocked by a firewall
* ❌ SSH service not running
* 🚫 Not listening on the right interface/IP
* ⛔ Port is changed or closed

---

## ✅ Step-by-Step: Fix SSH Timeout Issue

---

### ✅ 1. Boot into Rescue Mode or use console access (already done)

You're already in the system via console (e.g., from VPS panel or VNC), so proceed with fixing from there.

---

### ✅ 2. Check if SSH service is running

```bash
systemctl status ssh
```

If it says inactive or failed:

```bash
systemctl enable ssh
systemctl start ssh
```

---

### ✅ 3. Confirm SSH is listening on port 22

```bash
ss -tulpn | grep ssh
```

Expected output:

```
LISTEN  0  128  0.0.0.0:22   *:*   users:(("sshd",pid,...))
```

If nothing shows, open `/etc/ssh/sshd_config` and ensure:

```conf
Port 22
PermitRootLogin yes
PasswordAuthentication yes
```

Then restart:

```bash
systemctl restart ssh
```

---

### ✅ 4. Check firewall (UFW or iptables)

If using UFW:

```bash
ufw allow ssh
ufw enable
```

Or if using iptables:

```bash
iptables -I INPUT -p tcp --dport 22 -j ACCEPT
```

---

### ✅ 5. Check VPS firewall or cloud provider security group

Go to your **cloud provider’s panel** (e.g., IONOS, AWS, etc.) and make sure:

* Port `22` (TCP) is **open** in the firewall or security group settings
* Source IP is allowed (e.g., `0.0.0.0/0` for open access or restrict to your IP)

---

### ✅ 6. Reboot the system

```bash
reboot
```

---

## 🔍 Optional: Test SSH locally (if allowed)

If you know your server IP:

```bash
ssh root@your_server_ip
```

Still no response? Use:

```bash
telnet your_server_ip 22
```

If that hangs or times out, the port is **still blocked** — recheck firewall and provider settings.

---

