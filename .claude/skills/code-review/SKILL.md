---
name: project-code-review
description: Reviews code in this project against the project's own standards (docs/ENTERPRISE_STANDARDS.md, CLAUDE.md) and the approved story spec, and produces a structured pass/flag/block report with concrete fixes. Use this skill whenever the user asks to review code, check code quality, do a code review, review a PR or diff, review changes before merging, review AI-generated code, or asks "is this ready" / "is this good" / "did I do this right" about any file in the project. Also trigger proactively, without being asked, immediately after writing or editing any non-trivial backend or frontend code for this project (a new endpoint, a new model, a background task, a UI component) — self-review the code against this skill before presenting it as finished, and report the review inline along with the code.
---

# Project Code Review

Performs the review, not just describes it. When this skill triggers,
actually read the code in question and produce the structured report in
"Output format" below — don't just restate this checklist back to the
user.

If `docs/ENTERPRISE_STANDARDS.md` and/or `CLAUDE.md` exist in the project,
read them first and check against their 🔴 items specifically — they are
the authoritative source for this project's conventions. This skill's
checklist below is a fallback/summary for when those files aren't present
or as a fast first pass.

## What to check

Go through these in order. Stop expanding scope once you've covered what's
actually in the diff/file under review — don't review unrelated code that
wasn't touched.

### 1. Secrets and security
- No hardcoded API keys, passwords, or tokens — check string literals,
  not just obvious variable names. Anything shaped like `sk-...`, a DB
  URL with a real (non-placeholder) password, or a bare token string.
- User-supplied input reaches the database only via the ORM/query builder
  or bound parameters — never string formatting building raw SQL.
- User-controlled content sent to AI/external services is intentionally scoped;
  do not leak secrets or real personal/sensitive data. Check obvious prompt
  injection / tool-abuse risks if the feature lets AI trigger actions.
- If this is CORS/auth-related: is a wildcard origin or missing auth
  being shipped as a placeholder — if so, is that acknowledged (not
  silently presented as production-ready)?

### 2. Structure and conventions
- Routers/controllers stay thin: HTTP concerns only (parse input, call a
  service function, shape the response). Business logic in services.
- Database access only through the project's shared session/dependency
  pattern (no ad-hoc engine/session).
- AI/external calls only through the shared client named in `CLAUDE.md`
  §8 (no second client instantiated inline).
- Do not force every multi-second AI call through a queue. Interactive AI
  calls may stay synchronous if they have a timeout, loading state and visible
  failure path. Use a queue for genuinely long-running/non-interactive work
  or work whose result must survive a request/process failure.
- Typed request/response models — not raw dicts.
- One feature per router/component file — not unrelated endpoints bolted
  onto an existing router.

### 3. Domain rules
- Check any domain-specific 🔴 rules from `CLAUDE.md` and the approved
  specs it lists in §7 (e.g. input contracts, traceability IDs,
  human-review paths). Missing required input is surfaced, never invented.

### 4. Error handling
- Errors return the project's consistent error shape, not a raw stack
  trace or an ad-hoc one-off format.
- External calls (AI, third-party APIs) have their failure mode
  considered — what does the user see if this call fails or times out?

### 5. Database changes specifically
- If a model changed: does a matching migration exist, and does it
  actually reflect the change (not just an accepted autogenerate guess —
  read the generated file)?
- New/changed queries: any column being filtered or joined on that should
  be indexed?

### 6. Tests
- Does a test exist for this code? Happy path at minimum.
- If it touches the database: does the test use the project's isolated
  test-DB fixture pattern (not hitting dev data, not skipped)?
- If it calls an AI model or external API: is the call mocked in the test
  (never hitting the real API)?

### 7. AI-generated code — specific failure modes to check for
Apply this section whenever the code under review was AI-generated or
AI-assisted:
- **Hallucinated APIs**: does it call a method/parameter that doesn't
  actually exist on that library's installed version? If uncertain, say
  so explicitly rather than assuming it's correct because it looks
  idiomatic.
- **Plausible-but-wrong**: does it compile/run but handle an edge case
  differently than what was actually asked for?
- **Over-engineering**: abstraction, config options, or error handling
  for cases that can't occur in this app — flag for simplification.
- **Explainability**: could the person who generated this explain every
  non-trivial line if a reviewer asked? If a line's purpose isn't obvious
  from reading it, flag it for a comment or a rewrite, not just a pass.

### 8. Story spec conformance
- If reviewing a story implementation, read its approved `story.md`, `task.md`, `approach.md`, `spec.md` and `test-plan.md`.
- Does the code implement the approved `spec.md` rather than a different interpretation?
- Are acceptance criteria covered by tests or a clear manual check?
- Flag behavior added beyond the approved story scope.
- If implementation forced a spec change, require the affected artifact to be updated and re-approved rather than silently accepting drift.

### 9. Scope discipline
- Is this solving the actual requirement in the frozen
  `specs/REQUIREMENTS.md`, or has scope quietly grown beyond it?
- Flag anything that looks like premature production-hardening that's
  eating time better spent elsewhere (note it for the roadmap instead of
  building it now) — and, separately, flag anything that's a real
  🔴-tier gap (Section 1 items especially) that shouldn't wait.

## Output format

Report using this structure — don't bury findings in prose:

```
## Code Review: <file or feature name>

✅ Passes
- <thing that's genuinely fine, briefly>

⚠️ Flagged (fix if time allows)
- <issue> — <concrete fix, not just "improve this">

🔴 Blocking (fix before calling this done)
- <issue> — <concrete fix>

AI Evidence note: <one line suitable for pasting into the Evidence Log —
Tool: Claude Code review pass. Task: reviewed <what>. Contribution:
<what it caught>. Human Decision: <what the person should decide/confirm>.>
```

Keep ✅ items brief — the value is in what's flagged, not a long list of
things that are fine. If everything passes, say so plainly and keep the
report short rather than padding it.

## What this skill is not for

- Not a substitute for running the actual test suite — recommend running
  the tests in addition to this review, don't treat a clean review as
  proof the code works.
- Not for reviewing unrelated/pre-existing code the user didn't touch —
  stay scoped to the diff or file in question unless asked to review more
  broadly.
