# ☁️ Private Cloud Server and Automation Hub

![Raspberry Pi](https://img.shields.io/badge/-Raspberry_Pi-C51A4A?style=for-the-badge&logo=Raspberry-Pi)
![Nextcloud](https://img.shields.io/badge/-Nextcloud-0082C9?style=for-the-badge&logo=Nextcloud&logoColor=white)
![Nginx](https://img.shields.io/badge/-NGINX-009639?style=for-the-badge&logo=nginx&logoColor=white)
![PHP](https://img.shields.io/badge/-PHP-777BB4?style=for-the-badge&logo=php&logoColor=white)
![Node-RED](https://img.shields.io/badge/-Node--RED-8F0000?style=for-the-badge&logo=node-red&logoColor=white)
![SQLite](https://img.shields.io/badge/-SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)

> **A self-hosted, air-gapped Network Attached Storage (NAS) architecture and central logic hub engineered on resource-constrained edge hardware.**

---

## 📋 Table of Contents
- [Problem Statement](#-problem-statement)
- [Architecture & Tech Stack](#-architecture--tech-stack)
- [Core Engineering Achievements](#-core-engineering-achievements)
- [Implementation Proofs](#-implementation-proofs)
- [Lessons Learned](#-lessons-learned)
- [Future Scope](#-future-scope)

---

## 🚨 Problem Statement
Relying on public cloud infrastructure for local engineering projects introduces unnecessary latency, strict storage caps, monthly subscription overhead, and a total loss of data privacy. For edge computing and smart building systems, routing local sensor data to a remote public server only to trigger a local relay is highly inefficient. 

Furthermore, a critical failure point in embedded Linux systems is the degradation of SD cards; standard flash memory quickly fails under the heavy read/write cycles required by database and cloud operations.

**The Solution:** A fully self-hosted, zero-subscription edge server that writes exclusively to external physical storage, acting as a secure "Google Drive" alternative and a centralized logic hub for local IoT networks.

---

## ⚙️ Architecture & Tech Stack

### Hardware
* **Compute Node:** Raspberry Pi 4 Model B
* **Storage Array:** External USB Flash/SSD (Bypassing internal SD)
* **Network:** Local Wi-Fi Subnet (`192.168.137.x`)

### Software Stack
* **Web Gateway:** Nginx (Reverse Proxy & Routing)
* **Application Engine:** PHP 8.4 (`php-fpm` optimized)
* **Data Platform:** Nextcloud Hub
* **IoT Logic:** Node-RED (Port `1880`)
* **Database:** SQLite

---

## 🛠️ Core Engineering Achievements

### 1. Physical Storage Override (Hardware Preservation)
To prevent premature kernel panic and SD card corruption, the Nextcloud data directory was forcefully decoupled from the root OS. Utilizing Linux command-line configurations, the storage path was permanently mounted to `/mnt/clouddrive`, ensuring all heavy data writes occur strictly on external metal.

### 2. Hardware-Constrained Engine Tuning
Running enterprise PHP applications on 1GB of RAM required manually overriding default service limits. Configured custom `php-fpm` pools:
* Increased `memory_limit` to `512M` to prevent Out-Of-Memory (OOM) process termination.
* Extended `max_execution_time` and `request_terminate_timeout` to `600s` to allow large payload processing over wireless networks without Nginx 500/504 errors.

### 3. Infinite Loop Resolution
Successfully diagnosed and resolved infinite internal redirection loops between Nginx and Nextcloud's `index.php` by analyzing `/var/log/nginx/error.log` and implementing precise `fastcgi_split_path_info` routing rules.

---

## 📸 Implementation Proofs

Below is the visual documentation of the deployed architecture functioning over the local network.

| Component | Status | Visual Proof |
| :--- | :--- | :--- |
| **Web Gateway** | Active | [Nginx Initialization](2026-06-07-190222_1364x768_scrot.png) |
| **Storage Bypass** | Verified | [Terminal Directory Output](2026-06-07-222822_1364x768_scrot.jpg) |
| **Cloud GUI** | Active | [Nextcloud Dashboard](Screenshot%202026-06-07%20202543.jpg) |
| **Data Syncing** | Verified | [Internal File Explorer](Screenshot%202026-06-07%20221548.png) |
| **IoT Engine** | Active | [Node-RED Deployment](2026-06-07-183324_1364x768_scrot.png) |

---

## 🧠 Lessons Learned
* **Log Analysis is Non-Negotiable:** When graphical interfaces throw generic "500 Internal Server Error" messages, the only path to resolution is reading the raw daemon logs.
* **Service Priorities:** Increasing limits in a configuration file means nothing if the parent service (`systemctl`) isn't restarted to ingest the new rules. 
* **Ownership vs. Permissions:** Setting `chmod 777` is a security risk; properly assigning recursive ownership (`chown -R www-data:www-data`) is the correct engineering approach to file management.

---

## 🚀 Future Scope
1. **MQTT Broker Deployment:** Install Eclipse Mosquitto to establish a lightweight publish/subscribe network for embedded edge controllers (e.g., Circadian Lighting hardware).
2. **Zero-Trust Global Tunneling:** Implement a secure Tailscale mesh VPN to allow encrypted, anywhere-in-the-world access to the NAS without exposing router ports to the public internet.

---
**Architect:** Sameer Singh  
*Electronics & Communication Engineering* | *Embedded Systems & Building Automation*
