# Pingbao Administrator & Deployment Guide

This guide describes how to deploy, configure, maintain, and upgrade the **Pingbao** server platform on on-premise hardware, Docker environments, or cloud infrastructure.

---

## 1. System Requirements

* **Operating System**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, Oracle Linux
* **Container Runtime**: Docker Engine 24.0+ & Docker Compose v2+
* **Hardware Sizing**:
  * CPU: 4 Cores or higher
  * RAM: 8GB minimum (16GB+ recommended for 100+ active displays)
  * Disk: 100GB+ SSD (expandable based on media asset volume)

---

## 2. Docker Deployment

### 2.1 Extract Release Package
Upload and extract the Pingbao deployment package on the target host:

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release
```

### 2.2 Configure Environment Variables
Copy `.env.example` to `.env` and configure domain and database parameters:

```bash
cp .env.example .env
nano .env
```

Key environment parameters:
* `DATABASE_URL`: PostgreSQL connection string
* `REDIS_URL`: Redis cache and command queue connection string
* `STORAGE_ENDPOINT`: S3/MinIO object storage endpoint
* `JWT_SECRET`: Secret key for signing admin authentication tokens

### 2.3 Starting Services
Launch services using the bundled script or Docker Compose:

```bash
# Automated installer
./install.sh

# Or manual Docker Compose
docker compose -f docker-compose.onprem.yml up -d
```

---

## 3. Upgrades and Rollback

### 3.1 Zero-Downtime Upgrades
Apply new versions while preserving databases and media volumes:

```bash
./upgrade.sh <new_version_package.tar.gz>
```
* Database schema migrations are executed automatically.
* Existing configurations and storage volumes are preserved.

### 3.2 Instant Rollback
In case of unforeseen issues, instantly revert to the previous snapshot:

```bash
./rollback.sh
```

---

## 4. Monitoring & Maintenance

* **Database Backups**: Nightly PostgreSQL dumps recommended.
* **Storage Replication**: Mirror MinIO/S3 volumes for media asset preservation.
* **Health Checks**: Monitor server health via `GET /api/v1/health`.
