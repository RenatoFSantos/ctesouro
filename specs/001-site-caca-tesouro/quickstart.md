# Quickstart: Site Caça ao Tesouro

## Prerequisites

- Node.js LTS compatible with the chosen Next.js version.
- PostgreSQL 16+ with PGVector available.
- Docker and Docker Compose for local parity.
- `.env` configured from `.env.example`.

## Environment

Use a single `DATABASE_URL` in application code:

```env
DATABASE_URL=postgresql://postgres:${DB_POSTGRESDB_PASSWORD}@automatize_pgvector:5432/ctesouro?schema=public
NEXTAUTH_SECRET=
NEXTAUTH_URL=http://localhost:3000
APP_URL=http://localhost:3000
```

Do not configure SQLite.

## Local Setup

```bash
npm install
npx prisma generate
npx prisma migrate dev
npm run dev
```

Open `http://localhost:3000`.

## Validation

```bash
npm run lint
npm run typecheck
npm run test
npm run test:e2e
npx prisma validate
```

## Docker/EasyPanel Path

1. Build the Docker image.
2. Inject environment variables through EasyPanel.
3. Ensure the PostgreSQL service exposes PGVector.
4. Run `npx prisma migrate deploy` during release.
5. Start the Next.js production server.

## Manual Smoke Tests

- Landing page shows the official logo and all six navigation sections.
- Mobile menu opens, closes, and reaches each section by keyboard.
- Contact form exposes loading, success, validation error, and unexpected error states.
- Login redirects authenticated users to the dashboard.
- Admin users can create an adventure and clue.
- Participants can join an adventure and submit a clue attempt.
- Ranking orders users by score and completion rules.
