<div align="center">

# 📺 Pingbao (屏宝 / 핑바오)
### Enterprise Digital Signage Full-Domain Smart Management Platform (CMS)

**Pingbao (屏宝 / 핑바오)** is an enterprise-grade cloud digital signage management platform (CMS) designed for retail chains, restaurant chains, smart commercial complexes, and public hubs.  
It delivers hardware-level IoT remote control for tens of thousands of smart displays (Samsung SSSP/Tizen, Android, Linux, Windows), 24-hour visual timeline dayparting, millisecond-level multi-display synchronization (SyncPlay), and 100% autonomous offline playback.

[![Language](https://img.shields.io/badge/Language-English%20|%20简体中文%20|%20한국어-brightgreen.svg)](#)
[![Release](https://img.shields.io/github/v/release/pingbaosignage/pingbao-community?color=blue)](https://github.com/pingbaosignage/pingbao-community/releases)
[![Issues](https://img.shields.io/github/issues/pingbaosignage/pingbao-community)](https://github.com/pingbaosignage/pingbao-community/issues)
[![License](https://img.shields.io/badge/license-Proprietary-orange.svg)](#)

<br/>

**[ English (EN) ]** | **[ 简体中文 (ZH) ](README.md)** | **[ 한국어 (KO) ](README.ko.md)**

<br/><br/>

<img src="docs/assets/zh/dashboard.png" alt="Pingbao Operations Dashboard" width="100%" />
<sub><b>▲ Pingbao Operations Dashboard: Display Fleet Availability, Schedule Deployment Progress, Storage Consumption, and Fleet Health (Live Oracle Cloud Cluster Metrics)</b></sub>

</div>

---

## 🌟 Core Value Propositions

1. **Hardware Standardization & Deep IoT Remote Control**
   * Native deep integration with Samsung Smart Signage (Tizen 6.5 / SSSP 10+) and commercial Android players.
   * Remote LCD backlight panel On/Off, volume control, scheduled reboot, operating temperature tracking, and 1-second live screen capture retrieval.
2. **Autonomous Offline Playback (No Duration Limits)**
   * Powered by local edge pre-caching and an embedded SQLite state engine, displays seamlessly continue scheduled 24/7/365 broadcasting even during prolonged network outages without black screens or crashes.
3. **4-Tier Priority Timeline Scheduling Engine**
   * Hierarchical preemption architecture: `EMERGENCY` (instant disaster/VIP takeover) > `EVENT` (dayparted peak promotions) > `REGULAR` (daily schedule) > `DEFAULT` (24/7 fallback base playlist).
   * Dayparting (Breakfast / Lunch / Tea / Dinner / Night) auto-transitions smoothly without screen blinks or glitches.
4. **Multi-Display Frame-Accurate Synchronization (SyncPlay)**
   * LAN-based precision clock alignment (NTP/PTP) ensures synchronized transitions across 3 to 5 multi-screen digital menu boards and cross-screen ultra-wide video rendering.
5. **Auditable Proof-of-Play (PoP) & Business Settlement Reports**
   * Exact second-level playback audit logs, Completion Rate (%) computation, tamper-evident signatures, and one-click Excel/CSV export.
6. **Self-Healing Operations & Automated Community Error Telemetry**
   * Automatically captures backend exceptions, sanitizes sensitive data (PII/secrets), deduplicates by SHA-256 fingerprint, and dispatches error reports directly to GitHub Community Issues.

---

## 📸 Comprehensive Workspace Overview & Live Screens (All 9 Workspaces)

### 1. Operations Dashboard (Dashboard)
<img src="docs/assets/zh/dashboard.png" alt="Dashboard" width="100%" />
Consolidated fleet metrics: display online/offline percentages, schedule synchronization rate, object storage usage, and active alert ribbons.

---

### 2. Fleet & Device Management (Device Management)
<img src="docs/assets/zh/device-management.png" alt="Device Management" width="100%" />
Control thousands of screens across hierarchical organizations (HQ, Retail Center, Flagship Stores) with backlight control, volume adjustment, remote reboot, and live screenshot feedback.

---

### 3. Timeline Scheduling Engine (Schedules) —— [Core Specialization]
<img src="docs/assets/zh/schedules.png" alt="Schedules Overview" width="100%" />
Master-detail view combining organization schedule plans with an interactive 24-hour multi-track timeline, visual priority indicators, and one-click atomic publishing.

#### Detailed Slot Orchestration & Priority Matrix (Schedule Detail & Slots)
<img src="docs/assets/zh/schedule-detail.png" alt="Schedule Detail & Slots" width="100%" />
Drag-and-drop priority slot ordering (Priority 10 to 100), visual Effective Broadcast Windows calculation, and automatic preemption overlay.

---

### 4. Playlist Authoring (Playlists)
<img src="docs/assets/zh/playlists.png" alt="Playlists" width="100%" />
Combine video (MP4/WebM) and images (PNG/JPG), configure individual image exposure duration (e.g. 8s, 15s), apply GPU-accelerated crossfades, and calculate full loop cycle durations.

---

### 5. Media Asset Management (Media Assets)
<img src="docs/assets/zh/media-assets.png" alt="Media Assets" width="100%" />
Chunked streaming uploads for 4K 60fps video, binary magic bytes validation, automatic metadata extraction, and delete-protection locks for in-use assets.

---

### 6. Operations Monitoring & Diagnostics (Operations Monitoring)
<img src="docs/assets/zh/monitoring.png" alt="Operations Monitoring" width="100%" />
Real-time long-polling latency (Ping RTT), command queue inspection, 1-Click Batch Retry for network timeouts, and exportable Markdown technical diagnostic reports.

---

### 7. Proof-of-Play & Settlement Reports (Proof-of-Play & Analytics)
<img src="docs/assets/zh/reports.png" alt="Proof-of-Play Reports" width="100%" />
1-second precision device playback audit logs, Completion Rate (%) metrics distinguishing Full vs. Partial plays, and multi-tenant Excel/CSV export.

---

### 8. Multi-Tenant Organizations & RBAC (Users & RBAC)
<img src="docs/assets/zh/users.png" alt="Users and Permissions" width="100%" />
Hierarchical multi-tenant organization trees, granular CRUDX permission matrix, and RFC 6238 TOTP two-factor authentication with backup codes.

---

### 9. Commercial License & Error Telemetry (License & Telemetry)

| Commercial License (Ed25519) | Error Telemetry & Community Portal |
| :---: | :---: |
| <img src="docs/assets/zh/license.png" width="100%" /> | <img src="docs/assets/zh/telemetry.png" width="100%" /> |

* **Ed25519 Asymmetric Digital Signature**: Secure license activation verifying fleet quotas in air-gapped enterprise environments.
* **Automated Error Telemetry & Community Issue Reporting**:
  * Strict Opt-in consent required from SuperAdmin before telemetry activation.
  * Deep sanitization of passwords, API tokens, Authorization headers, internal IPs, and phone numbers.
  * SHA-256 fingerprint deduplication with automatic dispatch to [pingbaosignage/pingbao-community](https://github.com/pingbaosignage/pingbao-community/issues).
  * In-console manual feedback form for submitting bug reports and feature requests directly to GitHub.

---

## 🎯 Deep Dive: Scheduling Engine Architecture

### 1. 4-Tier Hierarchical Preemption
```
┌─────────────────────────────────────────────────────────────┐
│  Tier 1: EMERGENCY (P: 100 ~ 200)                           │
│  Disaster alarms / Fire evacuation / VIP instant takeover   │
├─────────────────────────────────────────────────────────────┤
│  Tier 2: EVENT (P: 50 ~ 99)                                 │
│  Lunch rush flash deals / Evening cocktail promotions       │
├─────────────────────────────────────────────────────────────┤
│  Tier 3: REGULAR (P: 10 ~ 49)                               │
│  Standard dayparted loops (Breakfast / Afternoon Tea)       │
├─────────────────────────────────────────────────────────────┤
│  Tier 4: DEFAULT (P: 0)                                     │
│  24/7 background fallback playlist (Zero blackout guarantee)│
└─────────────────────────────────────────────────────────────┘
```

### 2. Standard Dayparting Matrix
* **Breakfast Window (07:30 ~ 10:30)**: Morning combos & fresh coffee (`EVENT`, P:60)
* **Mid-Morning (10:30 ~ 11:30)**: Brand image & member rewards (`REGULAR`, P:20)
* **Lunch Rush (11:30 ~ 14:00)**: Best-sellers & queuing guides (`EVENT`, P:80)
* **Afternoon Tea (14:00 ~ 17:30)**: Bakery treats & drink promos (`REGULAR`, P:30)
* **Dinner & Party (17:30 ~ 21:30)**: Family dining sets & craft beer specials (`EVENT`, P:85)
* **Night / Closing (21:30 ~ 24:00)**: Closing notice & low-power posters (`DEFAULT`, P:0)

### 3. SyncPlay Multi-Display Synchronization
* NTP-synchronized display clusters ensure seamless simultaneous transitions and synchronized ultra-wide (5760x1080) video rendering across concatenated screens.

### 4. Autonomous Offline Loop
* All media assets are pre-cached locally on edge devices prior to broadcast. An embedded SQLite dispatcher executes schedules based on local hardware RTC clocks, ensuring continuous playback even during prolonged network isolation.

---

## 📋 Full Feature Matrix

| Functional Module | Key Capabilities & Descriptions | Architecture & Standards |
| :--- | :--- | :--- |
| **1. Dashboard** | Fleet overview, schedule sync rate, anomaly ribbons, storage telemetry | Multi-tenant scope isolation, MinIO/S3 metrics |
| **2. Fleet Management** | Hierarchical device tree, multi-filter, remote panel On/Off, volume, reboot | Samsung Tizen 6.5+ SSSP drivers, Redis command queue |
| **3. Scheduling Engine** | 4-tier preemption, Dayparting timeline tracks, Effective Windows analysis | SQLite local autonomous engine, atomic rollback |
| **4. Multi-Screen Sync** | Frame-accurate synchronization for multi-display menu boards, split-view rendering | LAN NTP/PTP synchronization, simultaneous transitions |
| **5. Playlist Authoring** | Video/image mixing, per-item duration, GPU crossfade transitions, loop timing | WebGL/CSS GPU acceleration, playlist schema integrity |
| **6. Media Assets** | 4K streaming upload, binary magic bytes validation, auto metadata, delete locks | MinIO/S3 object storage, SHA-256 checksums |
| **7. Monitoring & Operations** | Long-polling latency, command queue tracking, 1-Click Batch Retry, Markdown reports | Single-device fault tracing, automated self-healing |
| **8. Proof-of-Play (PoP)** | 1-second precision play audit logs, Completion Rate (%) metrics, Excel/CSV export | Edge transaction logging, audit compliance |
| **9. Security & RBAC** | Organization hierarchy, CRUDX role matrix, RFC 6238 TOTP two-factor authentication | Argon2id password hashing, enterprise security baseline |
| **10. License & Telemetry** | Ed25519 digital signature licensing, PII-sanitized error telemetry, GitHub sync | Opt-in consent required, SHA-256 error deduplication |

---

## 📖 Documentation

* 📘 [User Guide](docs/en/user-guide.md) - Device pairing, media curation, schedule authoring, and monitoring workflows
* 🛠️ [Admin Guide](docs/en/admin-guide.md) - Docker deployment, environment configuration, and maintenance
* ❓ [FAQ](docs/en/faq.md) - Offline playback mechanisms, firewall requirements, and troubleshooting
* 📜 [Changelog](CHANGELOG.md) - Release history and version updates

---

<div align="center">
<sub>Pingbao (屏宝 / 핑바오) © 2026. All rights reserved.</sub>
</div>
