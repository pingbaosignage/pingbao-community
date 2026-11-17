<div align="center">

# 📺 핑바오 (Pingbao / 屏宝)
### 엔터프라이즈 디지털 사이니지 전역 스마트 통합 관제 플랫폼 (Enterprise CMS)

**핑바오(Pingbao)**는 수천 대의 상용 스마트 디스플레이(삼성 SSSP/Tizen, Android, Linux, Windows)를 원격으로 통합 관제하고,  
24시간 타임라인 드래그 앤 드롭으로 콘텐츠를 무중단 자율 스케줄링하며, 밀리초 단위 다중 화면 동기화(SyncPlay) 및 완벽한 오프라인 자율 재생을 보장하는 엔터프라이즈 디지털 사이니지 플랫폼(CMS)입니다.

[![Language](https://img.shields.io/badge/Language-한국어%20|%20简体中文%20|%20English-brightgreen.svg)](#)
[![Release](https://img.shields.io/github/v/release/pingbaosignage/pingbao-community?color=blue)](https://github.com/pingbaosignage/pingbao-community/releases)
[![Issues](https://img.shields.io/github/issues/pingbaosignage/pingbao-community)](https://github.com/pingbaosignage/pingbao-community/issues)
[![License](https://img.shields.io/badge/license-Proprietary-orange.svg)](#)

<br/>

**[ 한국어 (KO) ]** | **[ 简体中文 (ZH) ](README.md)** | **[ English (EN) ](README.en.md)**

<br/><br/>

<img src="docs/assets/zh/dashboard.png" alt="핑바오 대시보드 화면" width="100%" />
<sub><b>▲ 핑바오(Pingbao) 실시간 관제 대시보드: 디스플레이 가동률, 스케줄 배포 진척률, 스토리지 점유율 및 시스템 헬스 관제 (오라클 클라우드 운영 환경 실측 데이터)</b></sub>

</div>

---

## 🌟 핵심 제품 특징 (Core Value Propositions)

1. **하드웨어 표준화 및 심층 IoT 원격 제어**
   * 삼성 스마트 사이니지 Standalone(Tizen 6.5 / SSSP 10 이상) 및 상용 Android 플레이어 완벽 지원.
   * 원격 패널 On/Off(백라이트 절전), 볼륨 조절, 단말 재부팅, 동작 온도 모니터링 및 실시간 송출 화면 1초 단위 캡처 회신.
2. **단절 무관·영구 자율 오프라인 재생 (Autonomous Offline Loop)**
   * 단말 로컬 스토리지 사전 프리캐싱과 SQLite 상태 머신을 통해 현장 네트워크가 장기간 단절되어도 스케줄에 맞춰 24/7/365 무중단 무결점 재생 보장.
3. **4계층 우선순위 타임라인 스케줄링 엔진 (4-Tier Smart Scheduling)**
   * `EMERGENCY`(비상 재난/VIP 의전) > `EVENT`(피크 타임 프로모션) > `REGULAR`(일상 타임파팅) > `DEFAULT`(24시간 배경 폴백) 4단계 자동 선점 구조.
   * Dayparting(조식/중식/티타임/석식/야간) 시각 도달 시 자동 무결점 전환 및 선점 종료 후 자연스러운 자동 복귀.
4. **다중 디스플레이 프레임 단위 동기화 재생 (SyncPlay)**
   * 로컬 네트워크 정밀 시계 동기화(NTP/PTP)를 통해 카운터 3~5연속 메뉴판 디스플레이의 동시 트랜지션 및 대화면 분할 영상 무결점 싱크 재생.
5. **정밀 재생 증빙 (Proof-of-Play - PoP)**
   * 1초 단위 실제 렌더링 프레임 및 시작/종료 타임스탬프 기록, 완주율(Completion Rate %) 산출 및 엑셀 감사 리포트 내보내기 지원.
6. **장애 자율 치유 및 자동화 커뮤니티 이슈 텔레메트리 (Automated Error Telemetry)**
   * 런타임 오류 자동 수집, PII/비밀키 마스킹, SHA-256 지문 중복 제거 후 GitHub 커뮤니티 이슈로 자동 리포팅하여 신속한 패치 수령 지원.

---

## 📸 9대 전 기능 워크스페이스 소개 및 공식 운영 화면

핑바오 관리 콘솔은 엔터프라이즈 운영에 필수적인 9개 독립 워크스페이스를 제공합니다:

### 1. 통합 대시보드 (Dashboard)
<img src="docs/assets/zh/dashboard.png" alt="대시보드" width="100%" />
전국 및 글로벌 매장의 스크린 가동률, 스케줄 동기화 완료율, 스토리지 사용량 및 이상 징후 알림을 한 화면에서 관제합니다.

---

### 2. 디스플레이 장치 관리 (Device Management)
<img src="docs/assets/zh/device-management.png" alt="장치 관리" width="100%" />
수만 대의 스크린을 조직별(본사/지사/매장), 방향(가로/세로), 온라인 상태별로 즉시 필터링하고 백라이트 On/Off, 원격 재부팅, 볼륨 조절 및 실시간 송출 캡처를 수행합니다.

---

### 3. 스케줄 편성 관리 (Schedules) —— [핵심 특화 엔진]
<img src="docs/assets/zh/schedules.png" alt="스케줄 관리" width="100%" />
좌측 마스터 스케줄 목록과 우측 24시간 타임라인이 결합된 직관적인 뷰를 통해, 일상 방송과 프로모션 방송을 시각적으로 결합하고 원클릭으로 현장에 즉시 배포합니다.

#### 상세 타임슬롯 편성 및 우선순위 매트릭스 (Schedule Detail & Slots)
<img src="docs/assets/zh/schedule-detail.png" alt="스케줄 상세 편성" width="100%" />
드래그 앤 드롭으로 슬롯 우선순위(Priority 10~100)를 재배치하고, 시간대 중복 시 실제 송출되는 유효 방송 윈도우(Effective Windows)를 시각적으로 검증합니다.

---

### 4. 재생목록 저작 (Playlists)
<img src="docs/assets/zh/playlists.png" alt="재생목록 관리" width="100%" />
비디오(MP4/WebM)와 이미지(PNG/JPG)를 자유롭게 혼합하고 이미지별 노출 초(예: 8초, 15초)와 부드러운 페이드(Crossfade) 전환 효과를 적용합니다.

---

### 5. 미디어 에셋 라이브러리 (Media Assets)
<img src="docs/assets/zh/media-assets.png" alt="미디어 에셋" width="100%" />
4K 60fps 대용량 영상의 분할 스트리밍 업로드, 바이너리 매직 바이트 위변조 검증, 자동 썸네일 생성 및 스케줄 사용 중 에셋에 대한 삭제 방지 락을 지원합니다.

---

### 6. 실시간 운영 모니터링 (Operations Monitoring)
<img src="docs/assets/zh/monitoring.png" alt="운영 관제" width="100%" />
장치별 롱폴링 응답 지연(RTT), 명령 대기 큐 및 전송 실패 항목을 실시간 추적하고, 원클릭 일괄 재전송(1-Click Batch Retry)을 통해 네트워크 장애를 신속히 극복합니다.

---

### 7. 정밀 재생 증빙 리포트 (Proof-of-Play & Analytics)
<img src="docs/assets/zh/reports.png" alt="재생 증빙 리포트" width="100%" />
단말 현지 시간 기준 1초 단위 실제 송출 사실 로그를 기반으로 완주율(Completion Rate %)을 집계하여 광고주 정산 및 감사용 엑셀 보고서를 생성합니다.

---

### 8. 사용자 및 다중 테넌트 조직 권한 (Users & RBAC)
<img src="docs/assets/zh/users.png" alt="사용자 및 권한 관리" width="100%" />
그룹 본사-리테일 센터-도시 플래그십-매장으로 이어지는 트리형 조직 구조와 CRUDX 단위 세분화 역할, RFC 6238 TOTP 기업 2단계 인증을 제공합니다.

---

### 9. 상용 라이선스 & 오류 보고 커뮤니티 (License & Error Telemetry)

| 상용 라이선스 (Commercial License) | 오류 보고 & 커뮤니티 (Error Telemetry) |
| :---: | :---: |
| <img src="docs/assets/zh/license.png" width="100%" /> | <img src="docs/assets/zh/telemetry.png" width="100%" /> |

* **Ed25519 전자서명 기반 상용 라이선스**: 에어갭(폐쇄망) 환경에서도 단말 활성화 쿼터를 안전하게 검증합니다.
* **오류 보고 & 커뮤니티 포털**:
  * 관리자 명시적 동의(Opt-in) 하에 서버 내부 오류를 자동 수집하고 민감정보(비밀번호, IP, 토큰)를 마스킹합니다.
  * SHA-256 지문으로 동일 오류를 중복 없이 취합하여 GitHub 커뮤니티 이슈로 전송하거나, 관리자가 콘솔 내에서 직접 수동 이슈/개선사항을 등록할 수 있습니다.

---

## 🎯 스케줄링 핵심 엔진 아키텍처 상세 (Deep Dive)

### 1. 4단계 우선순위 선점 계층
```
┌─────────────────────────────────────────────────────────────┐
│  Tier 1: EMERGENCY (P: 100 ~ 200)                           │
│  긴급 재난 방송 / 화재 대피 안내 / VIP 영접 전 화면 즉시 점유  │
├─────────────────────────────────────────────────────────────┤
│  Tier 2: EVENT (P: 50 ~ 99)                                 │
│  런치 피크 타임 타임세일 / 디너 파티 주류 프로모션             │
├─────────────────────────────────────────────────────────────┤
│  Tier 3: REGULAR (P: 10 ~ 49)                               │
│  일상 조식 메뉴판 / 오후 애프터눈 티 세트 롤링                 │
├─────────────────────────────────────────────────────────────┤
│  Tier 4: DEFAULT (P: 0)                                     │
│  24/7 배경 베이스 플레이리스트 (공백 발생 시 무조건 송출)        │
└─────────────────────────────────────────────────────────────┘
```

### 2. 표준 타임파팅 매트릭스 (Dayparting Matrix)
* **조식 (07:30 ~ 10:30)**: 모닝 세트 메뉴 및 모닝커피 프로모션 (`EVENT`, P:60)
* **오전 (10:30 ~ 11:30)**: 브랜드 이미지 및 멤버십 혜택 (`REGULAR`, P:20)
* **중식 피크 (11:30 ~ 14:00)**: 점심 특선 베스트셀러 및 주문 대기 가이드 (`EVENT`, P:80)
* **티타임 (14:00 ~ 17:30)**: 베이커리 & 디저트 음료 1+1 (`REGULAR`, P:30)
* **석식 파티 (17:30 ~ 21:30)**: 디너 패밀리 세트 & 수제 생맥주 행사 (`EVENT`, P:85)
* **야간/마감 (21:30 ~ 24:00)**: 마감 안내 및 저전력 브랜드 포스터 (`DEFAULT`, P:0)

### 3. SyncPlay 다중 디스플레이 프레임 동기화
* 로컬 네트워크 내 NTP 기준 시계를 통해 카운터 상단에 위치한 3~5대의 디스플레이가 동시 전환되며, 분할 렌더링을 통해 5760x1080 울트라 와이드 비디오를 끊김 없이 분할 재생합니다.

### 4. 오프라인 영구 자율 루프 (Autonomous Offline Loop)
* 스케줄 배포 시 모든 미디어 파일은 단말의 로컬 고속 SSD/eMMC에 사전 다운로드되며, 내장 RTC 시계를 기준으로 로컬 SQLite 상태 머신이 스스로 판단하여 재생하므로 365일 통신이 두절되어도 완벽히 작동합니다.

---

## 📋 전체 기능 리스트 (Full Feature Matrix)

| 모듈 영역 | 핵심 기능 및 상세 명세 | 기술 규격 및 차별점 |
| :--- | :--- | :--- |
| **1. 통합 대시보드** | 조직별 관제 요약, 스케줄 동기화율(%), 이상 징후 알림 리본, 스토리지 모니터링 | 멀티 테넌트 스코프 자동 분리, MinIO/S3 연동 |
| **2. 장치 관리 & 원격 제어** | 다차원 필터(인치/시리즈/IP), 상세 하드웨어 스펙, 전원 On/Off 타이머, 원격 제어 | 삼성 Tizen 6.5+ b2bapis, Redis 기반 영속 명령 큐 |
| **3. 타임라인 스케줄링** | 4계층 우선순위 선점, Dayparting 다중 트랙, 유효 방송 구간 분석, 원클릭 배포 | SQLite 로컬 자율 상태 머신, 무결점 폴백 복구 |
| **4. 멀티스크린 싱크 (SyncPlay)** | 3~5연속 전자메뉴판 밀리초 단위 동시 페이드 전환, 울트라 와이드 분할 렌더링 | 로컬 정밀 NTP 시계 동기화, 프레임 정렬 |
| **5. 플레이리스트 저작** | 직관적 D&D 저작, 이미지 노출 초 제어, 전환 효과(Fade/Cut), 총 재생시간 집계 | WebGL/CSS 하드웨어 가속 트랜지션, 가상 시뮬레이터 |
| **6. 미디어 라이브러리** | MP4/WebM/PNG 대용량 스트리밍 업로드, 매직 바이트 검증, 자동 메타 추출, 삭제 보호 | 멀티 청크 분할 스트리밍, SHA-256 서명 무결성 |
| **7. 운영 모니터링 & 진단** | 실시간 단말 상태·명령 큐 싱글 뷰, 원클릭 실패 필터, 일괄 재시도, 다포맷 리포트 | 1-Click Batch Retry, LLM 보고서용 Markdown 지원 |
| **8. 정밀 재생 증빙 (PoP)** | 1초 단위 실제 송출 증빙, 완주 vs 중도 중단 분석, 단말 현지 시간대 분석, 엑셀 Export | 15분 주기 암호화 배치 보고, 광고 정산용 완주율(%) |
| **9. 엔터프라이즈 보안 & RBAC** | 본사-지사-매장 조직 트리, CRUDX 매트릭스, 2FA(TOTP), 5-Strike 방어 | RFC 6238 TOTP, Argon2id, Ed25519 전자서명 라이선스 |
| **10. 라이선스 & 오류 텔레메트리** | Ed25519 비대칭 서명 활성화, 비정상 오류 PII 마스킹, SHA-256 중복제거 커뮤니티 전송 | 관리자 Opt-in 동의 기반, GitHub 커뮤니티 이슈 직동 |

---

## 📖 문서 및 매뉴얼 (Documentation)

* 📘 [사용자 매뉴얼 (User Guide)](docs/ko/user-guide.md) - 디스플레이 등록, 미디어 업로드, 플레이리스트 편성, 스케줄링 방법
* 🛠️ [관리자 및 설치 가이드 (Admin Guide)](docs/ko/admin-guide.md) - Docker 환경 구성, 환경변수 설정, 업그레이드 및 롤백
* ❓ [자주 묻는 질문 (FAQ)](docs/ko/faq.md) - 오프라인 재생, 지원 규격 및 트러블슈팅
* 📜 [변경 이력 (Changelog)](CHANGELOG.md) - 버전별 변경 사항 및 개선 내역

---

<div align="center">
<sub>Pingbao (핑바오 / 屏宝) © 2026. All rights reserved.</sub>
</div>
