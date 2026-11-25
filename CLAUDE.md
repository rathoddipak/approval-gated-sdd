# <Project name> — Claude Code Instructions

**Purpose:** Shared rules for AI-native, spec-driven engineering on this project.

> **Setup:** replace every `<…>` placeholder in this file (especially §7 and §8) when you copy the kit into a new project. The commands in `.claude/commands/` read §7 and §8 to find your spec files, test commands and shared files.

## 1. Product requirements ownership

The **Product / Functional owner** prepares the final requirement, module details, user stories, acceptance criteria and any provided subtasks. The engineering team reviews and approves that handoff.

- `specs/REQUIREMENTS.md` = approved product contract.
- Optional domain specs (listed in §7) = approved input/output contracts the stories must honour.
- Claude Code may identify ambiguity, contradictions, blockers, dependencies, untestable acceptance criteria or technical risk.
- Claude Code must **not silently rewrite, expand or invent product scope**.
- If requirements are not marked **APPROVED / FROZEN**, stop before technical design or implementation.

When AI is part of the product, prefer user-facing rationale, sources/evidence, confidence, assumptions or uncertainty where useful. Do not expose hidden chain-of-thought.

## 2. Global technical kickoff

After approved requirements arrive, run `/kickoff`. It:

1. Reads all `specs/*.md` and stops if `REQUIREMENTS.md` isn't **APPROVED / FROZEN**.
2. Reports blockers, dependencies, assumptions and scope/time risks (technical review only).
3. Changes `specs/ARCHITECTURE.md` only where the frozen requirements force it (logged in its §0), and maps stories onto tracks in `specs/IMPLEMENTATION_PLAN.md`.
4. **STOPs once for `APPROVE KICKOFF`** (= APPROVE ARCHITECTURE + APPROVE IMPLEMENTATION PLAN).

Prefer the smallest thing that satisfies the approved AC. Anything not P0 is cut first.

## 3. Story workflow — two gates, then build

Every story has its own folder `specs/stories/<STORY-ID>-<short-name>/` with `story.md`, `task.md`, `approach.md`, `spec.md`, `test-plan.md` (from `_TEMPLATE`).

| Step | Command | Writes | Human gate |
|---|---|---|---|
| Gate 1 | `/start-story <ID>` | `story.md` (verbatim source), `task.md`, `approach.md` | **`APPROVE TASK`** |
| Gate 2 | `/start-story <ID>` (same or new session) | `spec.md`, `test-plan.md` | **`APPROVE SPEC`** |
| Build | `/implement-story <ID>` | code + tests → `reviewer` agent → fixes → `/log-ai` → plan status | report; no commit unless asked |
| Board | `/status` | — (read-only) | — |

- Approval state is saved **in the files** (`**Status:** APPROVED — <HH:MM>`) and in the plan's Status column, so any session or teammate can resume.
- Only the exact phrases count. Never infer approval from silence, "ok" or "looks good".
- If an approved artifact must change: explain why, list the affected downstream files, update them only with human approval, and re-run the affected gate.
- `/start-story` always reads the story's row in `IMPLEMENTATION_PLAN.md` and reports dependency status. `/implement-story` stops on a **hard** dependency that is missing (e.g. the foundation models don't exist yet). It builds against stubs for **contract** dependencies.

## 4. Blockers and dependencies

`task.md` and `approach.md` must explicitly contain:

- Dependencies on other stories, APIs, data, credentials, services or decisions.
- Blockers that prevent correct implementation.
- Open questions.
- Assumptions.
- Risks and fallback options where relevant.

If there is a genuine blocker, **stop and surface it**. Do not invent missing requirements or silently work around a product decision.

## 5. Implementation workflow after approvals

`/implement-story <ID>` runs this for an approved story:

1. **Build** only what the approved `spec.md` defines, touching only the story's track files (`ARCHITECTURE.md` "Work split") plus the append-only shared files listed in §8.
2. **Test** against `test-plan.md` (external AI/services mocked). Never weaken a test to make it pass.
3. **Review + secure** with the `reviewer` agent (`code-review` + `security-review` skills) and fix every BLOCK finding.
4. **Integrate** early with the end-to-end flow. Only the foundation story creates DB migrations (if the project has a database) unless the spec says otherwise.
5. **Evidence**: `/log-ai`, then update the plan status.

A story is done when its acceptance criteria pass and blocking review/security findings are resolved, not just when code exists.

## 6. Agents

| Agent | Use for |
|---|---|
| `reviewer` | Fresh-context code + security review of a story's changed files (read-only) |

## 7. Repository structure

- `specs/REQUIREMENTS.md` — approved requirement + user stories.
- `specs/ARCHITECTURE.md` — global technical architecture (stack, data model, API, work split by track).
- `specs/IMPLEMENTATION_PLAN.md` — story ownership, dependencies, status.
- `specs/stories/` — gated story-level specs.
- `<specs/OTHER_DOMAIN_SPEC.md — optional; list any extra approved contracts here>`
- `docs/ENTERPRISE_STANDARDS.md` — mandatory engineering rules.
- `docs/AI_EVIDENCE_LOG.md` — AI-assisted working evidence.
- `docs/SECURITY_REVIEW.md` — final solution-level security/responsible-AI check.
- `<source folder(s) and what each contains>`

## 8. Project commands and conventions

The workflow commands read this section — keep it accurate.

| Key | Value |
|---|---|
| Test command(s) | `<command that runs the automated tests, one per module if needed>` |
| Build / lint command(s) | `<command that must pass before a story is done, or "none">` |
| Run the app | `<command that starts the app locally>` |
| DB migration command | `<command, or "none">` (foundation story only) |
| Shared AI/external client | `<module every AI/external call must go through, or "none">` |
| Append-only shared files | `<files every story may append to, e.g. route registry, DI/module registration, nav config>` |

Claude Code: `/kickoff` · `/start-story <ID>` · `/implement-story <ID>` · `/status` · `/log-ai`

## 9. Engineering conventions

- Keep the solution simple, reliable and demo-ready. Do not over-engineer.
- Approved requirements and story specs are the scope boundary.
- New feature: keep entry points (controllers, handlers, UI pages) thin, put business logic in services/modules, use typed inputs/outputs and add relevant tests.
- AI/external calls go through the shared client abstraction (§8); mock them in tests.
- Interactive AI calls may stay synchronous when appropriate, with timeout, loading state and visible failure handling.
- Use background jobs only for genuinely long-running/non-interactive work or work that must survive request/process failure.
- Secrets come only from environment variables and must never be printed or committed.
- Do not add infrastructure/dependencies unless the approved story needs them.
- `main` should remain deployable/demo-able. Integrate early.
- After non-trivial code, run relevant tests plus code/security review.
- After meaningful AI-assisted work, use `/log-ai`.
