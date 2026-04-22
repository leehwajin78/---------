---
name: Feature Task
about: SRS 기반의 구체적인 개발 태스크 명세
title: "[Infra] INFRA-001: Next.js App Router 프로젝트 초기화 (TypeScript, Tailwind CSS, shadcn/ui)"
labels: 'feature, infra, priority:high'
assignees: ''
---

## 🎯 Summary
- 기능명: [INFRA-001] Next.js App Router 프로젝트 초기화
- 목적: 시스템 전체의 단일 풀스택 프레임워크 기반을 구축한다. CON-07(아키텍처 고정)에 따라 Next.js App Router + TypeScript + Tailwind CSS + shadcn/ui 기술 스택을 초기화하고, 프로젝트 디렉토리 구조를 표준화한다.

## 🔗 References (Spec & Context)
> 💡 AI Agent & Dev Note: 작업 시작 전 아래 문서를 반드시 먼저 Read/Evaluate 할 것.
- 아키텍처 제약: [`SRS_v1.md#1.2.3 CON-07`](../SRS_v1.md) — Next.js App Router + Prisma ORM + Vercel AI SDK + Gemini API 스택 고정
- UI 프레임워크: [`SRS_v1.md#1.2.3 CON-09`](../SRS_v1.md) — 관리자 콘솔: Tailwind + shadcn/ui 강제, 랜딩페이지: 자유 스타일링
- 컴포넌트 다이어그램: [`SRS_v1.md#3.6`](../SRS_v1.md) — Next.js App Router 컴포넌트 전체 구조
- 클라이언트 앱: [`SRS_v1.md#3.2`](../SRS_v1.md) — CL-01 랜딩, CL-02 진단 웹뷰, CL-03 관리자 콘솔
- 호스팅: [`SRS_v1.md#1.2.3 CON-01`](../SRS_v1.md) — Vercel Hobby 티어 제약 (60초, 1024MB)

## ✅ Task Breakdown (실행 계획)
- [ ] `npx -y create-next-app@latest ./ --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"` 실행 (비대화형 모드)
- [ ] Next.js 프로젝트 구조 검증: `app/`, `src/` 디렉토리 존재 확인
- [ ] shadcn/ui 초기화: `npx -y shadcn@latest init` 실행
  - 기본 컴포넌트 설치: `button`, `input`, `card`, `table`, `dialog`, `badge`, `tabs`
- [ ] 프로젝트 디렉토리 구조 표준화:
  ```
  src/
  ├── app/
  │   ├── (public)/          # 랜딩, 진단, 리포트 (Public Zone)
  │   │   ├── page.tsx       # 랜딩페이지
  │   │   ├── diagnose/      # 진단 폼
  │   │   └── report/[id]/   # 리포트 웹뷰
  │   ├── (admin)/           # 관리자 콘솔 (Admin Zone)
  │   │   ├── layout.tsx     # Admin 레이아웃 (shadcn/ui)
  │   │   ├── dashboard/     # 프로젝트 대시보드
  │   │   ├── leads/         # 리드 관리
  │   │   ├── briefs/        # 브리프 관리
  │   │   └── settings/      # 설정
  │   ├── api/               # Route Handlers
  │   │   ├── diagnose/      # POST /api/diagnose
  │   │   ├── leads/         # POST/GET /api/leads
  │   │   ├── generate-brief/# POST /api/generate-brief
  │   │   ├── projects/      # GET /api/projects/:id
  │   │   ├── briefs/        # PATCH /api/briefs/:id
  │   │   └── dashboard/     # GET /api/dashboard/metrics
  │   ├── layout.tsx         # Root Layout
  │   └── globals.css        # Tailwind 글로벌 스타일
  ├── components/
  │   ├── ui/                # shadcn/ui 컴포넌트
  │   └── shared/            # 공유 컴포넌트
  ├── lib/
  │   ├── dto/               # DTO/스키마 정의
  │   ├── services/          # 비즈니스 로직 서비스
  │   ├── validators/        # 유효성 검증
  │   └── utils.ts           # 유틸리티
  ├── types/                 # 전역 TypeScript 타입
  └── middleware.ts          # Next.js Middleware (Admin 접근 제어)
  ```
- [ ] `tsconfig.json` path alias 설정 확인 (`@/*` → `src/*`)
- [ ] `.env.example` 파일 생성 — 필요한 환경변수 키 목록 템플릿:
  ```
  DATABASE_URL=
  GEMINI_API_KEY=
  SLACK_WEBHOOK_URL=
  NEXT_PUBLIC_GA4_MEASUREMENT_ID=
  ADMIN_ALLOWED_IPS=
  ```
- [ ] `next.config.ts`에 기본 설정:
  - `experimental.serverActions` 활성화 확인
  - 이미지 최적화 설정
- [ ] ESLint + Prettier 설정 표준화
- [ ] `.gitignore` 검토 및 보강 (node_modules, .env.local, .next, dev.db 등)
- [ ] README.md에 프로젝트 개요, 기술 스택, 로컬 실행 방법 기재

## 🧪 Acceptance Criteria (BDD/GWT)

**Scenario 1: 로컬 개발 서버 정상 실행**
- Given: Next.js 프로젝트가 초기화된 상태
- When: `npm run dev`를 실행하면
- Then: `http://localhost:3000`에서 기본 페이지가 정상 렌더링되고, 콘솔에 에러가 없다.

**Scenario 2: App Router 라우팅 동작 확인**
- Given: `app/(public)/page.tsx`와 `app/(admin)/dashboard/page.tsx`가 존재하는 상태
- When: 각 경로(`/`, `/dashboard`)에 접속하면
- Then: 각 페이지가 독립적으로 렌더링되고, 레이아웃이 올바르게 적용된다.

**Scenario 3: shadcn/ui 컴포넌트 렌더링 확인**
- Given: shadcn/ui Button 컴포넌트가 설치된 상태
- When: 페이지에 `<Button>테스트</Button>`를 렌더링하면
- Then: Tailwind 스타일이 적용된 버튼이 화면에 표시된다.

**Scenario 4: TypeScript 타입 검증**
- Given: `.tsx` 파일에 타입 오류가 포함된 코드가 작성된 상태
- When: `npx tsc --noEmit`을 실행하면
- Then: 타입 오류가 정확히 검출되고, 정상 코드에서는 오류가 0건이다.

**Scenario 5: Route Handler 기본 동작**
- Given: `app/api/diagnose/route.ts`에 기본 GET 핸들러가 작성된 상태
- When: `GET /api/diagnose`로 요청하면
- Then: JSON 응답이 정상 반환된다.

## ⚙️ Technical & Non-Functional Constraints
- **CON-07**: Next.js App Router 사용 강제 — Pages Router 사용 금지
- **CON-09**: 관리자 콘솔은 Tailwind CSS + shadcn/ui 사용 강제
- **CON-01**: Vercel Hobby 티어 — Serverless 실행 시간 ≤ 60초, 메모리 1024MB
- Next.js 13+ (App Router) 사용 — Server/Client Component 구분 준수
- Node.js 18+ 필수

## 🏁 Definition of Done (DoD)
- [ ] `npm run dev`로 로컬 서버가 정상 실행되는가?
- [ ] App Router 기반 라우팅이 동작하는가?
- [ ] shadcn/ui 컴포넌트가 정상 렌더링되는가?
- [ ] 표준화된 디렉토리 구조가 생성되었는가?
- [ ] `npx tsc --noEmit`에서 타입 에러가 0건인가?
- [ ] `.env.example`에 필수 환경변수 키가 명세되었는가?
- [ ] README.md에 로컬 실행 가이드가 작성되었는가?

## 🚧 Dependencies & Blockers
- **Depends on**: None (Phase 0 최우선 병렬 시작)
- **Blocks**: DB-001 (Prisma 초기화 — 프로젝트 루트 필요), INFRA-002~006 (모든 인프라 태스크), SEC-001 (Middleware 접근 제어), UI-001~006 (모든 UI 태스크), 사실상 전체 프로젝트의 루트 의존성
