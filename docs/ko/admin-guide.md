# 핑바오(Pingbao) 관리자 및 설치·보안 가이드

본 문서는 **핑바오(Pingbao)** 서버 환경을 온프레미스(On-Premise), NAS 또는 클라우드(Cloud) 환경에 구축하고, **11대 AI / LLM 자동화 엔진** 및 **외부연동 에이전트 제로-쉘(Zero-Shell) 보안 정책**을 설정·유지 관리하는 방법을 안내합니다.

---

## 1. 시스템 요구사항 및 배포 아키텍처 선택

* **서버 운영체제**: Linux (Ubuntu 22.04 LTS+, Rocky Linux 9+), Synology DSM 7+, QNAP QTS, Oracle Linux, Windows Server 2019+
* **지원 플레이어 단말 OS (`v2.2.0`)**:
  * **삼성 스마트 사이니지**: Tizen 6.5+ / SSSP 6~10+ 상업용 디스플레이 (`USB 자동 설치팩 .zip`, 하드웨어 프레임버퍼 및 동적 HTML5 캡처 지원)
  * **Windows 키오스크**: Windows 10 / 11 x64 (`.exe`)
  * **Android 사이니지**: Android 셋톱박스 / 태블릿 / DID (`.apk`)
  * **Linux 미니 PC**: Ubuntu / Debian x64 (`.deb` / `.sh`)
  * **웹 런처**: 무설치 표준 브라우저 직접 접속 (`/player`)
* **컨테이너 런타임**: Docker Engine 24.0+ 및 Docker Compose v2+ (베어메탈 실행 시 **Node.js 22.5+**)
* **설치 옵션별 권장 사양 (`DB_MODE`)**:
  * **경량 / 단독형 (`DB_MODE=sqlite`, 기본값)**: CPU 2 Core 이상, **RAM 2GB~4GB**, 20GB+ SSD (`dscn-server` + `dscn-nginx` 2컨테이너 구성, NAS 및 1~300대 매장 환경 최적)
  * **엔터프라이즈 / 고가용성 (`DB_MODE=postgres`)**: CPU 4 Core 이상, **RAM 8GB 이상** (100대 이상 운영 시 16GB 권장), 100GB+ SSD (PostgreSQL 16 + Redis 7 Pub/Sub 클러스터 버스 포함 4컨테이너 구성)
  * **(선택) 사내 로컬 LLM (`Ollama` / `vLLM`) 자체 구동 시**: GPU VRAM 8GB~16GB 이상 또는 Apple Silicon / 고성능 CPU 서버 (`gemma4:12b`, `qwen2.5:14b`, `qwen2.5vl:7b` 등 API 비용 없이 100% 폐쇄망 구동 가능)

---

## 2. Docker 환경 설치 및 업데이트

```bash
tar -zxvf pingbao-release.tar.gz
cd pingbao-release

# 대화형 설치 마법사 (1: SQLite 경량 모드 / 2: PostgreSQL + Redis 엔터프라이즈 모드)
./install.sh

# 무중단 업그레이드 (DB 자동 백업 및 헬스체크 실패 시 자동 롤백)
./upgrade.sh

# 이전 버전 즉시 롤백
./rollback.sh
```

주요 `.env` 보안 및 아키텍처 불변 항목:
* `DB_MODE`: `sqlite` (기본값) 또는 `postgres`
* `DATA_DIR`: 격리된 데이터베이스 보관 경로 (`./data`, 정적 미디어 경로인 `./storage`와 엄격 물리 분리)
* `SERVER_MASTER_KEY`: `.env.secret` (`0600`)에 보관되는 64자 마스터 키 — JWT 서명 파생, LLM API 키 암호화, 외부연동 에이전트 인증 볼트(`AES-256-GCM`) 암호화에 사용

---

## 3. 외부연동 에이전트(External Sync Agents) 보안 아키텍처 관리

**[외부연동 에이전트]** 워크스페이스는 오직 최고 관리자(`SUPER_ADMIN`)에게만 노출되며, 외부 데이터 인입 과정에서 내부 인프라를 완벽히 보호하는 **5중 제로-쉘(Zero-Shell) 방어선**으로 설계되어 있습니다.

<br/>

| 대화형 외부연동 스튜디오 (`STUDIO`) | 크론 에이전트 및 데이터 피드 스토어 (`AGENTS`) |
| :---: | :---: |
| <img src="../assets/ko/sync-concierge-studio.png" width="100%" /> | <img src="../assets/ko/sync-agents-list.png" width="100%" /> |

1. **임의 코드 실행 원천 차단 (`Zero-Shell Sandbox`)**: 에이전트는 오직 선언적 HTTP(S) GET/POST JSON 수집과 샌드박스 매핑만 수행하며, 쉘 명령 실행이나 로컬 파일시스템 접근 권한이 존재하지 않습니다.
2. **AES-256-GCM 암호화 볼트 (`Secret Vault`)**: Bearer 토큰, API Key(`Header`/`Query`), Basic, OAuth2 Client Credentials 시크릿은 저장 즉시 암호화되며 감사 로그나 프론트엔드 조회 응답에는 마스킹(`••••`) 처리된 상태만 반환됩니다.
3. **Anti-SSRF 방어 & 사내망 Allowlist (`ALLOWLIST` 탭)**:
   * 기본적으로 `127.0.0.1`, `localhost`, 링크-로컬/클라우드 메타데이터(`169.254.169.254`), 사설 IP 대역(`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) 및 내부 DB/Redis 포트(`5432`, `6379` 등)로의 아웃바운드 요청을 DNS 해석 단계에서 차단합니다.
   * 사내 ERP, POS, 인사(HR) API 연동이 필요한 경우 **`[사내망 호스트 허용 목록 (ALLOWLIST)]`** 탭에 승인된 호스트명(예: `erp-api.corp.internal`) 또는 CIDR 대역을 등록하여 안전하게 통신합니다.
4. **대량 변경 서킷브레이커 & 격리 승인함 (`QUARANTINE` 탭)**:
   * 에이전트별 `1회 최대 변경 허용 건수(max_mutations_per_run)` 또는 `최대 비활성화 비율(max_deactivate_ratio)`을 초과하는 응답이 수신되면 즉시 DB 반영을 중단하고 **격리 보류 승인함**으로 이관합니다.
   * 인사/조직 동기화(`USER_ORG`) 시 외부 피드를 통한 `SUPER_ADMIN` 권한 부여 시도는 서버단에서 무조건 차단되며, `USER_ORG` 데이터는 공개 데이터 피드 엔드포인트(`/api/v1/public/data-feeds/*`)에 절대 적재되지 않습니다.
5. **HTML5 키오스크 패키지(`.zip`) 보안 검증**: 업로드 시 Zip-Slip 상위 경로 이탈(`../`), 심볼릭 링크, 100MB 초과 압축 폭탄, 실행/서버 스크립트 확장자(`.exe`, `.sh`, `.php`, `.jsp` 등)를 엄격히 검사하여 거부합니다.

---

## 4. 시스템 설정 5대 관리 서브탭 & 11대 AI / LLM 자동화 구성

<br/>

| 관리자 환경설정 (`ADMIN_SETTINGS`) | 빌트인 AI / LLM 자동화 설정 (`AI_SETTINGS`) |
| :---: | :---: |
| <img src="../assets/ko/admin-settings.png" width="100%" /> | <img src="../assets/ko/ai-settings.png" width="100%" /> |

1. **라이선스 관리 (`LICENSE`)**: 오프라인/폐쇄망 완벽 대응 Ed25519 상용 라이선스(`.lic`) 검증 및 단말 쿼터 관리
2. **환경설정 (`ADMIN_SETTINGS`)**: 대표 서버 URL 변경 시 전 OS 플레이어 설치 패키지(`.zip`, `.exe`, `.apk`, `.deb`/`.sh`) 자동 재패키징 및 **기본 6개월(180일) 로그 자동 정리 주기** 설정
3. **★ AI / LLM 자동화 (`AI_SETTINGS` · 11대 공식 프리셋)**:
   * **중국 본토 직결 (5종 · GFW 무관)**: Alibaba `qwen`, `deepseek`, 지푸 `zhipu` (GLM), 바이트댄스 `doubao`, Moonshot `kimi`
   * **글로벌 클라우드 & 무료 게이트웨이 (5종)**: `qwen_intl`, `openai`, `gemini`, `claude`, **`freellmapi` (`FreeLLMAPI`)**
   * **100% 무료 사내 폐쇄망 온프레미스**: **`ollama`** (`Ollama · vLLM · SGLang` — API 비용 0원, 데이터 유출 0건)
   * 텍스트/비전 프로바이더 분리 조합, AES-256-GCM 키 보관, 실시간 연결 지연 테스트 및 일일 호출 상한(`daily_call_limit`) 지원
4. **원격 진단 (`DIAGNOSTICS`)**: 단말 진단 로그 번들 수집 및 민감정보 자동 마스킹(`***MASKED***`) 에러 리포팅
5. **API & AI 연동 (`CONNECTIVITY`)**: 스코프 제한 API 키(`pb_live_...`) 발급 및 외부 AI 에이전트(Claude Desktop, Cursor, Antigravity, Dify)용 표준 MCP 설정 JSON 제공
