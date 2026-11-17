# 屏宝 (Pingbao) 管理员与多云部署指南

本文档全面介绍如何在主流公有云（Oracle Cloud 甲骨文云、AWS、Google Cloud、Azure、阿里云、腾讯云）、私有化物理服务器及群晖 NAS 等多样化环境中部署**屏宝 (Pingbao)** 标牌管理系统服务端，并详细介绍如何利用 **大语言模型与 AI 智能体 (Claude Code, Cursor, Windsurf, Antigravity 等)** 实现全自动一键秒级安装与故障自愈。

---

## 1. 运行环境配置要求

* **推荐操作系统**：Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+, Oracle Linux 8/9, Debian 12), Synology DSM 7+
* **容器引擎**：Docker Engine 24.0+ 与 Docker Compose v2+
* **推荐硬件规格**：
  * **入门体验规格 (测试/小微展厅)**：2 核 vCPU, 4GB 内存, 50GB 高速 SSD (管理 1~20 台大屏)
  * **企业标准规格 (商用生产)**：4 核 vCPU, 8GB~16GB 内存, 100GB+ SSD (管理 50~500 台大屏)
  * **大规模集群规格**：8 核 vCPU, 32GB 内存, 500GB+ NVMe SSD (管理 1,000 台以上大屏)
* **网络防火墙开放端口 (入方向 Inbound)**：
  * `80/TCP`: HTTP Web 管控台及标牌终端安装包分发
  * `443/TCP`: HTTPS 双向安全加密通信
  * `3000/TCP`: 后端 API 服务端口 (Nginx 内部代理，建议不对外公开暴露)

---

## 2. 主流云厂商与私有化环境部署指南

### 2.1 甲骨文云 (Oracle Cloud Infrastructure - OCI)
甲骨文云提供强大的终身免费 (Always Free) 计算实例，是运行屏宝极具性价比的云平台。

1. **计算实例创建**：
   * 镜像系统：**Ubuntu 22.04 Minimal** 或 **Oracle Linux 8/9**
   * 规格形态：`VM.Standard.A1.Flex` (ARM 架构 4 核，24GB 内存 - Always Free) 或 `VM.Standard.E2.1.Micro` (AMD 1 核 1GB 内存)
2. **VCN 虚拟云网络安全列表 (Security Lists) 配置**：
   * 登录 Oracle 控制台 ➔ 联网 ➔ 虚拟云网络 ➔ 安全列表 ➔ 入站规则 (Ingress Rules) 添加：
     * 源 CIDR: `0.0.0.0/0`，IP 协议: `TCP`，目标端口范围: `80, 443`
3. **宿主机系统防火墙放行**：
   ```bash
   sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
   sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
   sudo netfilter-persistent save
   ```
4. **Cloudflare Edge 边缘代理配置（推荐免证书配置与 DDoS 防护）**：
   * 在 Cloudflare 控制台中将自定义域名的 `A 记录` 指向甲骨文云公网 IP，并开启 Proxy 代理（橙色小云朵），即可获得全球 CDN 加速与免维护的无缝 SSL (`https://`) 访问。

---

### 2.2 亚马逊云 (AWS - Amazon Web Services)
1. **启动 EC2 实例**：
   * AMI 镜像：`Ubuntu Server 22.04 LTS (HVM)`
   * 实例类型：`t3.small` (小规模) 或 `t3.medium` (推荐)
   * 存储空间：通用型 SSD (gp3) 100GB 及以上
2. **安全组 (Security Group) 入站规则**：
   * `HTTP` (TCP 80) ➔ `0.0.0.0/0`
   * `HTTPS` (TCP 443) ➔ `0.0.0.0/0`
   * `SSH` (TCP 22) ➔ 仅限管理员自有公网 IP 访问
3. **分配弹性公网 IP (Elastic IP)**：
   * 将弹性 IP 绑定至 EC2 实例，防止云服务器重启导致公网 IP 变动影响三星标牌终端连接。

---

### 2.3 谷歌云 (Google Cloud Platform - GCP)
1. **创建 Compute Engine 虚拟机**：
   * 机器系列：`E2` (机器类型：`e2-medium` 或 `e2-standard-2`)
   * 启动磁盘：Ubuntu 22.04 LTS，平衡永久性磁盘 100GB
   * 防火墙选项：同时勾选 **[允许 HTTP 流量]** 与 **[允许 HTTPS 流量]**
2. **确认 VPC 防火墙规则**：
   * 确认 `default-allow-http` 与 `default-allow-https` 规则已正确匹配实例的网络标签 `http-server` 与 `https-server`。

---

### 2.4 微软云 (Microsoft Azure)
1. **创建虚拟机 (Virtual Machine)**：
   * 镜像：`Ubuntu Server 22.04 LTS - x64 Gen2`
   * 大小：`Standard_B2s` (2 核 vCPU, 4GB 内存) 或以上
2. **网络安全组 (NSG) 入站端口规则**：
   * 优先级 300：`Port_80` (目标端口 80，协议 TCP，操作：允许)
   * 优先级 310：`Port_443` (目标端口 443，协议 TCP，操作：允许)

---

### 2.5 阿里云 (Alibaba Cloud) 与腾讯云 (Tencent Cloud)
1. **云服务器 ECS / CVM 选型**：
   * 实例规格：计算平衡型 `e-c2m4` (2 核 4GB) 或同等规格
   * 系统盘：ESSD 100GB
2. **安全组规则配置**：
   * 入方向放行 `TCP 80` 与 `TCP 443` 端口。
3. **中国大陆境内地域备案说明 (ICP 备案)**：
   * 若使用中国大陆境内地域（北京/上海/广州/深圳等）并绑定域名使用公网 80/443 端口，依据工信部要求必须完成**工信部网站 ICP 备案**与**公安联网备案**。
   * 若需免备案即刻上线，推荐选用**中国香港 (Hong Kong)** 或 **新加坡 (Singapore)** 地域，或直接通过公网 IP 模式访问。

---

### 2.6 私有局域网物理服务器与群晖 NAS (On-Premises / Synology)
适用于对数据安全性要求极高、视频素材完全不离开企业内网的政企私有化场景：

1. **Ubuntu / Rocky Linux 物理单机服务器**：
   * 分配内网固定 IP（如 `192.168.1.100`）。
   * 如有跨门店外网大屏访问需求，在企业网关路由器上针对该 IP 设置 `80` 和 `443` 端口转发 (Port Forwarding)。
2. **群晖 NAS (Synology DSM 7+)**：
   * 套件中心 ➔ 安装 `Container Manager` (Docker)。
   * 开启 SSH 终端，将发布包解压至 `/volume1/docker/pingbao` 并执行一键启动脚本。

---

## 3. 🤖 基于大语言模型与 AI 智能体的一键自动安装

屏宝深度拥抱 AI 原生生态，原生适配 **Claude Code, Cursor, Windsurf, Google Antigravity, Gemini CLI, OpenAI ChatGPT/Codex** 等全系列 AI 智能体终端。  
您无需记忆复杂的 Linux 指令，只需在目标服务器终端启动 AI 智能体并发送以下**万能黄金提示词 (Golden Prompt)**，AI 即可自主完成环境诊断、依赖安装、配置生成及全栈部署。

### 3.1 AI 智能体一键部署黄金提示词 (Golden Prompt)
通过 SSH 连接服务器后启动 AI 智能体 CLI（如 `claude`, `cursor`, `agy` 等），复制并发送以下指令：

```markdown
你是企业级数字标牌系统“屏宝 (Pingbao)”的专业 DevOps 工程师。
请在这台 Linux 服务器上完全搭建最新的屏宝运行环境，并完成最终的健康检查验证。

[执行步骤]
1. 诊断宿主机操作系统与基础包环境（Ubuntu/Debian, CentOS/RHEL/Oracle Linux 等）。
2. 检查 Docker Engine 与 Docker Compose v2 安装状态；若未安装，通过官方稳定源进行无人值守静默安装。
3. 检查 80 和 443 端口占用情况，并在系统防火墙（ufw, firewalld, iptables）中放行入站流量。
4. 下载 pingbaosignage/pingbao-community 官方最新发布包，或在当前发布目录下根据 `.env.example` 复制生成 `.env` 配置文件（SERVER_MASTER_KEY 和 JWT_SECRET 需随机生成 64 位强加密密钥）。
5. 执行 `docker compose -f docker-compose.onprem.yml up -d` 启动 PostgreSQL、Redis、后端 API 与 Nginx 容器集群。
6. 等待 10 秒后执行 `curl -s http://localhost/api/v1/health` 进行健康检查，确认 status: ok 后输出管控台 Web 访问链接。
```

### 3.2 AI 智能体运维故障自愈提示词
当服务器遭遇网络中断或服务异常时，向 AI 智能体发送以下提示词可快速实现自愈诊断：

```markdown
请对当前屏宝 (Pingbao) 标牌系统进行紧急运行状态诊断与自动排障：
1. 执行 `docker ps -a` 检查屏宝全量容器（dscn_server, dscn_nginx, dscn_postgres, dscn_redis）的存活状态。
2. 若发现异常退出容器，调用 `docker logs --tail 50 <容器名>` 提取并分析根因日志。
3. 检查端口监听状态 (`ss -tulpn`) 与网络安全组/防火墙入站规则。
4. 必要时安全重启容器，并验证 `http://localhost/api/v1/health` 返回正常的 HTTP 200。
```

---

## 4. 标准 Docker 手动安装流程

### 4.1 下载与解压部署包
从 GitHub Releases 官方发布页下载最新部署包（`pingbao-release.tar.gz`）：

```bash
mkdir -p /opt/pingbao && cd /opt/pingbao
tar -zxvf pingbao-release.tar.gz
```

### 4.2 配置环境变量
```bash
cp .env.example .env
nano .env
```

核心配置变量清单：
* `SERVER_DOMAIN`: 服务器对外公网 IP 或解析域名（如 `cms.mycompany.com`）
* `HTTP_PORT`: 宿主机对外暴露 HTTP 端口（默认 `80`）
* `HTTPS_PORT`: 宿主机对外暴露 HTTPS 端口（默认 `443`）
* `POSTGRES_PASSWORD`: PostgreSQL 数据库超级密码
* `REDIS_PASSWORD`: Redis 指令队列与长轮询缓存密码
* `JWT_SECRET`: 鉴权 Token 签名密钥（32 位以上字符串）
* `SERVER_MASTER_KEY`: 64 位底层数据加密主密钥（留空将自动生成）

### 4.3 运行一键安装脚本
```bash
chmod +x install.sh
./install.sh
```

安装完成后，在浏览器中访问 `http://<服务器公网IP>` 即可进入首次系统初始化向导 (First-Run OOBE)。

---

## 5. 版本平滑升级与回滚

### 5.1 零停机平滑升级
```bash
chmod +x upgrade.sh
./upgrade.sh <new_version_package.tar.gz>
```
* 数据库数据与媒体素材文件 100% 保持完整，安全挂载于宿主机卷中。
* 升级脚本会自动对比并执行 Prisma 数据表结构的差分迁移。

### 5.2 故障一键回滚
```bash
chmod +x rollback.sh
./rollback.sh
```

---

## 6. 数据备份与日常运维

* **数据库自动备份**：系统推荐配置每日凌晨自动执行 PostgreSQL 逻辑导出（保存于 `storage/backups/`）。
* **素材存储备份**：定期异地同步 `storage/media/` 媒体资源目录。
* **状态巡检接口**：`GET /api/v1/health`（返回系统运行时长、数据库连接健康度及设备加密指纹）。

---

## 7. 管理员设置与自动错误遥测 (Admin Settings & Telemetry)

屏宝 v2.4 起在**【系统设置与授权】**中提供了模块化管理界面：

### 7.1 自动化错误遥测与社区上报 (Opt-in)
* **目的**：客户现场发生未知异常时，可自发汇总异常堆栈并上报至官方社区 (`pingbaosignage/pingbao-community`)，以便研发团队快速发布修复补丁。
* **隐私脱敏保证**：所有密码、JWT 凭证、API 密钥及私有磁盘路径在发送前均会在内存中被置换为 `***MASKED***`。
* **管理员自主知情同意**：该功能默认关闭 (`DISABLED`)，必须由超级管理员手动勾选协议方可启用。
