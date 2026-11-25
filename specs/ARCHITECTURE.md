# Architecture

**Purpose:** Technical plan for the MVP: stack, data model, API, screens and who owns which files.
**Owner:** <Tech Lead>
**Status:** DRAFT  <!-- set to APPROVED — <HH:MM> by /kickoff on APPROVE KICKOFF -->
**Source requirements:** `specs/REQUIREMENTS.md` (must be APPROVED / FROZEN)

> Written by the team, reviewed by Claude in `/kickoff`. `/start-story` reads the section for the story's track; `/implement-story` and the `reviewer` agent use §7 to decide which files a story may touch.

## 0. Change log (read first)

Every change `/kickoff` or a story forces on this file gets a row here.

| # | Change | Why (requirement / story) | When |
|---|---|---|---|
| | | | |

## 1. Stack

- Language / framework(s): <…>
- Data store: <… or none>
- UI: <… or none>
- AI / external services: <… and the shared client module they go through>
- Run locally: <command>

## 2. Domain / input contracts

<Key inputs, formats and validation rules, or a link to a separate approved spec listed in CLAUDE.md §7.>

## 3. Data model

| Table / model | Key fields | Owner track |
|---|---|---|
| | | |

## 4. Core flow / pipeline

```text
<input> → <step> → <step> → <output>
```

## 5. API endpoints

| Method | Path | Purpose | Story | Track |
|---|---|---|---|---|
| | | | | |

## 6. Screens

| Screen | Route | Story | Track |
|---|---|---|---|
| | | | |

## 7. Work split (tracks)

One story = one owner. A story touches only its track's files plus the append-only shared files in `CLAUDE.md` §8.

| Track | Scope | Files / folders owned |
|---|---|---|
| F0 | Foundation: models, migration, shared schemas, stub routes, API client | <paths> |
| T1 | <area> | <paths> |
| T2 | <area> | <paths> |

## 8. Build order / time box

1. F0 foundation (stubs make every contract visible).
2. Tracks in parallel against stub contracts.
3. First end-to-end vertical slice.
4. Remaining P0 stories → freeze → release/demo.

## 9. Global technical risks

| Risk | Mitigation / fallback |
|---|---|
| | |

## Approval

- [ ] Stack and data model agreed
- [ ] Work split agreed
- [ ] **APPROVE ARCHITECTURE** received (via `APPROVE KICKOFF`)
