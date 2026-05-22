<!--
Sync Impact Report
Version change: template -> 1.0.0
Modified principles:
- PRINCIPLE_1_NAME placeholder -> I. Code Quality Is Non-Negotiable
- PRINCIPLE_2_NAME placeholder -> II. Tests Define Done
- PRINCIPLE_3_NAME placeholder -> III. Consistent User Experience
- PRINCIPLE_4_NAME placeholder -> IV. Performance Budgets Are Requirements
- PRINCIPLE_5_NAME placeholder -> V. Maintainable Delivery
Added sections:
- Quality Standards
- Development Workflow
Removed sections:
- None
Templates requiring updates:
- ✅ .specify/templates/plan-template.md
- ✅ .specify/templates/spec-template.md
- ✅ .specify/templates/tasks-template.md
- ⚠ .specify/templates/commands/*.md not present in this project
Follow-up TODOs:
- None
-->
# CacaTesouro Constitution

## Core Principles

### I. Code Quality Is Non-Negotiable
All production code MUST be simple, typed where the stack supports it, cohesive,
and consistent with the surrounding architecture. New abstractions MUST remove
measurable duplication or isolate real complexity; speculative abstractions are
not allowed. Code MUST pass the configured formatter, linter, and type checks
before review. Rationale: predictable code lowers maintenance cost and makes
feature work safer.

### II. Tests Define Done
Every behavior change MUST include automated tests at the appropriate level:
unit tests for isolated logic, integration tests for cross-module behavior, and
end-to-end or journey tests for critical user flows. Tests MUST be written so
they fail without the implementation change, and regressions MUST include a test
that reproduces the defect. Any skipped or deferred test requires an explicit
risk note in the implementation plan. Rationale: untested behavior is not
finished behavior.

### III. Consistent User Experience
User-facing changes MUST preserve established navigation, language, visual
patterns, accessibility expectations, and responsive behavior. New UI states
MUST cover loading, empty, error, success, and permission-limited cases when
applicable. Interactive elements MUST be keyboard-accessible and expose clear
labels for assistive technology. Rationale: consistency reduces user effort and
prevents isolated screens from degrading the product experience.

### IV. Performance Budgets Are Requirements
Each feature plan MUST define measurable performance goals or explicitly state
why no new budget is needed. User-facing flows MUST avoid avoidable blocking
work, layout instability, and excessive payload growth. Changes that affect data
loading, rendering, storage, or external calls MUST include a validation method
for the relevant budget. Rationale: performance must be designed and verified,
not treated as a late optimization pass.

### V. Maintainable Delivery
Features MUST be delivered in independently reviewable slices tied to user
stories. Implementation MUST prefer existing project conventions, dependency
patterns, and shared utilities unless the plan documents why a deviation is
necessary. Cross-cutting changes MUST include migration notes, compatibility
considerations, and rollback or recovery guidance when relevant. Rationale:
small, conventional changes are easier to review, test, and operate.

## Quality Standards

All specifications and plans MUST identify functional requirements, edge cases,
test coverage expectations, user experience constraints, and performance
budgets before implementation begins. Generated tasks MUST include concrete file
paths, explicit validation steps, and test tasks for every changed behavior.
Quality gates are blocking unless the implementation plan records a justified
exception with owner, risk, and follow-up.

## Development Workflow

Work proceeds from specification to plan to tasks to implementation. The
Constitution Check in each plan MUST verify code quality, test strategy, UX
consistency, and performance budgets before Phase 0 research and again after
Phase 1 design. Reviews MUST confirm that tests pass, UX states are accounted
for, performance validation is documented, and deviations from project patterns
are justified.

## Governance

This constitution supersedes conflicting local practices and templates. Changes
to principles or governance require a documented amendment that states the
reason, impact, migration needs, and version bump. Versioning follows semantic
versioning: MAJOR for incompatible governance or principle redefinitions, MINOR
for new principles or materially expanded guidance, and PATCH for clarifications
that do not change obligations. Compliance is reviewed during planning, task
generation, implementation review, and release readiness.

**Version**: 1.0.0 | **Ratified**: 2026-05-21 | **Last Amended**: 2026-05-21
