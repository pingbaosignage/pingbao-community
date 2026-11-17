# Pingbao Administrator & Deployment Guide

This guide comprehensively covers how to deploy, configure, maintain, and upgrade the **Pingbao** server platform across diverse cloud infrastructures (Oracle Cloud, AWS, GCP, Azure, Alibaba/Tencent Cloud), On-Premises environments (Linux / Synology NAS), and using **LLM / AI Coding Agents (Claude Code, Cursor, Windsurf, Antigravity, etc.)** for 1-click automated setup.

---

## 1. System Requirements

* **Operating System**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+, Oracle Linux 8/9, Debian 12), Synology DSM 7+
* **Container Runtime**: Docker Engine 24.0+ & Docker Compose v2+
* **Recommended Hardware Sizing**:
  * **Minimum (Lab / Proof of Concept)**: 2 vCPUs, 4GB RAM, 50GB SSD (1~20 displays)
  * **Standard Enterprise (Production)**: 4 vCPUs, 8GB~16GB RAM, 100GB+ SSD (50~500 displays)
  * **Large-scale Cluster**: 8 vCPUs, 32GB RAM, 500GB+ NVMe SSD (1,000+ displays)
* **Network Ports (Inbound)**:
  * `80/TCP`: HTTP Web Console & device package delivery
  * `443/TCP`: HTTPS Secure Web Console & Mutual Auth API
  * `3000/TCP`: Backend API Server (Internal proxy target, recommended private)

---

## 2. Multi-Cloud & On-Premises Deployment Guide

### 2.1 Oracle Cloud Infrastructure (OCI)
Oracle Cloud offers high-performance Always Free compute instances, making it one of the most cost-effective platforms for running Pingbao.

1. **Compute Instance Provisioning**:
   * Image: **Ubuntu 22.04 Minimal** or **Oracle Linux 8/9**
   * Shape: `VM.Standard.A1.Flex` (ARM 4 OCPUs, 24GB RAM - Always Free) or `VM.Standard.E2.1.Micro` (AMD 1 OCPU, 1GB RAM)
2. **VCN Security List Configuration**:
   * Oracle Console ➔ Networking ➔ Virtual Cloud Networks ➔ Security Lists ➔ Add Ingress Rules:
     * Source CIDR: `0.0.0.0/0`, IP Protocol: `TCP`, Destination Port Range: `80, 443`
3. **OS Firewall Ingress Rules**:
   ```bash
   sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
   sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
   sudo netfilter-persistent save
   ```
4. **Cloudflare Edge Proxy (Recommended for Zero-Config SSL & DDoS Mitigation)**:
   * Point your domain's `A Record` to the Oracle public IP in the Cloudflare dashboard and toggle the Proxy status (Orange Cloud). This gives you instant SSL (`https://`) termination and global CDN caching.

---

### 2.2 AWS (Amazon Web Services)
1. **Launch EC2 Instance**:
   * AMI: `Ubuntu Server 22.04 LTS (HVM)`
   * Instance Type: `t3.small` (small scale) or `t3.medium` (recommended)
   * Storage: General Purpose SSD (gp3) 100GB or higher
2. **Security Group Inbound Rules**:
   * `HTTP` (TCP 80) ➔ `0.0.0.0/0`
   * `HTTPS` (TCP 443) ➔ `0.0.0.0/0`
   * `SSH` (TCP 22) ➔ Restricted to your administrator IP
3. **Allocate Elastic IP**:
   * Associate an Elastic IP with your EC2 instance to ensure Samsung Signage displays do not lose connection across VM reboots.

---

### 2.3 GCP (Google Cloud Platform)
1. **Create Compute Engine VM Instance**:
   * Series: `E2` (Machine Type: `e2-medium` or `e2-standard-2`)
   * Boot Disk: Ubuntu 22.04 LTS, Balanced Persistent Disk 100GB
   * Firewall: Check both **[Allow HTTP traffic]** and **[Allow HTTPS traffic]**
2. **Verify VPC Firewall Rules**:
   * Confirm that `default-allow-http` and `default-allow-https` target tags `http-server` and `https-server` are properly bound to the instance.

---

### 2.4 Microsoft Azure
1. **Create Virtual Machine**:
   * Image: `Ubuntu Server 22.04 LTS - x64 Gen2`
   * Size: `Standard_B2s` (2 vCPUs, 4GB RAM) or higher
2. **Network Security Group (NSG) Inbound Rules**:
   * Priority 300: `Port_80` (Destination port 80, Protocol TCP, Action: Allow)
   * Priority 310: `Port_443` (Destination port 443, Protocol TCP, Action: Allow)

---

### 2.5 Alibaba Cloud & Tencent Cloud
1. **ECS / CVM Instance Sizing**:
   * Instance Type: `e-c2m4` (2 Cores, 4GB RAM) or equivalent
   * System Disk: ESSD 100GB
2. **Security Group Rules (安全组)**:
   * Allow inbound traffic for `TCP 80` and `TCP 443`.
3. **Mainland China Regulatory Compliance (ICP Filing)**:
   * Binding a custom domain to public ports 80/443 on mainland Chinese regions requires an **ICP License (ICP 备案)** from the Ministry of Industry and Information Technology (MIIT).
   * For instant deployment without ICP filing, choose **Hong Kong** or **Singapore** regions, or access via direct IP.

---

### 2.6 On-Premises Linux & NAS (Synology DSM / QNAP)
Deploy Pingbao completely on-premise within your private local network without sending video content or telemetry outside:

1. **Ubuntu / Rocky Linux Bare-Metal Server**:
   * Assign a static local IP (e.g., `192.168.1.100`).
   * If remote store displays need access, configure router Port Forwarding for ports `80` and `443`.
2. **Synology NAS (DSM 7+)**:
   * Open Package Center ➔ Install `Container Manager` (Docker).
   * Enable SSH terminal, extract the release package into `/volume1/docker/pingbao`, and run `./install.sh`.

---

## 3. 🤖 AI Agent & LLM-Assisted Automated Deployment Guide

Pingbao is fully compatible with modern AI Coding Agents, including **Claude Code, Cursor, Windsurf, Google Antigravity, Gemini CLI, and OpenAI ChatGPT/Codex**.  
By providing our **Golden Prompt** to your AI agent CLI in the server terminal, the agent autonomously inspects the OS, installs Docker, prepares environment variables, boots containers, and verifies server health.

### 3.1 Golden Prompt Template for AI Agents
Connect to your server via SSH, launch your preferred AI agent CLI (`claude`, `cursor`, `agy`, etc.), and execute the following instruction:

```markdown
You are an expert DevOps engineer for the enterprise digital signage platform "Pingbao".
Please deploy the latest Pingbao operating environment on this Linux host and perform end-to-end health verification.

[Execution Steps]
1. Inspect the host OS environment and existing packages (Ubuntu/Debian, CentOS/RHEL/Oracle Linux, etc.).
2. Check whether Docker Engine and Docker Compose v2 are installed; if missing, install them unattended using official upstream repositories.
3. Verify that ports 80 and 443 are free, and ensure host firewalls (ufw, firewalld, iptables) allow inbound traffic on both ports.
4. Download the latest release from pingbaosignage/pingbao-community or copy `.env.example` to `.env` in the release directory. Generate strong 64-character random cryptographic keys for SERVER_MASTER_KEY and JWT_SECRET.
5. Launch the container stack with `docker compose -f docker-compose.onprem.yml up -d` (PostgreSQL, Redis, Backend API, Nginx).
6. Wait 10 seconds, query `curl -s http://localhost/api/v1/health` to verify normal operation (`status: ok`), and output the web console URL.
```

### 3.2 AI Agent Autonomous Troubleshooting Prompt
If your server encounters connection failures or runtime anomalies, deliver this prompt to your AI agent for automated diagnosis and recovery:

```markdown
Perform an emergency health diagnosis and autonomous recovery for this Pingbao Signage Server:
1. Run `docker ps -a` to inspect all Pingbao containers (dscn_server, dscn_nginx, dscn_postgres, dscn_redis).
2. If any container has exited or crashed, inspect its root cause with `docker logs --tail 50 <container_name>`.
3. Check port listening status (`ss -tulpn`) and firewall ingress rules for ports 80 and 443.
4. Safely restart containers if required and verify clean HTTP 200 response from `http://localhost/api/v1/health`.
```

---

## 4. Standard Manual Installation (Docker)

### 4.1 Download & Extract Package
Download the latest deployment bundle (`pingbao-release.tar.gz`) from GitHub Releases:

```bash
mkdir -p /opt/pingbao && cd /opt/pingbao
tar -zxvf pingbao-release.tar.gz
```

### 4.2 Configure Environment Variables
Copy `.env.example` to `.env` and configure target domain and security secrets:

```bash
cp .env.example .env
nano .env
```

Key configuration parameters:
* `SERVER_DOMAIN`: Public IP or FQDN (e.g., `cms.mycompany.com`)
* `HTTP_PORT`: Exposed HTTP port (Default: `80`)
* `HTTPS_PORT`: Exposed HTTPS port (Default: `443`)
* `POSTGRES_PASSWORD`: Master password for PostgreSQL
* `REDIS_PASSWORD`: Password for Redis queue & long-polling cache
* `JWT_SECRET`: Secret key (32+ chars) for signing admin JWT sessions
* `SERVER_MASTER_KEY`: 64-char hex key for PII & device encryption (auto-generated if empty)

### 4.3 Run One-Click Installer
```bash
chmod +x install.sh
./install.sh
```

Once started, navigate to `http://<YOUR_SERVER_IP>` in your browser to complete the initial Setup Wizard (First-Run OOBE).

---

## 5. Upgrade & Rollback Procedures

### 5.1 Zero-Downtime Upgrade
```bash
chmod +x upgrade.sh
./upgrade.sh <new_version_package.tar.gz>
```
* Existing PostgreSQL database and media storage volumes are 100% preserved.
* Database schema migrations are executed automatically.

### 5.2 Rollback to Previous Version
```bash
chmod +x rollback.sh
./rollback.sh
```

---

## 6. Backup & Health Monitoring

* **Automated Database Backups**: Recommended daily PostgreSQL dump (`storage/backups/`)
* **Media Asset Backups**: Periodic replication of `storage/media/`
* **Health Check API**: `GET /api/v1/health` (Returns server version, DB connectivity, and crypto fingerprint)

---

## 7. Admin Settings & Automated Error Telemetry

Starting with Pingbao v2.4, an extensible sub-tab architecture is introduced under **[System & License]** for simplified enterprise administration.

### 7.1 Automated Error Telemetry & Reporting (Opt-in)
* **Purpose**: When runtime anomalies occur in customer environments, periodic bug reports can be dispatched automatically to `pingbaosignage/pingbao-community` for rapid patching.
* **Strict Privacy Masking**: All passwords, JWT tokens, API keys, and private host paths are masked as `***MASKED***` in server memory before transmission.
* **Explicit Admin Consent**: Disabled by default (`DISABLED`); requires explicit administrator opt-in agreement.
