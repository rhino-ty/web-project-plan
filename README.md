# Web Project Plan

> Claude Code skill that turns a vague web project idea into a concrete project setup through a structured interview.

## What it does

When you say things like "I want to build a web project" or "new project", this skill kicks in and walks you through a 4-phase interview:

| Phase | What happens |
|-------|-------------|
| **1. Identity** | App name, target users, web app type (SPA/SSR/SSG) |
| **2. Features** | DB, auth, search, payments, AI, file storage, notifications, i18n, realtime |
| **3. Design & UI** | References, responsive layout, dark mode, SEO, interactions |
| **4. Stack & MVP** | Tech stack proposal, MVP scope cut, deploy & CI/CD |

At the end, you get real files:

```
CLAUDE.md          # Agent execution context
README.md          # Project overview
docs/
  INDEX.md         # Document map
  QUICK_REF.md     # Commands & env vars cheatsheet
  ROADMAP.md       # MVP scope + backlog
  references/      # Tech reference docs (AUTH.md, DB.md, UI.md, ...)
```

## Install

```bash
npx skills add rhino-ty/web-project-plan
```

## Trigger keywords

- `web project plan`, `new project`, `project setup`
- `document structure`, `pick a stack`
- Korean: `웹 프로젝트 시작`, `새 프로젝트 시작`, `웹앱 기획`, `문서 구조 잡아줘`, `프로젝트 세팅`, `스택 잡아줘`

## File structure

```
SKILL.md                        # Main skill instructions
references/
  CLAUDE_MD_GUIDE.md            # CLAUDE.md writing guide & templates
```

## Example output: CLAUDE.md

After the interview, you get a CLAUDE.md like this (trimmed):

```md
## Project Overview
Taskflow: Team task management + real-time collaboration
Stack: Next.js 15, Tailwind v4, shadcn/ui, Supabase, Drizzle ORM

## Commands
dev:        pnpm dev
build:      pnpm build
test:       pnpm test
lint:       pnpm lint
typecheck:  pnpm typecheck
db:migrate: pnpm db:migrate

## Project Structure
src/
  app/           # Next.js App Router
  components/
    ui/          # shadcn/ui (DO NOT modify)
    tasks/       # feature components
  lib/
    db/          # Drizzle schema + client
    supabase/    # server / client separated

## Key Conventions
- Components: PascalCase, named export
- Server components by default, 'use client' only when needed
- DB access: Server Action or API Route only

## Do Not
- Never modify src/components/ui/
- Never use `any` type
- Never commit console.log
- Never expose SUPABASE_SERVICE_ROLE_KEY to client
```

## Who is this for

Developers who use Claude Code and want to skip the "blank project" paralysis. Instead of setting up docs manually, just talk through your idea and get a structured project scaffold.

## Language

- Skill instructions: Korean
- Works with: Korean conversations (English triggers also supported)

## License

MIT
