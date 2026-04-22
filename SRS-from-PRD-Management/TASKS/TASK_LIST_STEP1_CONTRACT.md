# Step 1. 계약·데이터 명세 태스크

## 1-A. 데이터베이스 스키마 태스크

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| DB-001 | Data Layer | Prisma 프로젝트 초기화 및 datasource 설정 (SQLite/PostgreSQL 듀얼) | §1.2.3 CON-07,08 | None | M |
| DB-002 | Data Layer | LEAD 테이블 스키마 + 마이그레이션 (LeadStatus Enum 포함) | §6.2.1, §6.2.8 | DB-001 | L |
| DB-003 | Data Layer | DIAGNOSIS 테이블 스키마 + 마이그레이션 (JSONB answers/report) | §6.2.2, §6.2.8 | DB-002 | L |
| DB-004 | Data Layer | CLIENT 테이블 스키마 + 마이그레이션 (Package Enum, 법인정보) | §6.2.3, §6.2.8 | DB-002 | L |
| DB-005 | Data Layer | PROJECT 테이블 스키마 + 마이그레이션 (ProjectStatus Enum) | §6.2.4, §6.2.8 | DB-004 | L |
| DB-006 | Data Layer | BRIEF 테이블 스키마 + 마이그레이션 (JSONB ai_output/hallucination_tags) | §6.2.5, §6.2.8 | DB-005 | M |
| DB-007 | Data Layer | RETAINER 테이블 스키마 + 마이그레이션 (RetainerStatus Enum) | §6.2.6, §6.2.8 | DB-004 | L |
| DB-008 | Data Layer | PrismaService 싱글톤 클라이언트 래퍼 구현 | §3.7 Class Diagram | DB-001 | L |

## 1-B. API 계약 (DTO/에러코드) 태스크

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| API-001 | API Contract | POST /api/diagnose Request/Response DTO 및 에러코드 정의 (422 불성실, 500 AI장애) | §3.3, §6.1 API-01 | None | M |
| API-002 | API Contract | POST /api/leads Request/Response DTO 및 에러코드 정의 (400 유효성) | §3.3, §6.1 API-02 | None | L |
| API-003 | API Contract | POST /api/generate-brief Request/Response DTO 및 에러코드 정의 (400 문항부족/입력부족) | §3.3, §6.1 API-03 | None | M |
| API-004 | API Contract | GET /api/projects/:id Response DTO 정의 | §3.3, §6.1 API-04 | None | L |
| API-005 | API Contract | PATCH /api/briefs/:id Request/Response DTO 정의 | §3.3, §6.1 API-05 | None | L |
| API-006 | API Contract | GET /api/leads Request(필터)/Response DTO 정의 | §3.3, §6.1 API-06 | None | L |
| API-007 | API Contract | POST /api/validate-diagnosis Request/Response DTO 정의 | §3.3, §6.1 API-07 | None | L |
| API-008 | API Contract | PATCH /api/leads/:id/status Request/Response DTO 정의 | §3.3, §6.1 API-08 | None | L |
| API-009 | API Contract | POST /api/briefs/:id/hallucination-tag Request/Response DTO 정의 | §3.3, §6.1 API-09 | None | L |
| API-010 | API Contract | GET /api/dashboard/metrics Response DTO 정의 (KPI 집계 구조) | §3.3, §6.1 API-10 | None | M |

## 1-C. Mock 데이터 태스크

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| MOCK-001 | Mock | 진단 폼 5문항 Mock 데이터 + 불성실 패턴 샘플 | §4.1.2 F2 | API-001 | L |
| MOCK-002 | Mock | 진단 리포트 (브랜드 지수/약점 분석) Mock JSON | §4.1.2 F2 | API-001 | L |
| MOCK-003 | Mock | 마스터 브리프 AI 출력 Mock JSON (가치선언문/타깃/강의주제) | §4.1.1 F1 | API-003 | M |
| MOCK-004 | Mock | 리드 목록/상태 Mock 데이터 | §4.1.4 F4 | API-002, API-006 | L |
| MOCK-005 | Mock | 프로젝트 대시보드 Mock 데이터 (6건 병렬) | §4.1.3 F3 | API-004 | L |
| MOCK-006 | Mock | KPI 대시보드 집계 Mock 데이터 | §4.1.6 F6 | API-010 | L |
| MOCK-007 | Mock | Gemini API Fallback용 업종별 샘플 진단 리포트 템플릿 3종 | §3.1 EXT-01 | MOCK-002 | M |
