# Pingbao (屏宝 / 핑바오) Frequently Asked Questions (FAQ)

### Q1. Will screens go black or freeze if store internet disconnects?
**A:** Not at all. Pingbao is built on an **Autonomous Offline Loop** architecture. All video and image assets are pre-cached to the display's local storage prior to playback. An embedded local state machine executes scheduled playlists based on the internal clock, guaranteeing 24/7/365 uninterrupted playback even during prolonged network outages.

### Q2. Which operating systems and commercial signage hardware are supported?
**A:** Pingbao provides deep native `b2bapis` hardware integration for **Samsung Smart Signage Standalone displays** (Tizen 6.5+ / SSSP 6–10+), while also officially supporting **Windows 10/11 Kiosk PCs (`.exe`)**, **Android Signage / Tablets (`.apk`)**, **Linux Mini PCs (`.deb` / `.sh`)**, and **Zero-Install Web Launchers (`/player`)**. Its per-OS Hardware Abstraction Layer (HAL) automatically routes capabilities (hardware backlight vs. window dimming, panel power vs. DPMS sleep) across mixed-OS fleets.

### Q3. Do field technicians need to manually type the server IP on every device?
**A:** No. Every installer downloaded from the **[Player Installer Download]** modal (Samsung Tizen USB `.zip`, Windows `.exe`, Android `.apk`, Linux `.deb`/`.sh`) is automatically stamped with your configured Server Access URL. Devices connect immediately upon launch and display a 6-digit PIN code for rapid pairing.

### Q4. Are there video file size limits? What codecs are recommended?
**A:** H.264 / AAC encoded MP4 and WebM formats are recommended. The server enforces 64-byte magic-byte binary verification and direct-to-disk streaming uploads, effortlessly handling multi-gigabyte 4K Ultra-HD video assets without memory spikes.

### Q5. How does the 4-tier scheduling hierarchy and dayparting work?
**A:** Pingbao enforces an automated preemption hierarchy: `EMERGENCY` (P:100~200) > `EVENT` (P:50~99) > `REGULAR` (P:10~49) > `DEFAULT` (P:0). When peak promotion windows conclude, screens gracefully fall back to base programming with zero flickering.

### Q6. How does SyncPlay multi-screen synchronization work?
**A:** No external hardware splitters or manual configuration are required. Whenever multiple displays share the **same device group, the same assigned schedule, and the same local network (LAN)**, real-time SSE SyncPlay streams and automatic clock-offset calibration activate millisecond-accurate frame synchronization automatically.

### Q7. Does Pingbao support external integration via APIs and AI Agents (MCP)?
**A:** Yes. Pingbao provides 60+ OpenAPI 3.0 RESTful endpoints and a built-in **Model Context Protocol (MCP)** server with scoped API Keys and hourly usage analytics. AI agents (Claude Desktop, Cursor, Antigravity, and custom LLM workflows) can diagnose displays, inspect uptime, and publish schedules via natural language.

### Q8. How long are logs retained, and how is privacy safeguarded in Error Telemetry?
**A:** Operations logs, Device Operating Times, Proof-of-Play (PoP) records, and Audit Logs follow a **6-month (180-day) default retention and auto-purge lifecycle** to maintain optimal storage health. Error Telemetry is strictly Opt-in (disabled by default) and masks all sensitive tokens, passwords, IPs, and paths (`***MASKED***`) with SHA-256 fingerprint deduplication before reporting to GitHub Community Issues.
