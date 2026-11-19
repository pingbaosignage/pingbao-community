# Pingbao Frequently Asked Questions (FAQ)

### Q1. What can the Built-In Pingbao AI Copilot Chatbot do, and how does it prevent accidental fleet-wide changes?
**A:** Press **`Ctrl+J`** or click the top-right **`[AI Copilot]`** button from any workspace to open the resizable assistant. In plain language, you can: (1) inspect live server health and Multi-OS online device counts in Markdown tables, (2) detect offline displays and expired promotional banners, (3) interpret hardware diagnostic logs, and (4) request remote volume/brightness/power/reboot commands, power timers, or smart schedule drafts.  
Every mutation is protected by a **Human-in-the-Loop (HITL) Approval Card (`APPROVAL REQUIRED`)** and **Strict Device Subsetting**—meaning commands are strictly locked to your selected/inspected screens (never accidentally broadcast to all stores) and only execute after you click **`[Approve & Execute]`**.

### Q2. Can we run Pingbao AI 100% offline/on-premise for free without external cloud API costs or data leaks?
**A:** Yes! Under **`System Settings > AI / LLM Automation`**, select **`On-Premise / OpenAI-Compatible (Ollama · vLLM)`** to connect directly to your internal Ollama or vLLM server (`gemma4`, `qwen2.5`, `deepseek-r1`, `qwen2.5vl`, etc.) with **zero API cost and zero external data egress**. Pingbao also natively supports OpenAI, Google Gemini, Anthropic Claude, and China Mainland direct providers (Alibaba Qwen, DeepSeek, Zhipu GLM, Doubao, Kimi).

### Q3. How does Pingbao AI prevent expired promotional banners from staying on store screens?
**A:** Upon media upload (or 1-click re-analysis), **Multimodal Vision AI** automatically identifies menu items, OCR text/prices, recommended dayparting windows, and visible **campaign end dates (`detected_valid_until`)**. Any expired promotional banner still assigned to an active schedule is immediately flagged in `[AI Morning Briefing]`, `[AI Schedule & Timer Audit]`, and the AI Copilot.

### Q4. Will screens go black if store internet disconnects?
**A:** Never. Pingbao's **Autonomous Offline Loop** pre-caches all video/image assets and schedule timelines onto local storage so displays continue seamless dayparting playback indefinitely offline.

### Q5. Which display operating systems and hardware are supported?
**A:** Deep native hardware control (`b2bapis`) for **Samsung Smart Signage (Tizen 6.5+ / SSSP 6–10+)**, plus unified **Multi-OS Fleet Management** across **Windows 10/11 Kiosks (`.exe`)**, **Android Signage/Tablets (`.apk`)**, **Linux Mini-PCs (`.deb`/`.sh`)**, and **Web Browsers (`/player`)**. All installers downloaded from the console automatically embed your server URL for zero-typing 6-digit PIN pairing.

### Q6. How do 4-Tier Scheduling, Auto SyncPlay, External MCP, and Log Retention work?
**A:** Schedules preempt in 4 tiers (`EMERGENCY > EVENT > REGULAR > DEFAULT`) and automatically synchronize to the millisecond (**SyncPlay**) whenever displays share the same **Group + Schedule + LAN**. External agents (Claude, Cursor, Antigravity) connect via the built-in `pingbao` **MCP server**, and all operational/uptime/PoP/audit logs follow a **6-month (180-day) auto-purge lifecycle**.
