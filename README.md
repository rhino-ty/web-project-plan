# Web Project Plan

> AI coding agent skill that turns a vague web project idea into a concrete project setup through a structured interview.

## What it does

When you say "I want to build a web project" or "new project", this skill walks you through a 4-phase interview:

| Phase | What happens |
|-------|-------------|
| **1. Identity** | Project name, target users, web project type (SPA/SSR/SSG) |
| **2. Features** | DB, auth, search, payments, AI, file storage, notifications, i18n, realtime |
| **3. Design & UI** | References, responsive layout, dark mode, SEO, interactions |
| **4. Stack & MVP** | Tech stack proposal, MVP scope cut, deploy & CI/CD |

At the end, you get real files:

```
AGENTS.md          # Cross-agent context (works with Claude, Codex, Copilot, Gemini)
CLAUDE.md          # Claude Code specific (generated if using Claude Code)
README.md          # Project overview
docs/
  INDEX.md         # Document map
  QUICK_REF.md     # Commands & env vars cheatsheet
  ROADMAP.md       # MVP scope + backlog
  references/      # Tech reference docs (AUTH.md, DB.md, UI.md, ...)
```

## Cross-agent compatibility

`AGENTS.md` is the primary output — it's the cross-agent standard:

| Agent | Reads AGENTS.md? | Extra file generated |
|-------|-------------------|----------------------|
| Claude Code | Yes | `CLAUDE.md` (auto-generated) |
| OpenAI Codex | Yes | — |
| GitHub Copilot | Yes (agent mode) | — |
| Gemini CLI | Yes | — |
| Cursor | No | `.cursorrules` (suggested) |

## Install

```bash
npx skills add rhino-ty/web-project-plan
```

## Trigger keywords

| Language | Keywords |
|----------|----------|
| English | `new project`, `project setup`, `web project plan`, `pick a stack`, `scaffold` |
| Korean | `새 프로젝트`, `프로젝트 세팅`, `웹앱 기획`, `스택 잡아줘`, `문서 구조 잡아줘` |
| Japanese | `新しいプロジェクト`, `プロジェクトセットアップ`, `スタック選定` |
| Chinese | `新项目`, `项目搭建`, `技术栈选择` |
| French | `nouveau projet`, `configuration du projet` |
| German | `neues Projekt`, `Projekt einrichten` |
| Spanish | `nuevo proyecto`, `configurar proyecto` |

## Example output: AGENTS.md

After the interview, you get an AGENTS.md like this (trimmed):

```md
## Project Overview
Taskflow: Team task management + real-time collaboration
Stack: Next.js 15, Tailwind v4, shadcn/ui, Supabase, Drizzle ORM

## Commands
dev:        pnpm dev
build:      pnpm build
test:       pnpm test
lint:       pnpm lint

## Project Structure
src/
  app/           # Next.js App Router
  components/
    ui/          # shadcn/ui (DO NOT modify)
  lib/
    db/          # Drizzle schema + client

## Key Conventions
- Components: PascalCase, named export
- Server components by default, 'use client' only when needed

## Do Not
- Never modify src/components/ui/
- Never use `any` type
- Never expose SUPABASE_SERVICE_ROLE_KEY to client
```

## File structure

```
SKILL.md                        # Main skill instructions
references/
  AGENTS_MD_GUIDE.md            # AGENTS.md writing guide & templates
```

## Who is this for

Developers who use any AI coding agent and want to skip the "blank project" paralysis. Talk through your idea and get a structured project scaffold that works across agents.

## Language

- Skill instructions and interview: English
- Responds in the user's language
- Triggers work across 7 languages

## License

MIT
