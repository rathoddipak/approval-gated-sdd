---
description: Show the build board, meaning every story's gate state, blockers, and what can start now
---

Read `specs/IMPLEMENTATION_PLAN.md` and the `**Status:**` line of every file in `specs/stories/*/` (skip `_TEMPLATE`). Do not modify anything.

Print:

```
BOARD  (<HH:MM>)
| Story | Track | Owner | Gate 1 | Gate 2 | Build | Blocked by |
…
Can start now: …
Waiting on a human approval: …
Waiting on another story: …
Next integration checkpoint: … (from the plan)
```

If the plan status and the files on disk disagree, show both and flag the mismatch. Don't fix it.

$ARGUMENTS
