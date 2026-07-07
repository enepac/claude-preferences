# Long-Horizon Continuity: Engineering the Multi-Session Harness at Atomic Resolution

**A complete process discovery and paste-ready master prompts for Step 4 of AI-assisted app development: making discrete, memoryless agent sessions behave like one continuous project that can run for days, weeks, or unattended stretches.**

---

## Part 1 — Orientation: What Long-Horizon Continuity Is and Why It Exists

### 1.1 The problem in one image

A long-running agent project is a workshop staffed by engineers working in shifts, where every arriving engineer has total amnesia about the previous shift. Nothing they were told survives; only what was *left in the workshop* survives — the code, the commit history, the notes pinned to the wall. Long-horizon continuity is the discipline of making the workshop itself carry the project, so that an infinite sequence of amnesiac shifts still converges on a finished product.

Steps 1–3 already gestured at this: the spec session wrote for "a future context window with amnesia," the harness session built PROGRESS.md machinery, and the build loop bootstrapped every pass from files. Step 4 is where that thread becomes the whole subject — because past a certain project size, the multi-session problem stops being an edge case and becomes the dominant engineering constraint.

### 1.2 Why compaction alone doesn't solve it

The naive hope is that context management features — compaction, summarization — make a single session effectively infinite, so continuity never needs designing. Empirically they don't. Given only a high-level prompt and a compaction-equipped loop, even a frontier model falls short of a production-quality application across multiple context windows. The failures come in two characteristic patterns:

1. **The one-shot attempt.** The agent tries to do too much at once, runs out of context mid-implementation, and leaves the next session a half-implemented, undocumented feature — the worst possible inheritance.
2. **Premature completion.** The agent declares the project done long before it is, because nothing in its context defines the full scope, and a fresh window has no memory of what "all of it" meant.

Both failures share a root: the knowledge needed to work correctly (full scope, current state, what's next) lived in a conversation instead of in artifacts. Compaction is a lossy summary of a conversation; artifacts are lossless statements of state. Continuity engineering is the systematic replacement of the former with the latter.

### 1.3 The two-agent solution shape

The working solution has two roles, played by the same model with different prompts:

| | **Initializer agent** | **Session (coding) agent** |
|---|---|---|
| Runs | Once, first context window only | Every subsequent context window |
| Prompt | Special first-run prompt | Standard recurring prompt |
| Job | Set up the environment and write the state artifacts every future session will need | Make incremental progress and leave clear artifacts for the next session |
| Key output | Comprehensive feature requirements expanding the initial prompt; seeded progress file; initialized repo | One increment of verified work; updated progress file; clean commits |
| Failure it prevents | Sessions guessing at scope forever | The one-shot and the undocumented half-finish |

The rest of this document decomposes the full lifecycle around these two roles into atomic stages.

### 1.4 The operating principles of the whole step

1. **The artifacts are the project; the sessions are visitors.** Any knowledge not written into a persistent artifact by session end has been destroyed, regardless of how important it felt during the session.
2. **Two memories, two jobs.** Git history is the *what-happened* memory (every change, bisectable, rollbackable). The progress file is the *where-are-we* memory (state, position, next step, gotchas). Neither substitutes for the other: history without a state summary forces archaeology every session; a state summary without history can't be verified or rolled back.
3. **Scope lives in a checklist, not a feeling.** The full feature-requirements document, written once at initialization and checked off item by item, is the only defense against premature completion. "It seems done" is not a judgment a fresh window is equipped to make; "17 of 43 requirements checked" is a fact it can read.
4. **Every session ends deliberately or has failed.** A session that dies mid-edit at context exhaustion has converted its remaining knowledge into damage. Ending early at a clean boundary is a success; ending late in a mess is the defining failure of long-horizon work.
5. **Small, verified, recorded — then stop.** The unit of long-horizon progress is not "as much as fits in a window" but "the largest increment that can be finished, verified, and documented with comfortable margin."
6. **Trust the artifacts, verify the artifacts.** Each session both relies on the inherited state record and audits it against reality (do the tests actually pass? does the recorded state match the repo?), because a corrupted state record silently poisons every downstream session.

---

## Part 2 — The Atomic Process Map

Step 4 decomposes into ten stages, L0 through L9. L0 runs once at the decision point; L1 runs once as the project's first session; L2 defines the state architecture both agent roles maintain; L3–L7 run inside every recurring session; L8–L9 govern the project across sessions. Each stage: **Purpose → Inputs → Atomic actions → Outputs → Exit criteria**.

---

### L0 — Deciding Whether Long-Horizon Machinery Is Warranted

**Purpose.** Multi-session harnessing has real overhead. Deploying it on a two-hour task is ceremony; skipping it on a two-week build is how projects die at the second context window. Decide deliberately.

**Inputs.** The frozen SPEC.md and PLAN.md (Steps 1–2); an honest size estimate.

**Atomic actions.**
- L0.1 — Estimate the horizon: will the total work plausibly fit in one context window with margin? Count the plan's slices and their sizes; anything beyond roughly one or two sessions of work is multi-session territory.
- L0.2 — Classify the supervision mode: interactive (a human restarts and steers each session), semi-attended (the human checks in at plan checkpoints), or unattended (an outer loop restarts sessions automatically until done). The mode raises the required rigor of every later stage — unattended runs get no benefit of the doubt.
- L0.3 — If single-session: adopt only the lightweight subset (a progress note at any pause point, commit hygiene) and skip the rest of this machinery. Over-harnessing small work is a real failure, not a virtue.
- L0.4 — If multi-session: commit to the full architecture below, and record the supervision mode in the plan, because it changes the session-end protocol (L5) and the orchestration design (L9).

**Outputs.** A horizon verdict; a supervision mode; the decision recorded.

**Exit criteria.** The machinery deployed matches the project's actual horizon; the supervision mode is written down, not assumed.

---

### L1 — The Initializer Session

**Purpose.** The one session that is allowed to be different: it runs with a first-run-only prompt and builds the world every future session will wake up into. Inspiration comes from what effective engineers do on day one of a project — set up the environment so the team can work, not start typing features.

**Inputs.** The frozen SPEC.md and PLAN.md; the Step 2 harness; an empty or scaffolded repo.

**Atomic actions.**
- L1.1 — Expand the scope into a **comprehensive feature-requirements file**. Take the spec's requirements and enumerate them as an exhaustive, checkable list — every feature, every acceptance criterion, granular enough that "done" becomes arithmetic (items checked / items total) rather than judgment. This single artifact is the direct countermeasure to both premature completion and the one-shot attempt: it shows every session how much genuinely remains, which makes "do a small piece" the obviously correct strategy.
- L1.2 — Initialize the continuity spine: the progress file (e.g., `claude-progress.txt` or `PROGRESS.md`) seeded with the project's starting state, the requirements file from L1.1, and git with an initial commit. These three plus the code are the entire inheritance mechanism.
- L1.3 — Set up the working environment completely: dependencies installed and pinned, run scripts proven, dev server verified to start, test runner verified to discriminate pass from fail. A future session that must debug environment setup before working has been taxed by the initializer's laziness.
- L1.4 — Build the walking skeleton if the plan calls for one (slice 0): the thinnest end-to-end path, deployed or runnable, committed. The skeleton doubles as proof the environment actually works and as the template every session extends.
- L1.5 — Write the session contract into the repo (in the progress file's header or the harness doc): the boot protocol every session must run (L3), the end protocol every session must run (L5), and the increment-sizing rule (L4). Future sessions read their own operating instructions from the workshop wall.
- L1.6 — End the initializer session by its own rules: progress file updated to reflect true state ("environment ready, skeleton deployed, 0 of N requirements complete, next: requirement X"), everything committed, nothing half-done.

**Outputs.** Requirements checklist; seeded progress file; working environment; optional skeleton; the session contract; a clean first inheritance.

**Exit criteria.** A brand-new session given only the repo could orient, run the project, and start the first real increment without a single question — the initializer passes the same amnesia test the spec did.

---

### L2 — The State Architecture

**Purpose.** Define precisely what persists, where, and which artifact answers which question — so sessions neither duplicate state nor drop it between the cracks.

**Inputs.** The initializer's artifacts.

**Atomic actions.**
- L2.1 — Assign the four canonical artifacts their non-overlapping jobs:
  - **The code + tests**: the only ground truth of what exists. Everything else is commentary.
  - **Git history**: the what-happened memory. Bisectable, rollbackable, and the audit trail against which the progress file can be verified.
  - **The progress file**: the where-are-we memory. Current state in one honest paragraph, position in the requirements list, the exact next step, and accumulated gotchas (environment quirks, known debt, things that bit a previous session).
  - **The requirements file**: the how-much-remains memory. The checklist whose unchecked items are the project's remaining scope; the sole arbiter of done.
- L2.2 — Enforce the freshness rule: the progress file describes *now*, not history. Superseded content is deleted or collapsed, because a progress file that grows monotonically becomes a second context window to exhaust — the artifact must stay readable in one gulp at session start.
- L2.3 — Enforce the honesty rule: recorded state must be verifiable state. "Feature X works" is only writable after the session watched its test pass; aspirational or assumed state in the progress file is the poison that L8 exists to detect.
- L2.4 — Keep session-scoped knowledge out of persistent artifacts: reasoning, exploration narratives, and dead ends belong to the session (or, at most, one line in gotchas if a dead end will tempt future sessions). Artifacts carry state, not stories.
- L2.5 — Version the state artifacts in git like everything else, so state changes are themselves diffable and a corrupted update can be rolled back.

**Outputs.** A defined, minimal, non-overlapping state architecture with freshness and honesty rules attached.

**Exit criteria.** Every question a fresh session asks ("what exists? what happened? where are we? how much remains?") maps to exactly one artifact, and each artifact stays small enough to read at every boot.

---

### L3 — The Session Boot Protocol

**Purpose.** Convert an amnesiac context window into an oriented worker in minutes, using only the inheritance.

**Inputs.** The repo, as the previous session left it.

**Atomic actions.**
- L3.1 — Read in fixed order: progress file first (the state summary), then the requirements checklist (position in scope), then recent git log (what the last sessions actually did), then the session contract (how to operate).
- L3.2 — **Verify before trusting**: run the canonical test target and confirm reality matches the record. The recorded state says tests pass — do they? The recorded state says feature X is done — is its requirement genuinely checked and tested?
- L3.3 — On any mismatch, reconciliation becomes the session's first task: diagnose whether the record is stale (previous session forgot to update — fix the record) or the repo regressed (something broke — fix the repo, via the Step 3 debugging discipline). Building on an unreconciled mismatch compounds it and corrupts the inheritance for every later session.
- L3.4 — Select the increment: by default, the progress file's "next" entry; if that's now moot, the first unchecked requirement in dependency order. One increment. The temptation to grab three "while we're here" is the one-shot failure knocking.
- L3.5 — Restate the working contract in one paragraph (this session builds X, verified by Y, and will end with the progress file updated) before touching anything.

**Outputs.** An oriented session with a verified starting state and a single selected increment.

**Exit criteria.** Boot completes in minutes, not a third of the window; the state record and reality agree (or their disagreement is the declared task); exactly one increment is in flight.

---

### L4 — Increment Sizing and the Anti-One-Shot Discipline

**Purpose.** Keep every session's ambition inside what can be *finished, verified, and documented* with margin — the single behavioral change that most improves long-horizon output.

**Inputs.** The selected increment; the remaining context budget.

**Atomic actions.**
- L4.1 — Size the increment against the finish-verify-document standard, not against the raw window size. The budget must cover: implementation, tests, running the tests, fixing what they find, updating the artifacts, and committing — plus reserve. An increment that fits only the implementation is oversized by definition.
- L4.2 — When an increment looks too big, split it at a boundary that leaves working state (a sub-feature that's independently testable), and record the split in the requirements file so the checklist stays the true map of scope.
- L4.3 — Resist scope gravity continuously: adjacent improvements, refactors, and discovered issues go into the progress file's gotchas/backlog note, not into this increment. A long-horizon project has infinite future sessions for them; this session has one job.
- L4.4 — Prefer many small commits inside the increment (Step 3's inner-loop discipline), so that even a badly interrupted session loses minutes, not hours.
- L4.5 — Apply the two-thirds rule: when roughly two-thirds of the practical budget is spent and the increment isn't finished, stop expanding and start converging — cut to the smallest completable version, or execute a deliberate mid-increment checkpoint (L5.5). Sessions that plan their ending from the two-thirds mark end cleanly; sessions that plan it at the end don't end, they die.

**Outputs.** Right-sized increments; a growing backlog of deferred items in the state artifacts rather than in lost context.

**Exit criteria.** No session ends with an unfinished increment it *chose* to start knowing it couldn't finish; interrupted work always sits at a committed, working boundary.

---

### L5 — The Session-End Protocol

**Purpose.** Manufacture the next session's inheritance. This is the most consequential stage in the entire step: everything the project knows travels through this bottleneck, every session, or is lost.

**Inputs.** The session's completed (or deliberately checkpointed) work.

**Atomic actions.**
- L5.1 — Verify the increment end-to-end one final time from committed state — tests green, the demo step of the touched requirement performed — before recording anything as done. The end protocol never records aspiration.
- L5.2 — Update the requirements checklist: check off exactly what was completed and verified, nothing more. The checklist's integrity is the project's completion arithmetic; one optimistic checkmark corrupts it.
- L5.3 — Rewrite the progress file for the successor, as if writing for a competent stranger (which is literally the reader): current state in one honest paragraph; the exact next step, specific enough to start on without archaeology ("implement requirement 18: session-expiry handling; the auth middleware is in src/auth/…; note the test fixture quirk recorded in gotchas"); updated gotchas; pruned stale content per the freshness rule.
- L5.4 — Commit everything, including the state artifacts, with an intent-stating message; leave zero uncommitted changes. An uncommitted working directory is knowledge in the process of being destroyed.
- L5.5 — Mid-increment checkpoint variant (when the two-thirds rule forced an early stop): stop at the nearest committed working boundary and write the intra-increment handoff — sub-position, exact next action, current hypothesis if mid-debugging — because "exactly where I was and what I was about to try" is precisely the knowledge that otherwise dies with the window.
- L5.6 — Run the one-minute handoff audit before ending: *could the next session start productive work within five minutes using only what's now committed?* If any needed fact lives only in this conversation, write it into the artifacts now — this is the last moment it exists.

**Outputs.** A verified, recorded, committed inheritance; a progress file that is the project's single most current document.

**Exit criteria.** The handoff audit passes; the recorded state is verified state; nothing uncommitted remains.

---

### L6 — Completion Discipline: Preventing the Premature "Done"

**Purpose.** Make "the project is finished" a checkable arithmetic fact rather than a fresh window's optimistic impression.

**Inputs.** The requirements checklist; the spec's global done-definition (Step 1, S6).

**Atomic actions.**
- L6.1 — Bind every "done" claim to the checklist: a session may declare the project complete only when every requirement is checked, and each check was earned by L5.1-grade verification at the time it was made.
- L6.2 — On full checklist completion, run the completion audit as its own session task — not a formality: execute the spec's global done-definition scene and the full demo script against the deployed system, end to end, fresh.
- L6.3 — Route completion-audit failures back as new unchecked items (with their regression tests, per Step 3's discipline), and the project continues. The checklist reopening is the system working, not failing.
- L6.4 — Distinguish *scope-complete* (all requirements checked) from *spec-complete* (the done-definition scene passes) from *ship* (the human accepts at the final checkpoint). Only the third ends the project; sessions never skip the ladder.
- L6.5 — Guard the checklist against silent scope edits: requirements are added, split, or amended with dated entries (the spec-amendment discipline from Step 1), never deleted because they're inconvenient. An unchecked requirement that stops appearing is the premature-completion failure wearing a disguise.

**Outputs.** A completion decision that is arithmetic plus audited demonstration plus human acceptance.

**Exit criteria.** No session can declare done from impression; the only path to "finished" runs through the checklist, the scene, and the checkpoint.

---

### L7 — Context-Window Economics

**Purpose.** Treat the window as the project's scarcest per-session resource and spend it on state-changing work, not on re-derivation or narrative.

**Inputs.** The running session; the boot artifacts.

**Atomic actions.**
- L7.1 — Spend boot tokens once: the fixed read order (L3.1) is deliberately minimal; sessions do not re-read the whole spec or the whole codebase, only the state spine plus the files the increment touches.
- L7.2 — Delegate bulk reads: exploration across many files runs in an isolated subagent context that returns a summary, keeping the main window for construction (Step 3, B8.2 — it matters more here because every wasted token shortens the horizon).
- L7.3 — Prefer ending over compacting at any clean boundary: a fresh session booting from honest artifacts is higher-fidelity than a long session running on a lossy summary of itself. Compaction is the fallback for windows that must stretch, not the continuity strategy.
- L7.4 — Keep narration out of the window: the session's product is edits, commands, results, and artifact updates. Long self-explanation is horizon burned.
- L7.5 — Watch the budget explicitly against the two-thirds rule (L4.5), so the end protocol always runs inside reserve rather than in panic.

**Outputs.** Sessions whose windows are spent overwhelmingly on increment work and inheritance-writing.

**Exit criteria.** The end protocol has never been squeezed out by exhaustion; boot cost stays a small fraction of the window.

---

### L8 — Drift, Corruption, and Recovery Across Sessions

**Purpose.** Detect and repair the failure modes unique to multi-session work: state records diverging from reality, quality eroding gradually, and inherited errors compounding.

**Inputs.** The boot verification results across sessions (L3.2); the git history.

**Atomic actions.**
- L8.1 — Treat every boot mismatch as a recorded event: when reconciliation (L3.3) fires, note in the progress file's gotchas what diverged and why, because repeated divergence is a process bug (some session is skipping its end protocol) that needs fixing at the contract level, not absorbing session by session.
- L8.2 — Watch for slow-drift signals across sessions: the test suite quietly slowing, skipped tests accumulating, the gotchas list growing without shrinking, requirement checks that keep reopening. Each is a compounding debt that a single session rationally ignores and the project fatally accumulates; the response is a dedicated maintenance session, scheduled like any increment.
- L8.3 — On discovering a corrupted inheritance (a checked requirement that's actually broken, a progress file describing a repo that doesn't exist), run a **repair session**: its sole increment is restoring artifact-reality agreement — re-verify recent checkmarks, rebuild the progress file from the repo's actual state, and git-bisect any regression to its source. No feature work rides along with a repair.
- L8.4 — Use git as the recovery floor: because every session committed at working boundaries (L4.4, L5.4), any disaster reduces to "roll back to the last verified boundary and re-derive the state file from there." The architecture makes the worst case cheap; the discipline keeps the worst case rare.
- L8.5 — Feed structural lessons back: a drift class that recurs becomes a mechanism (a stricter Stop-gate checklist item, a boot-protocol addition, a new hook) per the Step 2/3 reinforcement rule — the multi-session harness itself is subject to miss-becomes-mechanism.

**Outputs.** Divergences caught at boot rather than compounding; a repair playbook; a harness that hardens against its own observed failure modes.

**Exit criteria.** No known artifact-reality disagreement is ever built upon; drift signals have owners (a scheduled maintenance session) rather than being ambient anxiety.

---

### L9 — Orchestration and Termination: The Outer Loop

**Purpose.** Design the machinery *around* the sessions — what starts them, what stops them, and how the project ends — matching the supervision mode chosen in L0.

**Inputs.** The supervision mode; the plan's human checkpoints; the completion ladder (L6.4).

**Atomic actions.**
- L9.1 — Define the restart mechanism per mode: interactive (the human opens each session and pastes the recurring prompt), semi-attended (sessions chain until a plan checkpoint, then wait), unattended (a scripted outer loop — the Agent SDK pattern — launches the next session when one ends, with the recurring prompt, until a stop condition).
- L9.2 — Give the outer loop hard stop conditions, not just "until done": the checklist reaching completion (triggering the L6 completion audit), a human checkpoint reached, a failure threshold tripped (N consecutive sessions without a new checkmark — the thrash signal at project scale), or a budget ceiling (sessions, tokens, or spend). An unattended loop without stop conditions is an unbounded liability, not automation.
- L9.3 — Route the project-scale thrash signal like the session-scale one (Step 3, B9): consecutive no-progress sessions mean the problem is at a higher layer — a mis-specified requirement, a harness gate fighting the work, an environment breakage — and the loop should halt for diagnosis rather than burn sessions grinding.
- L9.4 — Keep the human checkpoints sacred in every mode: the plan's marked review points (post-riskiest-slice, pre-irreversible actions, final acceptance) are where unattended chains must stop and wait, by mechanism rather than by hope — encode them as loop stop conditions.
- L9.5 — Terminate deliberately: when the completion ladder is fully climbed (scope-complete → spec-complete → human ship acceptance), run the final session as a closeout — tag the release state, write the project post-mortem into the repo (what the harness caught, what drifted, what the next project should inherit), and archive the state artifacts as documentation rather than deleting them.
- L9.6 — Harvest the reusable layer: the session contract, prompt pair, artifact templates, and hardened hooks are project-agnostic assets; carry them forward so the next long-horizon project starts at this project's ending maturity.

**Outputs.** An outer loop matched to the supervision mode, with hard stops; a deliberate ending; a harvested, reusable multi-session harness.

**Exit criteria.** No session ever wonders whether it should exist (the loop decides); no unattended chain can run past a checkpoint or a thrash threshold; the project ends by acceptance, not by abandonment.

---

## Part 3 — The Master Prompts (Paste-Ready)

Long-horizon work needs **two** prompts: one for the first context window, one for every window after it. This split — "a different prompt for the very first context window" — is itself a core finding of multi-context-window practice.

### 3.1 The Initializer Prompt (first session only)

```
You are the INITIALIZER agent for a long-horizon, multi-session build. You
run exactly once, in this first context window. Every later session will be
staffed by an agent with ZERO memory of this or any other conversation: the
only things that persist are the files and git history you leave behind.
Your job is NOT to build the product. Your job is to build the world in
which an infinite sequence of amnesiac sessions can build the product.

Inputs available to you: the frozen SPEC.md and PLAN.md, and the proven
harness (hooks, canonical commands, permissions) in this repo.

EXECUTE IN ORDER:

1. REQUIREMENTS EXPANSION. Read SPEC.md and expand its scope into
   REQUIREMENTS.md: an exhaustive, checkable list of every feature and
   acceptance criterion, granular enough that project completion becomes
   arithmetic (items checked over items total), with a checkbox per item
   and stable item numbers. This file is the sole arbiter of "done" and
   the standing proof of how much remains — it is the direct
   countermeasure to premature completion and to any session attempting
   to one-shot the app. Do not compress scope to make the list shorter.

2. CONTINUITY SPINE. Create PROGRESS.md seeded with: project state ("not
   started"), position (0 of N requirements), the exact first increment,
   and an empty GOTCHAS section. Initialize/verify git with everything
   committed. From here forward the four canonical artifacts divide the
   memory: code+tests = what exists; git history = what happened;
   PROGRESS.md = where we are; REQUIREMENTS.md = how much remains. No
   artifact duplicates another's job.

3. ENVIRONMENT COMPLETION. Make the environment fully workable so no
   future session pays a setup tax: dependencies installed and pinned,
   every canonical script (dev, test, lint, typecheck, build) executed
   and proven, the test runner shown to distinguish a passing from a
   failing test. Record any environment quirk you hit in GOTCHAS — it
   WILL bite a future session otherwise.

4. WALKING SKELETON. If PLAN.md defines slice 0, build it: the thinnest
   end-to-end path, verified and committed. It proves the plumbing and
   becomes the template every session extends.

5. SESSION CONTRACT. Write into PROGRESS.md's header the operating rules
   every future session must follow:
   - BOOT: read PROGRESS.md, then REQUIREMENTS.md, then recent git log;
     RUN THE TESTS to verify the record against reality before trusting
     it; reconcile any mismatch as the first task.
   - WORK: exactly one increment per session, sized to be FINISHED,
     VERIFIED, AND DOCUMENTED with margin — never merely implemented.
     At two-thirds of the practical budget, converge or checkpoint.
   - END: verify from committed state; check off ONLY what was verified;
     rewrite PROGRESS.md for a competent stranger (state, exact next
     step, gotchas; prune stale content); commit everything including
     the state files; confirm the next session could start within five
     minutes on the committed artifacts alone.

6. INITIALIZER CLOSE. End by your own contract: PROGRESS.md truthfully
   states "environment ready, skeleton [done/absent], 0 of N complete,
   next: [first increment]"; everything committed; nothing half-done.
   Final self-check (the amnesia test): could a fresh session, given
   ONLY this repo, orient and begin productive work with zero
   questions? Fix the artifacts until the answer is yes.

Rules binding throughout: write state only after verifying it; artifacts
carry state, not stories; scope lives in REQUIREMENTS.md and nowhere
else; if SPEC.md or PLAN.md is missing or unfrozen, STOP and say so.
```

### 3.2 The Recurring Session Prompt (every session after the first)

```
You are the SESSION agent for a long-horizon, multi-session build. You
have no memory of previous sessions; that is by design. The repo IS the
project's memory: PROGRESS.md tells you where things stand,
REQUIREMENTS.md tells you how much remains, git history tells you what
happened, and the code is the only ground truth. Your job this session:
make ONE increment of verified progress and leave the inheritance better
than you found it.

EXECUTE IN ORDER:

1. BOOT. Read, in order: PROGRESS.md (state, next step, gotchas, and the
   session contract in its header), REQUIREMENTS.md (position in scope),
   recent git log. Read nothing else yet — boot is minutes, not a tour.

2. VERIFY THE INHERITANCE. Run the canonical test target. Confirm
   reality matches the record. On ANY mismatch (recorded-green tests
   failing, a checked requirement that isn't actually done, a progress
   file describing a repo that doesn't exist), RECONCILIATION IS YOUR
   INCREMENT: diagnose whether the record is stale or the repo
   regressed, repair the truthful side, note the divergence in GOTCHAS,
   and do no feature work on top of a known lie.

3. SELECT ONE INCREMENT. Default: PROGRESS.md's "next" entry; if moot,
   the first unchecked requirement in dependency order. Size it to be
   finished, VERIFIED, and documented with margin — if it can't be, split
   it at a boundary that leaves working state and record the split in
   REQUIREMENTS.md. Everything adjacent you're tempted to also do goes
   to GOTCHAS/backlog, not into this session. State in one paragraph:
   this session builds X, verified by Y, ending with artifacts updated.

4. WORK THE INCREMENT under the standing build discipline: smallest
   viable edits; let the hooks speak and fix findings immediately; new
   behavior gets its test WITNESSED FAILING first; run the relevant
   checks at every small boundary; commit small with intent-stating
   messages; never weaken a check to pass it; read actual command
   output, not expected output.

5. BUDGET WATCH. At roughly two-thirds of your practical budget, stop
   expanding and converge: finish the smallest complete version, or
   execute the checkpoint variant of the end protocol at the nearest
   committed working boundary (recording sub-position, the exact next
   action, and any live hypothesis). Delegate any bulk multi-file
   exploration to an isolated subagent that returns a summary. Prefer
   ending cleanly over compacting past a boundary. A session that dies
   mid-edit has failed regardless of how good its partial work was.

6. END PROTOCOL (never skipped, never rushed):
   a. Verify the increment end-to-end from COMMITTED state.
   b. Check off in REQUIREMENTS.md exactly what was verified — nothing
      aspirational, ever. If this checkmark completes the list, do NOT
      declare the project done: record "scope-complete; completion audit
      required" as the next step (the audit is its own future session
      running the spec's full done-scene and demo script).
   c. Rewrite PROGRESS.md for a competent stranger: honest one-paragraph
      state; the EXACT next step (specific enough to start without
      archaeology); updated GOTCHAS; stale content pruned.
   d. Commit everything, including state files. Zero uncommitted changes.
   e. Handoff audit: could the next session be productive within five
      minutes on the committed artifacts alone? If any needed fact still
      lives only in this conversation, write it into the artifacts NOW —
      this is the last moment it exists.

Thrash rule: if you are the latest of multiple consecutive sessions with
no new verified checkmark (per git log and PROGRESS.md), do not grind —
your increment becomes diagnosis: name whether the blocker is a
requirement defect, a harness gate fighting the work, or an environment
breakage, record it, and stop for the appropriate escalation.
```

---

## Part 4 — The Decision Bank

The judgment calls each stage must make well.

**Warrant (L0)**
- Honestly: does this fit in one window with the end protocol's reserve included? (If the answer needs optimism, it's multi-session.)
- Will any stretch run unattended? (Then every "should" below becomes "must," and the outer loop needs hard stops.)

**Initialization (L1)**
- Is the requirements list granular enough that no single item hides a week of work? (An item that big is the one-shot trap relocated.)
- What environment quirk did setup hit that will bite a stranger? (It goes in GOTCHAS today.)

**State architecture (L2)**
- Which artifact answers this question? (If two do, one is wrong; if none does, the architecture has a hole.)
- Is the progress file still readable in one gulp? (If it's grown into a document, the freshness rule has lapsed.)

**Boot (L3)**
- Do the tests actually pass, or does the record merely say so?
- Is the "next" entry still the right next thing, or did the last session's discoveries moot it?

**Sizing (L4)**
- Does the budget cover finish + verify + document + reserve, or just the fun part?
- What am I tempted to also do, and did it go to the backlog instead?

**Session end (L5)**
- Was every new checkmark earned by a witnessed verification this session?
- Could a stranger start within five minutes on the committed artifacts alone? (The only question that matters at the bottleneck.)

**Completion (L6)**
- Is "done" being claimed by arithmetic plus audit, or by impression?
- Has any inconvenient requirement quietly vanished rather than been amended with a dated entry?

**Economics (L7)**
- Is this read changing what the session will do, or is it re-derivation?
- Is there a clean boundary nearby that makes ending better than stretching?

**Recovery (L8)**
- Is this the first divergence of its kind, or a pattern that indicts the process?
- Does this repair session contain any feature work hiding in it? (It must not.)

**Orchestration (L9)**
- What stops the loop besides success? (Checkpoint, thrash threshold, budget ceiling — all three need answers.)
- Which decisions are the human's alone, and does the mechanism actually wait for them?

---

## Part 5 — Output Artifact Templates

### 5.1 PROGRESS.md (the continuity spine)

```
# PROGRESS — [PROJECT_NAME]

## SESSION CONTRACT (read first, every session)
BOOT: read this file, then REQUIREMENTS.md, then recent git log; RUN THE
TESTS; reconcile any record/reality mismatch before feature work.
WORK: one increment per session, sized to finish + verify + document with
margin; two-thirds budget rule; adjacent work goes to GOTCHAS/backlog.
END: verify from committed state; check off only verified items; rewrite
this file for a stranger; commit everything; run the five-minute handoff
audit.

## STATE ([DATE], session [N])
[One honest paragraph: what works, what is deployed, position — "K of N
requirements complete."]

## NEXT
[The exact next increment, specific enough to start without archaeology:
requirement number, entry-point files, the first concrete action.]

## GOTCHAS
- [Environment quirk / known debt / dead end worth warning about — one
  line each; prune when resolved.]

## BACKLOG (deferred, not forgotten)
- [Adjacent improvements noticed mid-session; candidates for future
  requirements-file amendments.]
```

### 5.2 REQUIREMENTS.md (the completion arithmetic)

```
# REQUIREMENTS — [PROJECT_NAME]        (sole arbiter of done)

Completion: [K]/[N] checked. Checkmarks are earned ONLY by session-end
verification. Amendments (splits, additions) get dated entries; nothing
is ever silently deleted.

## Core
- [x] R1. [Requirement + its observable acceptance criterion]   (done s.3)
- [ ] R2. ...
- [ ] R2a. [split from R2, dated YYYY-MM-DD: reason]

## [Further sections mirroring the spec's requirement clusters…]

## Amendment log
[DATE] — [R-number added/split/amended] — [reason]
```

### 5.3 The repair-session prompt (L8.3)

```
This is a REPAIR session. The inheritance is corrupted: [describe the
observed artifact/reality disagreement]. Your ONLY increment is restoring
truth. (1) Establish reality: run the full canonical checks; re-verify
the most recent checkmarks in REQUIREMENTS.md against actual behavior.
(2) Bisect any regression to its introducing commit via git. (3) Repair
the truthful side: fix the repo if it regressed, fix the records if they
lied; uncheck anything unverifiable. (4) Rebuild PROGRESS.md's STATE and
NEXT from the repo's actual condition. (5) Record in GOTCHAS what
diverged and the likely process cause. NO feature work rides along.
```

### 5.4 The outer-loop stop conditions (L9.2)

```
Restart the session prompt until ANY of:
1. REQUIREMENTS.md reads N/N → launch the COMPLETION AUDIT session, then
   halt for human acceptance.
2. A PLAN.md human checkpoint is reached → halt and wait.
3. [T] consecutive sessions end with no new verified checkmark → halt:
   project-scale thrash; human diagnosis required.
4. Budget ceiling hit ([S] sessions / [C] cost) → halt and report.
```

---

## Part 6 — The Long-Horizon Quality Checklist

Run against the project periodically (and at every checkpoint). A "no" names the stage to revisit.

1. The multi-session machinery was deployed by a deliberate horizon decision, not by default in either direction. (L0)
2. The supervision mode is written down, and the outer loop matches it. (L0/L9)
3. REQUIREMENTS.md exists, is exhaustive, and is granular enough that no item hides a week. (L1)
4. A fresh session can orient, run, and start work from the repo alone — the amnesia test passes continuously, not just at initialization. (L1/L5)
5. Each of the four canonical artifacts owns exactly one question; none duplicates another. (L2)
6. The progress file describes now, is readable in one gulp, and contains no aspirational state. (L2/L5)
7. Every session boots in the fixed order and verifies the record against reality before trusting it. (L3)
8. Reconciliation, when needed, preceded all feature work. (L3)
9. Every increment was sized to finish + verify + document with margin; splits were recorded in the requirements file. (L4)
10. The two-thirds rule has been honored; no session's end protocol has ever been squeezed out. (L4/L7)
11. Checkmarks are earned only by witnessed verification at session end. (L5)
12. Every session ends with zero uncommitted changes, state files included. (L5)
13. The five-minute handoff audit passes at every session end. (L5)
14. "Done" claims run the ladder: arithmetic → completion audit → human acceptance; no rung skipped. (L6)
15. No requirement has ever silently disappeared; all scope changes carry dated entries. (L6)
16. Bulk exploration is delegated to isolated contexts; the main window is spent on state-changing work. (L7)
17. Ending at clean boundaries is preferred over compacting past them. (L7)
18. Boot mismatches are logged, and repeated divergence triggered a process fix, not repeated absorption. (L8)
19. Repair sessions carry no feature work; slow-drift signals get scheduled maintenance sessions. (L8)
20. The outer loop has all three non-success stops: checkpoints, thrash threshold, budget ceiling. (L9)
21. Human checkpoints halt unattended chains by mechanism, not by hope. (L9)
22. The project can only end by acceptance at the final checkpoint, and the harness assets are harvested at closeout. (L9)

**Anti-patterns this checklist exists to catch**
- *The one-shot heir*: a session inherits a clean repo and attempts half the roadmap, dying mid-implementation and bequeathing an undocumented crime scene.
- *The optimistic checkmark*: "done" recorded on hope; three sessions later a repair session pays for it with interest.
- *The immortal progress file*: an append-only journal that becomes its own context-exhaustion problem and buries the one paragraph that matters.
- *The trusting boot*: a session builds directly on the inherited record without running the tests, and compounds a lie it could have caught in one command.
- *The conversation vault*: the session's crucial discovery — the environment quirk, the API's real behavior — lives only in the chat, and is destroyed at window close.
- *The vibes completion*: a fresh window surveys a plausible-looking repo and declares victory with a third of the requirements unchecked.
- *The scope eraser*: an inconvenient requirement quietly vanishes from the list instead of being amended with a dated entry.
- *The immortal chain*: an unattended loop with no thrash threshold or budget ceiling, grinding sessions against a mis-specified requirement for a weekend.
- *The skipped checkpoint*: an autonomous chain rolling through a human review point because the stop lived in intention rather than mechanism.
- *The panic ending*: the end protocol attempted in the window's last gasp — exactly the moment it can no longer be done well.

---

## Part 7 — Usage Notes

**Where to run it.** In the agentic environment, on top of the Steps 1–3 outputs. The unattended variant runs on an agent-SDK-style outer loop; the interactive variant is simply the human pasting the recurring prompt into each fresh session. The prompts in Part 3 work identically in both — the supervision mode changes who restarts sessions, not how sessions behave.

**Relation to the earlier steps.** Step 4 is not a new phase after the build loop; it is the *envelope around* Step 3 that becomes necessary when the build outgrows one window. The spec's amnesia-proof documents (Step 1), the harness's mechanical enforcement (Step 2), and the loop's per-slice discipline (Step 3) are all load-bearing here: the multi-session architecture assumes them and adds the initializer, the state spine, the session contract, and the outer loop. A weak spec or an unproven harness doesn't get rescued by continuity machinery — it gets amplified by it, one amnesiac session at a time.

**Scaling down.** A project of three or four sessions doesn't need the outer loop or the repair playbook in advance; it needs the spine (progress file + requirements checklist), the boot verification, and the end protocol — those three are the irreducible core at any multi-session size. The two stages never safe to skip: **L3.2, verify before trusting** (one test run protects every session from inheriting a lie) and **L5, the end protocol in full** (the bottleneck through which everything the project knows must pass, every single session).

**The deeper pattern.** Everything in this step is one idea applied relentlessly: *externalize the state, then trust only the externalized state.* The initializer externalizes scope; the end protocol externalizes position; git externalizes history; the boot protocol refuses to trust any of it without verification. When that discipline holds, the project becomes something genuinely unusual — a system whose intelligence is transient but whose progress is permanent, converging on completion through workers who will never meet. That is the whole trick of long-horizon agent engineering, and it is less a model capability than an engineering practice: the model provides the shifts; the artifacts provide the continuity; the arithmetic provides the ending.
