# AGENTS.md Writing Guide

`AGENTS.md` is the cross-agent standard context file read by multiple AI coding agents:

| Agent | Reads AGENTS.md? | Own file |
|-------|-------------------|----------|
| Claude Code | Yes | `CLAUDE.md` |
| OpenAI Codex | Yes (primary) | — |
| GitHub Copilot | Yes (agent mode) | `.github/copilot-instructions.md` |
| Gemini CLI | Yes | `GEMINI.md` |
| Cursor | No | `.cursorrules` |
| Cline | No | `.clinerules` |

Put all universal project context in `AGENTS.md`. It's not an "onboarding doc" — it's **execution context for AI agents**.

**Writing principles**
- Include: Things the agent would get wrong without knowing
- Exclude: Things that can be inferred, generic advice

Bad: "Write clean code"
Good: "`src/components/ui/` is shadcn only — DO NOT modify directly"

> **Note**: Conditional section examples below use Supabase/Next.js as defaults.
> Replace service names, paths, and commands to match the stack confirmed in Phase 4-1.

---

## Required Sections (All Projects)

### 1. Project Overview
```md
## Project Overview
[Project name]: [One-line description]
Stack: [Confirmed tech list]
```
Two lines max.

---

### 2. Commands
```md
## Commands
dev:        pnpm dev
build:      pnpm build
test:       pnpm test          # Unit (Vitest)
test:e2e:   pnpm test:e2e      # E2E (Playwright) — only if exists
lint:       pnpm lint
typecheck:  pnpm typecheck
db:migrate: pnpm db:migrate    # only if DB exists
```
Specify package manager. Don't include commands that don't exist.

---

### 3. Project Structure
```md
## Project Structure
src/
  app/           # Next.js App Router
  components/
    ui/          # shadcn/ui (DO NOT modify)
    [feature]/   # Feature-specific components
  lib/           # Utilities & helpers
  hooks/
  types/
docs/
  references/    # Tech reference docs (skill-generated)
```
Just enough to know where to create files.

---

### 4. Key Conventions
```md
## Key Conventions
- Components: PascalCase, named export
- Hooks: camelCase, use prefix
- Import alias: @/ → src/
- Server components by default, 'use client' only when needed
- Types: prefer interface, use type for complex unions
```

---

### 5. Environment Variables
```md
## Environment Variables
NEXT_PUBLIC_SUPABASE_URL       # Client-safe
NEXT_PUBLIC_SUPABASE_ANON_KEY  # Client-safe
SUPABASE_SERVICE_ROLE_KEY      # Server-only — NEVER expose to client

Defined in .env.local. See .env.example.
```
Always specify `NEXT_PUBLIC_` prefix and exposure scope.

---

### 6. Do Not
```md
## Do Not
- Never modify src/components/ui/
- Never use `any` type
- Never commit console.log
- Never access DB directly from client (use Server Action or API Route)
- Never hardcode environment variables
```
Concrete prohibitions, not vague guidelines.

---

## Conditional Sections

Only add when the feature exists. Examples use Supabase/Next.js — **replace to match confirmed stack**.

### Auth
```md
## Auth
Provider: [Confirmed auth service]
Session: [Session check location]
Route protection: [Method — middleware / HOC / layout, etc.]
Auth-required APIs must verify session on server
```

Example (Supabase):
```md
## Auth
Provider: Supabase Auth (email + social)
Session: @/lib/supabase/server.ts createClient()
Route protection: middleware.ts
```

Example (NextAuth):
```md
## Auth
Provider: NextAuth.js (GitHub + Google)
Session: getServerSession() — call directly in server components / API routes
Route protection: middleware.ts matcher config
```

---

### Database
```md
## Database
DB: [Confirmed DB]
ORM/Client: [Confirmed ORM]
Schema: [Schema file path]
Migration: [Command]
Never query DB directly from client
```

Example (Supabase + Drizzle):
```md
## Database
DB: Supabase (PostgreSQL)
ORM: Drizzle — Schema: src/lib/db/schema.ts
Migration: pnpm db:migrate
```

Example (PlanetScale + Prisma):
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
Provider: [Confirmed AI service]
Model: [Model name]
Client: [Client path]
Prompts: docs/references/prompts/
Cost awareness: minimize unnecessary API calls
```

Example (Anthropic):
```md
## AI
Provider: Anthropic
Model: claude-sonnet-4-20250514
Client: @/lib/ai/client.ts
Streaming: Vercel AI SDK
Prompts: docs/references/prompts/
```

---

### RBAC (B-2: Roles exist)
```md
## Auth
...
Roles: [Role list — admin / user / guest, etc.]
Permission check: [Middleware / Server Action / both]
Admin routes: /admin/* — protected by separate layout
```

Example:
```md
## Auth
Provider: Supabase Auth
Roles: admin / user (managed via profiles.role column)
Permission check: Role verified inside Server Actions
Admin routes: app/(admin)/ — admin role only
```

---

### Search (D: Search exists)
```md
## Search
Provider: [PostgreSQL FTS / Algolia / Meilisearch]
Search target: [Table names or data types]
Real-time suggestions: [Yes / No]
```

Example (PostgreSQL FTS):
```md
## Search
Provider: PostgreSQL Full-Text Search (tsvector)
Search target: posts.title + posts.content
Index: GIN index on search_vector
Real-time suggestions: No (search on submit)
```

Example (Algolia):
```md
## Search
Provider: Algolia
Index: products
Real-time suggestions: Yes (InstantSearch.js)
Sync: Supabase webhook → Algolia update on DB change
```

---

### i18n (K: Multi-language exists)
```md
## i18n
Library: [next-intl / next-i18next]
Supported languages: [ko, en, etc.]
Default language: [ko]
URL structure: /[locale]/...
Translation files: messages/[locale].json
```

---

### Responsive Design (P-1: Responsive selected)
```md
## Responsive Design
Approach: mobile-first (Tailwind default — sm: md: lg: order)
Key breakpoints:
  - sm (640px): mobile → small tablet
  - md (768px): tablet
  - lg (1024px): desktop
Layout changes:
  - Navigation: desktop sidebar → mobile bottom tab bar
  - Grid: lg:grid-cols-3 → grid-cols-1
  - Table: desktop table → mobile card-based
Touch: minimum tap target 44px (Tailwind: min-h-11)
```
Omit this section if not responsive.

---

### SEO (N: SEO exists)
```md
## SEO
Strategy: [SSR / SSG / ISR]
Meta tags: generateMetadata()
OG image: [static / dynamic generation]
sitemap: app/sitemap.ts
robots: app/robots.ts
```

---

### Payments
```md
## Payments
Provider: [Stripe / Toss Payments / other]
Webhook: [webhook path]
Subscription state: [state management location]
Never implement payment logic directly on client
```

---

### File Storage
```md
## File Storage
Provider: [Confirmed storage]
Bucket: [bucket name + public/private]
Upload path: [path rules]
Max file size: [limit]
```

---

### Realtime
```md
## Realtime
[Confirmed realtime tech — Supabase Realtime / Socket.io / Ably, etc.]
Unsubscribe: must handle on component unmount
```

### Testing (Phase 4-3: Testing strategy exists)
```md
## Testing
Unit: [Vitest / Jest] — [test file location]
E2E: [Playwright / Cypress] — [test file location]  ← only if E2E exists
Coverage target: [business logic focused / full]
```

Example:
```md
## Testing
Unit: Vitest — src/**/*.test.ts
E2E: Playwright — e2e/**/*.spec.ts
Coverage target: business logic (lib/, hooks/) focused
```

---

### CI/CD (Phase 4-3: CI/CD exists)
```md
## CI/CD
Provider: [GitHub Actions / GitLab CI / other]
Pipeline:
  - PR: lint + typecheck + test
  - main merge: auto deploy → [platform]
Workflows: .github/workflows/
```

---

## Full Example

```md
## Project Overview
Taskflow: Team task management + real-time collaboration
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
    (auth)/       # Login/signup
    (dashboard)/  # Auth required
    api/
  components/
    ui/           # shadcn (DO NOT modify)
    tasks/
    layout/
  lib/
    db/           # Drizzle schema + client
    supabase/     # server / client separated
    ai/
  hooks/
  types/
docs/
  references/     # Tech references (skill-generated)

## Key Conventions
- Components: PascalCase, named export
- Import alias: @/ → src/
- Server components by default, 'use client' only when needed
- DB access: Server Action or API Route only
- Error handling: Result pattern ({ data, error })

## Environment Variables
NEXT_PUBLIC_SUPABASE_URL        # Client-safe
NEXT_PUBLIC_SUPABASE_ANON_KEY   # Client-safe
SUPABASE_SERVICE_ROLE_KEY       # Server-only, NEVER expose to client
ANTHROPIC_API_KEY               # Server-only

## Auth
Provider: Supabase Auth (email + social)
Session: @/lib/supabase/server.ts createClient()
Route protection: middleware.ts
Unauthenticated dashboard access → redirect to /login

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
Coverage: business logic focused, avoid UI snapshot tests

## Do Not
- Never modify src/components/ui/
- Never use `any` type
- Never commit console.log
- Never query DB directly from client
- Never expose SUPABASE_SERVICE_ROLE_KEY to client

## SEO
Strategy: SSR (dynamic) / ISR (semi-static like blog)
Meta tags: generateMetadata()
sitemap: app/sitemap.ts / robots: app/robots.ts

## CI/CD
GitHub Actions
PR: lint + typecheck + test
main merge: Vercel auto deploy
```

---

## Length Guidelines

| Project Complexity | Ideal Length |
|---|---|
| Simple tool (~3 screens) | 30–50 lines |
| Standard app (auth + DB) | 60–100 lines |
| Complex app (AI + payments + realtime) | 100–150 lines |

If over 150 lines, delegate details to dedicated docs in `docs/references/` and link from AGENTS.md:
```md
## Auth
→ Details: docs/references/AUTH.md
Key: Supabase Auth, session check in server.ts, route protection in middleware.ts
```
