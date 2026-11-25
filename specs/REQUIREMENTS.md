# Product Requirements & User Stories — <Product name>

**Owner:** <Product / Functional owner>
**Technical reviewer:** <Tech Lead>
**Status:** DRAFT  <!-- change to: APPROVED / FROZEN — <YYYY-MM-DD HH:MM>, confirmed by <name> -->
**Source files:** <FRD / PRD / feature list this was derived from>

> This file is the product contract. Everything below the line is the product owner's text, copied unchanged. Claude may flag ambiguity, conflicts or technical risk but must not rewrite, expand or invent scope. Any change needs product-owner approval and a new freeze time above.
>
> `/kickoff` and `/start-story` refuse to run until **Status** says `APPROVED / FROZEN`.

## How the team uses this file

- Each `# User Stories N` section below is **one story** (`S01`, `S02`, …): one owner, one story folder, one pass through the gates. Start it with `/start-story S01`.
- `US0xx` entries inside a section are **sub-stories**, built together with their parent story. Cite sub-story and AC IDs (`US001`, `AC-US001-01`) in `task.md`, `spec.md` and tests.
- Build order inside a story: `Must` sub-stories first, then `Should`, then `Could`.

### Story index

| Story | Title | Sub-stories (priority) |
|---|---|---|
| S01 | <title> | US001 (M), US002 (S) |

## Team decisions

| ID | Decision | Decided by / when |
|---|---|---|
| | | |

---

# User Stories 1 — <Title>

## US001 — <Title>

**Priority:** Must

**As a** <role>, **I want** <capability>, **so that** <outcome>.

### Acceptance criteria

- **AC-US001-01:** Given <context>, when <action>, then <result>.

### Traceability

- Requirement IDs: <FR-xxx, BR-xxx>
