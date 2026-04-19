---
name: app-plan
description: "앱 빌딩 프로젝트를 시작할 때 기획을 구체화하고 문서 구조를 세팅하는 스킬. 개발자를 대상으로 기능, 기술 스택, MVP 스코프를 인터뷰로 확정하고 프로젝트 문서 + CLAUDE.md를 생성한다. '앱 만들려고', '새 프로젝트 시작', '앱 기획', 'app plan', '문서 구조 잡아줘', '프로젝트 세팅', '스택 잡아줘' 같은 말이 나오면 트리거한다."
---

# App Plan Interview

새 앱 프로젝트 시작 시, 인터뷰로 기획을 구체화하고 문서 구조 + 파일 생성까지 완료하는 스킬.

**대상**: 개발자. 기술 용어 그대로 사용한다.
**플랫폼 스코프**: Web 앱 전용.
**목표**: 세션 끝에 `CLAUDE.md` + 필요한 `docs/` 구조가 실제 파일로 존재하는 것.

---

## 디렉토리 구조 전제

```
README.md
CLAUDE.md
docs/
  INDEX.md             ← 문서 지도
  QUICK_REF.md         ← 커맨드·환경변수 치트시트
  ROADMAP.md           ← MVP 스코프 + 백로그
  01-plan/             ← PDCA: Plan (사용자 관리)
  02-design/           ← PDCA: 개발 설계 (사용자 관리)
  03-analysis/         ← PDCA: Analysis (사용자 관리)
  04-report/           ← PDCA: Report (사용자 관리)
  archive/             ← 폐기 문서 보관 (사용자 관리)
  references/          ← 기술 레퍼런스 (이 스킬이 생성)
    UI.md              ← 화면/UI 디자인 ≠ 02-design (개발 설계)
    ...
```

---

## 세션 흐름

### Phase 1 — 앱 정체 파악

> **1. 앱 이름 / 가칭?**
>
> **2. 한 줄 설명?**
> (무엇을, 누구를 위해, 어떤 문제를 해결하는지)
>
> **3. 주요 사용자?**
> → Personal tool / Public product / Internal tool
>
> **4. Web 앱 형태?**
> → SPA / SSR (SEO 중요) / SSG (정적 콘텐츠 위주) / 아직 모름
>
> **5. 기존에 쓰는 스택이나 보일러플레이트 있어?**

---

### Phase 2 — 기능 구체화

Phase 1 답변과 연관된 질문만 골라 제시한다.

**[데이터 영속성]**
```
A. DB 필요해?
   → Yes — 어떤 데이터?
   → No
```

**[인증 & 권한]**
```
B. 로그인/계정 있어?
   → Yes → 소셜 / 이메일 / 둘 다
   → No

   (Yes면) B-1. 유저 데이터 공개 범위?
   → Private / 일부 public / 전부 public

   (Yes면) B-2. 역할 구분 있어?
   → 없음 (단일 유저)
   → 있음 → 어떤 역할? (admin / user / guest 등)
```

**[소셜/공유]**
```
C. 다른 유저 콘텐츠를 볼 수 있어? (피드, 게시판 등)
   → Yes / No
```

**[검색]**
```
D. 검색 기능 있어?
   → No
   → Yes → 어떤 데이터를 검색?
            실시간 검색어 제안 필요해? (Yes / No)
            데이터 규모? (소규모 → PostgreSQL FTS / 대규모 → Algolia·Meilisearch)
```

**[결제]**
```
E. 결제 있어?
   → No / one-time / subscription / per-feature
```

**[AI]**
```
F. AI 기능 필요해?
   → Yes → 어떤 기능? (생성 / 분석 / 추천 / 요약 등)
            스트리밍 응답 필요해? (Yes / No)
   → No
```

**[외부 API]**
```
G. 외부 서비스 연동?
   → Yes → 어떤 것?
   → No
```

**[파일/미디어]**
```
H. 파일 업로드 / 스토리지 필요해?
   → Yes → 어떤 파일? (이미지 / 문서 / 영상)
            유저별 private or public?
   → No
```

**[알림]**
```
I. 알림 있어? (push / email / SMS)
   → Yes → 어떤 트리거로?
   → No
```

**[실시간]**
```
J. 실시간 동기화나 협업 필요해?
   → Yes / No
```

**[다국어]**
```
K. 다국어 지원 있어?
   → No
   → Yes → 몇 개 언어? / v1부터 or 나중에?
```

---

### Phase 3 — 디자인 & UI

연관된 질문만 골라 제시한다.

**[레퍼런스 & 에셋]** — 항상 묻는다
```
L. 디자인 레퍼런스? (Figma / 참고 사이트 / 없음)

M. 브랜드 에셋? (컬러, 로고, 폰트) → Yes / No
```

**[렌더링 & SEO]** — Public product이면 반드시 묻는다
```
N. SEO 요구사항 있어?
   → 없음
   → OG 태그·sitemap 수준
   → 콘텐츠 SEO — 검색 노출 중요 (SSR/SSG 전략 필요)
```

**[테마]** — 항상 묻는다
```
O. 다크모드 지원?
   → Yes / No
```

**[화면 구조]** — 항상 묻는다
```
P. 화면 규모?
   → ~3개 / 4~10개 / 10개 이상

   반복 컴포넌트 있어? → Yes / No

P-1. 레이아웃 방식?
   → 반응형 (mobile-first)
   → 고정 너비 (대시보드, 어드민 등)
   → 둘 다

   (반응형이면) P-2. 주요 사용 환경?
   → 모바일 위주 / 데스크탑 위주 / 동등하게

   (반응형이면) P-3. 반응형 시 레이아웃 변화가 있는 영역?
   □ 네비게이션 (예: 데스크탑 사이드바 → 모바일 하단 탭바 or 햄버거)
   □ 그리드/카드 레이아웃 (예: 3열 → 1열)
   □ 테이블 → 카드형으로 재구성
   □ 폼 레이아웃 변경
   □ 변화 없음 (스케일만 줄어듦)

   (반응형이면) P-4. 터치 인터랙션 고려해야 해?
   → Yes (스와이프, 탭 영역 크기 등) / No
```

**[인터랙션]** — 항상 묻는다
```
Q. 인터랙션 요구사항?
   □ 드래그 앤 드롭
   □ 애니메이션 / 트랜지션
   □ 차트 / 데이터 시각화
   □ 없음
```

---

### Phase 4 — 스택 확정 & MVP 스코프

#### 4-1. 스택 Propose

Phase 1~3 답변 기반으로 스택을 제안한다.
기존 스택이 있으면 그걸 기준으로 맞춘다.
기능 답변에 따라 해당 행만 추가한다.

```
## 제안 스택

| 영역          | 선택                        | 이유 |
|---------------|-----------------------------|------|
| Frontend      | Next.js 15 (App Router)     | ...  |
| Styling       | Tailwind v4 + shadcn/ui     | ...  |
| Auth          | Supabase Auth               | ...  | ← B가 Yes면
| DB            | Supabase (PostgreSQL)       | ...  | ← A가 Yes면
| ORM           | Drizzle ORM                 | ...  | ← A가 Yes면
| Search        | PG FTS / Algolia            | ...  | ← D가 Yes면
| AI            | Anthropic / OpenAI          | ...  | ← F가 Yes면
| Storage       | Supabase Storage / S3       | ...  | ← H가 Yes면
| Notifications | Resend / FCM                | ...  | ← I가 Yes면
| Payments      | Stripe / 토스페이먼츠        | ...  | ← E가 Yes면
| i18n          | next-intl                   | ...  | ← K가 Yes면
| Testing       | Vitest + Playwright         | ...  | 항상
| Deploy        | Vercel                      | ...  |
```

**"변경하고 싶은 거 있어?"** — confirm 또는 수정 받으면 스택 확정.

#### 4-2. MVP 스코프 컷

Phase 2~3 전체 기능 목록을 보여주고:

> "v1에 꼭 있어야 하는 것 / 나중에 해도 되는 것 구분해줘. 없으면 내가 제안할게."

- **v1 (Must)** → `docs/ROADMAP.md` 확정 섹션
- **v2+ (Later)** → `docs/ROADMAP.md` 미결/백로그 섹션

#### 4-3. 배포 & 개발 환경

```
1. 배포 플랫폼? (Vercel / Railway / AWS / 기타)
2. CI/CD 파이프라인? (GitHub Actions / 기타 / 없음)
3. 테스트 전략?
   → 유닛만 / 유닛 + E2E / 없음
   → E2E 도구: Playwright / Cypress
4. 모노레포 vs 단일 레포?
5. AI 코딩 도구? (Claude Code / Cursor / 직접 코딩)
```

---

## 내부 변환 테이블

| 답변 | 생성 문서 |
|---|---|
| A: DB 있음 | `docs/references/DB.md` |
| B: 인증 있음 | `docs/references/AUTH.md` |
| B-2: 역할 구분 있음 | `docs/references/AUTH.md` — RBAC 섹션 추가 |
| C: 소셜/피드 | `docs/references/AUTH.md` + `docs/references/DB.md` |
| D: 검색 — PostgreSQL FTS | `docs/references/DB.md` — 검색 전략 섹션 추가 |
| D: 검색 — 외부 서비스 (Algolia 등) | `docs/references/API.md` — Search 섹션 추가 |
| E: 결제 있음 | `docs/references/BILLING.md` |
| F: AI 기능 있음 | `docs/references/AI_SYSTEMS.md` |
| G: 외부 API 있음 | `docs/references/API.md` |
| H: 스토리지 있음 | `docs/references/ARCHITECTURE.md` — Storage 섹션 |
| I: 알림 있음 | `docs/references/ARCHITECTURE.md` — Notifications 섹션 |
| J: 실시간 있음 | `docs/references/ARCHITECTURE.md` — Realtime 섹션 |
| K: 다국어 있음 | `docs/references/ARCHITECTURE.md` — i18n 섹션 |
| L~Q 중 하나라도 있음 | `docs/references/UI.md` |
| P-1: 반응형 선택 | `docs/references/UI.md` — Breakpoints·레이아웃 변화 섹션 추가 |
| N: SEO 있음 | `docs/references/UI.md` — SEO 섹션 추가 |
| CI/CD 있음 | `docs/references/ARCHITECTURE.md` — CI/CD 섹션 |
| AI 코딩 도구 사용 | `CLAUDE.md` 상세 + `docs/references/prompts/` |

> `docs/references/UI.md`는 화면 스펙 문서다. `docs/02-design/`(개발 설계)과 다르다.

**항상 생성**: `README.md`, `CLAUDE.md`, `docs/INDEX.md`, `docs/QUICK_REF.md`, `docs/ROADMAP.md`, `docs/references/README.md`

---

## 문서 작성 규칙

생성하는 모든 `docs/references/*.md`는 아래 섹션 순서를 따른다:

```md
## Purpose
## Current State
## Current Rules
## [주제별 핵심 섹션]
## Related Docs
```

파일 네이밍:
- Reference 문서: 대문자 주제명 — `AUTH.md`, `DB.md`, `UI.md`
- 세션/기록: 날짜 기반 — `SESSION-SUMMARY-2026-04-06.md`
- 파일명은 영어, 본문은 팀 주언어

---

## 최종 출력

```
## 문서 구조

[항상 생성]
README.md
CLAUDE.md
docs/
  INDEX.md
  QUICK_REF.md
  ROADMAP.md
  references/
    README.md

[기존 유지 — 건드리지 않음]
docs/01-plan/
docs/02-design/
docs/03-analysis/
docs/04-report/
docs/archive/

[이 앱에 추가]
docs/references/AUTH.md         ← 로그인 + RBAC
docs/references/DB.md           ← 데이터 영속성 + 검색 전략
docs/references/BILLING.md      ← 구독 결제
docs/references/AI_SYSTEMS.md   ← AI 기능 포함
docs/references/UI.md           ← 화면 스펙 + 다크모드 + SEO
docs/references/ARCHITECTURE.md ← 스토리지·알림·실시간·i18n·CI/CD

[생략]
docs/references/API.md ... (이유 한 줄씩)
```

> 이 구조로 파일 생성할까?

YES면 아래 순서로 생성한다:

1. `CLAUDE.md` — `references/CLAUDE_MD_GUIDE.md` 읽고 작성
2. `README.md` — 앱 개요 + 확정 스택 + 로컬 실행 방법
3. `docs/INDEX.md` — 모든 active 문서 링크 + 한 줄 설명
4. `docs/ROADMAP.md` — MVP 스코프 컷 결과 기반으로 작성
5. 나머지 `docs/references/*.md` — 위 섹션 템플릿 따름

---

## 톤

직접적이고 기술적인 대화체. Phase별로 끊어서 제시한다.
