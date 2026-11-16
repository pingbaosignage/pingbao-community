<div align="center">

# 📺 핑바오 (Pingbao / 屏宝)
### Enterprise Digital Signage Management Platform

**핑바오(Pingbao)**는 수천 대의 스마트 사이니지 디스플레이를 원격으로 통합 관제하고,  
타임라인 드래그 앤 드롭으로 콘텐츠를 무중단 자율 스케줄링하는 엔터프라이즈 디지털 사이니지 플랫폼(CMS)입니다.

[![Language](https://img.shields.io/badge/Language-한국어%20|%20English%20|%20简体中文-brightgreen.svg)](#)
[![Release](https://img.shields.io/github/v/release/pingbaosignage/pingbao-community?color=blue)](https://github.com/pingbaosignage/pingbao-community/releases)
[![Issues](https://img.shields.io/github/issues/pingbaosignage/pingbao-community)](https://github.com/pingbaosignage/pingbao-community/issues)
[![License](https://img.shields.io/badge/license-Proprietary-orange.svg)](#)

<br/>

**[ 한국어 ]** | **[ English ](README.en.md)** | **[ 简体中文 ](README.zh.md)**

<br/><br/>

<img src="docs/assets/ko/dashboard.png" alt="핑바오 대시보드 화면" width="100%" />
<sub><b>▲ 핑바오(Pingbao) 대시보드: 디스플레이 가동률, 스케줄 배포 현황 및 시스템 헬스 관제</b></sub>

</div>

---

## 🌟 주요 특징 (Key Features)

* **하드웨어 표준화 및 심층 원격 제어**
  * 삼성 스마트 사이니지 Standalone(Tizen 6.5 / SSSP 10 이상) 완벽 지원
  * 원격 화면 On/Off, 볼륨 조절, 단말 재부팅 및 실시간 화면 캡처 모니터링
* **기간 제한 없는 자율 반복 스케줄링 (Autonomous Offline Loop)**
  * 네트워크가 장시간 끊어져도 디스플레이 단말이 로컬 캐시를 통해 24시간 365일 무중단 자율 재생
  * 24시간 Base Fallback 루프 및 특정 시간대/요일 우선 이벤트 스케줄 지원
* **고성능 미디어 & 플레이리스트 파이프라인**
  * 대용량 고화질 비디오(MP4, WebM) 및 이미지 실시간 스트리밍 업로드
  * 매직 바이트 바이너리 시그니처 무결성 검증
* **반응형 관제 웹 콘솔 & 3개 국어 완벽 지원**
  * 데스크톱 관제실 대화면부터 모바일 현장 제어 화면까지 완벽 반응형 UI
  * **한국어, English, 简体中文** 실시간 다국어 전환 지원
* **검증된 엔터프라이즈 경량 아키텍처**
  * PostgreSQL + Redis + MinIO/S3 기반 고효율 구조
  * 온프레미스(Docker / Synology DSM) 및 클라우드 배포 지원

---

## 📸 주요 시스템 화면 (한국어 UI)

| 대시보드 (Dashboard) | 장치 관리 (Fleet Management) |
| :---: | :---: |
| <img src="docs/assets/ko/dashboard.png" width="100%" /> | <img src="docs/assets/ko/device-management.png" width="100%" /> |
| **모니터링 관제 (Monitoring / Operations)** | **스케줄 관리 (Timeline Scheduling)** |
| <img src="docs/assets/ko/monitoring.png" width="100%" /> | <img src="docs/assets/ko/schedules.png" width="100%" /> |

---

## 📋 전체 기능 리스트 (Full Feature Matrix)

> 상세한 세부 기능 명세는 📘 [사용자 매뉴얼 전체 기능 명세표](docs/ko/user-guide.md#5-핑바오-전체-풀기능-명세표-full-feature-matrix)에서 확인하실 수 있습니다.

| 모듈 영역 | 핵심 기능 및 상세 명세 | 기술 규격 및 차별점 |
| :--- | :--- | :--- |
| **1. 통합 대시보드** | 조직별 관제 요약, 스케줄 동기화율(%), 이상 징후 알림 리본, 스토리지 모니터링 | 멀티 테넌트 스코프 자동 분리, MinIO/S3 연동 |
| **2. 장치 관리 & 원격 제어** | 다차원 필터(인치/시리즈/IP), 상세 하드웨어 스펙, 전원 On/Off 타이머, 원격 제어 | 삼성 Tizen 6.5+ b2bapis, Redis 기반 영속 명령 큐 |
| **3. 운영 모니터링 & 진단** | 실시간 단말 상태·명령 큐 싱글 뷰, 원클릭 실패 필터, 일괄 재시도, 다포맷 리포트 | 1-Click Batch Retry, LLM 보고서용 Markdown 지원 |
| **4. 미디어 라이브러리** | MP4/WebM/PNG 대용량 스트리밍 업로드, 매직 바이트 검증, 자동 메타 추출, 삭제 보호 | 멀티 청크 분할 스트리밍, SHA-256 서명 무결성 |
| **5. 플레이리스트 저작** | 직관적 D&D 저작, 이미지 노출 초 제어, 전환 효과(Fade/Cut), 스케줄 연결 무결성 | WebGL/CSS 하드웨어 가속 트랜지션, 가상 시뮬레이터 |
| **6. 타임라인 스케줄링** | 24시간 Base Fallback 루프, 피크 이벤트 타임라인, 🚨 긴급 비상 방송 오버라이드 | Autonomous Offline Loop (단절 시 영구 자율 재생) |
| **7. 정밀 재생 증빙 (PoP)** | 1초 단위 실제 송출 증빙, 완주 vs 중도 중단 분석, 단말 현지 시간대 분석, 엑셀 Export | 15분 주기 암호화 배치 보고, 광고 정산용 완주율(%) |
| **8. 엔터프라이즈 보안** | 본사-지사-매장 조직 트리, RBAC 매트릭스, 2FA(TOTP), 5-Strike 방어, 에어갭 라이선스 | RFC 6238 TOTP, Argon2id, Ed25519 전자서명 라이선스 |

---

## 📖 문서 및 매뉴얼 (Documentation)

* 📘 [사용자 매뉴얼 (User Guide)](docs/ko/user-guide.md) - 디스플레이 등록, 미디어 업로드, 플레이리스트 편성, 스케줄링 방법
* 🛠️ [관리자 및 설치 가이드 (Admin Guide)](docs/ko/admin-guide.md) - Docker 환경 구성, 환경변수 설정, 업그레이드 및 롤백
* ❓ [자주 묻는 질문 (FAQ)](docs/ko/faq.md) - 오프라인 재생, 지원 규격 및 트러블슈팅
* 📜 [변경 이력 (Changelog)](CHANGELOG.md) - 버전별 변경 사항 및 개선 내역

---

## 💡 이슈 등록 및 제안 (Issue & Feature Request)

핑바오를 사용하시면서 버그를 발견하셨거나 새로운 기능이 필요하신 경우 언제든 이슈를 등록해 주세요! (한국어, English, 中文 모두 작성 가능합니다)

* [🐛 버그 리포트 등록하기](https://github.com/pingbaosignage/pingbao-community/issues/new?template=bug_report.md)
* [💡 기능 제안 등록하기](https://github.com/pingbaosignage/pingbao-community/issues/new?template=feature_request.md)

### 이슈 처리 워크플로우
```mermaid
flowchart LR
    A["이슈 등록\n(User)"] --> B["검토 및 승인\n(Admin: accepted)"]
    B --> C["AI 에이전트\n자동 브랜치 & 개발"]
    C --> D["자동 테스트 & 빌드\n(CI/CD)"]
    D --> E["해결 및 릴리즈 배포\n(Release Notes)"]
```

---

## 🚀 릴리즈 및 다운로드 (Releases)

최신 릴리즈 패키지 및 릴리즈 노트는 **[Releases](https://github.com/pingbaosignage/pingbao-community/releases)** 탭에서 확인하실 수 있습니다.
