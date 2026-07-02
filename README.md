# Termux Security Toolkit

[![GitHub stars](https://img.shields.io/github/stars/ykrishhh/termux-security-toolkit?style=social)](https://github.com/ykrishhh/termux-security-toolkit)
[![GitHub forks](https://img.shields.io/github/forks/ykrishhh/termux-security-toolkit?style=social)](https://github.com/ykrishhh/termux-security-toolkit/network/members)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Termux](https://img.shields.io/badge/Platform-Termux-0D1117?logo=android)](https://termux.dev)
[![Ethical Hacking](https://img.shields.io/badge/Use-Only%20Authorized-red)](#disclaimer)
[![OS](https://img.shields.io/badge/OS-Android-3DDC84?logo=android)](https://www.android.com/)

> Complete security toolkit for Termux on Android — network scanning, password cracking, web vulnerability assessment, wireless auditing, forensics, and OSINT gathering, all from your phone.

Turn your Android device into a portable penetration testing workstation. This repository contains verified installation commands, configuration scripts, and ready-to-use cheat sheets for 30+ security tools running natively in Termux.

---

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Setup](#quick-setup)
- [Network Scanning](#network-scanning)
- [Password Attacks](#password-attacks)
- [Web Application Security](#web-application-security)
- [Wireless Network Auditing](#wireless-network-auditing)
- [Digital Forensics](#digital-forensics)
- [OSINT Tools](#osint-tools)
- [Custom Scripts](#custom-scripts)
- [Resource Optimization](#resource-optimization)
- [Disclaimer](#disclaimer)
- [License](#license)

---

## Prerequisites

Before installing anything, ensure your environment is ready.

```bash
# Update package manager and system
pkg update && pkg upgrade -y

# Install essential build dependencies
pkg install -y python git clang make cmake pkg-config

# Install Termux API for device interaction
pkg install termux-api -y

# Install storage access
termux-setup-storage
```

---

## Quick Setup

Clone this repository and run the automated setup.

```bash
# Clone the toolkit
git clone https://github.com/ykrishhh/termux-security-toolkit.git
cd termux-security-toolkit

# Run full installer (installs all tools)
chmod +x setup.sh
./setup.sh

# Or install individual modules
chmod +x setup.sh
./setup.sh --only network    # Network tools only
./setup.sh --only password   # Password tools only
./setup.sh --only web        # Web security tools only
```

---

## Network Scanning

Discover hosts, enumerate services, and map attack surfaces from your phone.

### Nmap — Network Mapper

```bash
pkg install nmap -y

# Quick host discovery on local network
nmap -sn 192.168.1.0/24

# Service version detection with scripts
nmap -sV -sC -O target_ip

# Stealth SYN scan
nmap -sS -T4 target_ip

# Full TCP port scan with output
nmap -p- -oN full_scan.txt target_ip

# Vulnerability scan using default NSE scripts
nmap --script vuln target_ip

# UDP service discovery
nmap -sU --top-ports 100 target_ip
```

### Netcat — TCP/UDP Swiss Army Knife

```bash
pkg install netcat-openbsd -y

# Port scan
nc -zv target_ip 1-1000

# Start reverse shell listener
nc -lvnp 4444

# File transfer receiver
nc -lvnp 9999 > received_file
```

### Masscan — High-Speed Port Scanner

```bash
pkg install masscan -y

# Scan entire /16 in under 5 minutes
masscan 192.168.0.0/16 -p0-65535 --rate=10000 -oJ results.json
```

### Hping3 — Packet Crafting

```bash
pkg install hping3 -y

# SYN flood test (authorized targets only)
hping3 -S target_ip -p 80 --flood

# Traceroute
hping3 -T target_ip

# ICMP ping sweep
hping3 -1 target_ip
```

---

## Password Attacks

Credential brute-forcing, hash cracking, and offline password auditing.

### Hashcat Setup (CPU Mode)

```bash
# Install hashcat and dependencies
pkg install hashcat ocl-icd -y

# Create a wordlist from system dictionaries
cat /data/data/com.termux/files/usr/share/dict/* > wordlist.txt

# Crack MD5 hashes
hashcat -m 0 hash.txt wordlist.txt

# Crack SHA-256 hashes
hashcat -m 1400 hash.txt wordlist.txt

# Crack NTLM hashes
hashcat -m 1000 hash.txt wordlist.txt

# Generate rules for mutations
hashcat -r /data/data/com.termux/files/usr/share/hashcat/rules/best64.rule
```

### John the Ripper

```bash
pkg install john -y

# Identify hash type automatically
john --list=formats

# Crack password hash file
john hash.txt

# Use specific wordlist
john --wordlist=wordlist.txt hash.txt

# Show cracked passwords
john --show hash.txt

# Zip archive password cracking
john --format=zip hash.txt
```

### Hydra — Online Password Brute-Forcer

```bash
pkg install hydra -y

# SSH brute force
hydra -l admin -P wordlist.txt ssh://target_ip

# HTTP basic auth brute force
hydra -l admin -P wordlist.txt target_ip http-get /admin

# FTP brute force
hydra -l root -P wordlist.txt ftp://target_ip

# WordPress login brute force
hydra -l admin -P wordlist.txt target_ip http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:F=incorrect"
```

### CeWL — Custom Wordlist Generator

```bash
pip install cewl

# Generate wordlist from target website
cewl -d 3 -m 5 https://target.com -w custom_wordlist.txt

# Include email addresses
cewl -d 2 -m 4 --email_file emails.txt https://target.com -w wordlist.txt
```

---

## Web Application Security

Web vulnerability scanning, SQL injection, directory brute-forcing, and XSS detection.

### Nikto — Web Server Scanner

```bash
pkg install nikto -y

# Basic scan
nikto -h https://target.com

# Scan with specific port
nikto -h target_ip -p 8080

# Evasion techniques
nikto -h target.com -evasion 1,2,3
```

### SQLMap — SQL Injection Automation

```bash
pip install sqlmap

# Test a URL parameter
sqlmap -u "https://target.com/page?id=1" --batch

# Post request injection
sqlmap -u "https://target.com/login" --data="user=admin&pass=test" --batch

# Dump database
sqlmap -u "https://target.com/page?id=1" --dump --batch

# Bypass WAF
sqlmap -u "https://target.com/page?id=1" --tamper=space2comment,between --batch

# Shell access via SQL injection
sqlmap -u "https://target.com/page?id=1" --os-shell
```

### Dirb / Gobuster — Directory Brute-Forcing

```bash
pkg install dirb -y

# Directory scan with common wordlist
dirb https://target.com

# Gobuster (faster alternative)
pkg install gobuster -y

gobuster dir -u https://target.com -w /data/data/com.termux/files/usr/share/wordlists/dirb/common.txt -x php,html,txt
```

### WPScan — WordPress Scanner

```bash
gem install wpscan

# Scan WordPress installation
wpscan --url https://target.com

# Enumerate plugins and themes
wpscan --url https://target.com --enumerate ap,at

# Brute force WordPress credentials
wpscan --url https://target.com --passwords wordlist.txt --usernames admin
```

### XSSer — Cross-Site Scripting Detection

```bash
pip install xsser

# Test for reflected XSS
xsser -u "https://target.com/search?q=test" --auto
```

### WhatWeb — Web Technology Fingerprinting

```bash
pkg install whatweb -y

whatweb https://target.com
whatweb -v https://target.com
```

---

## Wireless Network Auditing

WiFi monitoring, packet capture, handshake cracking, and rogue AP detection.

### Aircrack-ng Suite (Requires Root + USB Adapter)

```bash
# Dependencies
pkg install root-repo -y
pkg install tsu aircrack-ng -y

# Put adapter in monitor mode (requires root)
tsu
airmon-ng start wlan0

# Capture traffic
airodump-ng wlan0mon

# Target specific network and capture handshake
airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w capture wlan0mon

# Deauth client to force handshake (authorized targets ONLY)
aireplay-ng --deauth 10 -a AA:BB:CC:DD:EE:FF wlan0mon

# Crack WPA handshake
aircrack-ng -w wordlist.txt capture-01.cap
```

### Wifite2 — Automated WiFi Auditor

```bash
pip install wifite2

# Run automated audit (requires root + adapter)
tsu
wifite
```

### Reaver — WPS PIN Attack

```bash
pkg install reaver -y

# WPS PIN brute force
reaver -i wlan0mon -b AA:BB:CC:DD:EE:FF -vv
```

---

## Digital Forensics

Memory analysis, file carving, binary inspection, and evidence extraction.

### Binwalk — Firmware Extraction

```bash
pip install binwalk

# Extract firmware image
binwalk firmware.bin

# Extract recursively with entropy analysis
binwalk -eM firmware.bin
```

### Strings — Hidden Text Extraction

```bash
# Find printable strings in binary
strings suspicious_binary

# Find Unicode strings
strings -el suspicious_binary

# Search for specific patterns (API keys, emails)
strings firmware.bin | grep -iE "key|token|password|@.*\.com"
```

### ExifTool — Metadata Extraction

```bash
pkg install perl -y
pip install image-exiftool

# Read all metadata from an image
exiftool photo.jpg

# Strip metadata
exiftool -all= photo.jpg

# Batch extract from directory
exiftool -r /path/to/images/
```

### foremost / Scalpel — File Carving

```bash
pkg install foremost -y

# Recover deleted files from disk image
foremost -i disk_image.img -o output/

# Scalpel (more file types)
pkg install scalpel -y
scalpel disk_image.img -o output/
```

### Volatility (Lite) — Memory Analysis

```bash
pip install volatility3

# List processes from memory dump
volatility3 -f memdump.raw windows.pslist

# Extract network connections
volatility3 -f memdump.raw windows.netscan
```

---

## OSINT Tools

Open-source intelligence gathering from public data sources.

### theHarvester — Email and Subdomain Enumeration

```bash
pip install theHarvester

# Gather emails and subdomains from Google
theHarvester -d target.com -b google,bing,duckduckgo

# Save output
theHarvester -d target.com -b all -f report.html
```

### Recon-ng — Reconnaissance Framework

```bash
pip install recon-ng

# Create workspace
recon-ng -w project

# Install marketplace modules
workspaces create project
marketplace install all
```

### Sherlock — Username Search

```bash
pip install sherlock

# Search username across 300+ platforms
sherlock username_here

# Output to file
sherlock username_here --output results.txt
```

### Photon — Web Crawler for OSINT

```bash
pip install photon

# Crawl target for emails, social profiles, files
photon -u https://target.com -o output --keys
```

### SpiderFoot — Automated OSINT

```bash
pip install spiderfoot

# Run full OSINT scan on a domain
spiderfoot -s target.com -m sfp_dnsresolve,sfp_certificate
```

---

## Custom Scripts

Ready-made scripts included in this repository.

| Script | Purpose | Usage |
|--------|---------|-------|
| `quick_scan.sh` | Automated Nmap scan with NSE scripts | `./quick_scan.sh <target_ip>` |
| `hash_crack.sh` | Auto-detect hash type and attempt crack | `./hash_crack.sh <hash_file>` |
| `web_recon.sh` | Combined directory, technology, and vuln scan | `./web_recon.sh <target_url>` |
| `wifi_audit.sh` | Wireless network enumeration and handshake capture | `./wifi_audit.sh` |
| `forensics_extract.sh` | Multi-tool firmware extraction pipeline | `./forensics_extract.sh <firmware>` |
| `osint_collect.sh` | Aggregate OSINT from multiple sources | `./osint_collect.sh <target>` |
| `update_all.sh` | Update all installed security tools | `./update_all.sh` |

### Example: Quick Network Audit

```bash
# Automated scan of local network
./quick_scan.sh 192.168.1.0/24

# Output saved to scans/ directory with timestamp
ls scans/
# 2025-01-15_143022_scan.txt
```

---

## Resource Optimization

Termux runs on mobile hardware — these tips keep tools responsive.

### Swap Space

```bash
# Create 1GB swap file for memory-intensive operations
dd if=/dev/zero of=~/swapfile bs=1M count=1024
mkswap ~/swapfile
swapon ~/swapfile
```

### Proot (Root Without Rooting)

```bash
# Install proot-distro for full Linux environments
pkg install proot-distro -y

# Install Ubuntu inside Termux
proot-distro install ubuntu

# Launch Ubuntu shell
proot-distro login ubuntu
```

### Performance Tips

- Use `--min-rate` with Nmap for faster scans on mobile
- Limit hashcat's workload profile: `-w 2` (lighter on battery)
- Run long tasks with `nohup` or `screen` to survive terminal disconnect
- Store wordlists on shared storage to avoid repeated downloads

---

## Disclaimer

**This toolkit is intended for authorized security testing and educational purposes only.**

- Obtain explicit written permission before testing any system you do not own
- Unauthorized access to computer systems is illegal and punishable by law
- The author assumes no liability for misuse of these tools
- Always follow responsible disclosure practices

---

## License

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)

Licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

<p align="center">
  <sub>Built by <a href="https://github.com/ykrishhh">ykrishhh</a> — Security Researcher & Developer</sub>
</p>
