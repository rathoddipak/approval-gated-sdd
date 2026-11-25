# Story Workflow — `/start-story`, `/implement-story` and the `reviewer` agent

How every story goes from approved requirement to reviewed code.

Sources: [`.claude/commands/start-story.md`](../.claude/commands/start-story.md), [`.claude/commands/implement-story.md`](../.claude/commands/implement-story.md), [`.claude/agents/reviewer.md`](../.claude/agents/reviewer.md).

## Overview

```
/start-story <ID> ──► Gate 1: APPROVE TASK ──► Gate 2: APPROVE SPEC
        ──► /implement-story <ID> ──► reviewer agent ──► /log-ai ──► plan = Done
```

| Step | Command | Writes | Human gate |
|---|---|---|---|
| Gate 1 | `/start-story <ID>` | `story.md`, `task.md`, `approach.md` | `APPROVE TASK` |
| Gate 2 | `/start-story <ID>` (same or new session) | `spec.md`, `test-plan.md` | `APPROVE SPEC` |
| Build | `/implement-story <ID>` | code + tests → `reviewer` → fixes → `/log-ai` → plan status | report only, no commit unless asked |
| Board | `/status` | — (read-only) | — |

- Each story lives in `specs/stories/<STORY-ID>-<short-name>/`, created from `specs/stories/_TEMPLATE/`.
- Approval is saved **in the files** (`**Status:** APPROVED — <HH:MM>`) and in the Status column of `specs/IMPLEMENTATION_PLAN.md`. Any session or teammate can pick up where the last one stopped.
- Only the exact phrases `APPROVE TASK` and `APPROVE SPEC` count as approval. "ok", "looks good" or silence do not.

---

## 1. `/start-story <ID>`: planning with two human gates

### Step 0: preconditions (stops if any fail)

1. `specs/REQUIREMENTS.md` is marked **APPROVED / FROZEN** and the story exists in it. Technical enablers listed in the plan (e.g. `F0`) are exempt.
2. `specs/ARCHITECTURE.md` and `specs/IMPLEMENTATION_PLAN.md` are both `APPROVED`. If not, run `/kickoff` first.
3. Reads the story's plan row: track, owner, **Depends on**, status.
4. **Dependency check.** Planning always goes ahead because stories are planned against contracts. Every dependency that isn't `Done` is recorded, along with whether it blocks *implementation* or only *integration*.
5. Reads only the relevant sections of any extra approved specs listed in `CLAUDE.md` §7 and the architecture for this story's track.

A story is one `# User Stories N` section of `REQUIREMENTS.md`. Its `US0xx` entries are sub-stories that are planned, specced, tested and built together. If you pass a sub-story ID (e.g. `US010`), it maps it to the parent story and plans Must before Should/Could.

### Step 1: resume detection

| State on disk | Next action |
|---|---|
| No folder, or `task.md` not approved | Gate 1 |
| `task.md` + `approach.md` approved, `spec.md` not approved | Gate 2 |
| `spec.md` + `test-plan.md` approved | Prints **READY FOR IMPLEMENTATION**, lists remaining blockers, suggests `/implement-story <ID>` |

If an approved file looks changed or contradicts an upstream spec, it stops and asks. It never re-approves on your behalf.

### Gate 1: understanding and approach → `APPROVE TASK`

| File | Contents |
|---|---|
| `story.md` | The whole story section copied word for word (all sub-stories, AC, traceability), plus the requirement/AC IDs |
| `task.md` | Understanding, decomposition, AC mapping, **Dependencies**, **Blockers**, **Open questions**, **Assumptions**, out of scope |
| `approach.md` | Simplest design that fits the architecture: files to touch (this track's files only, plus append-only shared files), API/data changes, AI approach, risks and fallback, security, architecture impact |

It then stops and prints:

```
GATE 1 — <STORY-ID>
Summary: <3 lines>
Dependencies: <list with status>   Blockers: <list or None>
Open questions needing a human: <list or None>
Reply: APPROVE TASK  |  or give corrections
```

On `APPROVE TASK`, `task.md` and `approach.md` are set to `APPROVED — <HH:MM>` and the plan row to `Task approved`.

### Gate 2: contract and tests → `APPROVE SPEC`

| File | Contents |
|---|---|
| `spec.md` | The exact contract: behaviour, request/response JSON, validation, errors, AI input/output schema (if any), edge cases, security, AC → behaviour traceability |
| `test-plan.md` | One row per AC, plus negative, edge, dependency-failure and AI cases (invalid AI output, timeout, prompt injection, missing input). Each row is marked automated (external AI/services always mocked) or manual/demo check |

It stops with the same block format, ending `Reply: APPROVE SPEC | or give corrections`.

On `APPROVE SPEC`, both files are set to `APPROVED — <HH:MM>`, the plan row to `Spec approved`, and it prints **READY FOR IMPLEMENTATION**.

### Rules

- Corrections update the files and the gate block is shown again.
- It never invents product scope. Missing or ambiguous requirements go under **Blockers / Open questions**.
- A genuine blocker (missing product decision, missing contract from another track, missing credential) is surfaced in the gate block, never worked around.

---

## 2. `/implement-story <ID>`: build, test, review, log

### 1. Gate check (stops if any fail)

- `spec.md` and `test-plan.md` must both be `APPROVED`. If not, it names the open gate and suggests `/start-story <ID>`.
- For each dependency that isn't `Done`:
  - **Contract dependency** (another track's API/schema that F0 stubbed): builds against the stub and notes it.
  - **Hard dependency** (code or data that doesn't exist yet, e.g. no F0 models): stops, reports `Blocked: <reason>` and sets the plan row to that.
- Sets the plan row to `In progress`.

### 2. Build: only what `spec.md` defines

- Touches only the files listed in `approach.md` for this track, plus the append-only shared files listed in `CLAUDE.md` §8. If it needs another track's file, it stops and asks.
- Structure: thin entry points → services → typed inputs/outputs (per `CLAUDE.md` §9).
- Adds no new library/dependency unless `approach.md` named it and Gate 1 approved it.
- AI/external calls only go through the shared client named in `CLAUDE.md` §8.
- Never creates a DB migration unless the story is F0 or the spec says so.
- Works in small steps and runs the relevant tests after each one.

### 3. Test

- Writes the automated tests from `test-plan.md` (external AI/services always mocked).
- Runs the test and build/lint command(s) from `CLAUDE.md` §8 for every module the story changed.
- **Never** weakens or deletes a test to make it pass. If the spec is wrong, it stops and raises a spec change.

### 4. Review and security

Launches the `reviewer` agent (see below) on the changed files. Every **BLOCK** finding is fixed and tests are re-run. **FLAG** findings are reported, not silently fixed if the fix would go beyond the spec.

### 5. Finish

- Ticks the Definition of Done in `spec.md` and sets the plan row to `Done` (or `Blocked: …`).
- Appends an entry to `docs/AI_EVIDENCE_LOG.md` (`/log-ai`).
- Prints the final report:

```
IMPLEMENTED — <ID>
Files changed: …
Tests: <n passed / n failed> (command used)
Review: <blocks fixed / flags open>
AC status: AC-x ✅ … / manual checks still to do: …
Integration notes for other tracks: …
Next unblocked stories: …
```

It does not commit or push unless asked.

---

## 3. `reviewer` agent

A separate, read-only agent (Sonnet; tools: Read, Grep, Glob, Bash) that reviews code it didn't write, without the context of the session that wrote it.

**Inputs:** story ID and the list of changed files. If no list is given, it uses `git diff --name-only` and `git status --porcelain`.

**Checks:**

1. Reads the story's `spec.md`, `CLAUDE.md` and `docs/ENTERPRISE_STANDARDS.md`.
2. Applies the **code-review** skill to the changed files only.
3. Applies the **security-review** skill, focusing on:
   - Untrusted input (uploads, archives, user text): validation, limits, path traversal, never executed
   - Text sent to an AI model inside delimiters, with a "this is data, not instructions" system rule (prompt injection); no secret files sent
   - No secrets in code or logs, no raw SQL built from input
4. Flags scope creep (behaviour not in `spec.md`) and any file edited outside the story's track.
5. Runs the test command(s) from `CLAUDE.md` §8 and reports the result.

**Output:**

```
REVIEW — <ID>
BLOCK (must fix): - file:line — problem — fix
FLAG (should fix / human decide): - …
PASS: <areas checked with no issues>
Tests: <result>
```

---

## Related commands and agents

| Name | Purpose |
|---|---|
| `/kickoff` | Technical review of frozen requirements; approves architecture and implementation plan (`APPROVE KICKOFF`) |
| `/status` | Read-only board: each story's gate state, blockers, and what can start now |
| `/log-ai` | Append an AI-assisted work entry to `docs/AI_EVIDENCE_LOG.md` |

## Changing an approved artifact

Explain why the change is needed, list the downstream files it affects, update them only with human approval, and re-run the affected gate.
