---
description: Implement an approved story (spec + test plan APPROVED), then test, review, secure, log and update the plan
argument-hint: <STORY-ID>
---

# Implement story

Input: `$ARGUMENTS` (story ID).

## 1. Check gates (stop if any fail)

- `specs/stories/<ID>-*/spec.md` and `test-plan.md` both have `Status: APPROVED`. If not → stop, say which gate is open, suggest `/start-story <ID>`.
- Read the plan row in `specs/IMPLEMENTATION_PLAN.md`. For every dependency that isn't `Done`:
  - **contract dependency** (another track's API/schema that F0 stubbed) → go ahead against the stub and note it;
  - **hard dependency** (code/data this story actually needs that doesn't exist yet, e.g. no F0 models) → **stop**, report `Blocked: <reason>`, set the plan row to that, and wait for the human.
- Set the plan row to `In progress`.

## 2. Build — only what `spec.md` defines

- Read `approach.md` "Files likely to change". Touch only this track's files plus the append-only shared files listed in `CLAUDE.md` §8. Needing another track's file → stop and ask.
- Follow `CLAUDE.md` §9 and `docs/ENTERPRISE_STANDARDS.md` (thin entry points → services → typed inputs/outputs). AI/external calls only through the shared client named in `CLAUDE.md` §8.
- Never create a DB migration unless this story is F0 or the spec says so.
- Never add a new library/dependency unless `approach.md` names it and it was approved at Gate 1.
- Work in small steps. After each meaningful step, run the relevant tests.

## 3. Test

- Write the automated tests listed in `test-plan.md` (external AI/services always mocked). Run the test command(s) and build/lint command(s) from `CLAUDE.md` §8 for every module this story changed.
- Fix failures. **Never** weaken or delete a test to make it pass. If the spec is wrong, stop and raise a spec change.

## 4. Review + secure (parallel, fresh context)

Launch the `reviewer` agent on the changed files, asking for both a code review (`code-review` skill) and a security review (`security-review` skill). Fix every **BLOCK** finding and re-run tests. Report FLAG findings, don't silently fix them if the fix would go beyond the spec.

## 5. Finish

- Tick the Definition of Done in `spec.md`. Set the plan row to `Done` (or `Blocked: …`).
- Append an entry to `docs/AI_EVIDENCE_LOG.md` (follow `/log-ai`).
- Print a final report:

```
IMPLEMENTED — <ID>
Files changed: …
Tests: <n passed / n failed> (command used)
Review: <blocks fixed / flags open>
AC status: AC-x ✅ … / manual checks still to do: …
Integration notes for other tracks: …
Next unblocked stories: …
```

Do not commit or push unless the human asks.
