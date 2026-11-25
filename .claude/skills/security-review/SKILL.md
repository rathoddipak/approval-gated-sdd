---
name: project-security-review
description: Performs a lightweight security, privacy and responsible-AI review of a story or change before merge/release. Use after security-sensitive changes, AI/tool integrations, external APIs, file/data handling, and before a release or demo.
---

# Project Security Review

Perform the review against the approved story `spec.md`, `docs/ENTERPRISE_STANDARDS.md`, and the actual changed code. Keep it scoped to the real risk of this change; do not invent enterprise work the story does not need.

## Check

### Secrets and configuration
- Hardcoded keys/tokens/passwords?
- Secrets printed/logged/exposed to frontend?
- Unsafe `.env` handling?

### Inputs, data and APIs
- If file/archive upload is involved: file/type/size limits, path traversal (ZIP-slip), excessive extraction/ZIP-bomb risk, and safe cleanup considered?
- Is uploaded content treated as data rather than executed code? No package hooks/scripts/tests should run implicitly.
- Are secret-like files (`.env`, keys, credentials) excluded from AI prompts/logs?
- Important untrusted inputs validated?
- SQL/injection/file/path risks?
- Sensitive or personal data unnecessarily sent to AI/external APIs?
- Raw stack traces or sensitive error details exposed?

### AI / tool use
- Prompt injection/tool-abuse risk relevant to this feature?
- Can AI trigger consequential actions without intended human approval?
- Unsupported/uncertain output handled safely?
- Timeout/failure path visible?
- Hidden chain-of-thought accidentally requested/exposed?

### Configuration / dependency assumptions
- CORS/auth/demo shortcuts acknowledged rather than presented as production-ready?
- New dependency or external service introduces an obvious risk?

## Output

```text
## Security Review: <story/change>

✅ Passes
- ...

⚠️ Risks / follow-up
- ...

🔴 Blocking before merge/release
- ...

Decision needed from human
- ...

AI Evidence note
- Tool: Claude Code security review
- Story/artifact: ...
- Contribution: ...
- Human decision: ...
```

A clean review does not replace actual tests or the final solution-level `docs/SECURITY_REVIEW.md`.
