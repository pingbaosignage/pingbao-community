# Pingbao Administrator & Deployment Guide

This guide covers deploying **Pingbao** across On-Premise, NAS, or Cloud infrastructure and configuring the **Built-In AI / LLM Automation Engine (Global Cloud, China Mainland Direct, and 100% Free Local Ollama/vLLM)**.

---

## 1. System Requirements & Architecture Options

* **Server OS**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, QNAP QTS, Oracle Linux, Windows Server 2019+
* **Supported Display Player OS**:
  * **Samsung Smart Signage**: Tizen 6.5+ / SSSP 6–10+ (`USB Auto-Installer .zip`)
  * **Windows Kiosk**: Windows 10 / 11 x64 (`.exe`)
  * **Android Signage**: Commercial Android boxes / tablets / DIDs (`.apk`)
  * **Linux Mini-PC**: Ubuntu / Debian x64 (`.deb` / `.sh`)
  * **Web Launcher**: Zero-install browser player (`/player`)
* **Container Runtime**: Docker Engine 24.0+ & Docker Compose v2+ (**Node.js 22.5+** for bare-metal)
* **Database Modes (`DB_MODE`)**:
  * **Lightweight / Standalone (`DB_MODE=sqlite`, default)**: 2+ CPU Cores, **2–4 GB RAM**, 20 GB+ SSD (`dscn-server` + `dscn-nginx`)
  * **Enterprise / High-Availability (`DB_MODE=postgres`)**: 4+ CPU Cores, **8–16 GB+ RAM**, 100 GB+ SSD (PostgreSQL 16 + Redis 7 Pub/Sub cluster bus)
  * **(Optional) Self-Hosted Local LLM (`Ollama` / `vLLM`)**: 8–16 GB+ GPU VRAM or Apple Silicon / modern CPU host for zero-cost, air-gapped inference (`gemma4:12b`, `qwen2.5:14b`, `qwen2.5vl:7b`). External cloud LLM APIs require zero extra server hardware.

---

## 2. Docker Installation

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release

# Interactive installer (1: SQLite WAL / 2: PostgreSQL + Redis HA)
./install.sh

# Silent SQLite WAL install
./install.sh --silent --db-mode=sqlite --domain=signage.mycompany.com

# Silent Enterprise PostgreSQL + Redis install
./install.sh --silent --db-mode=postgres --domain=signage.mycompany.com
```

Key `.env` & security invariants:
* `./data` (`pingbao.sqlite`) is strictly isolated from public static `./storage`.
* `.env.secret` (`0600`) stores `SERVER_MASTER_KEY` for HKDF JWT signing and **AES-256-GCM encryption of LLM API keys**.

---

## 3. Zero-Downtime Upgrade & Rollback

```bash
./upgrade.sh   # Auto-detects DB_MODE, backs up ./data, verifies health, auto-rollbacks on failure
./rollback.sh  # Instant rollback to previous snapshot & image
```

---

## 4. Backup & Health Monitoring

* **Backup**: Run `./dscn-ctl.sh backup` and archive `.env.secret` alongside `./data` and `./storage`.
* **Health & SSE SyncPlay**: Monitor `GET /api/v1/health` and keep `proxy_buffering off` on `/api/v1/device/sync-stream`.

---

## 5. System Settings (5 Admin Sub-Tabs) & AI / LLM Configuration

<br/>

| Preferences & 6-Month Log Lifecycle (`ADMIN_SETTINGS`) | AI / LLM Automation (`AI_SETTINGS`) |
| :---: | :---: |
| <img src="../assets/en/admin-settings.png" width="100%" /> | <img src="../assets/en/ai-settings.png" width="100%" /> |

1. **License Management (`LICENSE`)**: Air-gap ready Ed25519 `.lic` verification and device quota tracking.
2. **Preferences & Settings (`ADMIN_SETTINGS`)**:
   * **Auto-Stamped Multi-OS Installers**: Saving the Server Access URL automatically stamps and rebuilds `.zip`, `.exe`, `.apk`, and `.deb`/`.sh` installers.
   * **6-Month Default Log Retention (`Auto-Purge Lifecycle`)**: Automatically purges operations, uptime, PoP, and audit logs older than 180 days (configurable).
3. **★ AI / LLM Automation (`AI_SETTINGS` · `SUPER_ADMIN` Only)**:
   * **10 Built-In Provider Presets**:
     * **China Mainland Direct (No Proxy Needed)**: Alibaba `qwen`, `deepseek`, Zhipu `zhipu` (GLM-4/4V), ByteDance `doubao`, Moonshot `kimi`
     * **Global Cloud**: `qwen_intl`, `openai` (GPT-4o), `gemini` (2.5 Flash/Pro), `claude` (Sonnet/Haiku)
     * **On-Premise Air-Gapped (`ollama`)**: Connect directly to local `Ollama`, `vLLM`, or `SGLang` endpoints (`http://127.0.0.1:11434/v1`) with zero API fees and zero external data transfer.
   * **Split Text + Vision Providers**: Pair a text/reasoning model (e.g., `DeepSeek`) with a dedicated multimodal vision model (e.g., `Qwen-VL` or `Gemini`).
   * **AES-256-GCM Encrypted Keys, Live Connection Test & Daily Quota Guard**: Keys are encrypted at rest, verified via 1-click latency ping, and capped by `daily_call_limit`.
   * **4 Granular Feature Toggles**: Independently switch Vision Media Auto-Tagging, Device Telemetry AI Diagnostics, Domain Insights, and the Right-Side AI Operator Copilot (`Ctrl+J`).
4. **Remote Diagnostics (`DIAGNOSTICS`)**: Player diagnostic log bundles, opt-in PII-masked (`***MASKED***`) error telemetry, and GitHub Community issue filing.
5. **API & AI Connectivity (`CONNECTIVITY`)**: Scoped REST API keys, usage analytics, and 1-click MCP configuration for Claude Desktop, Cursor, and Antigravity.
