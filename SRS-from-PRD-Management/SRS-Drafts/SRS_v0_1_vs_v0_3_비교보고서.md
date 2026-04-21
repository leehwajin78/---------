# SRS v0.1 vs v0.3 변경사항 비교 보고서

| 항목 | 내용 |
| :--- | :--- |
| **비교 대상** | `SRS_v0_1_Opus.md` (Revision 1.0) ↔ `SRS_V0_3.md` (Revision 1.3) |
| **작성일** | 2026-04-21 |
| **비교 기준** | ① 기술 스택 명확성 ② MVP 목표 및 가치전달 조정 ③ 기타 차이점 |

---

## 1. 기술 스택의 명확성

> AI 엔진, DB 접근 계층, 프레임워크, 보안 모델, UI 스택 등 기술 의사결정이 변경·명확화된 항목

### 1.1 AI 엔진 교체

| # | 위치 | v0.1 (Before) | v0.3 (After) | 변경 사유 |
| :---: | :--- | :--- | :--- | :--- |
| T1 | CON-05 | Claude API **200K 토큰** 컨텍스트 윈도우 | Gemini API **1M+ 토큰** 컨텍스트 윈도우 | C-TEC-006 준수. 42문항 + 168패턴을 단일 호출로 처리 가능 |
| T2 | CON-07 | Next.js + Vercel Serverless + **Supabase** 스택 | Next.js App Router + **Prisma ORM + Vercel AI SDK + Gemini API** 스택 | C-TEC-001~007 전면 반영 |
| T3 | EXT-01 | **Anthropic Claude API** (Rate: 50 req/min, Cost: ~$0.015/1K) | **Google Gemini API** (Rate: RPM/TPM Tier, Cost: Flash ~$0.075/1M input) | 비용 효율 5~10배 개선 |
| T4 | REQ-FUNC-001~030 | `Claude API`를 호출하여 / `Claude API 장애 시` 등 ~15곳 | `Gemini API (via Vercel AI SDK)` / `AI 엔진(Gemini)` 으로 통일 | 문서 전체 정합성 확보 |
| T5 | 시퀀스 다이어그램 6종 | `participant Claude as Claude API` | `participant AI as Gemini API (via Vercel AI SDK)` | 다이어그램 ↔ 본문 정합성 |
| T6 | REQ-NF-015 | Anthropic Dashboard | Google AI Studio Dashboard | 비용 모니터링 도구 교체 |

### 1.2 DB 접근 계층 변경

| # | 위치 | v0.1 (Before) | v0.3 (After) | 변경 사유 |
| :---: | :--- | :--- | :--- | :--- |
| T7 | §3.3, §6.1 | `Supabase REST API` 직접 호출 | `Prisma ORM → Supabase PostgreSQL` | C-TEC-003. 타입 안전 ORM 도입 |
| T8 | 신규 CON-08 | — | **Prisma datasource 전환** 명시 (SQLite 또는 Docker PostgreSQL / 운영 PostgreSQL) | JSONB 4개 필드 호환성 리스크 관리 |
| T9 | §6.2 서두 | — | `schema.prisma` 통한 모델 선언 주의사항 추가 | Prisma 마이그레이션 가이드 명시 |
| T10 | §3.7 Class | — | `PrismaService` 클래스 신규 (싱글톤, 트랜잭션 래퍼) | Data Access 계층 명확화 |

### 1.3 보안 모델 변경

| # | 위치 | v0.1 (Before) | v0.3 (After) | 변경 사유 |
| :---: | :--- | :--- | :--- | :--- |
| T11 | REQ-NF-013 | **비밀번호 + IP 화이트리스트** / Supabase Auth 로그 | **App-level (Next.js Middleware)** 접근 제어 + IP 화이트리스트 / Middleware 로그 | Supabase RLS → App-level Auth 전환 |
| T12 | REQ-FUNC-016 | Supabase Auth 기반 접근 제어 | Next.js Middleware + Prisma 쿼리 필터 | 인증 주체가 DB에서 앱 레이어로 이동 |
| T13 | §3.7 Class | — | `AuthMiddleware` 클래스 신규 (IP 검증, 세션 검증) | 보안 책임 명확화 |
| T14 | §1.3 용어 | — | `RLS → App-level Auth` 용어 정의 추가 | 보안 모델 전환 근거 문서화 |

### 1.4 프레임워크 및 UI 스택 명확화

| # | 위치 | v0.1 (Before) | v0.3 (After) | 변경 사유 |
| :---: | :--- | :--- | :--- | :--- |
| T15 | CON-01 | Serverless 실행 시간 ≤ 60초 (단독) | 동일 + **Streaming 엔드포인트는 Edge Runtime 적용** 단서 추가 | 60초 한도 ↔ 10분 Streaming 모순 해소 |
| T16 | 신규 CON-09 | — (UI 미명시) | **관리자 콘솔: Tailwind CSS + shadcn/ui 강제** / 랜딩페이지: 자유 스타일링 | C-TEC-004. UI 책임 범위 한정 |
| T17 | §3.6 Component | Client → API → External (3계층) | Next.js App Router 단일 풀스택 (Pages / Route Handlers / Server Actions / ORM 통합) | C-TEC-001,002 반영. 아키텍처 전면 재구성 |
| T18 | §1.3 용어 | — | App Router, Server Actions, Route Handlers, Prisma ORM, Vercel AI SDK, shadcn/ui, LCP 등 **7개 신규 용어** 추가 | 신규 스택 용어 문서화 |

### 1.5 LLM 전환 전략

| # | 위치 | v0.1 (Before) | v0.3 (After) | 변경 사유 |
| :---: | :--- | :--- | :--- | :--- |
| T19 | REQ-NF-022 | Claude→타 LLM 전환 **72시간 이내**, AI 엔진과 비즈니스 로직 분리 | Vercel AI SDK 표준 인터페이스 + 환경변수 교체로 **즉시 전환 (≤ 1시간)** | AI SDK 추상화로 전환 비용 대폭 감소 |
| T20 | 신규 ASM-06 | — | Vercel AI SDK `generateText`, `streamText`가 Gemini/Claude/GPT-4o 간 **동일 호출 시그니처** 보장 | 모델 교체 시 비즈니스 로직 수정 불필요 |

---

## 2. MVP 목표 및 가치전달 조정 내용

> 기능 요구사항의 우선순위(Must→Should), 구현 범위 축소, 운영 방식 변경 등 MVP 가치 전달에 직접 영향을 주는 항목

### 2.1 Must → Should (Phase 2) 이관

| # | REQ | 기능 | v0.1 Priority | v0.3 Priority | 사유 | MVP 가치 영향 |
| :---: | :--- | :--- | :---: | :---: | :--- | :--- |
| M1 | **REQ-FUNC-005** | 고객 동의율 <50% 시 보완 인터뷰 자동 생성 | Must | **Should (Phase 2)** | 파일럿 3건 이전엔 운영자 수동 판단으로 충분 | ⬜ 없음 — 수동 대응 가능 |
| M2 | **REQ-FUNC-007** | 환각율 자동 산출 + 대시보드 표시 | Must | **Should (Phase 2)** | 파일럿 단계에서는 수동 엑셀 집계 가능 | ⬜ 없음 — 엑셀 대체 |
| M3 | **REQ-FUNC-018** | 환각 문장 태깅 UI (클릭→마킹) | Must | **Should (Phase 2)** | MVP에서는 브리프 텍스트에 수동 마크업 | ⬜ 없음 — 텍스트 마크업 대체 |

### 2.2 기능 범위 단순화 (Must 유지, 구현 축소)

| # | REQ | v0.1 구현 범위 | v0.3 구현 범위 | 사유 | MVP 가치 영향 |
| :---: | :--- | :--- | :--- | :--- | :--- |
| M4 | **REQ-FUNC-001** | 168개 프롬프트 패턴 **고정** 적용 | **최소 20개, 목표 168개** (점진 확장) | 프롬프트 설계 지연 방지. 코어 패턴으로 충분 | ⬜ 없음 — 품질 수준 동등 |
| M5 | **REQ-FUNC-011** | GA4 이벤트 **3종** (page_view, cta_click, report_scroll_complete) | GA4 이벤트 **1종** (cta_click). `report_scroll_complete`는 Phase 2 | 스크롤 완독 추적은 MVP 핵심 전환 지표 아님 | ⬜ 없음 — 핵심 CTA 전환율은 유지 |
| M6 | **REQ-FUNC-023** | 월별 리드 **대시보드 자동 집계** 표시 | Prisma Studio / Supabase Dashboard **직접 조회** | Admin 대시보드 자동 집계 공수 절감 | ⬜ 없음 — 데이터 접근 가능성 동일 |

### 2.3 비용 모니터링 전략 변경

| # | REQ | v0.1 방식 | v0.3 방식 | 사유 | MVP 가치 영향 |
| :---: | :--- | :--- | :--- | :--- | :--- |
| M7 | **CON-03** | $100 도달 시 **자동 중단** | $100 하드캡 유지, MVP에서는 **수동 모니터링** (Google AI Studio Dashboard) | Gemini Flash 기준 월 $0.26 미만 → 자동화 ROI 극미 | ⬜ 없음 — 하드캡 도달 확률 < 1% |
| M8 | **REQ-NF-016** | CostMonitor **자동 중단** + Slack 긴급 알림 | Google AI Studio Dashboard **수동 확인** (월 1회). 자동 구현은 Phase 2 | 구현 공수 3~4일 절감 | ⬜ 없음 |

### 2.4 데이터 백업 현실화

| # | REQ | v0.1 | v0.3 | 사유 | MVP 가치 영향 |
| :---: | :--- | :--- | :--- | :--- | :--- |
| M9 | **REQ-NF-008** | Supabase **자동 백업 일 1회** | **주 1회** 수동/크론 백업. Supabase Free 자동 백업 미지원 명시 | Free 티어 현실 반영 | ⬜ 없음 — 백업 주기만 조정 |

### 2.5 데이터 엔터티 추가 (Phase 2)

| # | 엔터티 | v0.1 | v0.3 | 사유 | MVP 가치 영향 |
| :---: | :--- | :--- | :--- | :--- | :--- |
| M10 | **EVENT_LOG** | 없음 (6개 엔터티) | **Phase 2로 마킹**하여 추가 (총 7개) | GA4 Fallback용. MVP에서는 GA4 단독 운영 | ⬜ 없음 |

### 2.6 예상 효과 요약

| 항목 | v0.1 | v0.3 | 변화 |
| :--- | :---: | :---: | :--- |
| Must 기능 수 | 23건 | **18건** | 5건 Phase 2 이관 |
| 추정 개발 공수 | ~34.5일 | **~23.5일** | ~11일 절감 |
| 총 일정 | 7~8주 | **5~6주** | ~2주 단축 |
| 핵심 여정 (J1~J4) 보존 | ✅ | ✅ | **변화 없음** |

---

## 3. 기타 차이점

> 문서 메타데이터, 용어, 다이어그램 구조, 추적성 등 비즈니스/기술 결정 외의 문서 품질 개선 항목

### 3.1 문서 메타데이터

| # | 항목 | v0.1 | v0.3 | 비고 |
| :---: | :--- | :--- | :--- | :--- |
| O1 | **Revision** | 1.0 | **1.3** | v1.1(스택 교체) → v1.2(정합성) → v1.3(MVP 최적화) |
| O2 | **문서 끝 버전** | v1.1 | **v1.3** | 상단↔하단 통일 완료 |
| O3 | UseCase Diagram 액터 | `🤖 Claude API` | `🤖 Gemini API` | 시각적 정합성 |
| O4 | 데이터 엔터티 수 | 6개 | **7개** (EVENT_LOG Phase 2 포함) | 카운트 업데이트 |

### 3.2 신규 용어 정의 (§1.3)

| # | 용어 | v0.1 | v0.3 |
| :---: | :--- | :---: | :--- |
| O5 | App Router | — | Next.js 13+ 라우팅 아키텍처 |
| O6 | Server Actions | — | 클라이언트 폼 → 서버 함수 RPC 패턴 |
| O7 | Route Handlers | — | `app/api/*/route.ts` HTTP 엔드포인트 |
| O8 | Prisma ORM | — | TypeScript ORM, 타입 안전 쿼리 |
| O9 | Vercel AI SDK | — | LLM 호출·스트리밍 표준화 프레임워크 |
| O10 | shadcn/ui | — | Radix UI 기반 컴포넌트 라이브러리 |
| O11 | RLS → App-level Auth | — | Supabase RLS 대체 보안 패턴 |
| O12 | LCP | — | Core Web Vitals 렌더링 지표 |

### 3.3 신규 가정 (§1.2.4)

| # | ASM | v0.1 | v0.3 |
| :---: | :--- | :---: | :--- |
| O13 | ASM-05 | — | Prisma SQLite↔PostgreSQL 스키마 호환성 보장 가정 |
| O14 | ASM-06 | — | Vercel AI SDK 동일 호출 시그니처 보장 가정 |

### 3.4 다이어그램 구조 변경

| # | 다이어그램 | v0.1 | v0.3 | 변경 내용 |
| :---: | :--- | :--- | :--- | :--- |
| O15 | §3.6 Component | Client→API→External 3계층 | **Next.js App Router 단일 풀스택** (Pages/Route Handlers/Services/ORM) | 아키텍처 전면 재구성 |
| O16 | §3.7 Class | 7개 클래스 | **9개 클래스** (+PrismaService, +AuthMiddleware) | Data Access, Security 계층 추가 |
| O17 | §6.2.8 ERD | 6개 엔터티 | **EVENT_LOG 추가** (Phase 2 표시) | GA4 Fallback 엔터티 |
| O18 | §6.3.3 비용 차단 | `Anthropic Dashboard` 참조 시퀀스 | **자체 DB 기반 CostMonitor** 시퀀스 | 외부 대시보드 의존 제거 |

### 3.5 §6.1 API Endpoint List 설명 정합성

| # | API | v0.1 설명 | v0.3 설명 | 변경 내용 |
| :---: | :--- | :--- | :--- | :--- |
| O19 | API-01 | `Claude API → 브랜드 지수·리포트 생성 + 리드 저장` | `Gemini API (via Vercel AI SDK) → 브랜드 지수·약점 리포트 생성 + Prisma ORM → Supabase PostgreSQL 리드 저장` | §3.3과 완전 일치 |
| O20 | API-03 | `Claude API → 마스터 브리프 생성 (Streaming)` / `Admin (비밀번호 + IP)` | `Gemini API (via Vercel AI SDK) → 마스터 브리프 생성 (JSON/Markdown, Streaming) + Prisma ORM → Supabase PostgreSQL 저장` / `Admin (Next.js Middleware + IP)` | 출력 포맷, 인증 방식 일치 |

---

## 요약 통계

| 관점 | 변경 항목 수 | 주요 특성 |
| :--- | :---: | :--- |
| **① 기술 스택 명확성** | 20건 (T1~T20) | AI 엔진 교체, DB 추상화, 보안 모델 전환, UI 명시, LLM 전환 전략 |
| **② MVP 목표·가치전달 조정** | 10건 (M1~M10) | Phase 2 이관 5건, 범위 단순화 3건, 비용/백업 현실화 2건 |
| **③ 기타 차이점** | 20건 (O1~O20) | 메타데이터 통일, 용어 8개 추가, 가정 2개 추가, 다이어그램 4종 변경 |
| **합계** | **50건** | |

> **핵심 판단:** 50건의 변경 중 **4개 핵심 사용자 여정(J1~J4)에 영향을 주는 항목은 0건**. 기술 스택은 명확해졌고, MVP 범위는 축소되었으나 고객 체감 가치는 완전히 보존됨.
