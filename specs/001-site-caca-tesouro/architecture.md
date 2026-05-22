# Architecture: Site Caça ao Tesouro

## Scope

The initial architecture supports a premium adventure-themed public website and prepares the product for authenticated participant and administrative workflows. The landing page is the first production-facing slice; authentication, admin, treasure hunt management, clues, participant progress, ranking, gamification, and future IA/RAG are designed as first-class modules from the start.

## Stack Decisions

| Area | Decision | Rationale |
|------|----------|-----------|
| Application | Next.js App Router | Server Components, route groups, metadata APIs, and full-stack deployment in one VPS-friendly app. |
| UI | React + TypeScript strict | Strong component model with compile-time safety. |
| Styling | TailwindCSS + shadcn/ui | Fast themed UI foundation while preserving custom adventure identity. |
| Database | PostgreSQL 16+ | Required primary database, production-ready relational model, compatible with PGVector. |
| ORM | Prisma ORM | Typed database client, migrations, schema-as-code, predictable PostgreSQL workflow. |
| Vector search | PGVector | Future IA/RAG storage in the same PostgreSQL infrastructure. |
| Auth | Auth.js/NextAuth-compatible architecture | Mature session handling for Next.js, Prisma adapter compatibility, role-aware callbacks. |
| Validation | Zod | Shared server/action/form validation without `any`. |
| Forms | React Hook Form + Zod resolver | Accessible, typed forms with clear success/error states. |
| Tests | Vitest, React Testing Library, Playwright | Unit, component, integration, and journey coverage aligned with constitution. |
| Deployment | Docker + EasyPanel | VPS-ready, environment-based deployment without hardcoded secrets. |

## Folder Structure

```text
src/
├── app/
│   ├── (marketing)/              # Public institutional routes
│   ├── (auth)/                   # Login/register/recovery routes
│   ├── (dashboard)/              # Authenticated participant/admin area
│   ├── api/                      # Route Handlers for auth, webhooks, external/RAG APIs
│   ├── layout.tsx
│   ├── error.tsx
│   └── not-found.tsx
├── components/
│   ├── ui/                       # shadcn/ui primitives only
│   ├── layout/                   # Header, footer, nav, shell
│   ├── marketing/                # Landing sections
│   ├── dashboard/                # Dashboard shared components
│   ├── forms/                    # Reusable form controls/compositions
│   └── thematic/                 # Compass, map, parchment, route, coin visuals
├── config/
│   ├── env.ts                    # Typed env parsing
│   ├── navigation.ts             # Menu definitions
│   └── site.ts                   # SEO/site constants
├── features/
│   ├── auth/
│   ├── adventures/
│   ├── clues/
│   ├── progress/
│   ├── ranking/
│   ├── gamification/
│   ├── contact/
│   └── rag/
├── hooks/
├── lib/
│   ├── auth/
│   ├── db/
│   ├── permissions/
│   ├── seo/
│   ├── utils/
│   └── validations/
├── providers/
├── services/
├── styles/
└── types/
```

Feature folders should contain their own `actions.ts`, `queries.ts`, `schemas.ts`, `types.ts`, and focused UI pieces when needed. Cross-feature primitives stay in `components`, `lib`, or `services`.

## Component Strategy

Use shadcn/ui only as accessible primitives in `src/components/ui`. Project components compose these primitives into themed UI: parchment cards, compass buttons, map-section bands, ranking tables, clue panels, progress trails, and admin forms.

Server Components are the default for content, lists, dashboards, and SEO-heavy pages. Client Components are allowed for form interactivity, menus, tabs, dialogs, optimistic progress actions, ranking filters, and game interactions.

The visual language must follow `AGENTS.md`: official logos, `#7D420C`, `#4E2A08`, `#F7B400`, `#D89B00`, `#F5E6C8`, `#FFF8ED`, `#1A1A1A`, and `#D6C3A5`; cinematic adventure tone; light antique-map texture; premium shadows; no generic SaaS, neon, cold tech, or excessive flat UI.

## Layout Organization

`src/app/layout.tsx` defines global fonts, metadata base, providers, and `globals.css`.

`(marketing)/layout.tsx` uses the public header, anchored navigation, footer, and immersive landing structure. `page.tsx` contains sections for "O que é?", "Tipos de Aventuras", "Funcionamento", "Aventuras", "Diferenciais", and "Contato".

`(auth)/layout.tsx` uses a simpler centered parchment-style shell, preserving brand and accessibility.

`(dashboard)/layout.tsx` enforces authentication, renders participant/admin navigation, and exposes permission-limited states. Admin pages live under `(dashboard)/admin`.

## Providers

Keep global providers minimal:

- `ThemeProvider` only if dark/light mode becomes explicit; default is official light parchment theme.
- `SessionProvider` only where client session awareness is necessary.
- `QueryClientProvider` only for dashboard client flows that need cache, optimistic updates, or refetching.
- Toast provider for form success/error feedback.

Server-side data should not be routed through client global state.

## Authentication Architecture

Use Auth.js/NextAuth-compatible sessions with Prisma adapter and PostgreSQL persistence.

Roles:

- `VISITOR`: unauthenticated user.
- `PARTICIPANT`: authenticated player.
- `ORGANIZER`: can manage assigned adventures.
- `ADMIN`: full administrative access.

Authorization rules live in `src/lib/permissions`. Route protection is enforced in dashboard layouts and sensitive server actions. The database stores users, accounts, sessions, verification tokens, roles, adventure assignments, and audit-friendly timestamps.

## Database Strategy

PostgreSQL 16+ is mandatory. Prisma models use UUID primary keys with `@default(uuid())`, `createdAt`, `updatedAt`, and UTC timestamps. Migrations must be generated with Prisma and validated against PostgreSQL, never SQLite.

Core domains:

- Identity: `User`, `Account`, `Session`, `VerificationToken`.
- Adventure management: `Adventure`, `AdventureTheme`, `AdventureParticipant`, `Clue`.
- Gameplay: `ProgressEvent`, `ClueAttempt`, `ParticipantProgress`.
- Ranking/gamification: `ScoreEntry`, `Achievement`, `UserAchievement`.
- Contact/commercial: `ContactRequest`.
- RAG-ready content: `KnowledgeDocument`, `KnowledgeChunk`, embeddings with PGVector.

PGVector is enabled through a PostgreSQL extension migration. Prisma can represent vector-specific columns with provider-supported types where available; if the Prisma version lacks native vector support, use raw SQL migrations and typed service functions around vector operations.

## Prisma Strategy

`prisma/schema.prisma` is the source of truth. `src/lib/db/prisma.ts` exports a singleton Prisma client. Feature code should access data through typed query/action helpers, not from UI components directly.

Migration rules:

- Use `prisma migrate dev` locally against PostgreSQL.
- Use `prisma migrate deploy` in Docker/EasyPanel.
- Seed only non-secret demo/reference data.
- Keep destructive changes explicit and documented.

## Backend Strategy

Use Server Actions for internal form mutations and authenticated UI workflows:

- contact request creation
- adventure create/update/delete
- clue create/update/reorder
- participant enrollment
- clue attempt submission
- progress updates
- achievement assignment

Use Route Handlers for protocol-style boundaries:

- Auth.js route
- webhooks
- future mobile/external API access
- IA/RAG search endpoints
- file upload callbacks, if introduced

All writes validate input with Zod, enforce permissions server-side, return typed result objects, and avoid leaking database errors to users.

## State Strategy

Prefer URL state for filters, tabs, search, and pagination. Prefer Server Components for loaded data. Use local React state for isolated UI controls. Use TanStack Query only for dashboard interactions that require client cache, optimistic updates, polling, or refetching after mutations. Do not introduce broad global stores unless a cross-page interactive workflow proves it is needed.

## SEO Strategy

Public pages use Next.js metadata APIs, semantic headings, OpenGraph metadata, optimized logos/images, canonical URL, Portuguese locale, structured content, and crawlable landing sections. Admin and dashboard pages should be `noindex`.

The landing page should include high-signal title and description around "Caça ao Tesouro", "aventuras personalizadas", "eventos", "experiência lúdica", and "tesouro escondido".

## Performance Strategy

Use Server Components by default, `next/image` for logos and thematic imagery, static-friendly marketing content, dynamic imports for heavy admin/game widgets, route-level loading states, and optimized font loading. Avoid large animation libraries for the landing page; prefer CSS transitions and limited parallax.

Validation:

- Lighthouse on public landing page.
- Playwright visual checks on mobile and desktop.
- Bundle analysis before adding heavy libraries.
- PostgreSQL query inspection for ranking/progress lists.

## Responsive Strategy

Design mobile-first. Navigation collapses into an accessible menu on small screens. Landing sections stack vertically first and scale to multi-column layouts at tablet/desktop widths. Fixed-format elements such as ranking rows, progress trails, clue cards, and dashboard controls need stable dimensions and no text overlap.

## Accessibility Strategy

Use semantic landmarks, one `h1` per page, visible focus states, keyboard-accessible menus/dialogs/forms, sufficient contrast against parchment backgrounds, labels and descriptions for form fields, meaningful alt text for logos and content images, decorative images marked as decorative, and clear permission-limited messaging.

## Deployment Strategy

Docker image runs Next.js in production mode. EasyPanel injects `.env` variables and connects the app to PostgreSQL/PGVector. The deployment process runs Prisma migrations before starting the app. Secrets are never committed.

Expected environment:

```env
DATABASE_URL=postgresql://postgres:${DB_POSTGRESDB_PASSWORD}@automatize_pgvector:5432/ctesouro?schema=public
NEXTAUTH_SECRET=
NEXTAUTH_URL=
APP_URL=
```

The existing DB variables from `AGENTS.md` may be used to build `DATABASE_URL` in deployment configuration, but application code should consume a single validated `DATABASE_URL`.

## Coding Conventions

- Use TypeScript strict mode and avoid `any`.
- Name React components in `PascalCase`.
- Name hooks as `useThing`.
- Name Server Actions as verb-first functions: `createAdventure`, `submitClueAttempt`.
- Name query helpers as intent-first functions: `getAdventureBySlug`, `listRanking`.
- Keep route segment names lowercase and kebab-case.
- Keep domain schemas in `features/*/schemas.ts`.
- Keep permission checks explicit and colocated in `src/lib/permissions`.
- Use Portuguese for user-facing copy and English for code identifiers.
- Keep comments rare and useful.
