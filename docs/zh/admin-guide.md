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
