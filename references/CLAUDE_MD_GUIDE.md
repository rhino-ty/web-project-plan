# CLAUDE.md Writing Guide

Claude Code가 프로젝트를 처음 열었을 때 이 파일을 읽고 바로 작업에 투입될 수 있어야 한다.
CLAUDE.md는 "온보딩 문서"가 아니라 **에이전트에게 주는 실행 컨텍스트**다.

**작성 원칙**
- Include: 에이전트가 모르면 실수하는 것
- Exclude: 추론 가능한 것, 일반론

나쁜 예: "코드를 깔끔하게 작성해줘"
좋은 예: "`src/components/ui/`는 shadcn 전용, 직접 수정 금지"

> **주의**: 아래 조건부 섹션의 예시는 Supabase/Next.js 기준이다.
> Phase 4-1에서 확정된 스택에 맞게 서비스명·경로·명령어를 교체해서 작성한다.

---

## 필수 섹션 (모든 프로젝트)

### 1. Project Overview
```md
## Project Overview
[프로젝트 이름]: [한 줄 설명]
Stack: [확정된 기술 목록]
```
두 줄 이내.

---

### 2. Commands
```md
## Commands
dev:        pnpm dev
build:      pnpm build
test:       pnpm test          # 유닛 (Vitest)
test:e2e:   pnpm test:e2e      # E2E (Playwright) — 있을 때만
lint:       pnpm lint
typecheck:  pnpm typecheck
db:migrate: pnpm db:migrate    # DB 있을 때만
```
패키지 매니저 명시. 없는 커맨드는 포함하지 않는다.

---

### 3. Project Structure
```md
## Project Structure
src/
  app/           # Next.js App Router
  components/
    ui/          # shadcn/ui (수정 금지)
    [feature]/   # feature별 컴포넌트
  lib/           # 유틸·헬퍼
  hooks/
  types/
docs/
  references/    # 기술 레퍼런스 문서 (스킬 생성)
```
파일을 어디에 만들어야 하는지 알 수 있을 정도만.

---

### 4. Key Conventions
```md
## Key Conventions
- 컴포넌트: PascalCase, named export
- 훅: camelCase, use prefix
- import alias: @/ → src/
- 서버 컴포넌트 기본, 클라이언트는 'use client' 필요 시만
- 타입: interface 우선, 복잡한 union은 type
```

---

### 5. Environment Variables
```md
## Environment Variables
NEXT_PUBLIC_SUPABASE_URL       # 클라이언트 노출 가능
NEXT_PUBLIC_SUPABASE_ANON_KEY  # 클라이언트 노출 가능
SUPABASE_SERVICE_ROLE_KEY      # 서버 전용 — 절대 클라이언트 노출 금지

.env.local 정의. .env.example 참고.
```
`NEXT_PUBLIC_` prefix 여부와 노출 범위를 반드시 명시한다.

---

### 6. Do Not
```md
## Do Not
- src/components/ui/ 수정 금지
- any 타입 금지
- console.log 커밋 금지
- 클라이언트에서 직접 DB 접근 금지 (Server Action 또는 API Route 경유)
- 환경변수 하드코딩 금지
```
모호한 지침 대신 구체적인 금지 목록.

---

## 조건부 섹션

기능 있을 때만 추가. 예시는 Supabase/Next.js 기준 — **확정 스택에 맞게 교체**.

### Auth
```md
## Auth
Provider: [확정 Auth 서비스]
Session: [세션 체크 위치]
Route 보호: [보호 방식 — middleware / HOC / layout 등]
인증 필요 API는 서버에서 세션 검증 후 처리
```

예시 (Supabase):
```md
## Auth
Provider: Supabase Auth (이메일 + 소셜)
Session: @/lib/supabase/server.ts의 createClient()
Route 보호: middleware.ts
```

예시 (NextAuth):
```md
## Auth
Provider: NextAuth.js (GitHub + Google)
Session: getServerSession() — server component / API route에서 직접 호출
Route 보호: middleware.ts의 matcher 설정
```

---

### Database
```md
## Database
DB: [확정 DB]
ORM/클라이언트: [확정 ORM]
Schema: [schema 파일 경로]
Migration: [명령어]
클라이언트에서 직접 쿼리 금지
```

예시 (Supabase + Drizzle):
```md
## Database
DB: Supabase (PostgreSQL)
ORM: Drizzle — Schema: src/lib/db/schema.ts
Migration: pnpm db:migrate
```

예시 (PlanetScale + Prisma):
```md
## Database
DB: PlanetScale (MySQL)
ORM: Prisma — Schema: prisma/schema.prisma
Migration: pnpm prisma migrate dev
```

---

### AI
```md
## AI
Provider: [확정 AI 서비스]
Model: [모델명]
Client: [클라이언트 경로]
Prompts: docs/references/prompts/
비용 주의: 불필요한 API 호출 최소화
```

예시 (Anthropic):
```md
## AI
Provider: Anthropic
Model: claude-sonnet-4-20250514
Client: @/lib/ai/client.ts
Streaming: Vercel AI SDK
Prompts: docs/references/prompts/
```

---

### RBAC (B-2: 역할 구분 있음)
```md
## Auth
...
Role: [역할 목록 — admin / user / guest 등]
권한 체크: [미들웨어 / Server Action 내부 / 둘 다]
어드민 라우트: /admin/* — 별도 layout으로 보호
```

예시:
```md
## Auth
Provider: Supabase Auth
Role: admin / user (profiles.role 컬럼으로 관리)
권한 체크: Server Action 내부에서 role 검증
어드민 라우트: app/(admin)/ — admin role만 접근 가능
```

---

### Search (D: 검색 있음)
```md
## Search
Provider: [PostgreSQL FTS / Algolia / Meilisearch]
검색 대상: [테이블명 또는 데이터 종류]
실시간 제안: [Yes / No]
```

예시 (PostgreSQL FTS):
```md
## Search
Provider: PostgreSQL Full-Text Search (tsvector)
검색 대상: posts.title + posts.content
인덱스: GIN index on search_vector
실시간 제안: No (엔터 후 검색)
```

예시 (Algolia):
```md
## Search
Provider: Algolia
Index: products
실시간 제안: Yes (InstantSearch.js)
동기화: DB 변경 시 Supabase webhook → Algolia 업데이트
```

---

### i18n (K: 다국어 있음)
```md
## i18n
Library: [next-intl / next-i18next]
지원 언어: [ko, en 등]
기본 언어: [ko]
URL 구조: /[locale]/...
번역 파일: messages/[locale].json
```

---

### Responsive Design (P-1: 반응형 선택)
```md
## Responsive Design
방식: mobile-first (Tailwind 기본 — sm: md: lg: 순서)
주요 breakpoint:
  - sm (640px): 모바일 → 소형 태블릿
  - md (768px): 태블릿
  - lg (1024px): 데스크탑
레이아웃 변화:
  - 네비게이션: 데스크탑 사이드바 → 모바일 하단 탭바
  - 그리드: lg:grid-cols-3 → grid-cols-1
  - 테이블: 데스크탑 테이블 → 모바일 카드형
터치: 탭 영역 최소 44px (Tailwind: min-h-11)
```
반응형이 아니면 이 섹션 생략.

---

### SEO (O: SEO 있음)
```md
## SEO
전략: [SSR / SSG / ISR]
메타태그: generateMetadata() 사용
OG 이미지: [정적 / 동적 생성]
sitemap: app/sitemap.ts
robots: app/robots.ts
```

---

### Payments
```md
## Payments
Provider: [Stripe / 토스페이먼츠 / 기타]
Webhook: [webhook 경로]
구독 상태: [상태 관리 위치]
클라이언트에서 결제 로직 직접 구현 금지
```

---

### File Storage
```md
## File Storage
Provider: [확정 스토리지]
Bucket: [버킷명 + public/private]
업로드 경로: [경로 규칙]
최대 파일 크기: [제한]
```

---

### Realtime
```md
## Realtime
[확정 실시간 기술 — Supabase Realtime / Socket.io / Ably 등]
구독 해제: 컴포넌트 unmount 시 반드시 처리
```

### Testing (Phase 4-3: 테스트 전략 있음)
```md
## Testing
Unit: [Vitest / Jest] — [테스트 파일 위치]
E2E: [Playwright / Cypress] — [테스트 파일 위치]  ← E2E 있을 때만
커버리지 목표: [비즈니스 로직 위주 / 전체]
```

예시:
```md
## Testing
Unit: Vitest — src/**/*.test.ts
E2E: Playwright — e2e/**/*.spec.ts
커버리지 목표: 비즈니스 로직 (lib/, hooks/) 위주
```

---

### CI/CD (Phase 4-3: CI/CD 있음)
```md
## CI/CD
Provider: [GitHub Actions / GitLab CI / 기타]
파이프라인:
  - PR: lint + typecheck + test
  - main 머지: 자동 배포 → [플랫폼]
워크플로우: .github/workflows/
```

---

## 전체 예시

```md
## Project Overview
Taskflow: 팀 태스크 관리 + 실시간 협업 앱
Stack: Next.js 15, Tailwind v4, shadcn/ui, Supabase, Drizzle ORM

## Commands
dev:        pnpm dev
build:      pnpm build
test:       pnpm test
test:e2e:   pnpm test:e2e
lint:       pnpm lint
typecheck:  pnpm typecheck
db:migrate: pnpm db:migrate

## Project Structure
src/
  app/
    (auth)/       # 로그인/회원가입
    (dashboard)/  # 인증 필요
    api/
  components/
    ui/           # shadcn (수정 금지)
    tasks/
    layout/
  lib/
    db/           # Drizzle schema + client
    supabase/     # server / client 분리
    ai/
  hooks/
  types/
docs/
  references/     # 기술 레퍼런스 (스킬 생성)

## Key Conventions
- 컴포넌트: PascalCase, named export
- import alias: @/ → src/
- 서버 컴포넌트 기본, 클라이언트는 'use client' 필요 시만
- DB 접근: Server Action 또는 API Route에서만
- 에러 처리: Result 패턴 ({ data, error })

## Environment Variables
NEXT_PUBLIC_SUPABASE_URL        # 클라이언트 노출 가능
NEXT_PUBLIC_SUPABASE_ANON_KEY   # 클라이언트 노출 가능
SUPABASE_SERVICE_ROLE_KEY       # 서버 전용, 절대 클라이언트 노출 금지
ANTHROPIC_API_KEY               # 서버 전용

## Auth
Provider: Supabase Auth (이메일 + 소셜)
Session: @/lib/supabase/server.ts의 createClient()
Route 보호: middleware.ts
미인증 dashboard 접근 시 /login 리다이렉트

## Database
DB: Supabase (PostgreSQL) / ORM: Drizzle
Schema: src/lib/db/schema.ts / Migration: pnpm db:migrate

## AI
Provider: Anthropic / Model: claude-sonnet-4-20250514
Client: @/lib/ai/client.ts / Streaming: Vercel AI SDK
Prompts: docs/references/prompts/

## Testing
Unit: Vitest — src/**/*.test.ts
E2E: Playwright — e2e/**/*.spec.ts
커버리지 목표: 비즈니스 로직 위주, UI 스냅샷 테스트 지양

## Do Not
- src/components/ui/ 수정 금지
- any 타입 금지
- console.log 커밋 금지
- 클라이언트에서 직접 DB 쿼리 금지
- SUPABASE_SERVICE_ROLE_KEY 클라이언트 노출 금지

## SEO
전략: SSR (동적 콘텐츠) / ISR (블로그 등 준정적)
메타태그: generateMetadata() 사용
sitemap: app/sitemap.ts / robots: app/robots.ts

## CI/CD
GitHub Actions
PR: lint + typecheck + test
main 머지: Vercel 자동 배포
```

---

## 길이 기준

| 프로젝트 복잡도 | 적정 길이 |
|---|---|
| 단순 도구 (~3화면) | 30~50줄 |
| 일반 앱 (인증 + DB) | 60~100줄 |
| 복잡한 앱 (AI + 결제 + 실시간) | 100~150줄 |

150줄 초과 시 세부 내용은 `docs/references/`의 전용 문서로 위임하고 CLAUDE.md엔 링크만:
```md
## Auth
→ 상세: docs/references/AUTH.md
핵심: Supabase Auth, 세션 체크는 server.ts, route 보호는 middleware.ts
```
