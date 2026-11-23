# Pingbao (屏宝 / 핑바오) Frequently Asked Questions (FAQ)

### Q1. How do ‘Conversational External Sync Agents’ and the ‘AI NL Content Studio’ work together?
**A:** Pingbao cleanly decouples **live external data collection** from **visual HTML5 presentation**:
* **Conversational External Sync Agents (`External Sync Agents`)**: Ask in plain language (e.g., *"Connect live USD FX rates"* or *"Add live city weather"*) to configure the API connection, encrypt credentials in the AES-256 vault, and accumulate verified snapshots in the **Data-Feed Store**. Connected APIs are saved as **Learned API Skill Cards** for 1-click reuse.
* **AI NL Content Studio (`+ Build New Content from This Data` / `✨ Edit with AI`)**: Subscribe multiple independent 16:9 HTML5 signage widgets—such as a lobby fullscreen dashboard, a VIP lounge 7-day trend chart, and an elevator summary card—to the exact same Data Feed and customize each in plain English.

### Q2. Can we safely connect internal corporate ERP/HR APIs? What happens if an external API malfunctions?
**A:** Yes. Pingbao enforces **Anti-SSRF protection** by default and allows internal corporate endpoints only when explicitly added to the **`Private Network Allowlist`** by a Super Admin. If an upstream API returns an anomalous spike in deletions or updates, the **Mass-Change Circuit Breaker** holds the batch in the **`Quarantine Inbox`** until an administrator reviews the diff and approves it.

### Q3. What are ‘AI Smart Recipes’ inside the AI Copilot, and can AI accidentally broadcast to all stores?
**A:** At the top of **`[AI Copilot]` (`Ctrl+J`)**, **AI Smart Recipes** provide 1-click runbooks (`Weekend Store Mode`, `Night Sleep Mode`, `Morning Open Prep`) and **`[+ Learn Recent Tasks]`** to memorize your recurring multi-step routines.  
Every control action and Smart Recipe is **Human-in-the-Loop (HITL) gated**—requiring explicit **`[Approve & Execute]`** confirmation—and **Strict Device Subsetting** locks execution exclusively to your selected displays.

### Q4. Can we run all AI features completely free on an air-gapped corporate network?
**A:** 100% yes. In **`[System Settings > AI / LLM Automation]`**, select **`On-Premise / Self-Hosted (Ollama · vLLM)`** to connect directly to local open-weights models (`gemma4`, `qwen2.5`, `deepseek-r1`, `qwen2.5vl`) with zero API fees and zero external data egress, alongside 10 cloud/gateway presets (`OpenAI`, `Gemini`, `Claude`, `FreeLLMAPI`, `Qwen`, `DeepSeek`, `GLM`, `Doubao`, `Kimi`).

### Q5. Does remote live screen capture also work on dynamic HTML5 widgets and `.zip` kiosk apps?
**A:** Yes! **Multi-OS Player `v2.2.0`** captures both native Samsung SSSP hardware framebuffers and live dynamic HTML5 / touch-kiosk DOM & Canvas screens in full HD for zero-visit visual verification.

### Q6. How does Pingbao prevent expired promotions from airing or blackouts during internet outages?
**A:** **Multimodal Vision AI** automatically extracts campaign end dates (`detected_valid_until`) on upload and alerts operators via Morning Briefings and Schedule Audits if an expired promo remains scheduled. Meanwhile, **Autonomous Offline Loop** pre-caches all videos, images, and HTML5 bundles locally so playback continues uninterrupted offline.

### Q7. How do 4-Tier Scheduling, SyncPlay, and External AI Agent MCP integration work?
**A:** Schedules preempt automatically across `EMERGENCY > EVENT > REGULAR > DEFAULT`, while displays in the **same group, same schedule, and same LAN** synchronize transitions to the millisecond via **SSE SyncPlay**. External agents (`Claude Desktop`, `Cursor`, `Antigravity`, `Dify`) connect directly via Pingbao’s built-in `pingbao` MCP server and scoped API keys.
