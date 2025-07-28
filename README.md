# Snort-Config_Project

Snort NIDS Setup on Kali Linux

This project documents the complete process of installing, configuring, and running **Snort** (version 2.9.20 GRE) as a Network Intrusion Detection System (NIDS) on **Kali Linux**.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Step-by-Step Installation](#step-by-step-installation)
  - [1. Install Dependencies](#1-install-dependencies)
  - [2. Download & Install Snort](#2-download--install-snort)
  - [3. Configure Snort Directories](#3-configure-snort-directories)
  - [4. Set Up Configuration Files](#4-set-up-configuration-files)
  - [5. Install Community Rules](#5-install-community-rules)
  - [6. Validate Snort Configuration](#6-validate-snort-configuration)
  - [7. Run Snort](#7-run-snort)
- [Custom Rules](#custom-rules)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## Overview

Snort is a powerful open-source network intrusion detection system (NIDS) capable of performing real-time traffic analysis and packet logging on IP networks.

---

## Prerequisites

- Kali Linux 
- Root or sudo privileges

---

## Step-by-Step Installation

### 1. Install Dependencies
```bash
sudo apt update
sudo apt install -y build-essential libpcap-dev libpcre3-dev libdumbnet-dev bison flex zlib1g-dev liblzma-dev openssl libssl-dev libnghttp2-dev libdnet autoconf libtool
```

### 2. Download & Install Snort
```bash
wget https://www.snort.org/downloads/snort/snort-2.9.20.tar.gz
sudo tar -xvzf snort-2.9.20.tar.gz -C /usr/src/
cd /usr/src/snort-2.9.20
./configure --enable-sourcefire
make
sudo make install
```

### 3. Configure Snort Directories
```bash
sudo mkdir -p /etc/snort/rules /etc/snort/preproc_rules /var/log/snort /usr/local/lib/snort_dynamicrules
sudo touch /etc/snort/rules/local.rules
sudo touch /etc/snort/snort.conf
sudo touch /etc/snort/rules/white_list.rules /etc/snort/rules/black_list.rules
```

### 4. Set Up Configuration Files
- Create `/etc/snort/snort.conf`
- Define:
```conf
var RULE_PATH /etc/snort/rules
var WHITE_LIST_PATH /etc/snort/rules
var BLACK_LIST_PATH /etc/snort/rules
include $RULE_PATH/local.rules
include $RULE_PATH/community-rules.rules
```

- Ensure the rules included match the actual files in `/etc/snort/rules/`

### 5. Install Community Rules
```bash
wget https://www.snort.org/downloads/community/community-rules.tar.gz -O /tmp/community-rules.tar.gz
sudo tar -xvzf /tmp/community-rules.tar.gz -C /etc/snort/rules
sudo mv /etc/snort/rules/community-rules/* /etc/snort/rules/
sudo rm -r /etc/snort/rules/community-rules
```

Ensure the `community.rules` file is included in `snort.conf`:
```conf
include $RULE_PATH/community.rules
```

### 6. Validate Snort Configuration
```bash
sudo /usr/local/bin/snort -T -c /etc/snort/snort.conf
```

Expected output:
```
Snort successfully validated the configuration!
Snort exiting
```

### 7. Run Snort
```bash
sudo /usr/local/bin/snort -i eth0 -c /etc/snort/snort.conf -A console
```

> Replace `eth0` with your actual network interface. Use `ip a` to check.

---

## Custom Rules
Edit `local.rules` to add your own detection rules:
```bash
sudo nano /etc/snort/rules/local.rules
```

---

## Testing
You can test rules using `ping`, `nmap`, or generate custom traffic.

Example:
```bash
ping -c 1 192.168.1.1
```
Check console output for alerts (if matching rules exist).

---

## Troubleshooting

- **Missing rules error:** Ensure the mentioned file exists in `/etc/snort/rules/`
- **Permission denied:** Always run Snort as root using `sudo`
- **Unknown interface:** Use `ip link` to find the correct interface name

---



---



