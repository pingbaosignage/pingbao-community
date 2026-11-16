# 핑바오(Pingbao) 관리자 및 설치 가이드

본 문서는 **핑바오(Pingbao)** 서버 환경을 온프레미스(On-Premise) 또는 클라우드(Cloud) 환경에 구축하고 유지 관리하는 방법을 안내합니다.

---

## 1. 시스템 요구사항

* **운영체제**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, Oracle Linux
* **컨테이너 런타임**: Docker Engine 24.0+ 및 Docker Compose v2+
* **권장 사양**:
  * CPU: 4 Core 이상
  * RAM: 8GB 이상 (디바이스 100대 이상 운영 시 16GB 권장)
  * 디스크: 100GB 이상 SSD (미디어 콘텐츠 저장소 용량에 따라 확장)

---

## 2. Docker 환경 설치

### 2.1 패키지 준비
핑바오 배포 패키지(`pingbao-release.tar.gz` 또는 배포 아티팩트)를 대상 서버에 업로드합니다.

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release
```

### 2.2 환경변수 설정
`.env.example`을 복사하여 `.env`를 생성하고 도메인 및 데이터베이스 접속 정보를 설정합니다.

```bash
cp .env.example .env
nano .env
```

주요 설정 항목:
* `DATABASE_URL`: PostgreSQL 접속 정보
* `REDIS_URL`: Redis 캐시 및 롱폴링 큐 접속 정보
* `STORAGE_ENDPOINT`: S3/MinIO 오브젝트 스토리지 주소
* `JWT_SECRET`: 관리자 인증 토큰 서명 키

### 2.3 서비스 실행
제공되는 컨트롤 스크립트 또는 Docker Compose로 서비스를 기동합니다.

```bash
# 리눅스 환경
./install.sh

# 또는 수동 실행
docker compose -f docker-compose.onprem.yml up -d
```

---

## 3. 업데이트 및 롤백 가이드

### 3.1 신규 버전 업데이트
새로운 패키지 버전을 적용할 때는 무중단 업데이트 스크립트를 사용합니다.

```bash
./upgrade.sh <new_version_package.tar.gz>
```
* 기존 데이터베이스 및 미디어 스토리지 볼륨은 안전하게 보존됩니다.
* 마이그레이션이 필요한 경우 자동으로 스키마 동기화가 수행됩니다.

### 3.2 이전 버전 롤백
예기치 않은 문제가 발생할 경우 이전 스냅샷으로 즉시 롤백할 수 있습니다.

```bash
./rollback.sh
```

---

## 4. 백업 및 모니터링

* **DB 백업**: 매일 새벽 PostgreSQL 덤프 자동 수행 권장
* **스토리지 백업**: MinIO/S3 미디어 에셋 스토리지 볼륨 복제
* **상태 확인**: `GET /api/v1/health` 엔드포인트를 통해 서버 상태 모니터링
