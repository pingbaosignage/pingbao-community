# 핑바오(Pingbao) 관리자 및 설치 가이드

본 문서는 **핑바오(Pingbao)** 서버 환경을 온프레미스(On-Premise), NAS 또는 클라우드(Cloud) 환경에 구축하고, **빌트인 AI / LLM 자동화 엔진(글로벌·중국 클라우드 및 무료 사내 로컬 Ollama/vLLM)**을 설정·유지 관리하는 방법을 안내합니다.

---

## 1. 시스템 요구사항 및 배포 아키텍처 선택

* **서버 운영체제**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, QNAP QTS, Oracle Linux, Windows Server 2019+
* **지원 플레이어 단말 OS**:
  * **삼성 스마트 사이니지**: Tizen 6.5+ / SSSP 6~10+ Standalone 상업용 디스플레이 (`USB 자동 설치팩 .zip`)
  * **Windows 키오스크**: Windows 10 / 11 x64 (`.exe`)
  * **Android 사이니지**: Android 셋톱박스 / 태블릿 / DID (`.apk`)
  * **Linux 미니 PC**: Ubuntu / Debian x64 (`.deb` / `.sh`)
  * **웹 런처**: 무설치 표준 브라우저 직접 접속 (`/player`)
* **컨테이너 런타임**: Docker Engine 24.0+ 및 Docker Compose v2+ (베어메탈 실행 시 **Node.js 22.5+**)
* **설치 옵션별 권장 사양 (`DB_MODE`)**:
  * **경량 / 단독형 (`DB_MODE=sqlite`, 기본값)**: CPU 2 Core 이상, **RAM 2GB~4GB**, 20GB+ SSD (`dscn-server` + `dscn-nginx` 2컨테이너 구성, NAS 및 1~300대 매장 환경 최적)
  * **엔터프라이즈 / 고가용성 (`DB_MODE=postgres`)**: CPU 4 Core 이상, **RAM 8GB 이상** (100대 이상 운영 시 16GB 권장), 100GB+ SSD (PostgreSQL 16 + Redis 7 Pub/Sub 클러스터 버스 포함 4컨테이너 구성, 다중 서버 HA 환경 최적)
  * **(선택) 사내 로컬 LLM (`Ollama` / `vLLM`) 자체 구동 시**: GPU VRAM 8GB~16GB 이상 또는 Apple Silicon / 고성능 CPU 서버 (`gemma4:12b`, `qwen2.5:14b`, `qwen2.5vl:7b` 등 API 비용 없이 100% 폐쇄망 구동 가능; 외부 클라우드 LLM API 연동 시 추가 서버 사양 불필요)

---

## 2. Docker 환경 설치

### 2.1 패키지 준비
핑바오 배포 패키지(`pingbao-release.tar.gz` 또는 배포 아티팩트)를 대상 서버에 업로드합니다.

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release
```

### 2.2 환경변수 및 데이터베이스 모드 설정
대화형 설치 스크립트 실행 시 자동으로 `.env`와 보안 키 파일(`.env.secret`, 권한 `0600`)이 생성되며, 수동 설정 시 `.env.example`을 복사하여 구성할 수 있습니다.

```bash
cp .env.example .env
nano .env
```

주요 설정 항목:
* `DB_MODE`: 데이터베이스 엔진 선택 (`sqlite` 기본값 또는 `postgres`)
* `DATA_DIR`: 격리된 데이터베이스 보관 경로 (기본값 `./data`, 외부 정적 서빙 경로인 `./storage`와 엄격 물리 분리)
* `DATABASE_URL` / `REDIS_URL`: `DB_MODE=postgres` 선택 시 PostgreSQL 16 및 Redis 7 클러스터 접속 정보
* `SERVER_MASTER_KEY`: `.env.secret`에 보관되는 64자 마스터 암호화 키 (JWT 서명 키 파생 및 LLM API 시크릿 키 AES-256-GCM 암호화 보관에 사용)

### 2.3 서비스 실행 (설치 시 DB 모드 선택)

```bash
# 대화형 설치 마법사 (1: SQLite 경량 모드 / 2: PostgreSQL + Redis 엔터프라이즈 모드 선택)
./install.sh

# 경량 SQLite WAL 모드 자동 설치 (docker-compose.sqlite.yml)
./install.sh --silent --db-mode=sqlite --domain=signage.mycompany.com

# 엔터프라이즈 PostgreSQL + Redis 클러스터 모드 자동 설치 (docker-compose.onprem.yml)
./install.sh --silent --db-mode=postgres --domain=signage.mycompany.com
```

---

## 3. 업데이트 및 롤백 가이드

### 3.1 신규 버전 무중단 업데이트
`DB_MODE`를 자동 감지하여 `./data` 및 DB를 사전 백업한 뒤 헬스체크 실패 시 자동 롤백합니다.

```bash
./upgrade.sh
```
* 기존 데이터베이스(`./data/pingbao.sqlite` 또는 PostgreSQL 볼륨), 암호화된 AI 설정 및 미디어 스토리지(`./storage`)는 안전하게 보존됩니다.

### 3.2 이전 버전 즉시 롤백

```bash
./rollback.sh
```

---

## 4. 백업 및 모니터링

* **DB 백업**: `./dscn-ctl.sh backup` 실행 또는 `./data` 디렉터리(`pingbao.sqlite`) / PostgreSQL 일일 덤프 자동 수행 권장 (`.env.secret` 마스터 키 파일 필수 동반 보관)
* **스토리지 백업**: `./storage` 미디어 에셋 볼륨 복제
* **상태 및 실시간 동기화 확인**: `GET /api/v1/health` 엔드포인트(`status`, `db_mode` 반환)로 서버 상태를 모니터링하며, 다중 디스플레이 실시간 SyncPlay를 위해 리버스 프록시 앞단에서 `/api/v1/device/sync-stream` SSE 경로의 버퍼링 비활성화(`proxy_buffering off`)를 유지합니다.

---

## 5. 시스템 설정 5대 관리 서브탭 & 빌트인 AI / LLM 자동화 구성

**[시스템 설정 / 라이선스]** 워크스페이스는 **5개의 전문 관리 서브탭**으로 구성되어 있습니다.

<br/>

| 관리자 환경설정 (`ADMIN_SETTINGS`) | 빌트인 AI / LLM 자동화 설정 (`AI_SETTINGS`) |
| :---: | :---: |
| <img src="../assets/ko/admin-settings.png" width="100%" /> | <img src="../assets/ko/ai-settings.png" width="100%" /> |

### 5.1 5대 관리 서브탭 구조
1. **라이선스 관리 (`LICENSE`)**: 오프라인/폐쇄망 완벽 대응 Ed25519 상용 라이선스(`.lic`) 서명 검증 및 단말 쿼터 현황 관리
2. **환경설정 (`ADMIN_SETTINGS`)**:
   * **서버 접근 주소 및 멀티 OS 설치 패키지 자동 생성**: 대표 서버 URL 저장 시 전 OS 플레이어 설치 패키지(`.zip`, `.exe`, `.apk`, `.deb`/`.sh`)에 서버 주소가 자동 내장(Stamping)되어 재패키징됩니다.
   * **6개월 기본 로그 보존 주기 및 자동 정리(Auto-Purge Lifecycle)**: 운영 관제, 가동 시간, PoP 증빙, 감사 로그에 **기본 6개월(180일) 보존 및 자동 정리 주기**가 작동합니다.
3. **★ AI / LLM 자동화 (`AI_SETTINGS` · 최고 관리자 전용)**:
   * **10대 공식 LLM 프로바이더 프리셋 내장**:
     * **중국 본토 직결 (GFW 프록시 불필요)**: Alibaba 통의천문(`qwen`), `deepseek`, 지푸 `zhipu` (GLM-4/4V), 바이트댄스 `doubao`, Moonshot `kimi`
     * **글로벌 클라우드**: `qwen_intl`, `openai` (GPT-4o / mini), `gemini` (Gemini 2.5 Flash / Pro), `claude` (Claude Sonnet / Haiku)
     * **사내 구축형 온프레미스 (무료 · 폐쇄망)**: `ollama` (`http://127.0.0.1:11434/v1` 또는 사내 GPU 서버 IP — Ollama, vLLM, SGLang 완벽 호환)
   * **텍스트 + 비전 프로바이더 분리 조합 지원**: 예를 들어 텍스트/챗봇은 가성비가 뛰어난 `DeepSeek`으로, 이미지 비전 분석은 `Qwen-VL` 또는 `Gemini`로 혼합 구성할 수 있습니다.
   * **강력한 키 보안 및 쿼터 통제**: 입력된 API 키는 `SERVER_MASTER_KEY` 기반 **AES-256-GCM**으로 암호화 저장되며 화면에는 마스킹(`sk-ab••••1234`)만 노출됩니다. 저장 전 **`[연결 테스트]`**로 실시간 응답 속도를 검증하고, **일일 최대 호출 제한(`daily_call_limit`)**으로 비용 폭주를 차단합니다.
   * **4대 세부 기능 독립 스위치**: ① 컨텐츠 업로드 비전 자동 태깅, ② 장치 텔레메트리·로그 AI 진단, ③ 모닝 브리핑·감사·스케줄 AI 인사이트, ④ 우측 슬라이드 AI 오퍼레이터 챗봇(`Ctrl+J`)을 개별 ON/OFF 할 수 있습니다.
4. **원격 진단 (`DIAGNOSTICS`)**: 단말 진단 로그 번들 수집, Opt-in 기반 민감정보 자동 마스킹(`***MASKED***`) 에러 리포팅 및 커뮤니티 이슈 포털 연동
5. **API & AI 연동 (`CONNECTIVITY`)**: 스코프 제한 API 키 발급, 호출량 모니터링 및 외부 AI 에이전트(Claude Desktop, Cursor, Antigravity)용 표준 MCP 설정 JSON 제공
