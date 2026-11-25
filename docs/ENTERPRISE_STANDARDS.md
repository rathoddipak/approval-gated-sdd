# Engineering Standards

**Purpose:** Lightweight rules that protect delivery quality without turning an MVP into a production program. Adjust the 🔴 items to your project.

- 🔴 **Must**: blocks calling a story/demo done.
- 🟡 **Roadmap**: capture for later; do not over-engineer the MVP.

## 1. Spec and scope discipline

- 🔴 Product scope comes from approved `specs/REQUIREMENTS.md`.
- 🔴 Story implementation requires approved `task.md`, `approach.md`, `spec.md` and `test-plan.md`.
- 🔴 Code must match the approved story `spec.md`; do not quietly implement extra behavior.
- 🔴 If an upstream approved artifact changes, assess downstream impact and re-approve affected artifacts.
- 🟡 Nice-to-have features stay out until P0 stories are stable.

## 2. Secrets, data and security

- 🔴 No secrets in code, prompts, logs or screenshots. Use environment variables/config.
- 🔴 Never read, print or commit `.env` files.
- 🔴 No real sensitive/personal data in demo prompts unless explicitly allowed and safely handled; prefer synthetic/anonymized data.
- 🔴 Validate untrusted input at API boundaries.
- 🔴 Treat uploaded files/archives as untrusted: validate limits/paths, prevent ZIP-slip, and never execute uploaded content by default.
- 🔴 Never send `.env`, credentials, private keys or obvious secret files to AI/external services.
- 🔴 Preserve source/requirement IDs where AI output must be auditable back to its inputs.
- 🔴 Never build raw SQL from user input using string formatting; use the ORM/query parameters.
- 🔴 AI/tool actions with meaningful side effects require an intentional human-control path unless the approved requirement explicitly says otherwise.
- 🟡 Production auth hardening, advanced rate limiting and enterprise monitoring unless required by the requirements.

## 3. Structure

- 🔴 Entry points (routes/controllers/handlers/pages) stay thin; business logic belongs in services/modules.
- 🔴 No new library/dependency unless the story's approved `approach.md` names it.
- 🔴 DB access follows the shared session/dependency pattern.
- 🔴 AI calls use the shared AI client/service abstraction; do not create ad-hoc clients across features.
- 🔴 Request/response bodies use typed models rather than unstructured dicts when practical.
- 🔴 Keep feature ownership/file boundaries clear to reduce merge conflicts.
- 🔴 Interactive AI calls may run synchronously if they have timeout, loading state and visible failure handling.
- 🔴 Use the job queue for genuinely long-running/non-interactive work or work that must survive request/process failure—not simply because a call takes a few seconds.

## 4. Errors and resilience

- 🔴 No raw stack traces shown to users.
- 🔴 Every external call has a defined timeout/failure path and user-visible state.
- 🔴 Demo-critical dependencies need a fallback plan where practical.
- 🟡 Production-grade retries/circuit breakers/tracing unless truly needed.

## 5. Database

Applies only if the project has a database.

- 🔴 Model/schema changes require a migration that a human reads.
- 🔴 Only the foundation story (or a story whose spec says so) creates migrations, to avoid parallel migration heads.
- 🟡 Production indexing/tuning unless the demo workload needs it.

## 6. Tests

- 🔴 Tests map to the approved story `test-plan.md` and acceptance criteria.
- 🔴 New endpoints/important behavior get at least a happy-path automated test where feasible.
- 🔴 DB tests use isolated test data/fixtures.
- 🔴 Automated tests do not call the real AI API unless the test is explicitly an integration/evaluation run.
- 🔴 Run relevant tests before merge/demo.
- 🟡 Broad coverage targets and full E2E automation.

## 7. AI-generated / AI-assisted code

- 🔴 The human merging the code owns it and should understand the non-trivial behavior.
- 🔴 Check likely hallucinated library methods/parameters against the installed version or actual runtime.
- 🔴 Flag plausible-but-wrong behavior, unnecessary abstractions and scope creep.
- 🔴 Use the code-review skill after non-trivial changes.
- 🔴 Log meaningful AI contribution in `docs/AI_EVIDENCE_LOG.md`.

## 8. AI behavior in the product

- 🔴 AI must serve an approved product need, not exist only for novelty.
- 🔴 Where relevant, give users useful rationale/evidence/source/confidence/uncertainty without exposing hidden chain-of-thought.
- 🔴 Provide human review/override for consequential AI output when appropriate.
- 🔴 Define unsupported/uncertain/failure behavior in the story spec.
- 🔴 Evaluate changed/new/ambiguous inputs, not only one perfect demo input.
- 🔴 Measure real outcomes where possible; do not present invented improvements as evidence.

## 9. Git and integration

- Keep `main` deployable/demo-able.
- Use small `feat/<story-or-slice>` branches and pull/rebase frequently.
- Keep PRs/merges small enough to review quickly.
- Integrate the first vertical slice early.
