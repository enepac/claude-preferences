# current-state.md maintenance guide

If you're already inside Claude Code with the repo open, drop the fetch instruction and just say "read current-state.md and update it based on this session."

---

## The six-step walkthrough

**1. Confirm the trigger.**
Something changed worth capturing: a blocker resolved, a decision made, a direction shift. If nothing changed, skip this cycle, don't force an update.

**2. Read the current file before writing anything.**
Pull the live version first, either through Claude Code reading the repo directly, or via a raw GitHub fetch. This matters because you may have updated it another way since the last session, skipping this step risks overwriting something.

**3. Gather what's actually new.**
Pull from this session: what decision got made, what's still blocking you, what the next concrete move is. Don't invent content for sections that didn't change, leave them as they are.

**4. Rewrite, don't append.**
current-state.md is a snapshot, not a log. Old entries that age out get dropped or moved to miss-log.md, they don't pile up here. If it starts growing indefinitely, it stops being scannable and the whole point is lost.

**5. Commit through Claude Code.**
Never a manual hand-edit. Either Claude Code edits the file directly if you're in that surface, or, if you're in a plain chat, you get a paste-ready handoff (exact diff, commit message) to run in Claude Code. This keeps the file's history reviewable.

**6. Push.**
The file deploys itself the moment it's pushed, any chat that fetches the raw URL sees the update immediately. No Settings paste required for this file, that's only needed for user-preferences.md.

---

## The current-state.md template

```markdown
# Current State

## ACTIVE BLOCKER
[The one thing you're stuck on right now. Just one. If there
are multiple, pick the highest-leverage one and note the rest
briefly under NEXT MOVE.]

## RECENT DECISIONS
- [YYYY-MM-DD] [Decision made, one line]
- [YYYY-MM-DD] [Decision made, one line]
(keep to the last 5, drop the oldest when a new one is added)

## NEXT MOVE
[The single next concrete action. Actor, and what "done" looks
like for it.]

## LAST UPDATED
[YYYY-MM-DD, which chat/session triggered this update]
```

---

## Quick checklist (print this part mentally, every time)

- [ ] Something actually changed since the last update
- [ ] Fetched the current version before editing
- [ ] Rewrote the relevant section(s), didn't just append
- [ ] Old entries aged out if RECENT DECISIONS exceeds 5
- [ ] Committed via Claude Code, not a manual edit
- [ ] Pushed to origin/main
- [ ] LAST UPDATED line reflects today's date

---

## What this doesn't do

No zero-touch automation. This process runs when you run it, from any chat, but always because you asked. If you want a weekly automated pass instead of running this by hand, that's a separate Cowork scheduled-task setup, not covered here.

No cross-project search merge. This file is the workaround for that limitation, it's the one place that's genuinely reachable from anywhere, precisely because everything else (memory, conversation search) is scoped per project.
