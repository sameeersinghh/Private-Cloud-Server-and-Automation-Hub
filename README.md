# ☁️ Private Cloud Server and Automation Hub

![Raspberry Pi](https://img.shields.io/badge/-Raspberry_Pi-C51A4A?style=for-the-badge&logo=Raspberry-Pi)
![Nextcloud](https://img.shields.io/badge/-Nextcloud-0082C9?style=for-the-badge&logo=Nextcloud&logoColor=white)
![Nginx](https://img.shields.io/badge/-NGINX-009639?style=for-the-badge&logo=nginx&logoColor=white)
![PHP](https://img.shields.io/badge/-PHP-777BB4?style=for-the-badge&logo=php&logoColor=white)
![Node-RED](https://img.shields.io/badge/-Node--RED-8F0000?style=for-the-badge&logo=node-red&logoColor=white)
![SQLite](https://img.shields.io/badge/-SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)

> **An air-gapped, zero-subscription Network Attached Storage (NAS) architecture and logic hub, engineered on resource-constrained edge hardware for secure building automation and data sovereignty.**

---

## 🚨 The Problem Statement
In modern IoT architectures and building automation arrays, relying on public cloud infrastructure (AWS, Google Drive, Azure) introduces three critical failure points:
1. **Network Latency & Inefficiency:** Routing localized sensor data (e.g., ambient light levels) to a remote public server, only to trigger a local relay in the same room, creates unnecessary latency and breaks if the external internet drops.
2. **Data Privacy & Overhead:** Public clouds require recurring subscription models and inherently strip away total ownership of sensitive telemetry and project data.
3. **Hardware Degradation:** On the hardware level, edge devices like the Raspberry Pi utilize standard SD cards for root file systems. Continuous read/write cycles from heavy database operations inevitably cause flash memory to degrade, leading to complete kernel failure.

## 💡 The Importance of this Project
This project establishes **Data Sovereignty** and **Local Network Resilience**. 

By building a fully self-hosted, air-gapped-capable edge server, the system acts as a secure, local "Google Drive." More importantly, it lays the foundation for advanced commercial building automation. It allows API-driven hardware—such as circadian lighting controllers and LT panel automation systems—to push heavy telemetry data and execute logic locally, with zero internet reliance, zero latency, and absolute privacy. 

---

## ⚙️ System Architecture 

* **Compute Node:** Raspberry Pi 4 Model B
* **Storage Array:** External USB Flash/SSD 
* **Web Gateway:** Nginx (Reverse Proxy & HTTP Routing)
* **Application Engine:** PHP 8.4 (FastCGI Process Manager)
* **Cloud Platform:** Nextcloud Hub (Enterprise GUI)
* **Database:** SQLite
* **IoT Logic Engine:** Node-RED (Port `1880`)

---

## 🛠️ Execution Methodology (Step-by-Step Build Process)

Deploying enterprise cloud software onto 1GB of edge hardware required strict systems engineering and manual kernel-level tuning to prevent Out-Of-Memory (OOM) crashes. Here is the step-by-step execution of the architecture:

**Step 1: Hardware Initialization & Storage Override**
* Bootstrapped the **Raspberry Pi 4** with a lightweight headless Linux environment.
* To solve the SD card degradation problem, the internal filesystem was overridden. An external USB drive was permanently mounted to `/mnt/clouddrive`.
* Directory ownership was recursively assigned to `www-data` to ensure the web server had exclusive, secure write access to the physical metal.

**Step 2: Core Web Stack Setup**
* Installed **Nginx** to act as the reverse proxy and HTTP gateway.
* Installed **PHP 8.4 (FastCGI Process Manager)** to serve as the backend application engine.
* Deployed **SQLite** to handle lightweight, file-based database management, avoiding the heavy RAM overhead associated with standard SQL databases.

**Step 3: CLI Nextcloud Deployment**
* Extracted the **Nextcloud** enterprise framework into the web directory.
* To prevent browser-based memory crashes and timeouts during setup, the installation was forced via the Linux terminal using Nextcloud's `occ` command. The database was explicitly mapped to SQLite, and the data directory was pointed directly to the external USB drive (`/mnt/clouddrive`).

**Step 4: Daemon Memory Allocation & Tuning**
* Running heavy PHP logic on limited RAM required overriding default background limits. The `php-fpm` pool was manually modified to expand the `memory_limit` to `512M` and the `request_terminate_timeout` to `600s`, preventing the Pi from assassinating the process during heavy wireless data transfers.

**Step 5: Nginx Routing Matrix Debugging**
* Complex URL structures initially caused infinite internal redirection loops between Nginx and PHP (resulting in HTTP 500 errors). By analyzing the raw `/var/log/nginx/error.log`, the Nginx server block was rewritten with precise `fastcgi_split_path_info` logic, successfully breaking the loop and stabilizing the data handoff.

**Step 6: IoT Logic Engine Integration**
* Installed and configured **Node-RED** to run alongside the web server on port `1880`. This serves as the visual logic controller, prepping the NAS to ingest, process, and automate real-time hardware telemetry from local microcontrollers.

---

## 📊 Results & Visual Verification

The following implementation logs serve as concrete proof of the system's successful deployment, routing, and backend stability.

### Verification 1: Web Gateway Initialization
The Nginx daemon was successfully configured to listen on port 80 across the local sub-network, serving as the primary bridge between client hardware and the backend logic.
> **Proof:** [Nginx Gateway Active](2026-06-07-190222_1364x768_scrot.png)

### Verification 2: Raw Metal Storage Override
Terminal logs verify the successful bypass of the Pi's internal storage. The `ls -l` command proves that telemetry data (`test_a_circadian.xlsx`) and code files are being written directly to the external `/mnt/clouddrive`. Permissions are strictly sandboxed to the `www-data` web server user.
> **Proof:** [Terminal Storage Output](2026-06-07-222822_1364x768_scrot.jpg)

### Verification 3: Wireless Cloud Syncing & GUI
The Nextcloud graphical interface operating flawlessly via the `192.168.137.16` local IP. This allows seamless wireless drag-and-drop operations for heavy project files, matching the raw data queried in the terminal.
> **Proof:** [Cloud Dashboard](Screenshot%202026-06-07%20202543.jpg) | [Internal File Sync](Screenshot%202026-06-07%20221548.png)

### Verification 4: Local Edge Logic Engine
Node-RED successfully deployed and running on port `1880`. This serves as the visual logic "brain" for future automation tasks, allowing the server to process incoming hardware data arrays and trigger network responses.
> **Proof:** [Node-RED Deployment](2026-06-07-183324_1364x768_scrot.png)

---

## 🚀 Strategic Future Integration
With the secure data backbone established, the next deployment phases focus on commercial-grade hardware integration:
1. **MQTT Broker Deployment:** Installing Eclipse Mosquitto to establish a lightweight publish/subscribe network, allowing microcontrollers to instantly push embedded sensor data to the Pi.
2. **Zero-Trust Global Tunneling:** Implementing a secure Tailscale mesh VPN to allow encrypted, remote access to the NAS dashboard and IoT controls from outside the local Wi-Fi network, bypassing the need for insecure port forwarding.

---
**Architect:** Sameer Singh  
*Systems Engineering | Edge Computing | Commercial Building Automation*
---
**Architect:** Sameer Singh  
*Systems Engineering | Edge Computing | Commercial Building Automation*
**Architect:** Sameer Singh  
*Electronics & Communication Engineering* | *Embedded Systems & Building Automation*
