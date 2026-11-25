---
description: Plan one story through two human approval gates (APPROVE TASK, APPROVE SPEC). Resumable from any session.
argument-hint: <STORY-ID> [pasted story text]
---

# Start / resume a story

Input: `$ARGUMENTS`: a story ID (e.g. `S04`, or an enabler like `F0`) and/or a pasted approved story.

A story is one story section of `REQUIREMENTS.md` (see its Story index). If that section contains sub-stories (e.g. `US010`), they are planned, specced, tested and built together in the one story folder. If given a sub-story ID, say which story it belongs to and continue with that story. In `task.md`, list the sub-stories with their priority, and plan Must before Should/Could.

## Step 0 — Preconditions (stop and report if any fail)

1. Read `CLAUDE.md` and `specs/REQUIREMENTS.md`. Unless the story is a technical enabler listed in `IMPLEMENTATION_PLAN.md` (e.g. `F0`), the story must exist there **and** the file must be marked `APPROVED / FROZEN`. Otherwise stop: "Requirements not frozen / story not found."
2. Read `specs/ARCHITECTURE.md` and `specs/IMPLEMENTATION_PLAN.md`. Both must be `APPROVED`. Otherwise stop and point to `/kickoff`.
3. Find the story's row in `IMPLEMENTATION_PLAN.md`: track, owner, **Depends on**, status.
4. **Dependency check.** For each dependency, read its row status.
   - Planning can always go ahead, since stories are planned against contracts.
   - Record every dependency that is not `Done`, and say whether it blocks *implementation* or only *integration*.
5. Read only the relevant sections of any other approved spec listed in `CLAUDE.md` §7, and the architecture sections for this story's track.

## Step 1 — Resume detection

Folder: `specs/stories/<STORY-ID>-<short-name>/` (create it from `specs/stories/_TEMPLATE/` if missing).
Read the `**Status:**` line of each file and continue from the first unapproved gate:

| State on disk | Next action |
|---|---|
| no folder / `task.md` not approved | Gate 1 |
| `task.md` + `approach.md` `APPROVED`, `spec.md` not approved | Gate 2 |
| `spec.md` + `test-plan.md` `APPROVED` | Say **READY FOR IMPLEMENTATION**, list remaining blockers, suggest `/implement-story <ID>`. Stop. |

If an approved file looks changed or contradicts upstream, stop and ask. Never re-approve for the human.

## Gate 1 — Understanding + approach  → `APPROVE TASK`

Write three files:
- `story.md`: the whole story section (every sub-story with its AC and traceability), **verbatim**. Add the requirement/AC IDs it traces to.
- `task.md`: understanding, decomposition, AC mapping, **Dependencies**, **Blockers**, **Open questions**, **Assumptions**, out of scope.
- `approach.md`: the simplest approach that fits `ARCHITECTURE.md`. Include files to touch (only this track's files, plus the append-only shared files in `CLAUDE.md` §8), API/data changes, AI approach, risks/fallback, security, and architecture impact.

Keep each file short. Bullet points, no filler, and leave template sections blank as `—` when they don't apply.

Then **STOP** and print this block:

```
GATE 1 — <STORY-ID>
Summary: <3 lines>
Dependencies: <list with status>   Blockers: <list or None>
Open questions needing a human: <list or None>
Reply: APPROVE TASK  |  or give corrections
```

On `APPROVE TASK` (exact phrase, in any session): set `task.md` and `approach.md` Status to `APPROVED — <HH:MM> by <name if given>`, tick their approval boxes, and set the plan row to `Task approved`.

## Gate 2 — Contract + tests  → `APPROVE SPEC`

Write:
- `spec.md`: the exact contract. Behaviour, request/response JSON, validation, errors, AI I/O schema (if any), edge cases, security, AC → behaviour traceability.
- `test-plan.md`: one row per AC, plus negative, edge, dependency-failure and (if AI is involved) AI cases (invalid AI output, timeout, prompt injection, missing input). Mark which tests are automated (external AI/services always mocked) and which are manual/demo checks.

Then **STOP** with the same block format, ending `Reply: APPROVE SPEC | or give corrections`.

On `APPROVE SPEC`: set `spec.md` and `test-plan.md` to `APPROVED — <HH:MM>`, set the plan row to `Spec approved`, print **READY FOR IMPLEMENTATION**, and suggest `/implement-story <ID>`.

## Rules

- Only the exact phrases `APPROVE TASK` / `APPROVE SPEC` count. Silence, "ok" or "looks good" do not.
- Corrections → update the files and show the gate block again.
- Never invent product scope. If a requirement is missing or ambiguous, list it under **Blockers / Open questions** and ask.
- Genuine blocker (missing product decision, missing contract from another track, missing credential) → do not work around it. Surface it in the gate block.
