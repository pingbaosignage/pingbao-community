# Pingbao User Manual

**Pingbao** is an enterprise-grade cloud digital signage management platform (CMS) designed for centrally controlling large-scale smart signage displays and broadcasting media content through non-stop, autonomous scheduling.

---

## 1. Quick Start

### 1.1 Access & Login
1. Open a web browser (Google Chrome or Microsoft Edge recommended) and navigate to your Pingbao server address.
2. Sign in with your administrator or operator credentials.
3. Switch your preferred language (**English / 한국어 / 简体中文**) via the profile dropdown in the top-right corner.

### 1.2 Dashboard Overview
* **Dashboard**: Real-time overview of online/offline display statuses, total registered units, and recent activities.
* **Device Management**: Display approval, remote panel On/Off, volume control, reboot, and live screen capture.
* **Media Library**: Upload and tag media assets (MP4, WebM, PNG, JPEG, GIF, WebP).
* **Playlists**: Assemble media assets with playback sequences, transitions, and image durations.
* **Timeline Scheduling**: Drag-and-drop playlist scheduling across dates and hours with 1-click publishing.
* **Operations & Monitoring**: Real-time long-polling command queues, latency, anomaly detection, and audit logs.

<br/>

![Pingbao Dashboard](../assets/dashboard.png)
*▲ [Screen 1] Pingbao Dashboard: Fleet Availability, Schedule Status, and System Health Summary*

---

## 2. Device Management & Control

### 2.1 Registering Displays
1. Launch the Pingbao Player application on your Samsung Smart Signage display (Tizen 6.5+ / SSSP 10+).
2. Note the 6-digit **PIN Code** or Device Unique ID (DUID) shown on screen.
3. In the Pingbao Web Console, click **Device Management > Register Device** and enter the PIN.
4. Assign an installation location (e.g., "HQ Lobby 1F") and group to approve and bind.

### 2.2 Remote Controls
* **Panel Screen On/Off**: Energy-saving display panel toggle outside business hours.
* **Remote Screen Capture**: Real-time visual verification of what is currently playing on site.
* **Reboot & Emergency Notices**: One-click remote device reboot and immediate emergency broadcast overlay.

<br/>

![Pingbao Fleet Management](../assets/device-management.png)
*▲ [Screen 2] Pingbao Device Management: Display Fleet, DUIDs, Power Controls & Volume Sliders*

---

## 3. Real-Time Operations & Monitoring

### 3.1 Command Queue & Heartbeat Telemetry
* **Desired State Synchronization**: Real-time tracking of applied configuration vs. desired snapshot versions.
* **Latency & Anomaly Alarms**: Instant alerts for offline transitions or delayed media asset downloads.

<br/>

![Pingbao Monitoring](../assets/monitoring.png)
*▲ [Screen 3] Pingbao Operations & Monitoring: Long-Polling Command Queues, Heartbeats & Hardware Telemetry*

---

## 4. Timeline Scheduling & Autonomous Offline Playback

### 4.1 Timeline Scheduling
* **24-Hour Base Fallback Loop**: Default continuous loop running whenever no higher-priority event is scheduled.
* **Peak & Event Schedules**: High-priority schedules targeted for specific hours (e.g., lunch specials 11:30–13:30) or specific days of the week.

### 4.2 Autonomous Playback & Instant Publishing
* When you click **Publish**, a snapshot is transmitted to all assigned displays.
* Displays download required media in the background and perform seamless, atomic playback switching.
* Even during network outages, displays continue playing autonomously using cached schedules and local storage indefinitely.

<br/>

![Pingbao Timeline Scheduling](../assets/schedules.png)
*▲ [Screen 4] Pingbao Timeline Scheduling: 24-Hour Base Fallback Loop and Peak Event Timeslots*
