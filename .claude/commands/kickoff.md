---
description: Technical review of frozen requirements, reconcile the team architecture, map stories into the implementation plan (one gate)
---

# Technical kickoff

The product owner owns product scope. This command does **not** write or expand requirements.

1. Read `CLAUDE.md`, `specs/REQUIREMENTS.md`, `specs/ARCHITECTURE.md`, `specs/IMPLEMENTATION_PLAN.md`, and any other approved spec listed in `CLAUDE.md` §7.
2. If `REQUIREMENTS.md` is not marked **APPROVED / FROZEN**, or has no user stories → **stop** and ask for the frozen handoff.
3. **Technical review.** Produce a short table (≤ 15 rows). Each row covers one of: ambiguity, contradiction, untestable AC, dependency, external dependency (API key/service/model), blocker, a scope risk for the agreed time box, missing AI failure/human-control behaviour, input/data-format gaps, untrusted-input handling gaps, decisions not traceable to requirements. Also check whether any story conflicts with an `ARCHITECTURE.md` §0 decision.
4. Product-decision blockers → **stop** and ask the Tech Lead / product owner. Do not invent answers.
5. Update `ARCHITECTURE.md` **only where the frozen requirements force a change**. List each change in §0 as a new row.
6. Map every story into `IMPLEMENTATION_PLAN.md`: track (F0 foundation + feature tracks from `ARCHITECTURE.md` "Work split"), priority, dependencies, and parallel groups. Keep F0 first. Mark anything that doesn't fit the time box as P1/P2 with a one-line reason.
7. **STOP** and print:

```
KICKOFF REVIEW
Blockers needing a human: …
Architecture changes: …
Stories → tracks: …
Parallel start now: F0 + <independent tracks>; after F0: …
Reply: APPROVE KICKOFF  (= APPROVE ARCHITECTURE + APPROVE IMPLEMENTATION PLAN)  |  or corrections
```

8. On `APPROVE KICKOFF`: set both files to `APPROVED — <HH:MM>` and tick their approval boxes. Tell each owner which command to run: `/start-story F0` first, and `/start-story <ID>` for any independent track in parallel. Append a Planning row to `docs/AI_EVIDENCE_LOG.md`.

$ARGUMENTS
