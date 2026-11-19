# 핑바오(Pingbao / 屏宝) 변경 이력 (Changelog)

핑바오(Pingbao)의 주요 변경 사항과 릴리즈 노트입니다.  
최신 바이너리 및 배포 패키지는 [GitHub Releases](https://github.com/pingbaosignage/pingbao-community/releases)에서 다운로드할 수 있습니다.

---

## [v1.3.0] - 2026-11-19
### 멀티 OS 플레이어 확장 및 엔터프라이즈 운영 고도화 (Multi-OS & Enterprise Operations)
* **5대 멀티 OS 통합 플레이어 아키텍처 및 원클릭 설치 모달 (`TIZEN` · `WINDOWS` · `ANDROID` · `LINUX` · `WEB`)**:
  * **삼성 스마트 사이니지 (Tizen 6.5+ SSSP 10+)** 심층 네이티브 제어를 주력으로 유지하면서, **Windows 10/11 키오스크(`.exe`)**, **Android 사이니지/태블릿(`.apk`)**, **Linux 미니 PC(`.deb` / `.sh`)**, **무설치 웹 런처(`/player`)**까지 단일 콘솔 통합 관제 확장
  * OS별 하드웨어 추상화 계층(HAL) 자동 라우팅 및 역량 자동 탐지: 하드웨어 패널 전원 vs DPMS 디스플레이 소등, 하드웨어 백라이트 vs 윈도우/앱 밝기 디밍, 혼합 OS 다중 선택 시 지원 가능 명령 지능형 안내
  * 장치 관리 상단에 서버 접속 주소가 설치 바이너리에 자동 내장(Stamping)되는 **멀티 OS 플레이어 설치 패키지 다운로드 모달** 탑재
* **전용 일괄 원격 제어 모달(7대 명령) 및 장치 가동 시간(Uptime) 워크벤치 고도화**:
  * 다중 디스플레이 선택 시 화면 켜기/끄기, 볼륨, 음소거, 밝기, 플레이어 재시작, 기기 재부팅, 긴급 방송 등 **7대 원격 명령 전용 일괄 제어 모달** 신설 및 중복 RTC 타이머 설정 패치 정리
  * 대시보드 7일/30일 가동시간 급감 이상 장치 패널 고도화 및 `통계 및 분석 > 장치 가동 시간` 워크벤치(회차별 ON/OFF `POWER_TIMER` / `MANUAL` 세션 상세 드릴다운 분석) 정밀화
* **운영 관제 다중 트리아지 필터 및 6개월 로그 자동 보존 라이프사이클**:
  * `운영 관제` 워크스페이스에 상태별(`전체`/`성공`/`진행중`/`실패`)·작업 분류별 필터 및 명령 전후 상태 비교(`Op Diff`) 리스트 아키텍처 통합 적용
  * `시스템 설정 > 환경설정`에 운영 관제·가동시간·PoP·감사 로그에 대한 **기본 6개월(180일) 보존 주기 및 자동 정리(Auto-Purge Lifecycle)** 탑재
  * 콘솔 전 영역(모달, 버튼, 필터 바, 데이터 테이블)에 엔터프라이즈 미니멀리스트 B2B 디자인 시스템 일괄 통일

---

## [v1.2.0] - 2026-11-18
### 아키텍처 고도화 및 신규 기능 (Architecture & New Features)
* **듀얼 데이터베이스 영속화 엔진 및 설치 옵션 제공 (`DB_MODE=sqlite` / `DB_MODE=postgres`)**:
  * **경량/단독형 모드 (`DB_MODE=sqlite`, 기본값)**: Node.js 내장 `node:sqlite` WAL(Write-Ahead Logging) 기반 증분 영속화 엔진(`data/pingbao.sqlite`) 및 2컨테이너 초경량 스택(`docker-compose.sqlite.yml`) 제공 — 저사양 NAS 및 단일 매장 서버 최적화
  * **엔터프라이즈/고가용성 모드 (`DB_MODE=postgres`)**: PostgreSQL 16 JSONB 증분 Upsert 스토리지 + Redis 7 Pub/Sub 클러스터 버스(`pingbao:cluster:bus`) 연동(`docker-compose.onprem.yml`) — 다중 서버 수평 확장(Scale-out) 시 원격 명령 `wakeup` 및 SyncPlay 펄스 실시간 클러스터 브로드캐스트 지원
  * 원클릭 설치/업그레이드/롤백 스크립트(`install.sh`, `install.ps1`, `upgrade.sh`, `upgrade.ps1`, `rollback.sh`, `dscn-ctl.sh`)에 `--db-mode=sqlite|postgres` (`-DbMode`) 옵션 및 자동 모드 감지 탑재
* **물리 다중 삼성 사이니지 실시간 네트워크 SyncPlay 엔진 탑재 (SSE Real-Time SyncPlay)**:
  * 단말 전용 인증 SSE 스트림(`GET /api/v1/device/sync-stream`) 및 리더 단말 네트워크 펄스 중계(`POST /api/v1/device/sync-pulse`) 구현으로 실제 매장의 서로 다른 물리 삼성 Tizen TV 간 밀리초 단위 동시 화면 전환 완성
  * 지수 이동 평균(EMA) 기반 서버-단말 시계 오프셋(`clock_offset_ms`) 실시간 자동 보정으로 기기 간 내장 시계 오차 극복
  * 원격 하드웨어 제어 및 긴급 스케줄 배포 시 SSE `wakeup` 신호를 즉시 푸시하여 최대 폴링 대기 없이 0.01초 내 단말 반응 달성

### 보안 및 안정성 강화 (Security & Reliability Hardening)
* **데이터베이스 저장소 물리 격리 및 기밀 보호**:
  * 모든 DB 및 스냅샷 파일을 웹 정적 서빙 경로(`./storage`)에서 분리된 전용 `./data` 디렉터리로 격리(구 버전 스냅샷 자동 마이그레이션 포함) 및 Nginx `/storage/` 내 DB/JSON 파일 접근 원천 차단
  * 상용 라이선스 서명용 벤더 개인키를 `@ds-cn/shared`에서 완전 분리하여 `license-server` 내부로 격리하고, 온프레미스 패키지에는 검증용 공개키(`LICENSE_PUBLIC_KEY`)만 배포
  * `SERVER_MASTER_KEY` 기반 HKDF-SHA256 `JWT_SECRET` 자동 파생, 평문 부트스트랩 패스워드/TOTP 시크릿 메모리·디스크 비노출 처리
* **인증·권한(RBAC)·MCP 보안 검증 강화**:
  * 프로덕션(`NODE_ENV=production`) 환경에서 초기 로그인 리셋, TOTP 테스트 헬퍼 및 매직 OTP 코드 하드락(원천 비활성화) 적용
  * 2FA 대기(`OTP_PENDING`) 및 리프레시 토큰의 일반 API 우회 호출 차단, 사용자 생성 시 호출자 보유 권한을 초과하는 `custom_permissions` 권한 상승 차단, MCP SSE 세션 소유권 및 `pingbao_verify_playback` 조직 스코프 검증 추가
* **4K 대용량 스트리밍 및 Tizen 단말 메모리/서명 최적화**:
  * 대용량 4K 비디오 업로드 시 전체 버퍼 메모리 적재 대신 64바이트 매직 바이트 검증 + 디스크 직결 스트리밍 및 실시간 SHA-256 해싱으로 서버 OOM 방지
  * Tizen 플레이어에 WebCrypto Ed25519 서버 응답 전자서명 검증 적용, 5MB 초과 미디어 파일의 JS Heap 중복 적재 방지, 프로덕션 환경 단말 실시간 오프라인(45초 타임아웃) 상태 전이 정상화

---

## [v1.1.0] - 2026-11-17
### 신규 기능 (New Features)
* **삼성 스마트 사이니지 (Samsung Smart Signage) 단일 하드웨어 집중 최적화**:
  * 삼성 Tizen 6.5+ SSSP 10+ 전 라인업(QMB, QBB, QHB, QMC, QBC, QHC 등) 네이티브 b2bapis 드라이버 완벽 제어
  * 패널 전원 백라이트 On/Off, 볼륨 제어, 원격 재부팅, 1초 단위 실시간 화면 캡처 회신
* **개방형 REST API 및 AI 에이전트 MCP (Model Context Protocol) 공식 지원**:
  * 60+ 엔드포인트 OpenAPI 3.0 RESTful 규격 완성
  * AI 에이전트(Claude Desktop, Cursor, Antigravity, LLM 자율 에이전트)를 위한 네이티브 MCP 서버(`pingbao`) 기본 탑재
  * API Key 발급, 스코프 분리 및 시간대별 토큰/호출량 모니터링 콘솔 연동
* **전 자동 멀티스크린 싱크 (SyncPlay Auto-Activation)**:
  * 별도 하드웨어 스플리터나 복잡한 수동 설정 없이 **【같은 그룹】 + 【같은 스케줄】 + 【같은 네트워크(동일 LAN)】** 환경에서 100% 자동 프레임 동기화 및 동시 화면 전환 활성화
  * 카운터 3~5연속 전자메뉴판 밀리초 단위 프레임 싱크 및 5760x1080 울트라 와이드 분할 렌더링 지원
* **스케줄링 엔진 4계층 우선순위 고도화 (4-Tier Smart Scheduling)**:
  * `EMERGENCY` (비상 재난/VIP 전 화면 즉시 독점) > `EVENT` (피크 프로모션) > `REGULAR` (일상 타임파팅) > `DEFAULT` (24/7 배경 베이스) 4계층 우선순위 선점 구현
  * Dayparting(조식/중식/티타임/석식/야간) 시각 도달 시 자동 무결점 전환 및 선점 종료 후 자연스러운 자동 복귀
  * 유효 방송 구간(Effective Broadcast Windows) 시각화 분석
* **오류 보고 & 커뮤니티 자동화 텔레메트리 (Error Telemetry & Community Portal)**:
  * 관리자 명시적 동의(Opt-in) 기반 런타임 오류 수집
  * 개인정보 및 비밀정보(비밀번호, 토큰, IP, 전화번호) 자동 마스킹 필터링
  * SHA-256 예외 지문 중복 제거 및 GitHub 커뮤니티 이슈 자동화 연동
  * 웹 콘솔 내 수동 버그 리포트 및 개선 아이디어 접수 폼 제공
* **글로벌 3개 국어 공식 매뉴얼 및 오라클 클라우드 실서버 스크린샷 전면 개편**:
  * GitHub 매뉴얼 중국어(简体中文) 최우선 배치 및 한국어/영어 동기화
  * 오라클 클라우드 대규모 리테일 실서버 기반 9대 전 기능 워크스페이스 고해상도(1920x1080) 스크린샷 11종 전면 교체

---

## [v1.0.0] - 2026-11-16
### 주요 기능 (Features)
* **엔터프라이즈 스마트 사이니지 관제 콘솔**:
  * 삼성 Tizen 6.5+ 스마트 사이니지 대규모 원격 제어 (화면 On/Off, 볼륨, 리부팅, 실시간 화면 캡처)
  * 단말 원격 등록 (6자리 PIN 코드 인증)
* **타임라인 기반 무중단 스케줄링**:
  * 24시간 Base Fallback 루프 및 특정 시간대 피크 이벤트 타임라인 스케줄링
  * 네트워크 단절 시에도 로컬 영구 자율 재생 보장
* **미디어 라이브러리 및 플레이리스트 저작**:
  * 대용량 비디오 및 이미지 파일 실시간 스트리밍 업로드 및 매직 바이트 바이너리 검증
  * 검색 및 페이지네이션 지원 플레이리스트 관리
* **단일 코드베이스 반응형 웹 UI**:
  * 다국어 지원 (한국어, English, 中文)
  * 데스크톱 관제 화면 및 모바일 현장 제어 화면 지원
* **배포 및 운영**:
  * Docker Compose 기반 온프레미스 및 클라우드 배포 지원
  * 원클릭 설치, 무중단 업그레이드 및 롤백 스크립트 제공
