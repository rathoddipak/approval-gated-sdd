# Final Security & Responsible-AI Review

**Owner:** Tech Lead + assigned reviewer  
**Status:** NOT STARTED

> Run near feature freeze and before the release/demo. Story-level security checks still happen during implementation/review.

## Secrets / configuration

- [ ] No real API keys/secrets committed
- [ ] No secrets visible in demo/logs/screenshots
- [ ] `.env.example` contains placeholders only

## Input / API / data

- [ ] Important user inputs validated
- [ ] No obvious SQL/injection/file-handling issue
- [ ] Sensitive/demo data handling is intentional
- [ ] External API/AI data sharing is understood

## AI-specific

- [ ] Prompt/tool abuse risks considered where relevant
- [ ] Unsupported/uncertain output has a safe behavior
- [ ] Human review/override exists where consequential
- [ ] AI failure/timeout has a visible fallback
- [ ] No hidden chain-of-thought exposed

## App/config

- [ ] CORS/auth assumptions are understood and not misrepresented as production-ready
- [ ] Error responses do not expose stack traces/secrets
- [ ] Demo-critical dependencies have a fallback where practical

## Blocking findings

| Finding | Fix / decision | Owner | Status |
|---|---|---|---|
| | | | |

## Final decision

- [ ] No blocking issue remains for the release/demo
