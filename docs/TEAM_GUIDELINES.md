# Team Guidelines — approval-gated-sdd

How a team adopts and uses the kit on any project, in any language or stack. The kit is plain Markdown that Claude Code reads. It has no runtime, no packages and nothing to install.

## 1. Roles

| Role | Owns | Says |
|---|---|---|
| Product owner | `specs/REQUIREMENTS.md` content and its freeze | "frozen" (sets the Status line) |
| Tech Lead | `CLAUDE.md` setup, `ARCHITECTURE.md`, `IMPLEMENTATION_PLAN.md` | `APPROVE KICKOFF` |
| Story owner (developer) | One story folder and the code for it | `APPROVE TASK`, `APPROVE SPEC` |
| Claude Code | Drafts, code, tests, reviews | Never approves anything |

One story = one owner = one track. Two people never build the same story at the same time.

## 2. Setting up a new project (Tech Lead, once)

1. Copy the kit into the repo root and commit it.
2. Fill in `CLAUDE.md` §7 (folders, extra specs) and §8 (test, build, run, migration commands, the shared AI/external client, append-only shared files). Write "none" where something doesn't apply. Don't leave it blank.
3. Tune `docs/ENTERPRISE_STANDARDS.md`: delete 🔴 rules that don't apply (e.g. database rules) and add the project's own.
4. Write `ARCHITECTURE.md` §7 (work split). It decides which files each story may touch, so make the folders/paths concrete.
5. Get the frozen requirements, run `/kickoff`, review its findings and reply `APPROVE KICKOFF`.

## 3. Daily loop (every developer)

1. `/status` to see what you can start.
2. `/start-story <ID>`. **Read** `task.md` and `approach.md` before approving. Check the dependencies, assumptions and files to touch. Fix anything wrong by replying with corrections, not by editing the files yourself.
3. `APPROVE TASK` → run `/start-story <ID>` again. Read `spec.md` and `test-plan.md`: is every AC covered by a test?
4. `APPROVE SPEC` → `/implement-story <ID>`.
5. Read the final report. Do the listed manual checks, review the diff yourself, then commit on your own branch (Claude doesn't commit unless asked).

You can stop and resume at any gate, in a new session or from another machine. The state lives in the files.

## 4. Rules

**Do**
- Approve only with the exact phrases: `APPROVE KICKOFF`, `APPROVE TASK`, `APPROVE SPEC`.
- Treat a blocker in a gate block as real: get the decision from the product owner or Tech Lead, record it under *Team decisions* in `REQUIREMENTS.md`, then continue.
- Commit the story folder together with its code, so reviewers see the spec next to the implementation.
- Run `/log-ai` for meaningful AI help that `/implement-story` didn't already log.

**Don't**
- Don't edit `REQUIREMENTS.md` to make a story easier. Scope changes go back to the product owner.
- Don't hand-edit an approved file. Ask Claude to raise a change: it lists the affected downstream files and re-runs the gate.
- Don't touch another track's files. If you need to, agree it with that owner first.
- Don't weaken or delete a failing test. Fix the code or raise a spec change.
- Don't add a library/dependency that isn't in the approved `approach.md`.
- Don't put secrets in specs, prompts or logs. `.env` files are blocked from Claude by `.claude/settings.json`.

## 5. Reviews

- `/implement-story` always runs the `reviewer` agent (fresh context, read-only). Every **BLOCK** must be fixed before the story is `Done`. **FLAG**s are for the story owner to decide.
- You can also ask "review this" at any time. The `code-review` and `security-review` skills load automatically.
- Before a release or demo, the Tech Lead completes `docs/SECURITY_REVIEW.md`.

## 6. Customising the kit

| Want to… | Change |
|---|---|
| Different test/build tools | `CLAUDE.md` §8 only. The commands read it. |
| Extra project rules | `CLAUDE.md` §9 and `docs/ENTERPRISE_STANDARDS.md` |
| Extra checks in reviews | `.claude/skills/code-review/SKILL.md` §3 "Domain rules" |
| Different story file sections | `specs/stories/_TEMPLATE/*` (keep the `**Status:**` line, since the commands depend on it) |
| Fewer permission prompts | Add your commands to `.claude/settings.json` → `permissions.allow` |
| Project-specific helper agents | New file in `.claude/agents/`, then list it in `CLAUDE.md` §6 |

Keep project-specific changes out of the shared kit copy. If an improvement helps every project, open an issue or pull request on the [approval-gated-sdd](https://github.com/rathoddipak/approval-gated-sdd) repo so all teams get it.

## 7. Updating the kit in an existing project

Replace `.claude/commands/`, `.claude/agents/`, `.claude/skills/`, `specs/stories/_TEMPLATE/` and `docs/STORY_WORKFLOW.md` with the new versions. **Don't** overwrite `CLAUDE.md`, `specs/*.md`, story folders or `docs/AI_EVIDENCE_LOG.md`. Those hold your project's data. Diff `CLAUDE.md` against the new kit version and merge any new sections by hand.
