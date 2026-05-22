# Research: Site Caça ao Tesouro

## Decision: Use a single Next.js App Router application

**Rationale**: The requested product combines public marketing, authentication, admin screens, participant gameplay, and internal APIs. A single App Router application provides Server Components, layouts, route groups, metadata, Server Actions, and Route Handlers without the operational overhead of separate frontend/backend services.

**Alternatives considered**: Separate API service plus frontend was rejected for initial architecture because it adds deployment and authentication complexity before external API consumers exist.

## Decision: PostgreSQL 16+ with Prisma ORM and PGVector

**Rationale**: PostgreSQL is mandatory and supports the relational gameplay model plus PGVector for future IA/RAG. Prisma provides typed schema, migrations, and a consistent access layer for Next.js.

**Alternatives considered**: SQLite is explicitly forbidden. Direct SQL for all access was rejected because Prisma improves type safety and migration discipline. Raw SQL remains acceptable only for vector operations not fully supported by the installed Prisma version.

## Decision: Auth.js/NextAuth-compatible authentication

**Rationale**: It fits Next.js route handlers, Prisma persistence, session callbacks, and role-based access for participant, organizer, and admin areas.

**Alternatives considered**: A custom auth system was rejected due to security risk and avoidable maintenance. Hosted auth can be revisited if operations require it, but it is not necessary for the initial VPS/EasyPanel deployment.

## Decision: Server Actions for internal mutations, Route Handlers for protocol boundaries

**Rationale**: Server Actions keep form-driven admin and gameplay mutations close to the UI with server-side validation and authorization. Route Handlers are better for Auth.js, webhooks, external/mobile APIs, and future RAG endpoints that need explicit HTTP contracts.

**Alternatives considered**: All-REST internal APIs were rejected because they add boilerplate for UI-only workflows. All-Server-Actions was rejected because auth callbacks, webhooks, and external APIs require stable HTTP routes.

## Decision: Minimal global client state

**Rationale**: Server Components and URL state cover most loading and filtering needs. Local state covers menus, dialogs, and form UI. TanStack Query is reserved for interactive dashboard flows that need cache or optimistic updates.

**Alternatives considered**: A global store was rejected because no current workflow requires cross-page client state.

## Decision: shadcn/ui primitives with custom themed compositions

**Rationale**: shadcn/ui provides accessible primitives while allowing a fully custom adventure/premium identity with parchment surfaces, map textures, compass visuals, official colors, and cinematic section composition.

**Alternatives considered**: A generic component kit was rejected because it would likely produce SaaS-like visuals that conflict with `AGENTS.md`.

## Decision: SEO-first public landing page

**Rationale**: The public site must explain the project quickly and convert interest. Next.js metadata, semantic sections, optimized images, OpenGraph, and crawlable Portuguese copy directly support discovery and sharing.

**Alternatives considered**: Client-rendered marketing sections were rejected because they hurt SEO and first meaningful render.

## Decision: Docker + EasyPanel deployment

**Rationale**: The project targets VPS/EasyPanel. Docker gives a reproducible runtime, and EasyPanel can inject environment variables and connect the app to the existing PostgreSQL/PGVector service.

**Alternatives considered**: Serverless deployment was not selected because the specified infrastructure is Docker/EasyPanel on VPS.

## Decision: Accessibility and performance are architecture gates

**Rationale**: The constitution requires UX consistency and performance budgets. The landing page must be navigable, readable, and fast on mobile; authenticated workflows must include loading, empty, error, success, and permission-limited states.

**Alternatives considered**: Deferring accessibility/performance to implementation was rejected because both affect component, layout, image, and data-fetching choices.
