# approval-gated-sdd

**Spec-driven development kit for Claude Code: frozen requirements → human approval gates → tested, reviewed code.**

A drop-in library of Claude Code commands, an agent, skills and spec templates that take a story from **frozen requirements → two human approval gates → implemented, tested and reviewed code**.

It is plain Markdown with **no runtime or package dependencies**, so it works with any language or stack. Your project's commands are configured in `CLAUDE.md` §8.

**Team rules and roles:** [docs/TEAM_GUIDELINES.md](docs/TEAM_GUIDELINES.md). Read this first.

```
/kickoff ──► APPROVE KICKOFF
/start-story S01 ──► Gate 1: APPROVE TASK ──► Gate 2: APPROVE SPEC
/implement-story S01 ──► tests ──► reviewer agent ──► fixes ──► /log-ai ──► plan = Done
/status   (board at any time)
```

Full walkthrough: [docs/STORY_WORKFLOW.md](docs/STORY_WORKFLOW.md).

## What's in the kit

```text
CLAUDE.md                          Project rules + the config the commands read (§7, §8)
.claude/
├── settings.json                  Safe defaults: deny reading .env, deny force-push
├── commands/
│   ├── kickoff.md                 /kickoff          — review frozen reqs, map stories to tracks
│   ├── start-story.md             /start-story <ID> — Gate 1 + Gate 2 (resumable)
│   ├── implement-story.md         /implement-story <ID> — build, test, review, log
│   ├── status.md                  /status           — read-only board
│   └── log-ai.md                  /log-ai           — append to the AI evidence log
├── agents/
│   └── reviewer.md                Fresh-context, read-only code + security reviewer
└── skills/
    ├── code-review/SKILL.md       Code-review checklist + report format
    └── security-review/SKILL.md   Security / responsible-AI checklist
specs/
├── REQUIREMENTS.md                Template: frozen product contract + stories
├── ARCHITECTURE.md                Template: stack, data model, API, work split (tracks)
├── IMPLEMENTATION_PLAN.md         Template: story rows with track, deps, status
└── stories/_TEMPLATE/             story / task / approach / spec / test-plan
docs/
├── TEAM_GUIDELINES.md             Roles, daily loop, do/don't, customising, updating
├── STORY_WORKFLOW.md              How the gates work, step by step
├── ENTERPRISE_STANDARDS.md        🔴 must / 🟡 roadmap engineering rules
├── AI_EVIDENCE_LOG.md             Empty log, filled by /log-ai
└── SECURITY_REVIEW.md             Final pre-release checklist
```

## Install into another project

1. Get the kit and copy it into the **root** of the target repo (the folder where you run `claude`):

   ```bash
   git clone https://github.com/rathoddipak/approval-gated-sdd.git
   cp -r approval-gated-sdd/{CLAUDE.md,.claude,specs,docs} /path/to/your-project/
   ```

   You can also use **Code → Download ZIP** on GitHub. If the project already has a `CLAUDE.md`, `.claude/` folder or `.gitignore`, merge by hand instead of overwriting. Merge this kit's `.gitignore` entries into yours.
2. Edit **`CLAUDE.md`**: replace every `<…>` placeholder. §7 lists your spec files and folders; §8 must hold your real test, build and migration commands, the shared AI/external client and your append-only shared files. The commands and the reviewer read §8.
3. Optional: add your stack's commands to `.claude/settings.json` → `permissions.allow` (e.g. `"Bash(<your test command>:*)"`) to cut permission prompts.
4. Review `docs/ENTERPRISE_STANDARDS.md` and drop or add 🔴 rules to fit the project.
5. Restart Claude Code in the repo and type `/`. You should see `kickoff`, `start-story`, `implement-story`, `status` and `log-ai`.

## Use it

1. **Requirements.** Paste the product owner's stories into `specs/REQUIREMENTS.md` using the `# User Stories N` / `US0xx` / `AC-US0xx-nn` layout, then set `**Status:** APPROVED / FROZEN — <date time>`. The commands refuse to run until it says that.
2. **Architecture + plan.** Fill in `specs/ARCHITECTURE.md` (§7 work split is the important part) and add one row per story to `specs/IMPLEMENTATION_PLAN.md`, with F0 (foundation) first.
3. **`/kickoff`** → Claude reviews for blockers and ambiguities, updates the plan, and stops. Reply `APPROVE KICKOFF`.
4. **`/start-story F0`** (then `S01`, …) → Claude writes `story.md`, `task.md`, `approach.md` and stops. Reply `APPROVE TASK`. Run it again (same or new session) → `spec.md`, `test-plan.md`. Reply `APPROVE SPEC`.
5. **`/implement-story F0`** → code + tests, `reviewer` agent, BLOCK fixes, evidence log entry, plan row set to `Done`. Nothing is committed unless you ask.
6. **`/status`** any time to see every story's gate state and what can start next.

Only the exact phrases `APPROVE KICKOFF`, `APPROVE TASK` and `APPROVE SPEC` count as approval. Approval is written into the files, so any teammate or session can resume a story.

## Team use

Commit the kit to the repo. Each developer takes stories from one track (`ARCHITECTURE.md` §7) and runs the commands in their own Claude Code session. Tracks own separate files, so parallel sessions rarely conflict.

## License

[MIT](LICENSE) © 2026 Rathod Dipak
