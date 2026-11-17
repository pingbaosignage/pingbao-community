# 핑바오(Pingbao) 관리자 및 설치 가이드

본 문서는 **핑바오(Pingbao)** 서버 환경을 다양한 클라우드(Oracle Cloud, AWS, GCP, Azure, Alibaba/Tencent), 온프레미스(On-Premises Linux / Synology NAS) 및 **LLM/AI 에이전트(Claude Code, Cursor, Antigravity 등)**를 활용하여 원클릭으로 손쉽게 구축하고 유지 관리하는 방법을 종합적으로 안내합니다.

---

## 1. 시스템 요구사항

* **운영체제**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+, Oracle Linux 8/9, Debian 12), Synology DSM 7+
* **컨테이너 런타임**: Docker Engine 24.0+ 및 Docker Compose v2+
* **권장 하드웨어 사양**:
  * **최소 사양 (소규모/테스트)**: 2 vCPU, 4GB RAM, 50GB SSD (단말 1~20대)
  * **표준 권장 사양 (기업 운영)**: 4 vCPU, 8GB~16GB RAM, 100GB+ SSD (단말 50~500대)
  * **대규모 클러스터 사양**: 8 vCPU, 32GB RAM, 500GB+ NVMe SSD (단말 1,000대 이상)
* **네트워크 개방 포트 (인바운드 Inbound)**:
  * `80/TCP`: HTTP 웹 콘솔 및 단말 패키지 다운로드
  * `443/TCP`: HTTPS 보안 통신 (SSL/TLS)
  * `3000/TCP`: 백엔드 API 서버 (내부 프록시용, 외부 비공개 권장)

---

## 2. 클라우드 및 온프레미스 환경별 설치 가이드

### 2.1 오라클 클라우드 (Oracle Cloud Infrastructure - OCI)
오라클 클라우드는 평생 무료(Always Free) 인스턴스를 제공하여 핑바오를 경제적으로 운영하기에 가장 적합한 환경 중 하나입니다.

1. **컴퓨트 인스턴스 생성**:
   * 이미지: **Ubuntu 22.04 Minimal** 또는 **Oracle Linux 8/9**
   * Shape: `VM.Standard.A1.Flex` (ARM 4 OCPU, 24GB RAM - Always Free) 또는 `VM.Standard.E2.1.Micro` (AMD 1 OCPU, 1GB RAM)
2. **VCN 가상 클라우드 네트워크 보안 목록(Security List) 설정**:
   * 오라클 콘솔 ➔ 네트워킹 ➔ VCN ➔ 보안 목록 ➔ 기본 수신 규칙(Ingress Rules) 추가:
     * 소스 CIDR: `0.0.0.0/0`, IP 프로토콜: `TCP`, 대상 포트: `80, 443`
3. **OS 방화벽 개방 (Ubuntu/Oracle Linux 공통)**:
   ```bash
   # iptables 개방
   sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
   sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
   sudo netfilter-persistent save
   ```
4. **Cloudflare Edge Proxy(무료 SSL/DDoS 방어) 연동 권장**:
   * Cloudflare 대시보드에서 `A 레코드`를 오라클 공인 IP로 지정하고 Proxy(주황색 구름)를 활성화하면 인증서 갱신 없이 즉시 무제한 무료 SSL(`https://`) 및 글로벌 CDN 가속이 적용됩니다.

---

### 2.2 AWS (Amazon Web Services)
1. **EC2 인스턴스 시작**:
   * AMI: `Ubuntu Server 22.04 LTS (HVM)`
   * 인스턴스 유형: `t3.small` (소규모) 또는 `t3.medium` (권장)
   * 스토리지: 범용 SSD (gp3) 100GB 이상
2. **보안 그룹(Security Group) 인바운드 규칙**:
   * `HTTP` (TCP 80) ➔ `0.0.0.0/0`
   * `HTTPS` (TCP 443) ➔ `0.0.0.0/0`
   * `SSH` (TCP 22) ➔ 본인 공인 IP 한정 허용
3. **탄력적 IP (Elastic IP) 할당**:
   * EC2 인스턴스에 고정 공인 IP(Elastic IP)를 할당하여 서버 재부팅 시에도 사이니지 단말의 접속 주소가 바뀌지 않도록 설정합니다.

---

### 2.3 GCP (Google Cloud Platform)
1. **Compute Engine VM 인스턴스 생성**:
   * 머신 시리즈: `E2` (머신 유형: `e2-medium` 또는 `e2-standard-2`)
   * 부팅 디스크: Ubuntu 22.04 LTS, 균형 있는 영구 디스크 100GB
   * 방화벽: **[HTTP 트래픽 허용]**, **[HTTPS 트래픽 허용]** 둘 다 체크
2. **VPC 방화벽 규칙 확인**:
   * `default-allow-http`, `default-allow-https` 규칙이 인스턴스 대상 태그 `http-server`, `https-server`에 바인딩되어 있는지 확인합니다.

---

### 2.4 Microsoft Azure
1. **가상 머신(Virtual Machine) 생성**:
   * 이미지: `Ubuntu Server 22.04 LTS - x64 Gen2`
   * 크기: `Standard_B2s` (2 vCPU, 4GB RAM) 이상 권장
2. **네트워크 보안 그룹(NSG) 인바운드 포트 규칙**:
   * 우선순위 300: `Port_80` (대상 포트 80, 프로토콜 TCP, 허용)
   * 우선순위 310: `Port_443` (대상 포트 443, 프로토콜 TCP, 허용)

---

### 2.5 알리바바 클라우드(Alibaba Cloud) & 텐센트 클라우드(Tencent Cloud)
1. **ECS / CVM 인스턴스 사양**:
   * 인스턴스 유형: `e-c2m4` (2 Core, 4GB RAM) 이상
   * 시스템 디스크: ESSD 100GB
2. **보안 그룹(安全组) 규칙**:
   * 인바운드(入方向)에 `TCP 80` 및 `TCP 443` 개방
3. **중국 본토 리전 배포 시 주의사항 (ICP 비안)**:
   * 중국 본토(베이징/상하이/광저우 등) 리전의 공인 80/443 포트를 도메인과 바인딩하여 서비스하려면 중국 공업정보화부(MIIT)의 **ICP 비안(ICP 备案)**이 필수입니다.
   * 비안 절차 없이 즉시 운영하려면 **홍콩(Hong Kong)**, **싱가포르(Singapore)** 리전을 선택하시거나 IP 직접 접속 방식으로 운영하시기 바랍니다.

---

### 2.6 온프레미스(On-Premises Linux / Synology NAS)
외부 인터넷 클라우드에 데이터를 올리지 않고 매장/본사 내부 폐쇄망에 핑바오를 구축할 수 있습니다.

1. **우분투 / 록키 리눅스 물리 서버**:
   * 고정 사설 IP(예: `192.168.1.100`) 부여
   * 외부 매장에서 접속이 필요한 경우 공유기에서 `80`, `443` 포트를 해당 IP로 포트포워딩(Port Forwarding) 처리
2. **시놀로지(Synology) NAS (DSM 7+)**:
   * 패키지 센터 ➔ `Container Manager` (Docker) 설치
   * SSH 터미널 활성화 후 패키지 폴더(`/volume1/docker/pingbao`)에 배포 패키지 압축 해제 후 실행

---

## 3. 🤖 LLM / AI 코딩 에이전트를 이용한 대화형 자동 설치 가이드

핑바오는 **Claude Code, Cursor, Windsurf, Google Antigravity, Gemini CLI, OpenAI ChatGPT/Codex** 등 최신 AI 에이전트와 완벽히 호환됩니다.  
터미널에서 AI 에이전트를 실행하고 아래의 **만능 골든 프롬프트(Golden Prompt)**를 그대로 입력하면, AI가 운영체제 감지부터 Docker 환경 구성, 핑바오 설치, 헬스체크까지 100% 전자동으로 완료합니다.

### 3.1 만능 골든 설치 프롬프트 (Golden Prompt for AI Agents)
서버에 SSH 접속 후 선호하는 AI CLI(`claude`, `cursor`, `agy` 등)를 실행하고 다음 프롬프트를 복사하여 전달하세요:

```markdown
당신은 엔터프라이즈 디지털 사이니지 솔루션 "핑바오(Pingbao)"의 전문 데브옵스 엔지니어입니다.
현재 리눅스 서버에 핑바오 최신 운영 환경을 완전히 구축하고 서비스 헬스체크까지 완료해 주세요.

[실행 단계]
1. OS 환경 및 기존 패키지 상태를 점검하세요. (Ubuntu/Debian, CentOS/RHEL/Oracle Linux 등)
2. Docker 및 Docker Compose v2가 설치되어 있는지 확인하고, 미설치 시 공식 저장소를 통해 무인 모드로 설치하세요.
3. 80번 및 443번 포트 점유 여부를 확인하고, OS 방화벽(ufw, firewalld, iptables)에서 해당 포트를 허용하세요.
4. pingbaosignage/pingbao-community의 최신 릴리즈 패키지를 다운로드하거나, 배포 디렉터리에서 `cp .env.example .env` 후 환경 변수를 생성하세요. (SERVER_MASTER_KEY 및 JWT_SECRET는 64자리 무작위 암호화 키 생성)
5. `docker compose -f docker-compose.onprem.yml up -d`로 데이터베이스, 레디스, 백엔드 서버, Nginx 컨테이너를 가동하세요.
6. 10초 대기 후 `curl -s http://localhost/api/v1/health`를 호출하여 정상 가동(status: ok) 여부를 최종 검증하고 웹 콘솔 접속 URL을 출력해 주세요.
```

### 3.2 AI 에이전트를 통한 장애 진단 및 자율 복구 프롬프트
운영 중 접속 불가 또는 장애 발생 시 AI 에이전트에게 아래 프롬프트를 전달하면 원인을 즉시 진단하고 해결합니다:

```markdown
핑바오(Pingbao) 사이니지 서버의 상태를 긴급 점검하고 문제를 해결해 주세요:
1. `docker ps -a`로 핑바오 컨테이너(dscn_server, dscn_nginx, dscn_postgres, dscn_redis) 상태를 점검하세요.
2. 비정상 종료된 컨테이너가 있다면 `docker logs --tail 50 <컨테이너명>`으로 오류 로그를 분석하세요.
3. 80, 443 포트 리스닝 상태(`ss -tulpn`)와 방화벽 인그레스 규칙을 점검하세요.
4. 필요 시 컨테이너를 안전하게 재시작하고 `http://localhost/api/v1/health` 정상 응답을 확인해 주세요.
```

---

## 4. 표준 수동 설치 절차 (Docker Manual Installation)

### 4.1 패키지 다운로드 및 압축 해제
공식 커뮤니티 릴리즈 페이지에서 최신 배포 패키지(`pingbao-release.tar.gz`)를 내려받습니다:

```bash
mkdir -p /opt/pingbao && cd /opt/pingbao
tar -zxvf pingbao-release.tar.gz
```

### 4.2 환경변수 설정
`.env.example`을 복사하여 `.env`를 생성하고 도메인 및 환경 정보를 입력합니다:

```bash
cp .env.example .env
nano .env
```

주요 설정 항목:
* `SERVER_DOMAIN`: 서버 공인 IP 또는 도메인 주소 (예: `cms.mycompany.com`)
* `HTTP_PORT`: 외부 노출 HTTP 포트 (기본: `80`)
* `HTTPS_PORT`: 외부 노출 HTTPS 포트 (기본: `443`)
* `POSTGRES_PASSWORD`: 데이터베이스 마스터 비밀번호
* `REDIS_PASSWORD`: 캐시 및 롱폴링 큐 비밀번호
* `JWT_SECRET`: 32자리 이상의 보안 토큰 서명 키
* `SERVER_MASTER_KEY`: 64자리 PII/디바이스 암호화 마스터 키 (비워둘 시 자동 생성)

### 4.3 원클릭 인스톨러 실행
```bash
chmod +x install.sh
./install.sh
```

설치가 완료되면 브라우저에서 `http://<서버IP 또는 도메인>`으로 접속하여 최초 관리자 초기 설정 마법사(OOBE)를 진행합니다.

---

## 5. 업데이트 및 롤백 가이드

### 5.1 무중단 버전 업데이트
```bash
chmod +x upgrade.sh
./upgrade.sh <new_version_package.tar.gz>
```
* PostgreSQL 데이터베이스 및 미디어 스토리지 볼륨은 100% 안전하게 유지됩니다.
* 신규 스키마 마이그레이션이 필요한 경우 자동으로 스키마 동기화가 수행됩니다.

### 5.2 이전 버전 롤백
```bash
chmod +x rollback.sh
./rollback.sh
```

---

## 6. 백업 및 시스템 관리

* **DB 자동 백업**: 매일 새벽 PostgreSQL 덤프 자동 수행 권장 (`storage/backups/`)
* **미디어 스토리지 백업**: `storage/media/` 폴더 주기적 원격 복제
* **상태 확인 엔드포인트**: `GET /api/v1/health` (서버 버전, DB 연결, 핑거프린트 반환)

---

## 7. 관리자 시스템 설정 및 자동 에러 텔레메트리 (Admin Settings & Telemetry)

핑바오 v2.4부터는 **[라이선스/설정]** 메뉴 상단에 확장 가능한 서브메뉴(탭) 구조가 도입되어 시스템 관리 기능을 손쉽게 관리할 수 있습니다.

### 7.1 자동 에러 텔레메트리 수집 및 리포팅 (Opt-in & Consent)
* **목적**: 고객 운영 중 서버 또는 단말에서 예기치 못한 런타임 오류가 발생할 경우, 주기적으로 공개 커뮤니티(`pingbaosignage/pingbao-community`)에 이슈로 자동 접수하여 신속한 패치 및 지원을 제공합니다.
* **철저한 개인정보 마스킹**: 패스워드, JWT 토큰, API Key, 사설 경로 등은 전송 전 메모리에서 `***MASKED***` 처리됩니다.
* **관리자 동의 필수 절차**: 기본값은 비활성화(`DISABLED`)이며, 관리자가 체크박스를 통해 동의해야만 활성화됩니다.
