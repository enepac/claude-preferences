# Development Workflow System - Reference Guide

Reference only. This document does not run live in any project. You upload it
into a plain global chat (outside any project workspace) and fire the project
launcher prompt in Part One. From there the guide walks you, one step at a time,
through building and standing up a new project on the Development OS.

Read order for Claude: read this whole guide before responding to the launcher.
It tells you what to produce, where each piece goes, and the rules that hold
throughout.

---

## 0. How to use this guide

1. Open a plain global chat, outside any project. Upload this guide.
2. Paste the Project Launcher prompt (Part One). It interviews you about the new
   project, then builds all the scaffolding: project instructions, the two
   system files (dev-os.md, build-state.md), CLAUDE.md, a filled starter prompt,
   and the setup walkthrough.
3. Follow Part Two to stand up the workspace by hand (the claude.ai project, the
   GitHub repo, WSL/VS Code, Claude Code, the connectors), placing each artifact
   the launcher produced.
4. Move all further work into the new project workspace. Leave the global
   launcher chat behind; it was only the workshop.

One honest limit, stated up front: a prompt in a chat cannot create a claude.ai
project, make a GitHub repo, or upload files for you. It has no hands for that.
The launcher produces the content and tells you exactly where each piece goes;
you place everything yourself (with /lockstep on, one move at a time).

---

## 1. The big picture (the lifecycle on one screen)

Every project runs the same spine. A project is never built "on a whim."

```
LAUNCH (global chat)
  Project Launcher prompt -> scaffolding artifacts
        |
STAND UP THE WORKSPACE (manual, /lockstep)
  claude.ai project + GitHub repo + WSL/VS Code + Claude Code + connectors
  place: instructions, dev-os.md, build-state.md, CLAUDE.md
        |
RUN THE LIFECYCLE (inside the project)
  /sdlc sequences, with a human gate between each phase:
    Phase 0 Discovery   -> confirmed problem, users, smallest shippable version
    Phase 1 /blueprint  -> observable "done", component inventory, critical path
    Phase 2 /stack      -> tech + methodology per layer, security posture
    Phase 3 /ecosystem  -> Anthropic tooling setup (surfaces, models, skills)
    Phase 4 BUILD LOOP  -> per component: /preview -> /forge -> /loop -> /verify
    Phase 5 Capture     -> /baseline (reusable doc) + /handoff (continuity)
        |
CLOSE THE LOOP
  A component is DONE only when it passes its acceptance check against the
  locked spec. On every close, build-state.md is updated. The build loop ends
  only when every component in the inventory is done.
```

Two files are the system, read first in every chat: `dev-os.md` (the route) and
`build-state.md` (where you are on the route). If a chat and build-state.md
disagree, build-state.md wins.

---

## 2. The four artifact roles

Each piece of the scaffolding has one job and one home. Do not mix them.

1. Project Instructions (claude.ai project > Instructions, a Settings paste).
   The behavior spine: the Development OS front door plus this project's role,
   goal, stack, model routing, skills, connectors, caveats.
2. Project Knowledge files (the repo's /docs, synced via the GitHub connector).
   `dev-os.md` (the operating map, identical across projects) and
   `build-state.md` (this project's live state-board). Also where /baseline and
   the build journal live.
3. CLAUDE.md (the repo root). Guidance Claude Code reads before editing: stack,
   model convention, deploy method, security, commit discipline.
4. The live state-board, build-state.md, is both a knowledge file and the
   tracker. It is the one file every session reads first and updates on every
   close.

---

## 3. The two surfaces (and why)

- Planner / reviewer chat: plans, architects, and emits Claude Code handoffs. It
  never edits the repo. It sees the repo as a snapshot, so you start a fresh
  planner chat after commits to review against the latest code.
- Claude Code (VS Code / WSL): the canonical editor. It reads live files, edits,
  commits, pushes.

Every repo change, including updates to build-state.md and dev-os.md, is one
Claude Code handoff, never a manual hand-edit. The only paste-by-hand exception
is project instructions, because they live in claude.ai Settings, not the repo.

---

## PART ONE - The Project Launcher prompt

Run this in a plain global chat, outside any project, with this guide uploaded.
It interviews you, then builds every scaffolding artifact for the new project.
Turn on /lockstep first if you want strict one-step pacing.

```
PROJECT LAUNCHER - build the scaffolding for a new project.

You are running in a plain global chat, outside any project workspace, with the
Development Workflow System reference guide uploaded. Read that guide first. Your
job is to BUILD the starting scaffolding a new claude.ai project and its repo
need to run on the Development OS. You produce artifacts and tell me exactly
where each one goes. You cannot create the project, the repo, or upload files
yourself; I place everything by hand from your output, one step at a time.

Hold these rules the whole time:
- Two surfaces: the planner/reviewer chat plans and emits Claude Code handoffs;
  Claude Code is the canonical editor. Never tell me to hand-edit a repo file;
  repo content is delivered as Claude Code handoffs. Project instructions are the
  one paste-by-hand exception (they live in claude.ai Settings).
- One question at a time. Do not bundle questions. Wait for my answer before the
  next one.
- Honesty: flag anything you cannot verify and any slot only I can fill. Do not
  invent facts about my project. Verify changeable facts (tool versions,
  availability) rather than recalling them.

STEP 1 - DISCOVERY. Interview me one question at a time, in this order, skipping
anything I have already answered:
  1. Project name and one-line purpose (the problem, for whom).
  2. Smallest shippable v1, stated as an observable end-state.
  3. Platform and surface (web app, API, CLI, mobile, desktop).
  4. Stack preference or reuse (existing repos/tools to port; languages I know).
  5. Hosting and deploy target, and whether deploy is manual or automatic.
  6. Data and auth (what data, where it lives, accounts or no accounts).
  7. External services/APIs and any keys involved.
  8. Standing toolchain to assume (default: WSL2 + Claude Code + GitHub connector
     + Vercel; correct me if different).
  9. Hard constraints (time, budget, privacy, anything non-negotiable for v1).
  10. The "done" rule: what counts as a component being DONE. Default is a formal
      /verify pass against a locked spec; a project may instead use an equivalent
      acceptance check (for example a live click-through against an approved
      mockup). Pin one, because the front-door block and the starter prompt both
      need it and a missing answer reintroduces "is it done or not" ambiguity.
Then summarize the full picture back and wait for my confirmation before building.

STEP 2 - BUILD THE SCAFFOLDING (only after I confirm discovery). Produce the
artifacts ONE AT A TIME, in the A-to-F order below, each in its own fenced block
labeled with its exact destination. After each artifact, stop and wait for me to
confirm before producing the next, so I can place each one as it lands. Fill
every project-specific slot from my confirmed answers; leave no placeholder
unfilled except ones only I can know, which you flag. Use the "done" rule from
discovery (question 10) wherever a close-the-loop or acceptance line appears.
  A. PROJECT INSTRUCTIONS  (destination: claude.ai project > Instructions).
     Lead with the Development OS front-door block (read dev-os.md + build-state.md
     first; how a session starts; the close-the-loop rule; the launcher-to-
     lifecycle line; the two-surface rule). Then the project-specific block: role
     (reviewer/planner, never edits the repo), goal, stack, model routing, skills,
     connectors, caveats, working method.
  B. dev-os.md  (destination: repo /docs, synced to Project Knowledge via GitHub).
     Do NOT retype or regenerate this file. It is identical across every project,
     and reproducing a long document from memory drifts. Instead, instruct me to
     copy Section R1 of this guide (the dev-os.md reference copy) verbatim into the
     repo, unchanged. Your output for B is the placement instruction plus the
     Claude Code handoff that creates docs/dev-os.md from that copied text, not a
     re-typed map.
  C. build-state.md  (destination: repo /docs, synced to Project Knowledge). A
     FRESH state-board for THIS project: Phase 0 Discovery; current component none
     yet; "Closes when" = discovery confirmed and /blueprint produces a locked
     spec; inventory seeded from discovery (HAVE/NEED/UNKNOWN); the smallest-
     shippable v1 as the first target; open decisions; pointers.
  D. CLAUDE.md  (destination: repo root). Repo guidance for Claude Code: what the
     project is, the stack, the model convention, the deploy method (manual or
     automatic per my answer), security notes, commit discipline, caveats.
  E. SETUP WALKTHROUGH  (destination: this chat, for me to follow). The ordered,
     /lockstep-friendly steps to stand up the workspace: create the claude.ai
     project, create the GitHub repo, set up WSL/VS Code and clone, install and
     point Claude Code at the repo, connect the GitHub and Vercel connectors,
     place each artifact above (repo files as Claude Code handoffs, instructions
     as a Settings paste), and make the first commit. One step per move.
  F. STARTER PROMPT  (destination: the FIRST chat inside the new project, once
     setup is done). The Development OS starter from this guide (Section R4),
     filled with this project's name, that reads dev-os.md + build-state.md and
     opens /sdlc at Phase 0.

STEP 3 - HAND OFF. Tell me, in order, what to do: stand up the workspace via the
setup walkthrough, place every artifact, then move all further work into the new
project and leave this launcher chat behind. Confirm this chat is the workshop,
not the home.

Begin with STEP 1, question 1, and wait.
```

---

## PART TWO - Standing up the workspace (manual, with /lockstep)

The launcher gives you the content. This is the order to place it. Do these one
at a time. The launcher's SETUP WALKTHROUGH (artifact E) will mirror this, filled
in for your specific project.

Step 1 - Create the claude.ai project.
  - claude.ai > Projects > New project. Name it after the project.
  - Leave instructions and knowledge empty for now; you fill them below.

Step 2 - Create the GitHub repo.
  - Create an empty repo (github.com > New). Note the URL.
  - Keep it private unless the project is meant to be public.

Step 3 - WSL2 / VS Code.
  - Open your WSL2 Ubuntu shell. Choose or make a working directory under your
    Linux home (not a Windows-mounted path), e.g. ~/projects/<name>.
  - Clone the repo there: `git clone <repo-url>` and `cd` into it.
  - Open it in VS Code from WSL: `code .`

Step 4 - Claude Code.
  - Install if needed (Node.js required). Confirm the version is current; verify
    against the official docs rather than assuming.
  - Start Claude Code in the repo directory so it reads live files.
  - Approve permissions deliberately; do not grant blanket access.

Step 5 - Connectors.
  - GitHub connector (claude.ai > Settings > Connectors): connect and scope it so
    it syncs the repo (or at least the /docs folder), so dev-os.md and
    build-state.md reach Project Knowledge without manual upload.
  - Vercel MCP (if the project deploys on Vercel): read/observability only, no
    auto-approved mutations.
  - Any other connector the project needs: connect read-only by default.

Step 6 - Skills (if the project uses them).
  - Place project skills in the repo's .claude/skills, and symlink to
    ~/.claude/skills if a non-repo surface (Cowork) needs them.

Step 7 - Place the artifacts.
  - Project Instructions (artifact A): paste into the claude.ai project >
    Instructions, then save. This is the one paste-by-hand step.
  - dev-os.md, build-state.md (B, C), and CLAUDE.md (D): land via a Claude Code
    handoff (the launcher emits them as handoff blocks). Claude Code creates the
    files, commits, and pushes. The GitHub connector then syncs the two /docs
    files into Project Knowledge.

Step 8 - First commit and sync check.
  - Confirm the repo holds dev-os.md, build-state.md, and CLAUDE.md.
  - Confirm the two /docs files appear in the project's Knowledge list (or that a
    fresh chat can read them). Connectors can lag a few minutes.

---

## PART THREE - Move in and run the lifecycle

Once the workspace stands up, leave the launcher chat behind and work inside the
project.

Step 1 - Open the FIRST chat inside the new project. Paste the filled Starter
  Prompt (launcher artifact F, based on Section R4). It reads the two system
  files, states where you are (Phase 0), and opens /sdlc.

Step 2 - Run /sdlc through the phases, approving each gate:
  - Phase 0 Discovery -> Phase 1 /blueprint (define done, inventory, critical
    path) -> Phase 2 /stack -> Phase 3 /ecosystem -> Phase 4 build loop ->
    Phase 5 capture.

Step 3 - In the build loop (Phase 4), per component on the critical path:
  - /preview first if it has a screen (lock the design on a throwaway mockup).
  - /forge to architect-then-engineer it.
  - /loop for any increment needing more than one pass.
  - /verify (or your project's chosen acceptance check) against the locked spec.
    Pass closes it; fail routes to /debug, then re-check.
  - Update build-state.md (a Claude Code handoff). Move to the next component.

Step 4 - Close the loop. The build loop ends only when every component in the
  inventory passes. Then Phase 5: run /baseline to capture the build as a
  reusable doc, and /handoff before you leave a long chat.

Step 5 - Continuity. New chat? Run /resume; it reads build-state.md and the last
  /handoff and reconstitutes where you were, then confirms before continuing.

---

## R1. Reference: dev-os.md (the operating map, verbatim)

Emit this unchanged into every project's repo /docs. It is project-agnostic.

```
# Development OS - Operating Map

Project-agnostic spine for building any application end-to-end with the
existing command suite. Plug into project knowledge. Read alongside
build-state.md (the live state-board). This map is the route; build-state.md
is where you are on it.

## The two-surface rule (always on)
- Planner chat: plans, architects, emits Claude Code handoffs. Never edits the repo.
- Claude Code: canonical editor. Reads, edits, commits, pushes.
- Every repo change is one handoff block (path, surgical edit, cross-file
  touches, commit message, verify step).

## The close-the-loop rule (the system's one hard gate)
A component is DONE only when it passes its acceptance check against the locked
spec (/verify by default, or the project's agreed equivalent). On every close:
update build-state.md (move the item to done, name the next task and the
condition that closes it). The build loop (Phase 4) closes only when every
component in the inventory is done. Nothing is "done on a whim."

## The phases (run in order; hard human gate between each)

| Phase | Command | Consumes | Produces |
|---|---|---|---|
| 0 Discovery | /sdlc Phase 0 (or /blueprint interview) | the idea, or existing repo + docs | confirmed problem, users, smallest shippable version |
| 1 Define done | /blueprint | discovery output | observable done-definition, full component inventory (HAVE/NEED/UNKNOWN), critical path, bottleneck |
| 2 Stack | /stack | component inventory | tech + methodology per layer, security posture |
| 3 Ecosystem | /ecosystem | the build's shape | Anthropic tooling setup (surfaces, model routing, Skills, connectors, weekly brief) |
| 4 Build loop | per component: /preview (if visual) -> /forge -> /loop (multi-pass) -> /verify | locked spec | each component built, verified against spec, state-board updated |
| 5 Capture | /baseline + /handoff | the finished/in-progress build | reusable build-baseline doc, continuity payload |

## The build loop (Phase 4, expanded)
For each component on the critical path, in dependency order:
1. If it has a screen: /preview to lock the design first.
2. /forge to architect-then-engineer the component.
3. /loop for any increment needing more than one pass.
4. /verify against the locked spec. PASS closes it; FAIL routes to /debug, then
   re-verify.
5. Update build-state.md. Move to the next component.
Loop exits only when every component verifies. That is the loop closing.

## Cross-cutting (run throughout, not as phases)
- Tracking: build-state.md, read first every chat, updated on every close.
- Monitoring: the Learning loop + miss-log.md catch and aggregate misses.
- Quality: /battery (red-team) and the Completion contract gate each build.
- Documentation: /baseline (build capture) + the build-journal + commit-log hook.
- Continuity: /handoff to check out of a chat, /resume to check back in.

## The front door
A new or existing project starts with the starter prompt, which points a chat at
this map + build-state.md and opens /sdlc at the right phase.
```

---

## R2. Reference: build-state.md (the live state-board template)

The launcher emits a fresh copy filled to Phase 0 for the new project. Structure
stays fixed so any chat can parse it.

```
# [PROJECT NAME] - Build State

THE canonical live state-board. Every chat reads this FIRST, before any work.
Updated on every component close (the close-the-loop rule, dev-os.md). If this
file and a chat disagree, this file wins. Snapshot may lag live repo; when in
Claude Code, the repo is truth.

Last updated: [ISO TIMESTAMP] / by [planner chat / Claude Code]

## Where I am
- Current phase: [0 Discovery / 1 Define done / 2 Stack / 3 Ecosystem / 4 Build loop / 5 Capture]
- Current component: [the one thing being built right now, or "none - between phases"]
- Closes when: [the exact condition - usually "acceptance check passes against the locked spec for [component]"]
- Next gate: [what the human approves before the next phase/component]

## Done (verified)
- [component] - verified [date], commit [hash]

## In flight
- [component] - status [building / multi-pass via /loop / awaiting verify], blocker: [none / what]

## Inventory (from /blueprint, kept current)
- HAVE: [components already done]
- NEED: [known, not yet built - in dependency order]
- UNKNOWN: [not yet resolvable - each is a question to verify, not guess]
- Critical path: [the chain that sets time-to-done]
- Current bottleneck: [the one item blocking the most downstream work]

## Open decisions (carried)
- [decision] - options: [A / B], leaning: [which + one-clause why], deciding fact needed: [what would settle it]

## Known issues (non-blocking)
- [issue] - [one line; does it corrupt the build signal? if yes, it jumps the queue]

## Deferred (parked on purpose)
- [idea] - why parked: [one line]

## Pointers (where things live)
- Spec: [/blueprint output file]   / Mockup: [/preview file]
- Baseline: [/baseline file]       / Journal: [build-journal path]
- Key source files: [the 2-4 files this build mostly touches]
```

---

## R3. Reference: the front-door block (top of project instructions)

The launcher leads the Project Instructions artifact with this, then adds the
project-specific role/goal/stack below it. Set the close-the-loop line to the
project's chosen "done" rule (formal /verify by default, or a project-specific
acceptance check such as a live click-through).

```
## Development OS - front door

This project runs on the Development OS. Two project-knowledge files are the
system; read BOTH before any build work, every chat:
- dev-os.md - the operating map (the phase spine, the build loop, the
  close-the-loop rule, the two-surface rule). This is the route.
- build-state.md - the live state-board (current phase, current component,
  what closes it, inventory, open decisions). This is where we are on the route.
  If a chat and build-state.md disagree, build-state.md wins.

## How a session starts
1. Read dev-os.md and build-state.md.
2. State, in one or two lines: current phase, current component, and the exact
   condition that closes it (from build-state.md "Closes when").
3. Resume at that point. Do not restart the lifecycle or invent a new component.
   To reconstitute after a gap, run /resume; it reads build-state.md and the
   last /handoff.

## The close-the-loop rule (enforced, not optional)
A component is DONE only when it passes its acceptance check against the locked
spec. On every close: update build-state.md (move the item to Done with date and
commit, set the next Current component and its "Closes when"). The Phase 4 build
loop closes only when every component in the inventory is done. Nothing ships
"on a whim": no spec, no build; no acceptance pass, no done.

## From launcher to lifecycle
The project-launcher prompt produces the project scaffolding (instructions,
knowledge files, CLAUDE.md). Immediately after scaffolding, start the lifecycle
with /sdlc (new project: full discovery; existing project: /sdlc self-sources
current state from the repo and docs, then asks only the forward-looking gaps).
/sdlc sequences the phases in dev-os.md with a human gate between each. This is
the front door: launcher scaffolds, /sdlc runs the build, build-state.md tracks
it, /baseline captures it.

## Two-surface division (always on)
- This planner/reviewer chat plans, architects, and emits Claude Code handoffs.
  It never edits the repo.
- Claude Code is the canonical editor: reads, edits, commits, pushes.
- Every repo change (including updates to build-state.md, dev-os.md, and the
  baseline) is one Claude Code handoff, never a manual hand-edit.

[PROJECT NAME]-specific roles, stack, and caveats live below this block; this
block is the project-agnostic spine and is identical across projects.
```

---

## R4. Reference: the starter prompt (first chat inside the project)

The launcher fills [PROJECT NAME] and matches the close-the-loop line to the
project's chosen "done" rule.

```
Run the Development OS on [PROJECT NAME].

First, read both system files in project knowledge before anything else:
- dev-os.md (the operating map: phase spine, build loop, close-the-loop rule,
  two-surface rule).
- build-state.md (the live state-board: current phase, current component, what
  closes it, inventory, open decisions). If you and build-state.md disagree,
  build-state.md wins.

Then orient, in two or three lines only:
1. New project (build-state.md is an empty template): say so, and start the
   lifecycle at Phase 0 with /sdlc, running full discovery.
2. Existing project (build-state.md has real state): state the current phase,
   the current component, and the exact "Closes when" condition from the board.
   Then start /sdlc, which self-sources current state from the repo and docs and
   asks only the forward-looking gaps. Resume at the current phase; do not
   restart the lifecycle or invent a new component.

Hold to the rules in dev-os.md throughout:
- The close-the-loop rule: a component is done only when it passes its acceptance
  check against the locked spec; update build-state.md on every close.
- The two-surface rule: you plan and emit Claude Code handoffs; you never edit
  the repo directly. Every file change (including build-state.md updates) is a
  handoff.
- Hard human gate between phases: present each phase output, stop, wait for
  approval before the next.

Begin by reading the two files and giving me the orientation lines. Then propose
the single next action and wait.
```

---

## R5. Command map (what runs when)

- /sdlc - the lifecycle orchestrator. Sequences the phases with a gate between
  each. The front door into a build.
- /blueprint - define done as an observable end-state; decompose into the full
  component set; inventory HAVE/NEED/UNKNOWN; name the critical path and
  bottleneck. Phase 1.
- /stack - choose the application's technology and engineering methodology per
  layer; name the security posture. Phase 2.
- /ecosystem - design the Anthropic tooling setup (surfaces, model routing,
  Projects, Skills, connectors, a weekly brief). Phase 3.
- /preview - for a component with a screen: lock the design on a throwaway
  clickable mockup before building. Phase 4, per visual component.
- /forge - architect-then-engineer one component end-to-end and ship it. Phase 4.
- /loop - drive one increment toward a target: measure the gap, prescribe one
  move, check it shrank the gap. Phase 4, multi-pass components.
- /verify - confirm a build meets its acceptance criteria against the locked
  spec. Phase 4, closes a component.
- /debug - diagnose and fix a reproduced failure; add a regression guard. Phase
  4, on a verify failure.
- /battery - adversarial red-team of a plan or artifact before reliance. Quality
  gate, any phase.
- /baseline - capture the build as a reusable, generalized document. Phase 5.
- /handoff - emit a continuity payload before leaving a long chat. Phase 5 and
  any time a chat saturates.
- /resume - reconstitute prior state in a fresh chat from build-state.md and the
  last handoff. Start of a new chat.
- /lockstep - one confirmed step at a time. Use during manual setup and any
  precise build sequence.
- /plain - plain-language delivery. Use whenever you want simple wording.

---

## R6. Checklists

Launch checklist (global chat):
- [ ] This guide uploaded.
- [ ] Launcher prompt fired; discovery answered one question at a time.
- [ ] Discovery summary confirmed.
- [ ] Six artifacts produced (instructions, dev-os.md, build-state.md, CLAUDE.md,
      setup walkthrough, starter prompt).

Stand-up checklist (manual, /lockstep):
- [ ] claude.ai project created.
- [ ] GitHub repo created and cloned into WSL.
- [ ] Claude Code running in the repo.
- [ ] GitHub connector scoped to sync /docs (or whole repo).
- [ ] Vercel/other connectors connected read-only as needed.
- [ ] Instructions pasted into project Settings and saved.
- [ ] dev-os.md, build-state.md, CLAUDE.md landed via Claude Code handoff,
      committed, pushed.
- [ ] Two /docs files visible in Project Knowledge (or readable by a fresh chat).

Per-session checklist (inside the project):
- [ ] Read dev-os.md and build-state.md first.
- [ ] State current phase, current component, and "Closes when."
- [ ] Resume at that point; do not restart the lifecycle.

Close-a-component checklist:
- [ ] Acceptance check passed against the locked spec.
- [ ] build-state.md updated (item to Done with date/commit; next Current set).
- [ ] Build journal entry, if the session changed the repo.

---

## R7. Honest limits

- A prompt cannot create the workspace. The launcher produces content and
  instructions; you place everything by hand.
- Connector sync can lag. A freshly committed /docs file may take a few minutes
  to appear in Project Knowledge.
- The planner chat sees the repo as a snapshot. After commits, a fresh planner
  chat reviews against the latest code.
- "Done" is whatever the project's close-the-loop line says. Formal /verify is
  the default; a project may set an equivalent acceptance check (for example a
  live click-through against an approved mockup). Pick one and write it into the
  front-door block and the board's "Closes when" line, so done is never a guess.
- Tool facts change. Verify Claude Code versions, connector behavior, and
  availability against current official sources rather than relying on this
  guide's wording.
```
