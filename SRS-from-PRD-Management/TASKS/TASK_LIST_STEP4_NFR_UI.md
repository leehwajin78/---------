# Step 4. 비기능 제약(NFR) + UI/프론트엔드 + 인프라 태스크 및 의존성

## 4-A. 인프라/보안/성능 태스크

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| INFRA-001 | Infra | Next.js App Router 프로젝트 초기화 (TypeScript, Tailwind, shadcn/ui) | §3.6 CON-07,09 | None | M |
| INFRA-002 | Infra | Vercel 배포 파이프라인 구성 (Git Push 자동 배포, 환경변수) | §3.1 EXT-03, §3.6 | INFRA-001 | M |
| INFRA-003 | Infra | Vercel AI SDK 설정 + Gemini API provider 연동 | §3.1 EXT-01, CON-05 | INFRA-001 | M |
| INFRA-004 | Infra | Supabase PostgreSQL 연결 + Prisma datasource 운영 설정 | §3.1 EXT-02, CON-08 | DB-001, INFRA-002 | M |
| INFRA-005 | Infra | Slack Webhook 알림 서비스 구현 (AlertService) | §3.1 EXT-06, §3.7 | INFRA-001 | L |
| INFRA-006 | Infra | GA4 gtag.js 연동 + 이벤트 트래킹 설정 (page_view, cta_click) | §3.1 EXT-04, REQ-FUNC-011 | INFRA-001 | L |
| INFRA-007 | Infra | Edge Runtime 적용 (/api/generate-brief Streaming 엔드포인트) | CON-01 | INFRA-003, CMD-006 | M |
| INFRA-008 | Infra | pg_dump 주 1회 백업 크론 스크립트 작성 | REQ-NF-008 | INFRA-004 | L |
| SEC-001 | Security | Next.js Middleware IP 화이트리스트 + Admin 라우트 접근 제어 | REQ-FUNC-016, REQ-NF-013 | INFRA-001 | H |
| SEC-002 | Security | API Key Vercel Environment Variables 전용 저장 + git grep 자동 스캔 | REQ-NF-012 | INFRA-002 | L |
| SEC-003 | Security | TLS 1.3 전송 암호화 + AES-256 저장 암호화 설정 | REQ-NF-010 | INFRA-004 | M |
| SEC-004 | Security | 개인정보처리방침 웹 페이지 작성 + 게시 | REQ-NF-011 | INFRA-001 | L |
| SEC-005 | Security | OWASP ZAP 분기 1회 셀프 스캔 체크리스트 작성 | REQ-NF-014 | INFRA-002 | L |
| PERF-001 | Performance | API Rate Limiting 구현 (IP당 50 req/hr Public, Admin별 차등) | REQ-NF-004 §6.1 | INFRA-001, SEC-001 | M |
| PERF-002 | Performance | 랜딩페이지 LCP ≤ 2.5초 최적화 (이미지/폰트/SSR) | REQ-NF-003 | UI-001 | M |
| MON-001 | Monitoring | 5분간 5xx ≥ 3건 시 Slack 자동 알림 로직 | REQ-NF-018 | INFRA-005 | M |
| MON-002 | Monitoring | API p95 > 20초 시 Slack 자동 알림 로직 | REQ-NF-019 | INFRA-005 | M |
| MON-003 | Monitoring | 월간 환각율 > 10% 시 프롬프트 튜닝 Sprint 트리거 알림 | REQ-NF-020 | CMD-008, INFRA-005 | M |
| MON-004 | Monitoring | 불성실 진단 비율 > 20% 시 문항 재설계 트리거 알림 | REQ-NF-023 | CMD-002, INFRA-005 | L |
| MON-005 | Monitoring | Supabase DB 용량 80% 도달 시 Slack 알림 + 아카이빙 안내 | REQ-NF-017 | INFRA-004, INFRA-005 | M |

## 4-B. UI/프론트엔드 태스크

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| UI-001 | Public Web | 랜딩페이지 (서비스 소개 + Before-After 데모 섹션) | §3.2 CL-01, REQ-FUNC-028 | INFRA-001 | H |
| UI-002 | Public Web | 5문항 진단 폼 UI (객관식 90% + 단답형 10%, 3분 이내 완료) | §4.1.2 REQ-FUNC-012 | INFRA-001, MOCK-001 | M |
| UI-003 | Public Web | 진단 결과 리포트 웹뷰 (브랜드 지수/약점 시각화) | §3.2 CL-02, REQ-FUNC-010 | MOCK-002 | H |
| UI-004 | Public Web | 상담 신청 폼 UI (인라인 유효성 검증, 200ms 이내 에러) | REQ-FUNC-020 | INFRA-001 | M |
| UI-005 | Public Web | CTA 버튼 + GA4 cta_click 이벤트 연동 | REQ-FUNC-010,011 | UI-003, INFRA-006 | L |
| UI-006 | Admin Web | 관리자 콘솔 레이아웃 (shadcn/ui 대시보드 쉘) | §3.2 CL-03, CON-09 | INFRA-001, SEC-001 | H |
| UI-007 | Admin Web | 인터뷰 텍스트 입력 + AI 변환 결과 출력 UI | REQ-FUNC-013 | UI-006, MOCK-003 | M |
| UI-008 | Admin Web | 브리프 Streaming 출력 + 검수 수정 UI | REQ-FUNC-002,006 | UI-006, MOCK-003 | H |
| UI-009 | Admin Web | 프로젝트 목록 대시보드 UI (상태/납기/고객) | REQ-FUNC-017 | UI-006, MOCK-005 | M |
| UI-010 | Admin Web | 리드 관리 목록 + 상태 변경 UI | REQ-FUNC-021 | UI-006, MOCK-004 | M |
| UI-011 | Admin Web | KPI 대시보드 UI (리드수/전환율/환각율) | REQ-FUNC-023,027 | UI-006, MOCK-006 | M |
| UI-012 | Admin Web | 환각 문장 태깅 UI (Phase 2, MVP: 수동 마크업) | REQ-FUNC-018 | UI-008 | M |
| UI-013 | Admin Web | 녹취 텍스트 입력 + B2B 카피 변환 UI | REQ-FUNC-014 | UI-006 | M |
| UI-014 | Admin Web | 고객 법인 정보 입력/수정 UI | REQ-FUNC-029 | UI-006 | L |
| UI-015 | Admin Web | 리테이너 구독 관리 UI (등록/갱신/해지) | REQ-FUNC-026 | UI-006 | M |
| UI-016 | Public Web | 고객 프로젝트 진척 대시보드 UI (Should) | REQ-FUNC-024 | MOCK-005 | M |
| UI-017 | Public Web | 개인정보처리방침 페이지 | REQ-NF-011 | INFRA-001 | L |
| UI-018 | Public Web | Gemini API 장애 시 Fallback 메시지 UI | REQ-FUNC-030 | UI-002, UI-003 | L |

## 4-C. LLM 전환성/확장성 태스크

| Task ID | Epic | Feature | 관련 SRS 섹션 | Dependencies | 복잡도 |
|---|---|---|---|---|---|
| EXT-001 | Scalability | Vercel AI SDK provider 추상화 (Gemini/Claude/GPT-4o 전환 ≤1시간) | REQ-NF-022 | INFRA-003 | M |
| EXT-002 | Scalability | Gemini API fallback provider 자동 전환 로직 | §3.1 EXT-01 | EXT-001 | H |
