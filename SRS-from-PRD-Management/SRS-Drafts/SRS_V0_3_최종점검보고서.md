# SRS_V0_3.md 최종 점검 보고서

| 항목 | 내용 |
| :--- | :--- |
| **점검 대상** | `SRS_V0_3.md` (SRS-001 v1.3, 1,203줄 / 75.8KB) |
| **점검일** | 2026-04-21 |
| **판정** | 🟡 **조건부 승인 — 잔여 정합성 오류 6건 수정 후 최종 확정** |

---

## 검증 축별 결과 요약

| # | 검증 축 | 판정 | 발견 사항 |
| :---: | :--- | :---: | :--- |
| 1 | Claude/Anthropic 잔존 | ✅ **Pass** | 0건 |
| 2 | Revision 상단↔하단 통일 | ✅ **Pass** | v1.3 통일 완료 |
| 3 | §3.3 ↔ §6.1 API 정합성 | ✅ **Pass** | 10개 엔드포인트 완전 일치 |
| 4 | UseCase ↔ REQ 매핑 | ✅ **Pass** | UC-01~15 전체 정합 |
| 5 | Traceability Matrix | ✅ **Pass** | Story↔REQ↔TC / Feature↔REQ / KPI↔REQ 완전 |
| 6 | 기술 스택 일관성 | ✅ **Pass** | Gemini/Prisma/Vercel AI SDK 통일 |
| 7 | **MVP 범위 ↔ 다이어그램/클래스 정합성** | 🔴 **Fail** | **6건 불일치 발견** |

---

## 🔴 발견된 정합성 오류 (6건)

> CON-03과 REQ-NF-016에서 비용 모니터링을 **"MVP 수동, Phase 2 자동"**으로 변경했으나, 관련 다이어그램·클래스·UseCase·외부 시스템 설명이 여전히 **"자동 차단"**을 전제하고 있음.

### 오류 1: §6.3.3 비용 차단 시퀀스 다이어그램

| 위치 | L1117~1141 |
| :--- | :--- |
| **현재** | 제목 "비용 **자동** 차단 플로우". `loop 매 API 호출 시`, `Monitor→API: API 호출 자동 중단 (하드캡)` |
| **문제** | CON-03/REQ-NF-016에서 MVP는 수동 모니터링으로 변경됨. 시퀀스가 자동 차단 로직을 표현 |
| **수정안** | 제목에 **(Phase 2)** 추가, 또는 시퀀스를 "수동 확인 → 수동 중단" 플로우로 교체 |

### 오류 2: §3.6 Component Table — Cost Monitor

| 위치 | L402 |
| :--- | :--- |
| **현재** | `Cost Monitor | TypeScript | Gemini API 비용 추적 및 **자동 차단** | CON-03` |
| **문제** | MVP에서는 자동 차단 없음 |
| **수정안** | "비용 추적 *(MVP: 수동 모니터링, Phase 2: 자동 차단)*" |

### 오류 3: §3.7 Class Diagram — CostMonitor 클래스

| 위치 | L518~527 |
| :--- | :--- |
| **현재** | `trackAPICall()`, `suspendAPICalls()`, `resumeAPICalls()` 등 자동 차단 메서드 포함 |
| **문제** | Phase 2 기능이 MVP 클래스 다이어그램에 포함 |
| **수정안** | 클래스 이름 뒤에 `*(Phase 2)*` 주석 추가, 또는 메서드를 Phase 2 섹션으로 분리 |

### 오류 4: §3.5 UseCase Diagram — UC-14

| 위치 | L286, L331 |
| :--- | :--- |
| **현재** | `UC-14 비용 **자동** 차단 ($100 하드캡)` / 관련 REQ: `REQ-NF-016` |
| **문제** | REQ-NF-016이 MVP에서 수동으로 변경됨 |
| **수정안** | UC-14 라벨에 "*(Phase 2)*" 추가 |

### 오류 5: §3.1 EXT-04 GA4 장애 시 우회 전략

| 위치 | L151 |
| :--- | :--- |
| **현재** | "GA4 장애 시 **Supabase `event_logs` 테이블**에 이벤트를 직접 기록" |
| **문제** | EVENT_LOG 엔터티가 Phase 2로 이관됨. MVP에서는 해당 테이블이 존재하지 않음 |
| **수정안** | "*(MVP: GA4 장애 시 수동 기록. event_logs 테이블은 Phase 2)*" |

### 오류 6: §4.2.7 REQ-NF-027 KPI 측정 공식

| 위치 | L720 |
| :--- | :--- |
| **현재** | `(GA4 cta_click / GA4 **report_scroll_complete**) × 100 ≥ 10%` |
| **문제** | REQ-FUNC-011에서 `report_scroll_complete` 이벤트를 Phase 2로 이관함. MVP에서는 분모 이벤트가 존재하지 않아 KPI 산출 불가 |
| **수정안** | MVP 측정 공식을 `(GA4 cta_click / GA4 page_view) × 100` 등으로 대체하거나, 해당 KPI를 Phase 2로 이관 |

---

## ✅ 정상 검증된 항목 (상세)

### 기술 스택 통일성
- `Gemini API (via Vercel AI SDK)`: 본문·다이어그램·API 설명 전체 일치 ✅
- `Prisma ORM → Supabase PostgreSQL`: §3.3, §6.1, §6.2 전체 일치 ✅
- `Next.js Middleware`: REQ-NF-013, REQ-FUNC-016, 시퀀스 전체 일치 ✅
- `Claude` / `Anthropic` 잔존: 0건 ✅

### 버전 정합성
- 상단 Revision: v1.3 ✅
- 하단 문서 끝: v1.3 ✅

### API 정합성 (§3.3 ↔ §6.1)
- 10개 엔드포인트 설명·인증·매핑 REQ 완전 일치 ✅

### Traceability
- Story 1~4 → REQ → TC: 완전 매핑 ✅
- Feature F1~F6 → REQ: 완전 매핑 ✅
- KPI 6건 → REQ: 완전 매핑 ✅ *(단, 보조 KPI 5 측정 공식 오류 — 오류 6 참조)*

### Must/Should 분류
- REQ-FUNC-005, 007, 018: Should (Phase 2) ✅
- REQ-FUNC-001: "최소 20개, 목표 168개" ✅
- REQ-FUNC-011: cta_click만 MVP ✅
- REQ-FUNC-023: 외부 도구 직접 조회 ✅
- REQ-NF-008: 주 1회 수동 백업 ✅
- REQ-NF-016: 수동 모니터링 ✅
- EVENT_LOG: Phase 2 마킹 ✅

---

## 수정 권고 요약

| # | 위치 | 심각도 | 수정 유형 | 예상 공수 |
| :---: | :--- | :---: | :--- | :---: |
| 1 | §6.3.3 시퀀스 제목+다이어그램 | 🟡 Mid | 제목에 "(Phase 2)" 추가 또는 시퀀스 교체 | 5분 |
| 2 | §3.6 Component 표 L402 | 🟢 Low | 역할 설명 수정 | 1분 |
| 3 | §3.7 Class CostMonitor | 🟢 Low | 클래스에 Phase 2 주석 | 1분 |
| 4 | §3.5 UC-14 라벨 | 🟢 Low | 라벨에 Phase 2 추가 | 1분 |
| 5 | §3.1 EXT-04 우회 전략 | 🟡 Mid | event_logs → 수동 기록 | 2분 |
| 6 | §4.2.7 REQ-NF-027 공식 | 🟡 Mid | 분모 이벤트 교체 | 2분 |

**총 예상 수정 시간: ~12분**

---

## 최종 판정

> 🟡 **조건부 승인**: SRS_V0_3.md는 기술 스택 전환·MVP 범위 최적화·핵심 정합성 면에서 **95% 완성 상태**이다.
> 위 6건의 "자동 ↔ 수동" 불일치만 수정하면 **최종 SRS로 확정 가능**하다.
> 6건 모두 텍스트 레벨 수정이며, 아키텍처·기능·비용에 영향 없음.
