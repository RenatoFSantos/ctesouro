# Implementation Plan: Site Caça ao Tesouro

**Branch**: `main` | **Date**: 2026-05-22 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-site-caca-tesouro/spec.md` plus architecture request for authentication, admin, treasure hunts, clues, participant progress, ranking, gamification, and future AI/RAG.

## Summary

Build the initial architecture for the official "Caça ao Tesouro" website as a single Next.js App Router application with a premium adventure-themed landing page, authenticated user area, admin workflows, and PostgreSQL-backed domain models. The first public experience prioritizes the institutional landing page from the feature spec; the architecture also establishes clean boundaries for authenticated gameplay, administration, ranking, gamification, and future PGVector-based IA/RAG without introducing separate services prematurely.

## Technical Context

**Language/Version**: TypeScript strict mode, React, Next.js App Router.

**Primary Dependencies**: Next.js, React, TailwindCSS, shadcn/ui, Prisma ORM, PostgreSQL driver, Auth.js/NextAuth-compatible authentication, Zod, React Hook Form, TanStack Query only for client-side interactive data where Server Components are insufficient.

**Storage**: PostgreSQL 16+ with Prisma ORM. PGVector extension reserved for embeddings and RAG tables. SQLite is forbidden.

**Testing**: Vitest for unit tests, React Testing Library for component behavior, Playwright for critical journeys, Prisma migration validation against PostgreSQL, accessibility checks with axe-compatible tooling.

**Target Platform**: Dockerized web application deployed to VPS through EasyPanel, reading configuration from `.env`.

**Project Type**: Full-stack web application using Next.js App Router.

**Performance Goals**: First meaningful landing-page view under 3 seconds on common mobile connection; Lighthouse targets of 90+ performance, 95+ accessibility, 95+ SEO for public pages; avoid avoidable layout shift for hero/logo/media.

**Constraints**: Mobile-first responsive design, official visual identity and palette from `AGENTS.md`, PostgreSQL only, UUID primary keys, UTC timestamps, no hardcoded credentials, no `any`, reusable components, Server Components by default.

**Scale/Scope**: Initial single app prepared for public landing page, contact capture, authentication, admin CRUD for adventures and clues, participant progress, rankings, achievements, and future RAG content retrieval.

## Constitution Check

*GATE: Passed before Phase 0 research. Re-check after Phase 1 design.*

- **Code Quality**: Architecture isolates route groups, domain services, server actions, route handlers, Prisma access, UI primitives, and feature components. Commands: `npm run lint`, `npm run typecheck`, `npm run test`, `npm run test:e2e`, `npx prisma validate`.
- **Test Strategy**: Unit tests cover domain validation, scoring, progress, ranking, and authorization helpers. Integration tests cover Prisma repositories/actions against PostgreSQL. E2E tests cover landing navigation, contact flow, login, admin CRUD, participant progress, and ranking visibility.
- **User Experience Consistency**: All UI follows the official adventure premium identity, Portuguese copy, keyboard navigation, focus states, loading/empty/error/success states, and permission-limited states for admin/authenticated routes.
- **Performance Budgets**: Landing page must render essential content under 3 seconds on mobile, use optimized images, Server Components, route-level code splitting, static metadata, and lazy-load non-critical interactive sections.
- **Maintainable Delivery**: Delivery can be sliced into landing foundation, auth foundation, admin CRUD, participant gameplay, ranking/gamification, and RAG groundwork. Database changes are Prisma migrations with rollback notes.

## Project Structure

### Documentation (this feature)

```text
specs/001-site-caca-tesouro/
├── architecture.md
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── api.md
└── tasks.md              # Created later by /speckit-tasks
```

### Source Code (repository root)

```text
.
├── prisma/
│   ├── schema.prisma
│   ├── migrations/
│   └── seed.ts
├── public/
│   ├── Logo_1284x627px.png
│   └── Logo_148x72px.png
├── src/
│   ├── app/
│   │   ├── (marketing)/
│   │   │   ├── layout.tsx
│   │   │   └── page.tsx
│   │   ├── (auth)/
│   │   │   ├── login/page.tsx
│   │   │   └── register/page.tsx
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx
│   │   │   ├── admin/
│   │   │   ├── aventuras/
│   │   │   ├── progresso/
│   │   │   └── ranking/
│   │   ├── api/
│   │   │   ├── auth/[...nextauth]/route.ts
│   │   │   ├── webhooks/
│   │   │   └── rag/search/route.ts
│   │   ├── layout.tsx
│   │   ├── not-found.tsx
│   │   └── error.tsx
│   ├── components/
│   │   ├── ui/
│   │   ├── layout/
│   │   ├── marketing/
│   │   ├── dashboard/
│   │   ├── forms/
│   │   └── thematic/
│   ├── config/
│   │   ├── env.ts
│   │   ├── navigation.ts
│   │   └── site.ts
│   ├── features/
│   │   ├── auth/
│   │   ├── adventures/
│   │   ├── clues/
│   │   ├── progress/
│   │   ├── ranking/
│   │   ├── gamification/
│   │   ├── contact/
│   │   └── rag/
│   ├── hooks/
│   ├── lib/
│   │   ├── auth/
│   │   ├── db/
│   │   ├── permissions/
│   │   ├── seo/
│   │   ├── utils/
│   │   └── validations/
│   ├── providers/
│   ├── services/
│   ├── styles/
│   │   └── globals.css
│   └── types/
├── tests/
│   ├── e2e/
│   ├── integration/
│   └── unit/
├── docker/
│   └── entrypoint.sh
├── Dockerfile
├── docker-compose.yml
└── .env.example
```

**Structure Decision**: Use a single Next.js application with feature folders and route groups. This keeps the initial product deployable on one VPS/EasyPanel service while still separating marketing, auth, dashboard, admin, domain logic, and infrastructure concerns.

## Complexity Tracking

No constitution violations. The architecture introduces clear domain modules because the requested scope spans marketing, authentication, admin management, gameplay, ranking, gamification, and future RAG. A monolithic app is still the simplest deployable shape.

## Phase Outputs

- [architecture.md](./architecture.md): initial architecture, stack decisions, frontend/backend/database/deployment/coding conventions.
- [research.md](./research.md): decisions and rationale for framework, auth, data, API, state, SEO, performance, accessibility, and deployment.
- [data-model.md](./data-model.md): PostgreSQL/Prisma entity model and relationships.
- [contracts/api.md](./contracts/api.md): API, Server Actions, and Route Handler contracts.
- [quickstart.md](./quickstart.md): local setup and validation path.

## Post-Design Constitution Check

- **Code Quality**: Passed. The design defines typed module boundaries, Prisma access through `src/lib/db` and feature services, and reusable UI/component conventions.
- **Test Strategy**: Passed. The plan defines unit, integration, contract, e2e, migration, accessibility, and performance validation.
- **User Experience Consistency**: Passed. All public and authenticated UI states are specified with the official palette, theme, responsive behavior, and accessibility constraints.
- **Performance Budgets**: Passed. Public pages use Server Components, optimized assets, metadata, static-friendly sections, and lazy loading for interactive modules.
- **Maintainable Delivery**: Passed. Work can be decomposed into reviewable slices with migration and deployment notes.
