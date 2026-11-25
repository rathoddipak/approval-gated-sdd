# Implementation Plan

**Owner:** Tech Lead + Technical Team
**Status:** DRAFT  <!-- set to APPROVED — <HH:MM> by /kickoff on APPROVE KICKOFF -->

> This file is the single source of truth for **what to build next**. `/start-story` reads the story's row here: owner, track, dependencies. `/implement-story` updates the Status column. Allowed statuses: `Not started` · `Task approved` · `Spec approved` · `In progress` · `Done` · `Blocked: <reason>`.

## First end-to-end vertical slice

<The thinnest path through the system that proves it works end to end.>

## Story ownership and order

| Story | Title | Track | Priority | Owner | Depends on | Story packet | Status |
|---|---|---|---|---|---|---|---|
| F0 | Foundation: models, migration, schemas, stubs, API client | F0 | P0 | | — | `specs/stories/F0-foundation/` | Not started |
| S01 | <title> | T1 | P0 | | F0 | `specs/stories/S01-<short-name>/` | Not started |

F0 is a technical enabler story. It has no product scope and implements only the foundation sections of `ARCHITECTURE.md`.

Dependency notes: mark a dependency as `(contract: …)` when the story can build against a stub, so `/implement-story` knows it is not a hard blocker.

## Cross-story / integration tasks

| Task | Owner | Depends on | Done check | Status |
|---|---|---|---|---|
| Vertical slice end to end | | F0 + … | <observable result> | Not started |
| Code + security review of merged P0 | each owner | P0 done | no blockers | Not started |

## Dependency order

F0 → (feature tracks in parallel against stub contracts) → vertical slice → full P0 → release/demo.

## Integration checkpoints

1. <time> — F0 merged, app runs, stubs visible
2. <time> — vertical slice end to end
3. <time> — all P0 stories done, tests pass
4. <time> — feature freeze

## Approval

- [ ] Story ownership agreed
- [ ] Dependencies understood
- [ ] Integration order agreed
- [ ] **APPROVE IMPLEMENTATION PLAN** received (via `APPROVE KICKOFF`)
