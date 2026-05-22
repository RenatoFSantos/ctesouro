# Contracts: Site Caça ao Tesouro

## Contract Strategy

Internal UI mutations use Server Actions with typed Zod inputs and typed results. Route Handlers are reserved for Auth.js, external integrations, webhooks, and future IA/RAG API boundaries.

All protected contracts must enforce permissions server-side. Client-side checks are only for UX.

## Server Actions

### Contact

`createContactRequest(input)`

Input:

```ts
type CreateContactRequestInput = {
  name: string;
  email?: string;
  phone?: string;
  organization?: string;
  eventType?: string;
  message: string;
};
```

Result:

```ts
type ActionResult<T> =
  | { ok: true; data: T }
  | { ok: false; fieldErrors?: Record<string, string[]>; message: string };
```

States: loading, success, validation error, unexpected error.

### Adventures

`createAdventure(input)`, `updateAdventure(id, input)`, `archiveAdventure(id)`, `publishAdventure(id)`.

Permissions: `ADMIN` or assigned `ORGANIZER`.

Validation: title, slug, format, audience, status, and schedule rules.

### Clues

`createClue(adventureId, input)`, `updateClue(id, input)`, `reorderClues(adventureId, orderedIds)`, `deleteClue(id)`.

Permissions: `ADMIN` or assigned `ORGANIZER`.

Validation: unique order per adventure, non-negative points, safe answer handling.

### Participant Progress

`joinAdventure(adventureId)`, `submitClueAttempt(clueId, input)`, `useHint(clueId)`.

Permissions: authenticated participant enrolled in the adventure.

Result must include updated progress summary and next unlocked state.

### Gamification

`recalculateScore(adventureId, userId)` and `grantAchievement(userId, achievementId, adventureId?)`.

Permissions: system, `ADMIN`, or trusted gameplay action.

## Route Handlers

### `GET|POST /api/auth/[...nextauth]`

Auth.js route handler.

### `POST /api/webhooks/*`

Reserved for future external services. Must validate signature before processing.

### `POST /api/rag/search`

Future IA/RAG search endpoint.

Request:

```ts
type RagSearchRequest = {
  query: string;
  limit?: number;
  filters?: {
    adventureId?: string;
    sourceType?: string;
  };
};
```

Response:

```ts
type RagSearchResponse = {
  results: Array<{
    chunkId: string;
    documentId: string;
    title: string;
    excerpt: string;
    score: number;
    metadata?: Record<string, unknown>;
  }>;
};
```

Permissions: authenticated admin/organizer until a public RAG use case is approved.

## Error Rules

- Validation errors return field-level messages.
- Permission failures return a permission-limited UI state for Server Actions or `403` for Route Handlers.
- Authentication failures redirect to login for pages or return `401` for API routes.
- Database/internal failures are logged server-side and return generic user-facing messages.

## Contract Tests

- Validate Zod schemas for each action.
- Test permission denial for participant/admin boundaries.
- Test route handlers for method, status code, and response shape.
- Test RAG endpoint contract before enabling public consumption.
