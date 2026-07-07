# The Build Loop: Engineering "Verification as the Core Loop" at Atomic Resolution

**A complete process discovery and a paste-ready master prompt for Step 3 of AI-assisted app development: executing one vertical slice at a time, inside the harness, with verification as the engine rather than the afterthought.**

---

## Part 1 — Orientation: What the Build Loop Is and Why It Exists

### 1.1 Where this step sits

The build loop runs after the spec freeze (Step 1) and the harness freeze (Step 2). It consumes their outputs — a frozen SPEC.md and PLAN.md, a proven enforcement environment, and a live PROGRESS.md — and it produces the actual application, one vertical slice per pass. Steps 1 and 2 each ran once; Step 3 runs repeatedly, once per slice, until PLAN.md has no incomplete slices left. It is the phase where nearly all of the project's hours are spent, which is exactly why its loop structure — not the model's raw capability — is the binding constraint on quality.

### 1.2 The core inversion: verification is the loop, not the tail

The naive picture of a build loop is: generate code, then check it at the end. The engineering that actually works inverts this. Verification is the engine the loop runs on, and generation is what happens between verifications:

| Naive loop | Verification-core loop |
|---|---|
| Write the whole slice, then test | Write the failing test, then make it pass |
| Check quality at commit time | Hooks check every edit in milliseconds |
| The author judges "done" | An independent context judges "done" |
| A green run means finished | A green run means one gate passed; the exit gate decides |
| Bugs found by the user later | Bugs found by an adversarial second instance now |

The reason this inversion matters is asymmetry of observation: a model (like a human) reads what it meant, not what it wrote. The agent that produced the code is structurally the worst-positioned agent to judge it. Every mechanism in this step — the red-green discipline, the goal-condition evaluator, the Stop gate, the fresh-context reviewer — exists to route judgment away from the author and toward something with independent evidence: a test result, a deterministic gate, or a second context window.

### 1.3 The escalation ladder for any quality check

Every check in the loop lives at one of four rungs, ordered by cost and by strength:

1. **In-prompt** — ask for the check and the fix in the same message. Works anywhere, weakest guarantee.
2. **Goal condition** — the check becomes a condition a separate evaluator re-verifies after every turn; the loop keeps working until it holds.
3. **Deterministic gate** — a Stop hook runs the check as a script and blocks the turn from ending until it passes (built in Step 2; consumed here).
4. **Second opinion** — a verification subagent in a fresh context tries to refute the result, so the agent doing the work is never the one grading it.

Each rung trades setup for attention: the prompt version works today on any task; the goal-condition and Stop-gate versions are what let an unattended run finish correctly without a human watching. The build loop's craft is choosing the right rung per check, per slice.

### 1.4 The operating rules of the whole step

1. **One slice per pass, one pass per session.** The slice was sized in Step 1 to fit a context window. Starting a second slice in the same session — or drifting into unrelated asks — is the kitchen-sink failure that degrades everything after it.
2. **Cold start from files, never from memory.** Every session begins by reading SPEC.md, PLAN.md, PROGRESS.md, and HARNESS.md. The conversation history of prior sessions does not exist; the files are the project's entire memory.
3. **Red before green.** A test that has never been seen failing proves nothing when it passes. Every new behavior gets its failing test first, or (minimum) immediately after, with the failure witnessed.
4. **The exit gate defines done — nothing else.** Not "the code looks complete," not "the reviewer ran out of comments." The slice's exit gate from PLAN.md is the only finish line, and it is checked, not felt.
5. **Independent eyes on every slice.** The author-context never self-certifies. A fresh context (subagent, second session, or second model) reviews against the exit gate before closure.
6. **Every miss becomes a mechanism the same day.** A bug that reached review becomes a test; a style miss that reached review becomes a lint rule or hook. The loop feeds the harness; this is where the compounding actually happens.
7. **Thrash is a signal, not a grind.** Repeated failed attempts at the same fix, or a gate blocking many consecutive times, means the approach — or the spec — is wrong. The loop escalates instead of pushing harder.

---

## Part 2 — The Atomic Process Map

Step 3 decomposes into ten stages, B0 through B9. B0–B7 run in order within every slice pass; B8 and B9 run continuously across the pass. Each stage is specified as **Purpose → Inputs → Atomic actions → Outputs → Exit criteria**.

---

### B0 — Session Bootstrap and Slice Selection

**Purpose.** Reconstitute the project state in a memoryless session and lock the pass onto exactly one target.

**Inputs.** The repo: frozen SPEC.md and PLAN.md, live PROGRESS.md, HARNESS.md, git history.

**Atomic actions.**
- B0.1 — Read PROGRESS.md first: completed / current state / next. This is the bootstrap the previous session left deliberately; it outranks any guess about where things stand.
- B0.2 — Read PLAN.md's entry for the next incomplete slice, and only that slice: its deliverable, the requirement numbers it satisfies, its tests, its exit gate, its size.
- B0.3 — Cross-check reality against the record: run the canonical test target and skim recent git log. If the repo's actual state contradicts PROGRESS.md (a test failing that was recorded green, a half-finished change uncommitted), reconcile before any new work — the discrepancy is the first task, because building on an unrecorded broken state compounds the breakage.
- B0.4 — Confirm scope lock: this pass delivers this slice's exit gate and nothing else. Adjacent ideas, refactors, and "while I'm here" improvements get written to a parking list in PROGRESS.md, not acted on.
- B0.5 — Verify any time-decaying facts this slice depends on (an external API's current behavior, a library's current version) against live sources rather than recall, if the slice's plan entry or risk register flagged them.

**Outputs.** A one-paragraph working statement: "This pass implements slice N, whose exit gate is X; current repo state verified as Y." A parking list (possibly empty).

**Exit criteria.** The working statement is accurate against both the files and the actual repo; exactly one slice is in scope.

---

### B1 — The Slice Micro-Spec

**Purpose.** Expand the slice's one-paragraph plan entry into an executable sequence for this pass — the only planning this step does, because the real architecture already happened in Step 1.

**Inputs.** The slice's PLAN.md entry; the requirement texts (FR/NFR/DR/SR numbers) it satisfies; the relevant code already in the repo.

**Atomic actions.**
- B1.1 — Restate the exit gate as the pass's finish line, verbatim from PLAN.md. If the gate is ambiguous as written, that is a spec defect: log it, resolve it via a one-turn spec amendment (per Step 1's re-entry rule), and only then proceed — do not privately reinterpret it.
- B1.2 — Read the actual code the slice touches before planning changes to it. Recollection of the codebase from a prior session is not evidence; the current files are.
- B1.3 — Decompose the slice into an ordered ladder of small increments, each independently verifiable, each leaving the repo in a working state. The unit of work is "smallest change that a test can confirm," not "logical module."
- B1.4 — Front-load the slice's own riskiest increment (the unfamiliar API call, the tricky state transition) to the top of the ladder — if the slice's core bet fails, fail in minutes, not at hour three.
- B1.5 — For each increment, name its check: which test will confirm it, at which rung of the escalation ladder its gate sits, and whether the check exists yet or gets written in B3.
- B1.6 — Identify the unhappy paths this slice owns (from its FR entries) and place them in the ladder as first-class increments, not as a cleanup step — deferred unhappy paths are the single most common source of "looks done, isn't."

**Outputs.** The increment ladder: ordered steps, each with its check named.

**Exit criteria.** Every requirement number the slice satisfies maps to at least one increment; every increment has a named, checkable confirmation; the riskiest increment is first or near-first.

---

### B2 — The Inner Loop: Act, Observe, Correct

**Purpose.** Execute increments through the tightest feedback cycle the harness allows — the millisecond-scale loop where nearly all actual construction happens.

**Inputs.** The increment ladder; the Step 2 harness (post-edit hooks live, canonical commands, permissions).

**Atomic actions.**
- B2.1 — Work one increment at a time, smallest viable edit first. Large multi-file changes in one motion destroy the ability to localize whatever breaks.
- B2.2 — Let the harness speak after every edit: the post-edit hook formats and lints instantly; its failures are corrected immediately, in the same breath, before any next edit. Never batch hook failures for later — "later" is where they metastasize.
- B2.3 — Run the relevant canonical command (test subset, typecheck) at every increment boundary, not just at the end of the ladder. The question after every increment is binary: does the repo still work? A "no" is handled now, while the diff that caused it is one increment small.
- B2.4 — Commit at increment boundaries with intent-stating messages. Commit granularity is loop infrastructure: it is what makes B6's bisection possible and B7's closure clean. An uncommitted hour of work is an unbisectable hour.
- B2.5 — Hold the one-change discipline under pressure: when a fix suggests a second fix, the second goes on the ladder (or the parking list), not into the current diff.
- B2.6 — Observe the honest result of every command — read the actual output, not the expected output. Declaring a run green from its exit banner while a warning scrolled past is the inner loop's characteristic self-deception.

**Outputs.** A chain of small, green, committed increments climbing the ladder.

**Exit criteria (per increment).** Hook-clean, relevant checks green, committed. The next increment does not start on a red repo.

---

### B3 — Test Authoring and the Red-Green Discipline

**Purpose.** Create the slice's evidence. Tests written here are the only thing that lets every later rung of the ladder (goal conditions, Stop gate, reviewer) verify anything at all.

**Inputs.** The slice's per-plan tests; the increment ladder's named checks; the test runner proven in Step 2.

**Atomic actions.**
- B3.1 — For each new behavior, write the test that expresses the requirement, and **run it to watch it fail** before or immediately after writing the implementation. The witnessed red is the proof the test can detect the absence of the behavior; a test born green is a decoration.
- B3.2 — Test the requirement, not the implementation: assert on observable behavior (the FR's "observable result" clause), not on internal structure, so the test survives refactors and fails only when the promise breaks.
- B3.3 — Write the unhappy-path tests as peers of the happy-path test: invalid input, empty state, failure of the dependency, permission denial — each one traced to the FR unhappy-path entries from B1.6.
- B3.4 — Include the slice's one end-to-end check: the thinnest full-stack exercise of the new capability, extending the walking-skeleton pattern from slice 0. Unit tests prove parts; the end-to-end check proves the wiring.
- B3.5 — Keep the fast suite fast: a test that makes the canonical fast target slow gets marked for the CI-only tier, because a slow fast-suite trains the loop (and its human) to skip it.
- B3.6 — When a mid-loop bug is found by any means other than a test, write the test that would have caught it before fixing it — the regression guard lands with the fix, not after it (this is B6.6's rule, applied whenever it happens).

**Outputs.** The slice's tests: witnessed-red then green, requirement-traced, happy and unhappy paths, one end-to-end check.

**Exit criteria.** Every increment's named check exists and has been seen in both states; every requirement number the slice satisfies has at least one test asserting it.

---

### B4 — The Verification Ladder in Operation

**Purpose.** Wire the slice's completion to the right rungs of the escalation ladder, so "done" is decided by mechanisms rather than by the author's fatigue or optimism.

**Inputs.** The slice's exit gate; the Step 2 Stop gate and goal-condition pattern; the tests from B3.

**Atomic actions.**
- B4.1 — Express the exit gate as a checkable condition (the tests that must pass plus any demo-script step that must succeed). This is the condition every higher rung consumes.
- B4.2 — Choose the rung per the session mode: interactive pass → in-prompt checks plus the Stop gate as backstop; unattended pass → the exit gate as a goal condition re-evaluated every turn, plus the hard Stop gate. State the choice once at the pass's start.
- B4.3 — Respect the Stop gate rather than gaming it: when the gate blocks, the failure it reports is the next increment. Repeated consecutive blocks are not a nuisance to wait out until the gate's escape yields — they are B9's thrash signal, and the correct move is escalation, not attrition.
- B4.4 — Never weaken a check to pass it. Deleting a failing test, loosening an assertion, or skipping a hook to get green is the loop's cardinal sin: it converts a visible failure into an invisible one. If a check is genuinely wrong, changing it is its own increment with its own justification, logged in the commit message.
- B4.5 — Keep the goal-condition evaluator's judgment separate from the worker's narrative: the evaluator checks the condition against actual command output, not against the worker's claim that the condition holds.

**Outputs.** The slice's completion wired to explicit mechanisms; a stated verification mode for the pass.

**Exit criteria.** The exit gate exists as a machine-checkable condition; no check was weakened without a logged, justified decision.

---

### B5 — Independent Review: The Fresh-Context Adversary

**Purpose.** Apply the strongest verification rung — a separate context trying to refute the work — before the slice may close. One instance can cause bugs and another instance of the same model can find them, precisely because the second one carries none of the first one's intentions.

**Inputs.** The completed increment ladder (all checks green); the slice's exit gate and requirement list; the diff between the slice's start and end.

**Atomic actions.**
- B5.1 — Invoke the verification reviewer in a genuinely fresh context (the Step 2 subagent, a new session, or a second model), giving it only: the diff, the slice's exit gate, and the requirement texts. It does not receive the author's narrative of why the code is fine.
- B5.2 — Charge it adversarially and calibratedly at once: *find the gaps that affect correctness or the stated requirements; try to construct the input or sequence that breaks the exit gate; flag only findings at that bar.* The calibration clause is load-bearing — a reviewer prompted merely to "find issues" will generate findings forever, and chasing them produces defensive over-engineering rather than correctness.
- B5.3 — Demand demonstration where possible: a claimed gap accompanied by the failing input, reproduction step, or violated requirement number outranks an aesthetic objection. "Prove it breaks" is a legitimate response to a finding.
- B5.4 — Route each finding: correctness/requirement gaps → new increments (with their B3.6 regression tests) in this pass; legitimate improvements below the bar → the parking list; misfires → dismissed with one recorded line of reasoning.
- B5.5 — Loop fix-and-re-review inside the pass: the implementing context fixes, the reviewer re-checks, without a human ferrying findings between windows — until the reviewer can no longer demonstrate a gap at the bar.
- B5.6 — Apply the author-side mirror when working interactively: before requesting review, run the self-adversarial prompt ("knowing everything you know now, what would you scrap and rebuild?" / "prove this works against the exit gate") — it catches the cheap half of the findings before spending the reviewer's context on them.

**Outputs.** A review verdict at the calibrated bar; fixes landed with regression tests; a short record of dismissed findings.

**Exit criteria.** The reviewer, in a fresh context, can no longer demonstrate a correctness or requirement gap against the exit gate.

---

### B6 — Debugging Inside the Loop

**Purpose.** Handle the inevitable mid-pass defect with a forensic discipline that finds causes, not symptoms — because a symptom patched under loop pressure is a bug scheduled for re-delivery.

**Inputs.** A failing check (from a hook, a test, the Stop gate, or the reviewer) plus the commit-granular history from B2.4.

**Atomic actions.**
- B6.1 — Reproduce before hypothesizing: pin the smallest reliable repro (exact input, exact command, observed vs. expected). No fix is designed against an unreproduced failure.
- B6.2 — Isolate by bisection where history exists: a working commit plus a broken commit is a binary search, and B2.4's granularity is what makes it fast. Otherwise narrow by halving the input or the code path.
- B6.3 — State the leading cause as a falsifiable hypothesis, plus the cheapest alternative not yet ruled out — then run the one probe that distinguishes them. Confirm the cause before changing code; a refuted hypothesis is progress (it halves the search space), not failure.
- B6.4 — Fix the confirmed cause with the minimal change. If the honest available fix is a symptom-level workaround, say so explicitly in the commit message and log the underlying cause as a debt item in PROGRESS.md — a hidden workaround is a trap for the next session.
- B6.5 — Change one thing at a time throughout: simultaneous edits during debugging destroy the signal the whole discipline exists to read.
- B6.6 — Land the regression guard with the fix: the test that fails if this defect returns, committed in the same increment. A fix without its guard is not done. Then feed the loop-improvement rule: if the defect class could recur across the project, it also becomes a mechanism (lint rule, hook, assertion) per B7.5.

**Outputs.** Root-cause fix, its witnessed-red regression test, and any debt honestly logged.

**Exit criteria.** Original repro passes; surrounding checks still green; the guard exists; nothing was fixed that wasn't first understood.

---

### B7 — Slice Closure: Gate, Commit, Record, Reinforce

**Purpose.** End the pass the way the next session needs it ended: gate verified, state recorded, and the harness stronger than the pass found it.

**Inputs.** Green ladder, passed review; PROGRESS.md; the pass's accumulated misses.

**Atomic actions.**
- B7.1 — Run the exit gate one final time, end to end, from the current committed state — including the slice's demo-script step performed literally, not mentally. The gate is checked at the finish line even though every part passed earlier, because integration rot between "all parts green" and "the whole thing works" is real.
- B7.2 — Run the full pre-commit/CI ladder on the final state; a slice closes only on a clean clone's green, not a working directory's.
- B7.3 — Update PROGRESS.md as part of closure, not after it: slice N complete (date), current state in one honest paragraph, next slice named, parking list and debt items carried forward. The Stop-gate checklist from Step 2 enforces this mechanically; the content is written as if for a stranger, because it is.
- B7.4 — Commit and mark the closure (tag or clearly-labeled commit). PLAN.md's slice entry is checked off via its own small commit if the plan is tracked as a living checklist.
- B7.5 — Run the reinforcement pass over the session's misses: every defect, style miss, or process stumble that occurred gets its mechanism — a test (already landed via B6.6), a lint rule, a hook tweak, a HARNESS.md amendment — landed now, with its dated entry. This five-minute step is where the project's compounding lives; skipping it converts the same lesson into a recurring bill.
- B7.6 — Check the human-review checkpoints from PLAN.md: if this slice was marked as one (post-riskiest-slice, pre-irreversible), the pass ends by presenting the demo and the diff for human sign-off rather than by rolling into the next slice.

**Outputs.** Closed slice: gate-verified, clean-clone green, recorded, tagged; harness amendments landed; checkpoint honored.

**Exit criteria.** A fresh session reading only the repo would correctly state: slice N done, state X, next is slice N+1 — and would inherit a strictly stronger harness than this session started with.

---

### B8 — Session and Context Management (continuous)

**Purpose.** Protect the finite resource every other stage runs on: the context window and the session's coherence.

**Inputs.** The running session; the one-slice scope lock from B0.4.

**Atomic actions.**
- B8.1 — Enforce the scope lock continuously: unrelated questions, new feature ideas, and opportunistic refactors go to the parking list. The kitchen-sink session — one task, then an unrelated ask, then back — degrades everything after the detour and is the most common self-inflicted quality collapse.
- B8.2 — Delegate heavy reads to subagents: exploration that would ingest many files (tracing an unfamiliar dependency, surveying usage of a symbol) runs in an isolated context that returns a summary, keeping the main window for construction.
- B8.3 — Watch the budget honestly: when the pass approaches context exhaustion mid-slice, stop at the current increment boundary — green, committed — and write the intra-slice handoff into PROGRESS.md (increment ladder position, current state, exact next step). A deliberate mid-slice checkpoint beats an involuntary mid-edit death by a wide margin.
- B8.4 — Prefer ending over compacting when the choice exists at a clean boundary: a fresh session bootstrapping from honest files (B0) is more reliable than a long session running on a lossy summary of itself.
- B8.5 — Keep narrative out of the window: the session's job is edits, commands, and results. Long self-explanations spend the budget that increments need.

**Outputs.** A session that either closes its slice or checkpoints cleanly inside it — never one that dies mid-edit with its knowledge unrecorded.

**Exit criteria (continuous).** At any moment, an abrupt session end would cost at most the current increment.

---

### B9 — Loop Health: Thrash Detection and Escalation (continuous)

**Purpose.** Detect when the loop has stopped converging and route the failure upward — to the plan, the spec, or the harness — instead of grinding.

**Inputs.** The pass's own event stream: gate blocks, repeated failures, reviewer round counts.

**Atomic actions.**
- B9.1 — Track the thrash signals: the same test failing after three genuinely different fix attempts; the Stop gate blocking many consecutive times; the reviewer and implementer looping past two fix-and-re-review rounds on the same finding; an increment ballooning past its expected size.
- B9.2 — On a thrash signal, stop generating attempts and reclassify the problem: is this an implementation problem (stay in B6 with a fresh hypothesis), a plan problem (the slice was mis-sized or mis-ordered → split it or re-order via a small PLAN.md amendment), a spec problem (the requirement is ambiguous or wrong → one-turn spec amendment per Step 1's re-entry rule), or a harness problem (a gate is fighting legitimate work → a deliberate, logged loosening per Step 2's calibration rule)? The reclassification question is cheap; a day of grinding on a mis-classified problem is not.
- B9.3 — Use fresh eyes as the escalation of first resort: a new context (or second model) given only the files and the failing state, asked to diagnose without the current session's accumulated framing. Half of thrash is frame-lock, and frame-lock does not survive a context boundary.
- B9.4 — Honor the kill criteria: if the spec's kill/pivot conditions (Step 1, S8.5) are observably met — the core dependency can't do what the plan assumed, the approach's cost has invalidated the constraint sheet — the correct output of the pass is a documented stop and a re-spec recommendation, not a heroic workaround. Escalating honestly is a successful outcome of the loop, not a failure of it.
- B9.5 — Record every escalation and its resolution in PROGRESS.md: which signal fired, how the problem was reclassified, what changed. Escalation patterns across slices are themselves harness/spec feedback.

**Outputs.** Either a converging loop, or a clean, recorded escalation to the layer that actually owns the failure.

**Exit criteria (continuous).** No failure gets more than three genuinely different attempts at the same layer before the layer itself is questioned.

---

## Part 3 — The Master Prompt (Paste-Ready)

Paste this as the opening message of each build session, in the agentic coding environment, with the repo (SPEC.md, PLAN.md, PROGRESS.md, HARNESS.md) present. It executes one full slice pass, B0–B9.

```
You are running a BUILD PASS — Step 3 of an architect/harness/build workflow.
The spec and plan are FROZEN; the harness is live. Your single job this
session is to take the next incomplete slice in PLAN.md from its current
state to its exit gate — one slice, nothing else — with verification as the
engine of the work, not its tail.

OPERATING RULES (bind for the whole session)
R1. One slice, one pass. Anything adjacent — ideas, refactors, unrelated
    fixes — goes to a PARKING LIST in PROGRESS.md, not into this diff.
R2. Files over memory. The repo's files and command outputs are the only
    truth. Prior sessions' reasoning does not exist; recollection of the
    codebase is not evidence — read the actual files you touch.
R3. Red before green. Every new behavior's test is WITNESSED FAILING before
    it passes. A test born green proves nothing.
R4. The exit gate is the only definition of done. Not code that looks
    complete, not a quiet reviewer — the gate, checked mechanically.
R5. Never weaken a check to pass it. Deleting/loosening a failing test or
    dodging a hook converts a visible failure into an invisible one. If a
    check is genuinely wrong, changing it is its own justified, logged
    increment.
6. Small steps, always committed. One increment per edit-test-commit cycle;
    a red repo is fixed before anything else proceeds.
R7. Read actual outputs. Judge every command by what it printed, not what it
    was expected to print.
R8. Three-attempt ceiling. No failure gets more than three genuinely
    different attempts at the same layer before the layer itself is
    questioned (see STAGE B9).
R9. Do not proceed past a stage until its exit check passes. Announce stage
    transitions in one line.

EXECUTE THESE STAGES IN ORDER:

STAGE B0 — BOOTSTRAP AND LOCK.
Read PROGRESS.md, then the next incomplete slice's PLAN.md entry (only that
slice), then HARNESS.md's canonical commands. Cross-check the record against
reality: run the canonical test target, skim recent git log. If reality
contradicts PROGRESS.md, reconciling that discrepancy is your first task.
Verify any time-decaying external facts this slice's plan/risk entries
flagged, against live sources. State in one paragraph: the slice, its exit
gate verbatim, and the verified current state. Open the parking list.
Exit check: the working statement matches both files and repo; exactly one
slice is in scope.

STAGE B1 — SLICE MICRO-SPEC.
Read the actual code this slice touches. Decompose the slice into an ordered
ladder of small increments — each independently checkable, each leaving the
repo working — with the slice's RISKIEST increment first. For each increment
name its check (which test, at which rung: in-prompt / goal condition / Stop
gate / reviewer) and whether that check exists yet. Place the slice's
unhappy paths (from its FR entries) in the ladder as first-class increments.
If the exit gate is ambiguous as written, STOP and surface it as a spec
defect for a one-turn amendment — do not privately reinterpret it.
Exit check: every requirement number the slice satisfies maps to an
increment; every increment has a named check; riskiest increment leads.

STAGE B2/B3 — THE INNER LOOP (run per increment, up the ladder).
For each increment: (a) write or update its test and WITNESS IT RED; (b)
make the smallest edit that should turn it green; (c) let the post-edit hook
speak and fix its findings immediately; (d) run the relevant canonical
checks — the whole repo must still be green; (e) commit with an
intent-stating message. Test the requirement's observable behavior, not the
implementation's internals. Keep the fast suite fast — anything slow is
marked for the CI tier. When a bug is found by any means other than a test,
write the test that would have caught it BEFORE fixing it.
Exit check (per increment): witnessed red→green, hooks clean, repo green,
committed. Ladder complete when all increments including the end-to-end
check are green.

STAGE B4 — WIRE THE COMPLETION.
Express the exit gate as a machine-checkable condition (tests + demo step).
State the verification mode for this pass: interactive (in-prompt checks +
Stop gate backstop) or unattended (exit gate as a goal condition re-checked
every turn + hard Stop gate). When the Stop gate blocks, its reported
failure is the next increment; repeated consecutive blocks are a B9 thrash
signal, not something to wait out.
Exit check: the gate exists as a checkable condition; no check was weakened
anywhere without a logged justification.

STAGE B5 — INDEPENDENT REVIEW.
Invoke the verification reviewer in a FRESH context (subagent or second
session), giving it ONLY: the slice diff, the exit gate, and the requirement
texts — not your narrative. Its charge, verbatim: "Try to construct the
input, sequence, or state that breaks this slice's exit gate or violates its
requirements. Flag ONLY gaps that affect correctness or the stated
requirements; demonstrate each with a failing input or requirement number."
Route findings: correctness/requirement gaps → new increments with
regression tests, fixed and re-reviewed in this pass; below-bar improvements
→ parking list; misfires → dismissed with one recorded line. Loop
fix-and-re-review until the reviewer can no longer demonstrate a gap at the
bar. Before invoking it, run the self-adversarial pass yourself: "knowing
everything you now know, what would you scrap and rebuild? Prove the slice
survives its exit gate."
Exit check: fresh-context reviewer cannot demonstrate a remaining
correctness/requirement gap.

STAGE B6 — DEBUGGING DISCIPLINE (whenever a defect appears, any stage).
Reproduce minimally before hypothesizing. Isolate by bisection over the
commit history (this is why increments are committed) or by halving the
input/path. State the leading cause as a falsifiable hypothesis plus the
cheapest alternative; run the ONE probe that distinguishes them; confirm
before changing code. Fix the cause minimally; if only a symptom-level
workaround is honestly available, say so in the commit and log the true
cause as debt in PROGRESS.md. Land the regression test WITH the fix — a fix
without its guard is not done. One change at a time throughout.
Exit check: repro passes, guards exist, nothing was fixed that wasn't first
understood.

STAGE B7 — CLOSURE.
Run the exit gate end-to-end from the committed state, including the demo
script performed literally. Run the full pre-commit/CI ladder — the slice
closes on a clean clone's green. Update PROGRESS.md as part of closure:
slice done (date), honest one-paragraph state, next slice named, parking
list and debt carried forward — written for a stranger. Commit/tag the
closure. Then run the REINFORCEMENT PASS: every miss this session (defect,
style slip, process stumble) becomes a mechanism NOW — a lint rule, hook
tweak, or HARNESS.md amendment with a dated entry (regression tests already
landed with their fixes). If PLAN.md marks this slice as a human checkpoint,
end by presenting the demo and diff for sign-off instead of proceeding.
Exit check: a fresh session reading only the repo would state the project's
position correctly, and inherits a strictly stronger harness.

STAGE B8 — CONTEXT DISCIPLINE (continuous).
Enforce the scope lock; park everything adjacent. Delegate many-file
exploratory reads to an isolated subagent that returns a summary. If context
runs short mid-slice: stop at the current increment boundary green and
committed, write the intra-slice handoff into PROGRESS.md (ladder position,
state, exact next step), and end the session cleanly. Prefer ending at a
clean boundary over compacting past it. Keep self-narrative out of the
window.
Standing check: at any moment, an abrupt end would cost at most the current
increment.

STAGE B9 — THRASH AND ESCALATION (continuous).
Thrash signals: same test failing after three genuinely different fixes;
many consecutive Stop-gate blocks; reviewer loops past two rounds on one
finding; an increment ballooning past its size. On a signal, STOP generating
attempts and reclassify: implementation problem (new hypothesis in B6), plan
problem (split/reorder the slice via a small PLAN.md amendment), spec
problem (one-turn amendment per the spec's re-entry rule), or harness
problem (deliberate, logged gate calibration). Escalation of first resort:
fresh eyes — a new context given only the files and the failing state. If
the spec's kill/pivot criteria are observably met, the pass's correct output
is a documented stop and re-spec recommendation, not a heroic workaround.
Record every escalation in PROGRESS.md.
Standing check: no failure exceeds the three-attempt ceiling at one layer.

Begin now with STAGE B0.
```

---

## Part 4 — The Decision Bank

The judgment calls each stage must make well. Use when a stage needs deeper drilling.

**Bootstrap (B0)**
- Does the repo agree with PROGRESS.md? (Any disagreement is the first task, not a footnote.)
- What in this slice depends on the outside world, and has the outside world been checked today?

**Micro-spec (B1)**
- What is the one increment in this slice most likely to fail? (It goes first.)
- Which unhappy paths does this slice own, and are they increments or afterthoughts?
- Is the exit gate checkable exactly as written? (If interpretation is needed, the spec — not the interpretation — gets fixed.)

**Inner loop (B2/B3)**
- Is the repo green right now? (The only acceptable answers are "yes" and "fixing that before anything else.")
- Has this test been seen red? (If not, it hasn't earned trust.)
- Is this assertion about the requirement's observable promise, or about the code's current shape?

**Verification wiring (B4)**
- Which rung does each check deserve: is a prompt enough, or does an unattended stretch need the goal condition?
- Is anyone about to make a red check green by touching the check instead of the code?

**Review (B5)**
- Did the reviewer get a fresh context and only the evidence — or did it inherit the author's story?
- Is each finding demonstrated (failing input, requirement number) or asserted?
- Is the fix loop converging, or has it become a two-round-plus thrash signal?

**Debugging (B6)**
- Has the failure been reproduced, or is the "fix" aimed at a theory?
- What one probe distinguishes the two leading hypotheses?
- Where is the regression guard for this fix?

**Closure (B7)**
- Was the demo script performed, literally?
- What did this session's misses teach, and which mechanism now encodes each lesson?
- Would a stranger reading PROGRESS.md know exactly where to start?

**Context (B8)**
- Is this session still about one slice?
- If the window died right now, what would be lost? (If the answer exceeds one increment, checkpoint.)

**Escalation (B9)**
- Is this the third genuinely different attempt? (Then the question changes from "what's the fix" to "which layer is wrong.")
- Would fresh eyes with no narrative see this differently?

---

## Part 5 — Output Artifact Templates

### 5.1 PROGRESS.md entry at slice closure (B7.3)

```
# PROGRESS — [PROJECT_NAME]

## Completed
Slice [N] — [NAME] — done [DATE]. Exit gate verified end-to-end incl. demo
step; clean-clone CI green at [COMMIT/TAG].

## Current state
[One honest paragraph a stranger could act on: what works, what is deployed,
any debt items with their locations.]

## Next
Slice [N+1] — [NAME]. First increment: [EXACT NEXT STEP].

## Parking list
- [Deferred idea/improvement] (raised during slice [N])

## Debt
- [Workaround at FILE:LOCATION] — true cause: [X] — logged [DATE]

## Escalations log
- [DATE] slice [N]: [signal] → reclassified as [layer] problem → [resolution]
```

### 5.2 Intra-slice checkpoint (B8.3 — context ran short)

```
## Current state — MID-SLICE CHECKPOINT [DATE]
Slice [N] in progress. Increment ladder position: [K of M] complete, all
committed and green at [COMMIT].
State: [what the last green increment established].
EXACT NEXT STEP: [increment K+1: the test to write / the edit to make].
Nothing uncommitted. No red checks.
```

### 5.3 Verification reviewer charter (B5.1–B5.2 — subagent or fresh session)

```
You are reviewing a completed slice in a fresh context. You receive ONLY:
(1) the diff, (2) the slice's exit gate, (3) the requirement texts it
claims to satisfy. You have no access to the author's reasoning, and that
is deliberate.

Your charge: try to construct the input, sequence, or state that breaks the
exit gate or violates a listed requirement. Check the unhappy paths named
in the requirements explicitly.

Flag ONLY findings that affect correctness or the stated requirements.
Demonstrate each finding: the failing input, the reproduction step, or the
requirement number violated. Style preferences, speculative robustness, and
improvements beyond the requirements are out of scope — the harness owns
style, and the plan owns scope.

If you cannot demonstrate a gap at this bar, say exactly that: "No
demonstrable correctness or requirement gap found against the exit gate."
That sentence is a legitimate and complete review.
```

### 5.4 Self-adversarial pre-review prompt (B5.6)

```
Before requesting independent review, answer against your own slice:
1. Knowing everything you know now, which part would you scrap and
   re-implement, and why isn't that reason a defect?
2. Prove the slice survives its exit gate: walk the demo script literally
   and quote the actual outputs.
3. Which unhappy path did you test least? Run it now.
4. Diff your work against the requirement texts line by line: which FR
   clause has the weakest test?
```

---

## Part 6 — The Build-Pass Quality Checklist

Run at closure (B7). A "no" names the stage to revisit.

1. The pass began by reading PROGRESS.md, PLAN.md (one slice), HARNESS.md — and reconciled any record/reality gap first. (B0)
2. Exactly one slice was in scope; everything adjacent is on the parking list. (B0/B8)
3. The exit gate was used verbatim; any ambiguity went back to the spec rather than being privately interpreted. (B1)
4. The riskiest increment ran first. (B1)
5. Unhappy paths were increments, not cleanup. (B1/B3)
6. Every new behavior's test was witnessed red before green. (B3)
7. Tests assert observable requirements, not internal structure. (B3)
8. The end-to-end check exists and passed. (B3)
9. Increments were committed individually with intent-stating messages. (B2)
10. No check was weakened to pass; any check change was its own justified increment. (B4)
11. The Stop gate's blocks were treated as work items, not waited out. (B4)
12. Review ran in a fresh context with evidence only — no author narrative. (B5)
13. The reviewer's charge included the calibration clause (correctness/requirements only, demonstrated). (B5)
14. Every accepted finding landed with a regression test; every dismissal has a recorded line. (B5)
15. Every defect was reproduced before it was "fixed," and bisection used the commit history. (B6)
16. Every fix carries its guard; every honest workaround is logged as debt. (B6)
17. The exit gate ran end-to-end at closure, demo script performed literally. (B7)
18. Closure happened on a clean clone's green, not a working directory's. (B7)
19. PROGRESS.md was updated as part of closure, written for a stranger. (B7)
20. The reinforcement pass ran: every session miss became a mechanism with a dated entry. (B7)
21. Human checkpoints from PLAN.md were honored rather than rolled through. (B7)
22. At no point would an abrupt session end have cost more than one increment. (B8)
23. No failure got more than three genuinely different attempts at one layer. (B9)
24. Any met kill criterion produced a documented stop, not a heroic workaround. (B9)

**Anti-patterns this checklist exists to catch**
- *The memory build*: coding against a recollection of the repo instead of its files; the session's first surprise arrives as a broken assumption mid-diff.
- *The green-born test*: tests written after the code and never seen failing — a suite that measures nothing and reassures everyone.
- *The check massage*: the assertion loosened, the test skipped, the hook bypassed — a red converted to invisible instead of to fixed.
- *The kitchen-sink session*: one slice, then an unrelated question, then a refactor — and a context window that no longer knows what it's building.
- *The self-certifying author*: "I reviewed it myself carefully" — the one context structurally incapable of the finding doing the finding.
- *The uncalibrated reviewer*: prompted to "find issues," it always does; three rounds later the slice has defensive abstractions and no additional correctness.
- *The symptom patch*: the error message silenced, the cause alive; the same bug re-billed to a future session with interest.
- *The heroic grind*: attempt eleven at the same layer, when attempt four should have re-asked which layer was wrong.
- *The silent death*: the session ends at context exhaustion mid-edit, and the next session inherits an undocumented crime scene.
- *The skipped reinforcement*: the pass's lessons evaporate at closure, and the project pays the same tuition every week.

---

## Part 7 — Usage Notes

**Where to run it.** In the agentic coding environment, inside the Step 2 harness — the master prompt assumes live hooks, canonical commands, and a Stop gate, and it degrades without them. Interactive mode suits slices with judgment calls; unattended mode (goal condition + hard Stop gate) suits well-specified mechanical slices, and the plan's human checkpoints mark where unattended stretches must end.

**Sequencing with Steps 1 and 2.** The loop consumes what the earlier steps froze: the exit gates come from the spec, the gates' enforcement from the harness, the slice order from the plan. When the loop keeps fighting one of them, that is B9's whole point — the loop escalates to the owning layer through a small, dated amendment rather than absorbing the mismatch as permanent friction.

**Scaling down.** On a small project the stages compress but keep their order: bootstrap is one minute of reading, the ladder is three increments, review is a second fresh session rather than a configured subagent. The two stages never safe to skip at any size: witnessing tests red (B3.1 — everything downstream trusts the tests, and unwitnessed tests deserve no trust) and independent review in a fresh context (B5 — the author's blindness to the author's work is not a discipline problem, it is a structural one, and only a context boundary fixes it).

**The rhythm across slices.** Each pass leaves three things better than it found them: the product (one more slice at its gate), the record (a PROGRESS.md a stranger could resume from), and the machine (a harness that mechanically prevents this pass's mistakes). The first is the visible work; the second is what makes the next session possible; the third is what makes the tenth session dramatically better than the first. A build loop that ships slices but skips the record and the reinforcement is spending the project's compounding to buy this week's velocity — the one trade this whole system exists to refuse.
