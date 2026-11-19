# 屏宝 (Pingbao) 管理员与私有化部署指南

本文档介绍如何在私有物理服务器、群晖 NAS (Synology DSM)、公有云或 Docker 容器环境中部署、配置、维护及升级**屏宝 (Pingbao)** 标牌管理系统服务端。

---

## 1. 运行环境要求与多系统架构支持

* **服务端推荐操作系统**：Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), 群晖 Synology DSM 7+, 威联通 QNAP QTS, Oracle Linux, Windows Server 2019+
* **支持的播放终端平台 (Multi-OS)**：
  * **三星智能商用标牌**：Tizen 6.5+ / SSSP 6~10+ 全系列商用屏（`U盘自动安装包 .zip`）
  * **Windows 工控/自助终端**：Windows 10 / 11 x64（`.exe`）
  * **Android 广告机/平板**：Android 机顶盒 / 商业屏 / 平板（`.apk`）
  * **Linux 迷你主机**：Ubuntu / Debian x64（`.deb` / `.sh`）
  * **免安装网页端**：标准现代浏览器直连（`/player`）
* **容器引擎**：Docker Engine 24.0+ 与 Docker Compose v2+（裸机运行需 **Node.js 22.5+**）
* **按数据库模式 (`DB_MODE`) 推荐硬件规格**：
  * **轻量级 / 单机模式 (`DB_MODE=sqlite`，默认)**：CPU 2 核起，**内存 2GB~4GB**，20GB+ SSD（仅 `dscn-server` + `dscn-nginx` 双容器架构，适合群晖 NAS、迷你主机及 1~300 台屏幕门店独立部署）
  * **企业级 / 高可用集群模式 (`DB_MODE=postgres`)**：CPU 4 核及以上，**内存 8GB 起**（100 台以上设备推荐 16GB+），100GB+ SSD（含 PostgreSQL 16 + Redis 7 Pub/Sub 集群实时总线的 4 容器架构，适合大型连锁总部与多节点横向扩展）

---

## 2. Docker 一键部署流程

### 2.1 解压部署包

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release
```

### 2.2 配置环境变量与数据库引擎
运行交互式安装脚本会自动生成 `.env` 与权限隔离的 `.env.secret`（`0600` 权限）：

```bash
cp .env.example .env
nano .env
```

核心配置变量：
* `DB_MODE`：持久化数据库引擎选择（默认 `sqlite`，可选 `postgres`）
* `DATA_DIR`：独立隔离的数据库存储目录（默认 `./data`，与对外静态媒体目录 `./storage` 严格物理隔离）
* `DATABASE_URL` / `REDIS_URL`：当 `DB_MODE=postgres` 时使用的 PostgreSQL 16 与 Redis 7 集群连接串
* `JWT_SECRET`：鉴权签名密钥（未指定时自动由 `SERVER_MASTER_KEY` 通过 HKDF-SHA256 派生）

### 2.3 启动服务

```bash
# 交互式安装向导（可选 [1] SQLite 轻量模式 或 [2] PostgreSQL + Redis 企业集群模式）
./install.sh

# 轻量级嵌入式 SQLite WAL 模式（docker-compose.sqlite.yml）
./install.sh --silent --db-mode=sqlite --domain=signage.mycompany.com

# 企业级 PostgreSQL 16 + Redis 7 高可用模式（docker-compose.onprem.yml）
./install.sh --silent --db-mode=postgres --domain=signage.mycompany.com
```

---

## 3. 系统平滑升级与版本回退

### 3.1 安全平滑更新与自动回滚
执行升级脚本时会自动识别当前 `DB_MODE`，预先备份 `./data` 及数据库快照，并在健康检查异常时自动触发无损回滚：

```bash
./upgrade.sh
```

### 3.2 异常快速回退

```bash
./rollback.sh
```

---

## 4. 日常巡检与容灾备份

* **数据库备份**：建议执行 `./dscn-ctl.sh backup` 或每日定时备份 `./data` 目录（`pingbao.sqlite`）/ PostgreSQL Dump，并同步妥善保管 `.env.secret` 主密钥文件。
* **媒体文件同步**：定期将 `./storage` 素材卷同步至容灾备份存储。
* **健康检查与多屏同步保障**：通过 `GET /api/v1/health` 监控服务在线状态与 `db_mode`；反向代理需对 `/api/v1/device/sync-stream` SSE 流路径关闭缓冲（`proxy_buffering off`），确保多屏毫秒级 SyncPlay 同步与实时唤醒。

---

## 5. 系统设置 · 日志留存周期 · 远程诊断 · MCP 联动

**[系统设置 / 商业授权]** 工作台内置四大管理子模块：

<br/>

![系统环境设置](../assets/zh/admin-settings.png)
*▲ [系统设置 > 环境设置] 服务器访问地址自动重打包与 6 个月默认日志自动清理生命周期*

### 5.1 四大子选项卡架构
1. **商业授权管理 (`LICENSE`)**：支持内网离线环境的 Ed25519 电子签名授权激活（`.lic`）、屏幕配额监控与 `.lic-req` 申请文件生成。
2. **环境设置 (`ADMIN_SETTINGS`)**：
   * **服务器访问地址与多系统安装包自动重打包**：修改并保存主服务器访问地址后，系统自动将该地址内嵌（Stamping）至三星 Tizen U 盘包（`.zip`）、Windows（`.exe`）、Android（`.apk`）及 Linux（`.deb` / `.sh`）安装包中，实现现场零配置安装。
   * **6 个月默认日志留存与自动清理机制 (Auto-Purge Lifecycle)**：针对运维指令记录 (Operations)、设备开机时长 (Uptime)、播放凭据 (PoP) 与操作审计日志 (Audit Logs) 内置**默认 6 个月（180 天）留存周期**与自动过期清理机制，保障长期稳定运行不涨盘。
3. **远程诊断 (`DIAGNOSTICS`)**：硬件与协议兼容矩阵展示、Opt-in 隐私脱敏错误自动遥测及社区工单直通门户。
4. **API 与 AI 联动 (`CONNECTIVITY`)**：按组织、角色与操作范围隔离的 API 密钥签发、小时级 API/MCP 调用量与鉴权失败监控，以及 Claude Desktop / Cursor / Antigravity 一键 MCP 配置与 AI 提示词示例。

### 5.2 错误自动遥测与社区门户 (`DIAGNOSTICS`)
* **严格隐私脱敏**：所有密码、JWT 令牌、API 密钥、IP 与本地路径在发送前均在内存中强制替换为 `***MASKED***`，并通过 SHA-256 指纹聚合去重。
* **显式授权开启 (Opt-in)**：默认关闭（`DISABLED`），仅在超级管理员勾选隐私确认声明后生效。
* **控制台内直提工单**：无需离开管理后台，点击 `[提交缺陷反馈]` 或 `[提交功能建议]` 即可直通 `pingbaosignage/pingbao-community` 创建 Issue。
