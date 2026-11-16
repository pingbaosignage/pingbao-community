# 屏宝 (Pingbao) 管理员与私有化部署指南

本文档介绍如何在私有物理服务器、群晖 NAS (Synology DSM)、公有云或 Docker 容器环境中部署、配置、维护及升级**屏宝 (Pingbao)** 标牌管理系统服务端。

---

## 1. 运行环境配置要求

* **推荐操作系统**：Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, Oracle Linux
* **容器引擎**：Docker Engine 24.0+ 与 Docker Compose v2+
* **推荐硬件规格**：
  * CPU：4 核及以上
  * 内存：8GB 起（管理 100 台以上设备推荐 16GB+）
  * 存储：100GB 以上高速 SSD（视媒体素材储备量相应扩容）

---

## 2. Docker 一键部署流程

### 2.1 解压部署包
将官方发布的发布包上传至目标服务器并解压：

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release
```

### 2.2 配置环境变量
基于 `.env.example` 模版复制并创建 `.env` 文件，填入域名与连接信息：

```bash
cp .env.example .env
nano .env
```

核心配置变量：
* `DATABASE_URL`: PostgreSQL 数据库连接串
* `REDIS_URL`: Redis 高性能指令队列与长轮询缓存连接串
* `STORAGE_ENDPOINT`: S3 / MinIO 对象存储访问地址
* `JWT_SECRET`: 鉴权 Token 签名密钥

### 2.3 启动所有服务
使用自动化安装脚本或 Docker Compose 启动容器集群：

```bash
# 执行自动化安装脚本
./install.sh

# 或通过 Compose 后台拉起
docker compose -f docker-compose.onprem.yml up -d
```

---

## 3. 系统平滑升级与版本回退

### 3.1 零停机平滑更新
应用新版本时，数据库数据及媒体素材将完整保留：

```bash
./upgrade.sh <new_version_package.tar.gz>
```
* 系统会自动完成数据库 Schema 迁移升级。
* 现有租户与设备绑定数据不受任何影响。

### 3.2 异常快速回退
如遇突发兼容性异常，可通过快照机制快速回退至升级前版本：

```bash
./rollback.sh
```

---

## 4. 日常巡检与容灾备份

* **数据库备份**：建议设置每日凌晨自动执行 PostgreSQL Dump。
* **媒体文件同步**：定期将 MinIO / S3 素材卷同步至备用容灾存储。
* **健康状态检查**：可通过 `GET /api/v1/health` 接口监控服务在线状态。

---

## 5. 管理员系统设置与错误自动遥测 (Admin Settings & Telemetry)

自屏宝 v2.4 起，**[商业授权/设置]** 菜单新增可扩展的二级选项卡（Tab）架构，方便后续持续扩展企业管理功能。

### 5.1 二级选项卡架构
* **商业授权与配额 (`LICENSE`)**：离线 Ed25519 商业永久授权激活、终端配额监控及 `.lic-req` 证书申请文件导出。
* **管理员系统设置 (`ADMIN_SETTINGS`)**：系统自动化运维、错误自动遥测与社区反馈门户中心。

### 5.2 运行时错误自动遥测 (Opt-in & 隐私授权)
* **功能目标**：客户在现场运营过程中，若服务端或终端发生非预期运行时异常，系统将按设定周期自动脱敏并提交至官方公开社区 (`pingbaosignage/pingbao-community`)，加速官方团队排查与补丁发布。
* **严格隐私脱敏与安全保障**：
  * 所有密码、JWT 访问令牌、密钥凭证与绝对磁盘路径在传输前均强制在内存中完成 `***MASKED***` 脱敏替换。
  * 采用 SHA-256 错误指纹算法自动聚合相同原因异常，避免重复刷屏，并准确统计发生频次。
* **管理员显式授权机制 (Opt-in 规则)**：
  * 本功能默认处于关闭状态 (`DISABLED`)。
  * 管理员必须完整阅读隐私与公开数据声明，并勾选**显式同意复选框**后方可开启。
* **配置参数**：
  * 遥测总开关：开启 / 关闭
  * 提交同步周期：30 分钟、1 小时（推荐）、6 小时、24 小时
  * 立即提交 (Dispatch Now)：手动一键触发未提交错误的即时同步。
  * 本地错误日志预览表：实时查看本地持久化的异常记录、频次及已关联的 GitHub Issue 编号直达链接。

### 5.3 社区 Issue 与功能建议中心 (Manual Issue Submission)
* 管理员可在控制台界面内一键呼出弹窗，直接向官方社区提交缺陷或建议：
  * `[🐛 提交缺陷反馈]`：录入问题标题、详情与复现步骤，一键在 GitHub 生成缺陷 Issue。
  * `[💡 提交功能建议]`：向官方团队提交全新业务功能与现场改进需求。
