# Pingbao Administrator & Deployment Guide

This guide describes how to deploy, configure, maintain, and upgrade the **Pingbao** server platform across on-premise servers, NAS appliances, Docker environments, or cloud infrastructure.

---

## 1. System Requirements & Supported Platforms

* **Server Operating System**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, QNAP QTS, Oracle Linux, Windows Server 2019+
* **Supported Signage Player OS Platforms**:
  * **Samsung Smart Signage**: Tizen 6.5+ / SSSP 6–10+ Standalone commercial displays (`Auto-Install USB Bundle .zip`)
  * **Windows Kiosk**: Windows 10 / 11 x64 (`.exe`)
  * **Android Signage**: Android TV / Set-Top Boxes / Tablets (`.apk`)
  * **Linux Mini PC**: Ubuntu / Debian x64 (`.deb` / `.sh`)
  * **Web Launcher**: Zero-install browser player (`/player`)
* **Container Runtime**: Docker Engine 24.0+ & Docker Compose v2+ (**Node.js 22.5+** when running bare-metal)
* **Hardware Sizing by Database Mode (`DB_MODE`)**:
  * **Lightweight / Standalone (`DB_MODE=sqlite`, Default)**: 2+ CPU Cores, **2GB–4GB RAM**, 20GB+ SSD (2-container stack: `dscn-server` + `dscn-nginx`, ideal for NAS, mini-PCs, and 1–300 displays)
  * **Enterprise / High Availability (`DB_MODE=postgres`)**: 4+ CPU Cores, **8GB+ RAM** (16GB+ recommended for 100+ active displays), 100GB+ SSD (4-container stack including PostgreSQL 16 + Redis 7 Pub/Sub cluster bus, ideal for multi-node HA clusters)

---

## 2. Docker Deployment

### 2.1 Extract Release Package

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release
```

### 2.2 Configure Environment & Database Mode
Running the interactive installer automatically generates `.env` and an isolated `.env.secret` (`0600` permissions). For manual configuration, copy `.env.example` to `.env`:

```bash
cp .env.example .env
nano .env
```

Key environment parameters:
* `DB_MODE`: Persistence engine selection (`sqlite` default, or `postgres`)
* `DATA_DIR`: Isolated database directory (`./data` default, strictly separated from public `./storage` media files)
* `DATABASE_URL` / `REDIS_URL`: PostgreSQL 16 and Redis 7 cluster connection strings when `DB_MODE=postgres`
* `JWT_SECRET`: Secret key for signing admin authentication tokens (derived via HKDF-SHA256 from `SERVER_MASTER_KEY` if omitted)

### 2.3 Starting Services

```bash
# Interactive installation wizard ([1] SQLite Lightweight or [2] PostgreSQL + Redis Enterprise)
./install.sh

# Lightweight SQLite WAL mode (docker-compose.sqlite.yml)
./install.sh --silent --db-mode=sqlite --domain=signage.mycompany.com

# Enterprise PostgreSQL + Redis HA mode (docker-compose.onprem.yml)
./install.sh --silent --db-mode=postgres --domain=signage.mycompany.com
```

---

## 3. Upgrades and Rollback

### 3.1 Safe Automated Upgrades
Automatically detects `DB_MODE`, snapshots `./data` and databases, and triggers auto-rollback if post-upgrade health checks fail:

```bash
./upgrade.sh
```

### 3.2 Instant Rollback

```bash
./rollback.sh
```

---

## 4. Monitoring & Maintenance

* **Database Backups**: Run `./dscn-ctl.sh backup` or schedule nightly backups of `./data` (`pingbao.sqlite`) / PostgreSQL dumps alongside `.env.secret`.
* **Storage Replication**: Mirror `./storage` media volumes for disaster recovery.
* **Health & Real-Time SyncPlay Checks**: Monitor server health and active `db_mode` via `GET /api/v1/health`. Ensure upstream reverse proxies keep buffering disabled (`proxy_buffering off`) on `/api/v1/device/sync-stream` for sub-millisecond multi-display SSE SyncPlay.

---

## 5. System Settings, Log Retention, Diagnostics & MCP Connectivity

The **[System Settings / License]** workspace provides four administrative sub-tabs:

<br/>

![Admin Settings](../assets/en/admin-settings.png)
*▲ [System Settings > Preferences] Server Access URL auto-stamping & 6-month default log retention lifecycle*

### 5.1 Four Administrative Sub-Tabs
1. **License Management (`LICENSE`)**: Air-gapped Ed25519 commercial license (`.lic`) verification, device quota tracking, and `.lic-req` generation.
2. **Preferences (`ADMIN_SETTINGS`)**:
   * **Server Access URL & Multi-OS Auto-Packaging**: Updating the primary Server Access URL automatically stamps and regenerates pre-configured installers for Samsung Tizen USB (`.zip`), Windows (`.exe`), Android (`.apk`), and Linux (`.deb` / `.sh`).
   * **6-Month Default Log Retention & Auto-Purge Lifecycle**: Enforces a default **6-month (180-day) retention lifecycle** with automated background purging across Operations history, Device Operating Times, Proof-of-Play (PoP) records, and Audit Logs to prevent storage exhaustion.
3. **Remote Diagnostics (`DIAGNOSTICS`)**: Platform capability matrix, Opt-in sanitized error telemetry, and integrated Community Issue submission portal.
4. **API & AI Connectivity (`CONNECTIVITY`)**: Organization- and role-scoped API Key issuance, hourly API/MCP usage & auth-failure analytics, and one-click MCP client configurations for Claude Desktop, Cursor, Antigravity, and Remote SSE.

### 5.2 Automated Error Telemetry & Community Portal (`DIAGNOSTICS`)
* **Strict Sanitization**: Passwords, JWT tokens, API secrets, IPs, and file paths are masked (`***MASKED***`) in memory prior to transmission and deduplicated via SHA-256 fingerprints.
* **Explicit Opt-in Consent**: Disabled by default (`DISABLED`) until a SuperAdmin reviews the privacy terms and enables explicit consent.
* **In-Console Issue Filing**: Submit bug reports or feature requests directly to `pingbaosignage/pingbao-community` without leaving the console.
