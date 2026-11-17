# Pingbao (屏宝 / 핑바오) Frequently Asked Questions (FAQ)

### Q1. Will screens go black or freeze if store internet disconnects?
**A:** Not at all. Pingbao is built on an **Autonomous Offline Loop** architecture. All video and image assets are silently pre-cached to the display's local high-speed SSD prior to playback. An embedded SQLite state machine executes scheduled playlists based on the internal hardware RTC clock, guaranteeing 24/7/365 uninterrupted playback even during prolonged network outages.

### Q2. What commercial digital signage displays are supported?
**A:** Pingbao natively supports Samsung Smart Signage Standalone displays running Tizen 6.5+ / SSSP 10+ (QMB, QBB, QHB, QMC, QBC, QHC series). A standardized Web Player kernel is also provided for cross-platform deployment across Android commercial players and Windows PCs.

### Q3. Are there video file size limits? What codecs are recommended?
**A:** H.264 / AAC encoded MP4 and WebM formats are strongly recommended. The platform supports chunked streaming uploads and resumable transfers, effortlessly handling multi-gigabyte 4K Ultra-HD video assets.

### Q4. How does the 4-tier scheduling hierarchy and dayparting work?
**A:** Pingbao enforces an automated preemption hierarchy: `EMERGENCY` (P:100~200) > `EVENT` (P:50~99) > `REGULAR` (P:10~49) > `DEFAULT` (P:0). When peak promotion windows conclude, screens gracefully fall back to base programming without manual intervention or screen flickering.

### Q5. What is SyncPlay and is specialized video wall hardware required?
**A:** SyncPlay is a frame-accurate synchronization protocol designed for multi-display digital menu boards. Using standard LAN NTP clock alignment, concatenated displays transition simultaneously without external matrix switchers or hardware splitters, supporting synchronized ultra-wide (5760x1080) video playback.

### Q6. What data does Error Telemetry upload, and how is privacy safeguarded?
**A:** Telemetry is disabled by default (Opt-in) and requires explicit SuperAdmin consent. When exceptions occur, sensitive credentials (passwords, tokens, Bearer headers, internal/public IPs, and phone numbers) are deeply sanitized before being aggregated by SHA-256 fingerprint and reported to GitHub Community Issues.

### Q7. How can I submit bug reports or feature requests?
**A:** Visit the [Pingbao Community Issue Tracker](https://github.com/pingbaosignage/pingbao-community/issues) to submit reports using our structured templates. Once triaged with the `accepted` label by maintainers, the automated agent development workflow implements and verifies fixes. Operators can also submit issues directly from the in-console **Error Telemetry & Community** portal.
