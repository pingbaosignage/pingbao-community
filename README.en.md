<div align="center">

# 📺 Pingbao (屏宝 / 핑바오)
### Enterprise Digital Signage Management Platform

**Pingbao** is an enterprise-grade cloud digital signage management platform (CMS) designed for centrally controlling large-scale smart signage displays and broadcasting media content through non-stop, autonomous scheduling.

[![Language](https://img.shields.io/badge/Language-English%20|%20한국어%20|%20简体中文-brightgreen.svg)](#)
[![Release](https://img.shields.io/github/v/release/pingbaosignage/pingbao-community?color=blue)](https://github.com/pingbaosignage/pingbao-community/releases)
[![Issues](https://img.shields.io/github/issues/pingbaosignage/pingbao-community)](https://github.com/pingbaosignage/pingbao-community/issues)
[![License](https://img.shields.io/badge/license-Proprietary-orange.svg)](#)

<br/>

**[ English ]** | **[ 한국어 ](README.md)** | **[ 简体中文 ](README.zh.md)**

<br/><br/>

<img src="docs/assets/en/dashboard.png" alt="Pingbao Operations Dashboard" width="100%" />
<sub><b>▲ Pingbao Operations Dashboard: Display Fleet Availability, Schedule Status, and System Health</b></sub>

</div>

---

## 🌟 Key Features

* **Hardware Standardization & Deep Remote Control**
  * Full native support for Samsung Smart Signage Standalone displays (Tizen 6.5 / SSSP 10+)
  * Remote display panel On/Off, volume control, reboot, and live screen capture monitoring
* **Autonomous Offline Playback (No Duration Limits)**
  * Displays continue 24/7/365 uninterrupted autonomous playback via local cache even during prolonged network outages
  * Seamless timeline scheduling with 24-hour Base Fallback loop and prioritized peak event slots
* **High-Performance Media & Playlist Pipeline**
  * Streaming upload support for large high-definition video (MP4, WebM) and image assets
  * Magic bytes binary signature integrity validation
* **Responsive Web Console & Multi-Language Support**
  * Fully responsive user interface covering large desktop control rooms down to mobile on-site technicians
  * Real-time tri-lingual support: **English, 한국어, 简体中文**
* **Field-Proven Lightweight Architecture**
  * Optimized stack powered by PostgreSQL + Redis + MinIO/S3
  * On-Premise (Docker / Synology DSM) and Cloud hosting options

---

## 📸 System Previews (English UI)

| Dashboard | Fleet Management |
| :---: | :---: |
| <img src="docs/assets/en/dashboard.png" width="100%" /> | <img src="docs/assets/en/device-management.png" width="100%" /> |
| **Real-time Monitoring & Operations** | **Timeline Scheduling** |
| <img src="docs/assets/en/monitoring.png" width="100%" /> | <img src="docs/assets/en/schedules.png" width="100%" /> |

---

## 📋 Full Feature Matrix

> For extensive technical specifications, please refer to the 📘 [User Guide Full Feature Matrix](docs/en/user-guide.md#5-full-feature-matrix).

| Functional Module | Key Capabilities & Descriptions | Architecture & Standards |
| :--- | :--- | :--- |
| **1. Central Dashboard** | Scope-aware fleet stats, schedule sync percentage, anomaly triage ribbon, storage meters | Multi-tenant organization scoping, MinIO/S3 storage |
| **2. Device Management** | Multi-dimensional search (inch/series/IP), hardware specs, power On/Off timers, remote controls | Samsung Tizen 6.5+ b2bapis, Redis persistent zero-loss queue |
| **3. Operations & Monitoring** | Single-pane-of-glass status & command queue, 1-click failure filters, batch retry, export | 1-Click Batch Retry, LLM Markdown report generator |
| **4. Media Library** | Large MP4/WebM/PNG streaming uploads, magic bytes check, auto-metadata, deletion locks | Multi-chunk streaming pipeline, SHA-256 signature checks |
| **5. Playlist Authoring** | Drag-and-drop sequencing, image exposure timers, Cut/Fade transitions, schedule locks | Hardware-accelerated WebGL/CSS transitions, browser preview |
| **6. Timeline Scheduling** | 24-Hour Base Fallback loop, peak event slots, 🚨 1-Click Emergency Override broadcast | Autonomous Offline Loop (indefinite disconnected playback) |
| **7. Proof of Play (PoP)** | Second-accurate playback logs, completion vs. interruption analysis, local timezone views | 15-minute encrypted batch reports, advertiser-grade stats |
| **8. Security & Licensing** | Corporate hierarchy tree, RBAC matrix, 2FA (TOTP), 5-strike lockout, offline airgap licensing | RFC 6238 TOTP, Argon2id hashing, Ed25519 digital signatures |

---

## 📖 Documentation

* 📘 [User Guide](docs/en/user-guide.md) - Device registration, media upload, playlist creation, timeline scheduling
* 🛠️ [Admin & Deployment Guide](docs/en/admin-guide.md) - Docker environment, configurations, zero-downtime upgrades and rollbacks
* ❓ [FAQ](docs/en/faq.md) - Offline playback behavior, hardware specs, troubleshooting
* 📜 [Changelog](CHANGELOG.md) - Release history and version updates

---

## 💡 Issues & Feature Requests

Feel free to open an issue for bug reports or feature suggestions in **English, Korean, or Chinese**!

* [🐛 Report a Bug](https://github.com/pingbaosignage/pingbao-community/issues/new?template=bug_report.md)
* [💡 Request a Feature](https://github.com/pingbaosignage/pingbao-community/issues/new?template=feature_request.md)

---

## 🚀 Releases & Downloads

Check the **[Releases](https://github.com/pingbaosignage/pingbao-community/releases)** page for deployment packages and release notes.
