# Data Model: Site Caça ao Tesouro

## Global Rules

- Database: PostgreSQL 16+ only.
- ORM: Prisma.
- Primary keys: UUID.
- Timezone: store timestamps in UTC.
- Common fields: `id`, `createdAt`, `updatedAt`; add `deletedAt` only when soft delete is required.
- Enums should be represented in Prisma and mapped to PostgreSQL-compatible migrations.

## Entities

### User

Represents a registered participant, organizer, or admin.

Fields: `id`, `name`, `email`, `emailVerified`, `image`, `role`, `createdAt`, `updatedAt`.

Relationships: has many `Account`, `Session`, `AdventureParticipant`, `ProgressEvent`, `ClueAttempt`, `ScoreEntry`, `UserAchievement`.

Validation: email unique; role one of `PARTICIPANT`, `ORGANIZER`, `ADMIN`.

### Account, Session, VerificationToken

Auth.js-compatible persistence models.

Validation: follow adapter schema; session tokens and provider account identifiers must be unique.

### Adventure

Represents a treasure hunt experience.

Fields: `id`, `slug`, `title`, `summary`, `description`, `status`, `format`, `audience`, `locationName`, `startsAt`, `endsAt`, `createdById`, `createdAt`, `updatedAt`.

Relationships: has many `Clue`, `AdventureParticipant`, `ScoreEntry`, `Achievement`; belongs to creator `User`.

Validation: `slug` unique; status one of `DRAFT`, `PUBLISHED`, `ACTIVE`, `COMPLETED`, `ARCHIVED`; format one of `CUSTOM`, `THEMATIC`, `TEMPORARY`, `PERMANENT`.

### Clue

Represents an ordered clue in an adventure.

Fields: `id`, `adventureId`, `order`, `title`, `body`, `hint`, `answerHash`, `points`, `unlockMode`, `isFinal`, `createdAt`, `updatedAt`.

Relationships: belongs to `Adventure`; has many `ClueAttempt` and `ProgressEvent`.

Validation: unique `adventureId + order`; points non-negative; final clue should be unique per adventure.

### AdventureParticipant

Connects a user to an adventure.

Fields: `id`, `adventureId`, `userId`, `status`, `joinedAt`, `completedAt`, `createdAt`, `updatedAt`.

Relationships: belongs to `Adventure` and `User`.

Validation: unique `adventureId + userId`; status one of `INVITED`, `ACTIVE`, `COMPLETED`, `ABANDONED`, `DISQUALIFIED`.

### ParticipantProgress

Stores the current aggregate progress for a participant in an adventure.

Fields: `id`, `adventureParticipantId`, `currentClueId`, `completedClues`, `totalPoints`, `lastActivityAt`, `createdAt`, `updatedAt`.

Relationships: belongs to `AdventureParticipant`; optionally references current `Clue`.

Validation: one progress row per participant/adventure registration.

### ClueAttempt

Represents a submitted answer or interaction with a clue.

Fields: `id`, `clueId`, `userId`, `adventureParticipantId`, `submittedAnswer`, `isCorrect`, `pointsAwarded`, `attemptedAt`.

Relationships: belongs to `Clue`, `User`, and `AdventureParticipant`.

Validation: answer required; points awarded cannot be negative.

### ProgressEvent

Append-only event log for gameplay state transitions.

Fields: `id`, `adventureId`, `clueId`, `userId`, `type`, `metadata`, `occurredAt`.

Relationships: belongs to `Adventure`, optional `Clue`, and `User`.

Validation: type one of `JOINED`, `CLUE_UNLOCKED`, `CLUE_SOLVED`, `HINT_USED`, `ADVENTURE_COMPLETED`, `POINTS_ADDED`, `ACHIEVEMENT_UNLOCKED`.

### ScoreEntry

Ranking entry by user/adventure.

Fields: `id`, `adventureId`, `userId`, `points`, `completedClues`, `completionTimeSeconds`, `rankedAt`, `createdAt`, `updatedAt`.

Relationships: belongs to `Adventure` and `User`.

Validation: unique `adventureId + userId`; order by points desc, completed clues desc, completion time asc.

### Achievement

Represents a gamified badge or reward.

Fields: `id`, `adventureId`, `code`, `title`, `description`, `icon`, `points`, `criteria`, `createdAt`, `updatedAt`.

Relationships: belongs to optional `Adventure`; has many `UserAchievement`.

Validation: `code` unique per adventure/global scope.

### UserAchievement

Connects users to unlocked achievements.

Fields: `id`, `userId`, `achievementId`, `adventureId`, `unlockedAt`.

Relationships: belongs to `User`, `Achievement`, optional `Adventure`.

Validation: unique `userId + achievementId + adventureId`.

### ContactRequest

Represents a visitor contact or proposal request.

Fields: `id`, `name`, `email`, `phone`, `organization`, `eventType`, `message`, `status`, `source`, `createdAt`, `updatedAt`.

Validation: name, email or phone, and message required; status one of `NEW`, `CONTACTED`, `QUALIFIED`, `ARCHIVED`.

### KnowledgeDocument

Future IA/RAG source document.

Fields: `id`, `title`, `sourceType`, `sourceRef`, `contentHash`, `status`, `createdAt`, `updatedAt`.

Relationships: has many `KnowledgeChunk`.

Validation: content hash unique per source.

### KnowledgeChunk

Future searchable text chunk with vector embedding.

Fields: `id`, `documentId`, `chunkIndex`, `content`, `embedding`, `metadata`, `createdAt`, `updatedAt`.

Relationships: belongs to `KnowledgeDocument`.

Validation: unique `documentId + chunkIndex`; vector dimension must match the chosen embedding model.

## State Transitions

Adventure: `DRAFT -> PUBLISHED -> ACTIVE -> COMPLETED -> ARCHIVED`.

Participant: `INVITED -> ACTIVE -> COMPLETED`; `ACTIVE -> ABANDONED`; admin can mark `DISQUALIFIED`.

ContactRequest: `NEW -> CONTACTED -> QUALIFIED` or `NEW -> ARCHIVED`.

KnowledgeDocument: `DRAFT -> INDEXED -> STALE -> ARCHIVED`.

## Index Strategy

- Unique index on `User.email`.
- Unique index on `Adventure.slug`.
- Composite indexes for `Clue(adventureId, order)`, `AdventureParticipant(adventureId, userId)`, `ScoreEntry(adventureId, points, completedClues, completionTimeSeconds)`.
- Index `ProgressEvent(adventureId, userId, occurredAt)`.
- PGVector index on `KnowledgeChunk.embedding` when RAG search is implemented.
