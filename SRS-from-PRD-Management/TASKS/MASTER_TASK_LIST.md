# 5060 프리미엄 브랜드 매니지먼트 — 개발 태스크 마스터 리스트

> **Source:** SRS-001 v1.3 (SRS_v1.md)  
> **Generated:** 2026-04-22  
> **총 태스크:** 104건 (DB 8 | API 10 | Mock 7 | Query 6 | Command 15 | Test 18 | Infra 8 | Sec 5 | Perf 2 | Mon 5 | UI 18 | Ext 2)  
> **상세 파일:** [Step1](./TASK_LIST_STEP1_CONTRACT.md) | [Step2](./TASK_LIST_STEP2_LOGIC.md) | [Step3](./TASK_LIST_STEP3_TEST.md) | [Step4](./TASK_LIST_STEP4_NFR_UI.md)

---

## 전체 태스크 통합 테이블

### Epic 1: Data Layer (DB 스키마)

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| DB-001 | Data Layer | Prisma 초기화 + datasource 듀얼 설정 | CON-07,08 | None | M |
| DB-002 | Data Layer | LEAD 테이블 스키마·마이그레이션 | §6.2.1 | DB-001 | L |
| DB-003 | Data Layer | DIAGNOSIS 테이블 스키마·마이그레이션 | §6.2.2 | DB-002 | L |
| DB-004 | Data Layer | CLIENT 테이블 스키마·마이그레이션 | §6.2.3 | DB-002 | L |
| DB-005 | Data Layer | PROJECT 테이블 스키마·마이그레이션 | §6.2.4 | DB-004 | L |
| DB-006 | Data Layer | BRIEF 테이블 스키마·마이그레이션 | §6.2.5 | DB-005 | M |
| DB-007 | Data Layer | RETAINER 테이블 스키마·마이그레이션 | §6.2.6 | DB-004 | L |
| DB-008 | Data Layer | PrismaService 싱글톤 래퍼 | §3.7 | DB-001 | L |

### Epic 2: API Contract (DTO·에러코드)

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| API-001 | API Contract | POST /api/diagnose DTO·에러코드 | §6.1 API-01 | None | M |
| API-002 | API Contract | POST /api/leads DTO·에러코드 | §6.1 API-02 | None | L |
| API-003 | API Contract | POST /api/generate-brief DTO·에러코드 | §6.1 API-03 | None | M |
| API-004 | API Contract | GET /api/projects/:id DTO | §6.1 API-04 | None | L |
| API-005 | API Contract | PATCH /api/briefs/:id DTO | §6.1 API-05 | None | L |
| API-006 | API Contract | GET /api/leads DTO | §6.1 API-06 | None | L |
| API-007 | API Contract | POST /api/validate-diagnosis DTO | §6.1 API-07 | None | L |
| API-008 | API Contract | PATCH /api/leads/:id/status DTO | §6.1 API-08 | None | L |
| API-009 | API Contract | POST /api/briefs/:id/hallucination-tag DTO | §6.1 API-09 | None | L |
| API-010 | API Contract | GET /api/dashboard/metrics DTO | §6.1 API-10 | None | M |

### Epic 3: Mock Data

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| MOCK-001 | Mock | 진단 5문항 Mock + 불성실 패턴 샘플 | F2 | API-001 | L |
| MOCK-002 | Mock | 진단 리포트 Mock JSON | F2 | API-001 | L |
| MOCK-003 | Mock | 마스터 브리프 AI 출력 Mock JSON | F1 | API-003 | M |
| MOCK-004 | Mock | 리드 목록/상태 Mock | F4 | API-002,006 | L |
| MOCK-005 | Mock | 프로젝트 대시보드 Mock (6건) | F3 | API-004 | L |
| MOCK-006 | Mock | KPI 대시보드 집계 Mock | F6 | API-010 | L |
| MOCK-007 | Mock | Fallback용 샘플 리포트 템플릿 3종 | EXT-01 | MOCK-002 | M |

### Epic 4: Feature/Query (Read)

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| QRY-001 | F2 진단 | 진단 리포트 웹뷰 조회 | REQ-FUNC-010 | DB-003, API-001 | M |
| QRY-002 | F3 관리자 | 프로젝트 목록 대시보드 조회 | REQ-FUNC-017 | DB-005, API-004 | M |
| QRY-003 | F4 리드 | 리드 목록 필터 조회 | REQ-FUNC-021,023 | DB-002, API-006 | M |
| QRY-004 | F6 리테이너 | 리테이너 전환율 산출 조회 | REQ-FUNC-027 | DB-004,007, API-010 | M |
| QRY-005 | F3 관리자 | KPI 메트릭 집계 조회 | REQ-FUNC-023,027 | DB-002,006, API-010 | H |
| QRY-006 | F5 트래킹 | 고객 프로젝트 진척 조회 | REQ-FUNC-024 | DB-005, API-004 | M |

### Epic 5: Feature/Command (Write)

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| CMD-001 | F2 진단 | 진단 응답 → 검증 → AI 리포트 생성 → DB 저장 | REQ-FUNC-008,009 | DB-002,003, API-001 | H |
| CMD-002 | F2 진단 | 불성실 응답 필터링 로직 (정확도≥95%) | REQ-FUNC-009 | API-007 | M |
| CMD-003 | F4 리드 | 상담 신청 → 리드 DB 저장 (유효성 검증) | REQ-FUNC-019,020 | DB-002, API-002 | M |
| CMD-004 | F4 리드 | 리드 상태 변경 + 일시 자동 기록 | REQ-FUNC-021 | DB-002, API-008 | L |
| CMD-005 | F1 브리프 | 인터뷰 입력 검증 (20문항/500자 차단) | REQ-FUNC-003,015 | API-003 | M |
| CMD-006 | F1 브리프 | AI 마스터 브리프 생성 (Streaming) | REQ-FUNC-001,002,004 | DB-006, API-003, CMD-005 | H |
| CMD-007 | F1 브리프 | 브리프 검수 수정 + 버전 자동 증가 | REQ-FUNC-006 | DB-006, API-005 | M |
| CMD-008 | F1 브리프 | 환각 문장 태깅 저장 (Phase 2) | REQ-FUNC-007,018 | DB-006, API-009 | M |
| CMD-009 | F3 관리자 | 녹취→B2B 카피 AI 변환 (1,000자 검증) | REQ-FUNC-014,015 | API-003, CMD-005 | H |
| CMD-010 | F3 관리자 | Raw 텍스트→AI 일괄 변환 | REQ-FUNC-013 | DB-006, CMD-006 | H |
| CMD-011 | F6 리테이너 | 구독 등록/갱신/정지/해지 + 이력 | REQ-FUNC-026 | DB-007 | M |
| CMD-012 | 공통 | 법인 정보 입력 저장 | REQ-FUNC-029 | DB-004 | L |
| CMD-013 | F4 리드 | 리드 적재 실패 Slack 알림 | REQ-FUNC-022 | CMD-003 | M |
| CMD-014 | 공통 | Gemini 장애 Fallback + Slack 알림 | REQ-FUNC-030 | CMD-001, CMD-006 | M |
| CMD-015 | F5 트래킹 | 대시보드 접속 이벤트 기록 | REQ-FUNC-025 | QRY-006 | L |

### Epic 6: Test (AC→테스트 코드)

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| TEST-001 | F1 | 20문항 미만 400 에러 + Gemini 미호출 | REQ-FUNC-003 | CMD-005 | M |
| TEST-002 | F1 | 브리프 구조화 출력 검증 | REQ-FUNC-004 | CMD-006 | M |
| TEST-003 | F1 | 버전 증가 + 이전 보존 검증 | REQ-FUNC-006 | CMD-007 | M |
| TEST-004 | F1 | Streaming 빈 화면 0초 검증 | REQ-FUNC-002 | CMD-006 | H |
| TEST-005 | F2 | 10초 이내 리포트 생성 검증 | REQ-FUNC-008 | CMD-001 | M |
| TEST-006 | F2 | 동일 선택 422 + 불성실 메시지 검증 | REQ-FUNC-009 | CMD-002 | M |
| TEST-007 | F2 | 불성실 필터 정확도≥95% 검증 | REQ-FUNC-009 | CMD-002 | H |
| TEST-008 | F2 | CTA 노출 + 폼 이동 검증 | REQ-FUNC-010 | QRY-001 | L |
| TEST-009 | F2 | GA4 cta_click 전송 검증 | REQ-FUNC-011 | UI-005 | M |
| TEST-010 | F4 | DB 저장 성공률≥99.5% 검증 | REQ-FUNC-019 | CMD-003 | M |
| TEST-011 | F4 | 인라인 에러 200ms + DB 0건 검증 | REQ-FUNC-020 | CMD-003 | M |
| TEST-012 | F4 | 상태 변경 + 일시 기록 검증 | REQ-FUNC-021 | CMD-004 | L |
| TEST-013 | F4 | 적재 실패 Slack 1분 이내 검증 | REQ-FUNC-022 | CMD-013 | M |
| TEST-014 | F3 | 500자 이하 차단 검증 | REQ-FUNC-015 | CMD-009 | M |
| TEST-015 | F3 | IP 차단 100% + 로그 + 알림 검증 | REQ-FUNC-016 | SEC-001 | H |
| TEST-016 | 공통 | Fallback 메시지 + Slack 알림 검증 | REQ-FUNC-030 | CMD-014 | M |
| TEST-017 | F6 | 구독 이력 추적 검증 | REQ-FUNC-026 | CMD-011 | M |
| TEST-018 | 공통 | 법인 정보 저장/조회 검증 | REQ-FUNC-029 | CMD-012 | L |

### Epic 7: Infrastructure

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| INFRA-001 | Infra | Next.js App Router 프로젝트 초기화 | CON-07,09 | None | M |
| INFRA-002 | Infra | Vercel 배포 파이프라인 + 환경변수 | EXT-03 | INFRA-001 | M |
| INFRA-003 | Infra | Vercel AI SDK + Gemini provider 연동 | EXT-01, CON-05 | INFRA-001 | M |
| INFRA-004 | Infra | Supabase PostgreSQL + Prisma 운영 연결 | EXT-02, CON-08 | DB-001, INFRA-002 | M |
| INFRA-005 | Infra | Slack Webhook AlertService 구현 | EXT-06 | INFRA-001 | L |
| INFRA-006 | Infra | GA4 gtag.js 연동 + 이벤트 설정 | EXT-04 | INFRA-001 | L |
| INFRA-007 | Infra | Edge Runtime (generate-brief Streaming) | CON-01 | INFRA-003, CMD-006 | M |
| INFRA-008 | Infra | pg_dump 주 1회 백업 스크립트 | REQ-NF-008 | INFRA-004 | L |

### Epic 8: Security

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| SEC-001 | Security | Middleware IP 화이트리스트 + Admin 접근 제어 | REQ-FUNC-016, REQ-NF-013 | INFRA-001 | H |
| SEC-002 | Security | API Key 환경변수 전용 + git grep 스캔 | REQ-NF-012 | INFRA-002 | L |
| SEC-003 | Security | TLS 1.3 + AES-256 암호화 설정 | REQ-NF-010 | INFRA-004 | M |
| SEC-004 | Security | 개인정보처리방침 페이지 | REQ-NF-011 | INFRA-001 | L |
| SEC-005 | Security | OWASP ZAP 분기 스캔 체크리스트 | REQ-NF-014 | INFRA-002 | L |

### Epic 9: Performance · Monitoring

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| PERF-001 | Performance | API Rate Limiting (IP당 차등) | REQ-NF-004, §6.1 | INFRA-001, SEC-001 | M |
| PERF-002 | Performance | 랜딩페이지 LCP ≤ 2.5초 최적화 | REQ-NF-003 | UI-001 | M |
| MON-001 | Monitoring | 5xx ≥ 3건/5분 Slack 알림 | REQ-NF-018 | INFRA-005 | M |
| MON-002 | Monitoring | p95 > 20초 Slack 알림 | REQ-NF-019 | INFRA-005 | M |
| MON-003 | Monitoring | 환각율 > 10% Sprint 트리거 | REQ-NF-020 | CMD-008, INFRA-005 | M |
| MON-004 | Monitoring | 불성실 > 20% 문항 재설계 트리거 | REQ-NF-023 | CMD-002, INFRA-005 | L |
| MON-005 | Monitoring | DB 80% Slack 알림 + 아카이빙 | REQ-NF-017 | INFRA-004, INFRA-005 | M |

### Epic 10: UI/Frontend

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| UI-001 | Public Web | 랜딩페이지 (소개 + Before-After) | REQ-FUNC-028 | INFRA-001 | H |
| UI-002 | Public Web | 5문항 진단 폼 UI | REQ-FUNC-012 | INFRA-001, MOCK-001 | M |
| UI-003 | Public Web | 진단 리포트 웹뷰 | REQ-FUNC-010 | MOCK-002 | H |
| UI-004 | Public Web | 상담 신청 폼 (인라인 검증) | REQ-FUNC-020 | INFRA-001 | M |
| UI-005 | Public Web | CTA + GA4 이벤트 연동 | REQ-FUNC-010,011 | UI-003, INFRA-006 | L |
| UI-006 | Admin Web | 관리자 콘솔 레이아웃 (shadcn/ui) | CL-03, CON-09 | INFRA-001, SEC-001 | H |
| UI-007 | Admin Web | 인터뷰 입력 + AI 변환 출력 UI | REQ-FUNC-013 | UI-006, MOCK-003 | M |
| UI-008 | Admin Web | 브리프 Streaming + 검수 UI | REQ-FUNC-002,006 | UI-006, MOCK-003 | H |
| UI-009 | Admin Web | 프로젝트 대시보드 UI | REQ-FUNC-017 | UI-006, MOCK-005 | M |
| UI-010 | Admin Web | 리드 관리 + 상태 변경 UI | REQ-FUNC-021 | UI-006, MOCK-004 | M |
| UI-011 | Admin Web | KPI 대시보드 UI | REQ-FUNC-023,027 | UI-006, MOCK-006 | M |
| UI-012 | Admin Web | 환각 태깅 UI (Phase 2) | REQ-FUNC-018 | UI-008 | M |
| UI-013 | Admin Web | 녹취→B2B 카피 변환 UI | REQ-FUNC-014 | UI-006 | M |
| UI-014 | Admin Web | 법인 정보 입력 UI | REQ-FUNC-029 | UI-006 | L |
| UI-015 | Admin Web | 리테이너 구독 관리 UI | REQ-FUNC-026 | UI-006 | M |
| UI-016 | Public Web | 고객 진척 대시보드 UI (Should) | REQ-FUNC-024 | MOCK-005 | M |
| UI-017 | Public Web | 개인정보처리방침 페이지 | REQ-NF-011 | INFRA-001 | L |
| UI-018 | Public Web | Fallback 메시지 UI | REQ-FUNC-030 | UI-002,003 | L |

### Epic 11: Scalability

| Task ID | Epic | Feature | SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| EXT-001 | Scalability | AI SDK provider 추상화 (모델 전환 ≤1h) | REQ-NF-022 | INFRA-003 | M |
| EXT-002 | Scalability | Gemini fallback provider 자동 전환 | EXT-01 | EXT-001 | H |

---

## 의존성 DAG (실행 순서)

```
Phase 0 — 병렬 시작 (의존성 없음)
├─ INFRA-001  Next.js App Router 초기화
├─ DB-001     Prisma 프로젝트 초기화
└─ API-001~010  전체 DTO/에러코드 정의 (10건 병렬)

Phase 1 — Phase 0 완료 후
├─ DB-002~008   스키마 마이그레이션 (순차 체인)
├─ INFRA-002~006  인프라 연동
├─ SEC-001~005    보안 설정
├─ MOCK-001~007   Mock 데이터
└─ UI-001~006     레이아웃·기본 폼 UI

Phase 2 — Phase 1 완료 후
├─ CMD-001~015   비즈니스 로직 (Write)
├─ QRY-001~006   조회 로직 (Read)
├─ UI-007~018    기능 UI
├─ PERF-001~002  성능 최적화
└─ INFRA-007~008 Edge Runtime·백업

Phase 3 — Phase 2 완료 후
├─ TEST-001~018  테스트 코드
├─ MON-001~005   모니터링 알림
├─ EXT-001~002   확장성
└─ 통합 테스트 + E2E
```

---

## 요약 통계

| 카테고리 | 건수 | H | M | L |
|---|---|---|---|---|
| DB 스키마 | 8 | 0 | 2 | 6 |
| API Contract | 10 | 0 | 3 | 7 |
| Mock 데이터 | 7 | 0 | 2 | 5 |
| Query (Read) | 6 | 1 | 5 | 0 |
| Command (Write) | 15 | 4 | 7 | 4 |
| Test | 18 | 3 | 12 | 3 |
| Infra | 8 | 0 | 5 | 3 |
| Security | 5 | 1 | 1 | 3 |
| Performance | 2 | 0 | 2 | 0 |
| Monitoring | 5 | 0 | 4 | 1 |
| UI/Frontend | 18 | 4 | 10 | 4 |
| Scalability | 2 | 1 | 1 | 0 |
| **합계** | **104** | **14** | **54** | **36** |
