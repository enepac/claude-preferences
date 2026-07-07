# Harden As You Go: Engineering the Compounding Harness at Atomic Resolution

**A complete process discovery and paste-ready master prompts for Step 5 of AI-assisted app development: the reinforcement loop that converts every observed miss into a permanent mechanism, so the harness appreciates while everything else depreciates.**

---

## Part 1 — Orientation: What Hardening-As-You-Go Is and Why It Exists

### 1.1 The one asset that compounds

Every other artifact in an AI-assisted project depreciates. Code rots as dependencies move. Specs drift from reality. Progress files describe last week. Context windows die outright. One asset moves the other way: the harness. Add one lint rule and every future session avoids that mistake, forever, at zero marginal cost. Add one test and every future session catches that regression. Add one hook and an entire class of review comments stops existing. Harness investment is the only work in the project with a permanently increasing return — but only if it is actually made, continuously, at the moment each lesson appears.

Step 2 built the initial harness; Step 3 and Step 4 consumed it. Step 5 is the standing loop that runs across all of them: **every observed miss becomes a mechanism, the same day, and the mechanism set itself is maintained so it keeps compounding instead of decaying into bloat.** It is the difference between a project whose tenth week is dramatically better than its first and a project that pays the same tuition every week.

### 1.2 The failure this step exists to prevent, stated precisely

When an agent makes a mistake, the natural response is the **prompt patch**: add a sentence to the instructions ("remember to always X"). Prompt patches feel like fixes and behave like decay. They cost context every session, they weaken as instructions accumulate (the more rules in the always-loaded set, the less reliably any one of them fires), and they don't bind — the first long session under pressure ignores them. Meanwhile the alternative — a mechanism (a test, a lint rule, a hook, a permission, a schema constraint) — costs context never, weakens never, and binds always.

So the step's core discipline is a routing rule: **misses route to mechanisms, not to stronger words.** And its second-order discipline is that the mechanism set itself is an asset under management — measured, pruned, and prevented from becoming the very bloat it was meant to replace.

### 1.3 The enforcement ladder (the destination hierarchy for any lesson)

Every lesson learned can land at one of five layers, ordered from strongest to weakest binding. The rule of the step: land every lesson at the **highest layer that can hold it**, and treat landing at a lower layer than possible as a defect.

| Layer | Binds | Costs | Example landing |
|---|---|---|---|
| **1. Structural** (schema, type, permission, config) | Always — violation is impossible | Nothing per session | "Agent deleted a protected path" → permission deny rule |
| **2. Deterministic gate** (test, lint rule, hook, CI check) | Always — violation is caught | Milliseconds per run | "Bug shipped in date parsing" → regression test; "same style slip" → lint rule |
| **3. Checked process** (checklist item enforced by a Stop gate or command) | When the checkpoint runs | Seconds | "Progress file keeps going stale" → Stop-gate checklist item |
| **4. Advisory memory** (a line in the always-loaded memory file) | Sometimes — decays with volume | Context every session | "Prefer library X's idiom here" (genuinely un-mechanizable judgment) |
| **5. Conversation** (saying it in the session) | This session only | The lesson dies at window close | Never an acceptable final landing |

Layers 1–2 are where compounding lives. Layer 4 is a scarce budget, not a dumping ground. Layer 5 is where lessons go to die.

### 1.4 The operating principles of the whole step

1. **Miss → mechanism, same day.** The window between observing a failure and landing its mechanism is where lessons evaporate. "I'll add a test for that later" is the compounding loop's death sentence.
2. **One miss, one classified entry, one landing.** Every miss gets sensed, logged with its origin, and routed — even when the routing decision is "log only, no mechanism yet." Unlogged misses can't aggregate into patterns.
3. **Point fix plus pattern check.** Fixing the instance is table stakes. Every fix also asks: is this the third time this *class* has occurred? Recurrence converts point fixes into structural fixes at the layer that actually failed.
4. **A recurred fix is a louder alarm than a new miss.** When a miss repeats *after* its mechanism landed, the mechanism failed — re-escalate immediately, replace (don't re-patch) the failed fix, and count it, because a rising recurrence rate indicts the improvement system itself.
5. **The mechanism set is pruned as deliberately as it is grown.** Rules that never fire, tests that test nothing, memory lines gone stale — each costs attention or context forever. Subtraction is maintenance, and a harness that only ever grows is on the same decay curve as the prompt patches it replaced.
6. **Measure or it didn't happen.** A mechanism whose firing is never observed is faith, not engineering. Lightweight telemetry — which rules fire, which catch, which never do — is what separates a managed asset from a hoard.
7. **The loop serves the build.** Hardening is judged by defects prevented per minute invested, not by the elegance of the improvement system. Meta-work that stops paying is itself a miss to log.

---

## Part 2 — The Atomic Process Map

Step 5 decomposes into ten stages, C0 through C9. C1–C4 form the **inner reinforcement loop**, running whenever a miss is observed (many times per week). C5 runs on pattern thresholds. C6–C8 run as **periodic maintenance** (a scheduled session). C9 governs the whole improvement system. Each stage: **Purpose → Inputs → Atomic actions → Outputs → Exit criteria**.

---

### C0 — The Ledger: Making Compounding Visible

**Purpose.** Establish the bookkeeping that turns "we fix things sometimes" into an auditable, compounding asset. Runs once, then persists.

**Inputs.** The Step 2 harness (HARNESS.md); the repo.

**Atomic actions.**
- C0.1 — Create the **miss log**: one append-only table, one row per observed miss, with columns for date, description, origin classification (C2's taxonomy), the mechanism landed (or "logged only"), and a recurrence marker. This file is the loop's memory and its pattern detector.
- C0.2 — Create the **amendment log** inside HARNESS.md (if Step 2's freeze didn't already): every post-freeze mechanism gets a dated entry naming the miss that motivated it. A mechanism without a recorded motivation becomes an unexplainable rule someone later deletes — or fears to.
- C0.3 — Define the loop's three metrics, cheap enough to actually maintain: **mechanisms landed** (per week), **recurrence rate** (misses that repeat after a mechanism landed ÷ mechanisms landed), and **never-fired set** (mechanisms with no observed catch since landing — pruning candidates, or victories, distinguished in C7).
- C0.4 — Bind the loop to an existing reliable trigger rather than to memory: the reinforcement pass runs inside every session's closure protocol (Step 3, B7.5 / Step 4, L5), so it fires whenever work ends rather than whenever someone remembers.

**Outputs.** Miss log; amendment log; three metrics defined; the loop wired to session closure.

**Exit criteria.** A miss observed anywhere in the workflow has exactly one place to land and a mechanical moment at which landing happens.

---

### C1 — Miss Sensing

**Purpose.** A miss can only compound into a mechanism if it is *noticed as a miss*. Most aren't — they're absorbed as "the agent being the agent." This stage widens the sensor.

**Inputs.** The live workflow: sessions, reviews, gates, user corrections.

**Atomic actions.**
- C1.1 — Enumerate the sensing channels and treat each as a miss source, not just an event: a human correcting or redirecting the agent; a reviewer finding a defect; a gate blocking (especially repeatedly); a bug found after "done"; a session dying mid-edit; a boot-time record/reality mismatch; the agent visibly acting on stale memory; the same clarification being typed for the third time.
- C1.2 — Apply the sensing rule of thumb: **any moment a human thinks "again?" or "I shouldn't have had to say that" is a miss**, regardless of how small. Smallness is irrelevant — the mechanism costs once and pays forever, so even trivial recurring annoyances clear the bar.
- C1.3 — Sense process misses, not just product misses: a skipped protocol step, a squeezed-out end protocol, a verification that got hand-waved. Process misses are upstream of product misses and land at higher-leverage layers.
- C1.4 — Sense *successes that were accidents*: a near-miss caught by luck (a human happened to look) rather than by mechanism is logged as a miss for the mechanism that should have caught it.
- C1.5 — Keep sensing cheap: noticing plus one line of capture. Sensing that requires ceremony gets skipped, and a skipped sensor is a dead loop.

**Outputs.** A steady stream of noticed misses reaching C2 instead of evaporating.

**Exit criteria (continuous).** Misses surface at the moment they occur; the miss log grows during real work, not only during retrospectives.

---

### C2 — Miss Capture and Classification

**Purpose.** Convert a noticed miss into an aggregatable record. Classification is what lets ten small entries reveal one structural problem.

**Inputs.** A sensed miss; the miss log.

**Atomic actions.**
- C2.1 — Log one row, immediately, in the same working session: date, one-line description, classification, landing decision. Deferred logging is unlogged logging.
- C2.2 — Classify by **origin stage** — where in the pipeline the failure was born, not where it was caught: intent/spec (the wrong thing was specified or interpreted), scope (something needed wasn't in the plan), assumption (an unverified belief was built on), build (correct target, defective construction), verification (the check existed but didn't catch, or didn't exist), process (a protocol step skipped or mis-sequenced), staleness (action taken on outdated memory or records). One category per miss — the discipline of choosing forces the diagnosis.
- C2.3 — Record where it was *caught* as well: hook, gate, review, human, production. The distance between origin and catch is the cost of the miss and points at which layer needs the new mechanism (a build defect caught by a human means the verification layer has the hole).
- C2.4 — Run the tally: after logging, glance at the category counts. This is the pattern detector C5 consumes, and the read costs five seconds.
- C2.5 — Keep the log honest about Claude-side and human-side misses alike: a human who skipped the review checkpoint is a process miss like any other, and the mechanism (make the checkpoint mechanical) is the same kind of fix.

**Outputs.** A classified, tallied, append-only record of every miss.

**Exit criteria (per miss).** The row exists before the session ends; the category was chosen deliberately, not defaulted.

---

### C3 — Mechanism Selection: Routing the Lesson

**Purpose.** Choose *where on the enforcement ladder* the lesson lands. This is the step's central judgment, and the default answer is always "one layer higher than feels natural."

**Inputs.** The classified miss; the enforcement ladder (1.3); the existing mechanism set.

**Atomic actions.**
- C3.1 — Ask the ladder question top-down: can a structural change make this violation *impossible* (schema constraint, type, permission, config)? If not, can a deterministic gate make it *always caught* (regression test, lint rule, hook, CI check)? If not, can a checked process make it caught at checkpoints (Stop-gate checklist item, command step)? Only when all three genuinely fail does the lesson earn a memory line — and "it was easier to write a sentence" is not a failure of the first three.
- C3.2 — Match mechanism type to origin class: build defects → regression tests; recurring style/idiom slips → lint rules or post-edit hook logic; dangerous-action classes → permissions or guard hooks; skipped process steps → gate checklist items or command steps; staleness → freshness rules and boot verification; verification holes → new checks at the rung that should have caught it; intent/spec misses → spec amendments plus, when recurring, a sharpened interview question in the Step 1 protocol itself.
- C3.3 — Before creating anything, check whether an **existing mechanism should have caught this** and didn't. If yes, the fix is sharpening that mechanism (tighter assertion, wider lint scope, stricter gate), not adding a sibling. Two half-firing rules on one topic are worse than one that fully fires — additions are the exception, sharpening is the default.
- C3.4 — Size the mechanism to the smallest thing that closes the hole. A miss about one edge case earns one test, not a testing framework; a miss about one command earns one checklist line, not a new protocol document.
- C3.5 — For the rare genuinely un-mechanizable lesson (a taste judgment, a contextual preference), admit it to the advisory-memory layer deliberately, counting the cost: the memory file is a fixed budget (Step 2 set the ceiling), so a line entering usually means a line leaving.

**Outputs.** A routing decision per miss: the mechanism type, its layer, and whether it's a new mechanism or a sharpening.

**Exit criteria (per miss).** The lesson landed at the highest layer that can hold it, and the "why not higher?" question was answered explicitly for anything landing at layer 3 or below.

---

### C4 — Same-Day Landing

**Purpose.** Implement the mechanism while the miss is fresh, prove it, and record it — the atomic transaction that actually banks the compound interest.

**Inputs.** The routing decision; the repo; the harness.

**Atomic actions.**
- C4.1 — Land the mechanism in the same working session as the miss whenever the mechanism is small (most are: a test, a lint rule, a checklist line — minutes each). For the rare large one, land a placeholder gate *now* (even a crude one) and log the full version as a scheduled task; the crude version catching 80% today beats the elegant version never.
- C4.2 — Prove it by violation, always: re-create the miss and watch the new mechanism catch it. This is Step 2's "no unproven gates" rule applied to every increment of hardening — a mechanism that has never fired against its own motivating miss is a decoration with a commit message.
- C4.3 — Check the mechanism doesn't fight legitimate work: run it against the current honest workflow once. A mechanism that blocks real work gets calibrated now, at birth, not resented and bypassed later.
- C4.4 — Commit the mechanism with its motivation in the message, and write the dated amendment-log entry linking mechanism → miss. Update the miss-log row with the landing.
- C4.5 — Couple landing to the correction: when the miss is one being corrected in the current session (a bug being fixed, a redirect being absorbed), the mechanism ships in the same commit-set as the correction. A correction without its mechanism is incomplete — this coupling, not memory, is what makes the loop reliable, because the correction always happens and the mechanism rides on it.

**Outputs.** A landed, violation-proven, committed, logged mechanism.

**Exit criteria (per miss).** The same failure, replayed, is now caught mechanically; the ledger shows the link.

---

### C5 — Pattern Escalation: From Point Fixes to Structural Fixes

**Purpose.** Detect when individual misses are symptoms of one deeper defect, and fix the layer that's actually broken instead of laying point fixes on it forever.

**Inputs.** The miss log's category tallies; recurrence markers.

**Atomic actions.**
- C5.1 — Apply the three-occurrence threshold: when one origin category accumulates three misses, stop point-fixing and diagnose structurally. Three intent misses indict the spec-session protocol; three assumption misses indict verification discipline; three staleness misses indict the freshness rules; three process misses indict a protocol step's enforceability. A single high-confidence observation can trigger early — three is the ceiling for ignoring, not the floor for acting.
- C5.2 — Route the structural fix to the *owning layer*: a protocol defect amends the relevant step's master procedure; a recurring task-type defect amends that task type's spec or template; a harness defect amends the harness. The structural fix is itself a mechanism and goes through C3–C4 like any other (routed, sized, proven, logged).
- C5.3 — Treat **recurrence after a fix as a regression of the fix**, the loudest signal the loop produces: re-escalate on the first recurrence (don't count to three again), mark the category as regressed in the log, and *replace* the failed mechanism rather than patching around it — the original diagnosis was wrong, and the replacement starts from re-diagnosis, not from the failed fix's framing.
- C5.4 — Track the recurrence rate (C0.3) across categories: a rising rate means fixes aren't holding, which indicts the fix *mechanism* (landing at too-low layers? unproven at birth? mis-classified origins?) rather than any one rule — a C9 governance finding.
- C5.5 — On every structural fix, sweep for siblings: the defect that produced three logged misses usually produced unlogged ones; check the adjacent surface while the diagnosis is loaded.

**Outputs.** Structural fixes at the layers that actually failed; a recurrence record that audits fix quality.

**Exit criteria.** No category sits at three-plus without a structural response; no regressed fix survives as-is.

---

### C6 — Decay Control: Pruning and Bloat Management

**Purpose.** Keep the mechanism set from becoming the new decay. Every rule, test, hook, and memory line costs something forever (run time, context, attention, false positives); a set that only grows eventually re-creates the instruction-decay problem at the mechanism layer.

**Inputs.** The mechanism set; telemetry (C7); the never-fired set.

**Atomic actions.**
- C6.1 — Run the retirement review at each maintenance session: for every mechanism, ask what it costs (latency, context, noise) versus what it has caught. Layer-1/2 mechanisms are nearly free and default to keep; layer-4 memory lines are expensive and default to justify-or-retire.
- C6.2 — Distinguish the two kinds of never-fired: **deterrent successes** (the class of miss stopped occurring because the mechanism exists — keep, cheap layers especially) versus **dead rules** (the situation they guarded never arises anymore — retire). The tiebreaker: would deleting it plausibly let the original miss recur? If the honest answer is no, it's dead.
- C6.3 — Prune the advisory-memory layer hardest and on a schedule: every line either survives the mechanization filter again (still genuinely un-mechanizable? still true? still needed?) or is converted upward / deleted. The memory file shrinks by default; growth requires displacement.
- C6.4 — Prune stale prose everywhere the agent reads: superseded plans, obsolete notes, old TODOs — the executable-artifacts-only rule (Step 2, H1.4) enforced as recurring maintenance, because agents believe whatever they grep and staleness is an active poison, not passive clutter.
- C6.5 — Watch for false-positive fatigue as a decay signal: a gate that's routinely bypassed, an approval clicked without reading, a warning tuned out — each is a mechanism that has died socially while remaining technically alive. Recalibrate or remove; a bypassed gate trains the bypass habit for the gates that matter.
- C6.6 — Log every retirement in the amendment log with its reason, exactly like an addition. Silent deletion is how a team later re-learns, expensively, why the rule existed.

**Outputs.** A mechanism set whose every member pays rent; a memory file at or under budget; a repo with no stale prose.

**Exit criteria.** Total harness cost (latency, context, attention) is flat or falling while coverage grows — the definition of compounding rather than accreting.

---

### C7 — Telemetry and Evaluation

**Purpose.** Replace faith with observation: know which mechanisms fire, which catch, which never do, and whether the hardened harness actually performs better than last month's.

**Inputs.** Gate/hook/test run records; the miss log; the metrics from C0.3.

**Atomic actions.**
- C7.1 — Instrument cheaply: mechanisms that catch something should leave a trace (a hook writing one line to a firing log, CI's own history, test failure records). Perfect telemetry is unnecessary; *any* telemetry beats none, and the budget is minutes.
- C7.2 — Read the traces at each maintenance session: catches per mechanism feed C6's retirement review; catch *location* (which rung) verifies the feedback-speed ladder is working (catches should cluster at fast rungs; a pattern of CI-only or human-only catches means faster rungs have holes).
- C7.3 — Maintain a small **held-out check set**: a handful of past misses, kept as replayable cases, run against the harness after significant changes. This answers the question growth alone can't: does the *current* harness still catch the *old* misses? A hardening change that silently un-catches an old miss is a regression the check set makes visible.
- C7.4 — Review the three metrics as trends, not points: mechanisms landing steadily (the loop is alive), recurrence rate flat or falling (fixes hold), never-fired set stable and explained (the set is managed). Any metric moving the wrong way for two consecutive reviews is a C9 finding.
- C7.5 — Spot-check the advisory layer's decay directly: occasionally test whether a memory line is actually being honored in fresh sessions. A consistently-ignored line is evidence for the mechanization filter, not for a sterner sentence.

**Outputs.** Firing records; a held-out check set; trend reads on the three metrics.

**Exit criteria.** Every keep/retire/sharpen decision in C6 can cite an observation; harness changes are checked against the replay set before being trusted.

---

### C8 — Portability: Harvesting Across Projects

**Purpose.** The deepest compounding crosses project boundaries. A mechanism that cost a real miss to learn should never be re-learned from scratch on the next project.

**Inputs.** The project's mechanism set and ledger; any personal/organizational harness library.

**Atomic actions.**
- C8.1 — Maintain the two-layer split: **project-specific** mechanisms (this codebase's idioms, this app's regression tests) versus **portable** mechanisms (session protocols, hook patterns, gate designs, spec templates, the miss-log format itself). Tag on landing; the tag costs nothing and the harvest depends on it.
- C8.2 — Promote deliberately: when the same portable mechanism proves out in a second context — or is obviously context-free at birth — it moves to the personal/global layer (the harness library, the global template set, the standard session prompts). Promotion is a C3–C4 transaction with its own log entry.
- C8.3 — Demote deliberately too: a "global" rule that only ever fires in one project's context belongs to that project; carrying it globally is bloat at the layer where bloat costs most.
- C8.4 — At project close (Step 4, L9.6), run the formal harvest: the ledger review that asks which mechanisms, templates, protocols, and check-set cases the next project should inherit — and packages them so inheriting is copying, not archaeology.
- C8.5 — Seed the next project's Step 2 from the library: the minimum viable harness of project N+1 starts at project N's ending maturity, which is the whole point — the compounding curve continues across projects instead of restarting.

**Outputs.** A tagged mechanism set; a growing portable library; each project starting further up the curve.

**Exit criteria.** No lesson paid for twice: a miss that produced a portable mechanism in one project cannot recur unmechanized in the next.

---

### C9 — Governance: The Improvement System's Own Health

**Purpose.** Point the loop at itself. The improvement system can fail in both directions — dying quietly (misses stop being logged) or metastasizing (meta-work displaces real work) — and both are misses of the highest order.

**Inputs.** The metrics trends (C7); the maintenance cadence; honest observation of where time goes.

**Atomic actions.**
- C9.1 — Schedule the maintenance session (C6–C8) on a cadence proportional to activity — and treat a missed cadence as a logged process miss like any other.
- C9.2 — Audit the loop's liveness: if the miss log has gone quiet during active development, the sensor died (C1), not the misses. Re-bind the reinforcement pass to its closure trigger and log the lapse.
- C9.3 — Audit the loop's proportionality: hardening time should be a small, steady fraction of build time. When maintaining the improvement system starts crowding out the work it exists to improve — ever-more-elaborate logs, taxonomies, meta-rules — that inversion is itself a miss, and the mechanism is simplification: collapse categories, shorten templates, cut ceremony. The loop is judged by defects prevented per minute, never by its sophistication.
- C9.4 — Audit the decision boundaries drawn throughout: the three-occurrence threshold, the memory-file budget, the maintenance cadence — each is a tunable that should move when evidence says so (a rising recurrence rate may mean the threshold is too lax; a starved memory file may mean the budget is too tight).
- C9.5 — Keep the amendment log as the system's constitution-of-record: every rule's motivation traceable, every retirement reasoned, every threshold change dated. The governance test is the amnesia test one level up: could a competent stranger, from the ledgers alone, understand why this harness is shaped the way it is — and safely continue hardening it?

**Outputs.** A loop that stays alive, stays proportional, and remains explainable.

**Exit criteria (continuous).** Both failure directions are monitored; the improvement system passes its own miss→mechanism discipline when it slips.

---

## Part 3 — The Master Prompts (Paste-Ready)

Step 5 is continuous, so it needs two prompts: the **reinforcement pass** that runs at every working session's closure (the inner loop, C1–C4 with a C5 check), and the **maintenance session** that runs on a cadence (C5–C9).

### 3.1 The Reinforcement Pass (run at the close of every working session)

```
Run the REINFORCEMENT PASS on this session before it ends. This is the
harden-as-you-go step: every miss observed this session becomes a
mechanism now, or a logged entry awaiting one. The standard is
mechanism over prompt — no lesson may land as merely a stronger
instruction when a test, lint rule, hook, permission, schema constraint,
or gate checklist item could hold it instead.

EXECUTE IN ORDER:

1. SWEEP FOR MISSES. Walk this session's events and list every miss,
   including small ones: corrections or redirects I gave you; defects
   found by review, gates, or after "done"; gates that blocked
   repeatedly; anything caught by luck (a human happened to look)
   rather than by mechanism; process slips (a skipped protocol step, a
   rushed verification, a stale record built upon); and any moment the
   reaction was "again?". Smallness does not exempt: the mechanism
   costs once and pays forever.

2. LOG EACH MISS: one row each in the miss log — date, one-line
   description, ORIGIN class (intent/spec, scope, assumption, build,
   verification, process, staleness; pick exactly one, deliberately),
   and where it was CAUGHT (hook, gate, review, human, production).
   The origin-to-catch distance names the layer with the hole.

3. ROUTE EACH MISS down the enforcement ladder, answering "why not
   higher?" out loud for anything landing below the top:
   (a) Can a STRUCTURAL change make it impossible (schema, type,
       permission, config)?
   (b) Else, can a DETERMINISTIC GATE always catch it (regression
       test, lint rule, hook, CI check)?
   (c) Else, can a CHECKED PROCESS catch it at checkpoints (Stop-gate
       checklist item, command step)?
   (d) Only if (a)-(c) genuinely fail may it become an advisory memory
       line — and the memory file is a fixed budget, so name what
       leaves to make room.
   FIRST check whether an EXISTING mechanism should have caught it:
   if yes, sharpen that mechanism instead of adding a sibling.
   Sharpening is the default; adding is the exception. Size every
   mechanism to the smallest thing that closes the hole.

4. LAND AND PROVE, same session, for every small mechanism (most
   are): implement it; PROVE IT BY VIOLATION (replay the miss, watch
   it get caught); check it doesn't block legitimate work; commit with
   the motivation in the message; write the dated amendment-log entry
   linking mechanism to miss; mark the miss-log row landed. If a
   mechanism is genuinely large, land a crude placeholder gate now and
   log the full version as a scheduled task. When a miss is one being
   corrected this session, the mechanism ships with the correction —
   a correction without its mechanism is incomplete.

5. PATTERN CHECK. Read the miss log's category tallies:
   - Any category at three or more → do not point-fix again; flag it
     as a structural finding naming the owning layer (spec protocol,
     plan, harness, process step) for a structural fix.
   - Any miss recurring AFTER its mechanism landed → louder alarm:
     mark the category REGRESSED, and flag the failed mechanism for
     REPLACEMENT (re-diagnose; do not patch the failed fix's framing).

6. REPORT in a few lines: misses logged (by class), mechanisms landed
   (by layer), anything deferred with its scheduled task, and any
   pattern flags raised. Zero misses is a legitimate report only if
   the sweep in step 1 was actually run.
```

### 3.2 The Maintenance Session (run on cadence — weekly under active development, monthly otherwise)

```
Run a HARNESS MAINTENANCE SESSION. The mechanism set is an asset under
management: today's job is to verify it still compounds — coverage
growing while total cost (latency, context, attention) stays flat or
falls — and to prune, escalate, evaluate, and harvest accordingly. No
feature work rides along.

EXECUTE IN ORDER:

1. STRUCTURAL ESCALATIONS. Open the miss log. For every category at
   three-plus without a structural fix, and every REGRESSED marker:
   diagnose the owning layer (spec protocol, plan template, harness
   gate, process step), design the structural fix or the replacement
   for the failed fix (re-diagnose from scratch; do not inherit the
   failed fix's framing), and land it through the standard route:
   smallest fix, proven by violation, committed, amendment-logged.
   While each diagnosis is loaded, sweep its adjacent surface for
   sibling defects the log missed.

2. TELEMETRY READ. From firing logs, CI history, and test records:
   which mechanisms caught something since last time, at which rung?
   Catches should cluster at fast rungs (hook > pre-commit > CI >
   human); a pattern of slow-rung or human catches names a hole at a
   faster rung — fix it. Update the three metrics as TRENDS:
   mechanisms landed (loop alive?), recurrence rate (fixes holding?),
   never-fired set (managed?). Any metric wrong-way two reviews
   running is a governance finding for step 5.

3. RETIREMENT REVIEW. Walk the mechanism set against its costs and
   catches. Distinguish never-fired DETERRENTS (the miss class
   stopped occurring because the mechanism exists — keep, especially
   at cheap layers) from DEAD RULES (deleting it would not plausibly
   let the original miss recur — retire, with a dated, reasoned
   amendment entry). Prune the advisory memory file hardest: every
   line re-passes the mechanization filter (still un-mechanizable?
   still true? still needed?) or is converted upward / deleted; the
   file shrinks by default. Sweep the repo for stale prose (old
   plans, dead notes) and delete it — agents believe what they grep.
   Flag any gate being routinely bypassed or approved-without-reading:
   it has died socially; recalibrate or remove it.

4. HELD-OUT CHECK. Replay the held-out set of past misses against the
   current harness. Every one must still be caught; any that slipped
   is a harness regression — fix before anything else lands. Add this
   period's most instructive new misses to the set (keep it small and
   replayable).

5. GOVERNANCE PASS, the loop pointed at itself:
   - Liveness: if the miss log went quiet during active development,
     the SENSOR failed, not the misses — re-bind the reinforcement
     pass to session closure and log the lapse as a process miss.
   - Proportionality: if hardening time is crowding out build time,
     the fix is SIMPLIFICATION (collapse categories, shorten
     templates, cut ceremony) — the loop is judged by defects
     prevented per minute, never by its sophistication.
   - Tunables: re-examine the three-occurrence threshold, the memory
     budget, and this cadence against the evidence; change with a
     dated entry or leave deliberately alone.
   - Explainability: spot-check that a stranger could trace, from the
     ledgers alone, why three randomly-chosen mechanisms exist.

6. HARVEST. Tag this period's new mechanisms project-specific vs
   portable. Promote proven portable ones to the global library
   (templates, protocols, hook patterns, check-set cases) with a log
   entry; demote any "global" rule that only ever fires in this
   project. If the project is closing, run the full harvest so the
   next project's minimum viable harness starts at this one's ending
   maturity.

7. REPORT: escalations landed, retirements (with reasons), telemetry
   trends, held-out result, governance findings, harvest actions —
   and the one-line verdict: is the harness compounding (coverage up,
   cost flat) or accreting (both up)? If accreting, the top item for
   next session is subtraction.
```

---

## Part 4 — The Decision Bank

The judgment calls each stage must make well.

**Sensing (C1)**
- What did I absorb this session as "the agent being the agent" that was actually a miss?
- What got caught by luck that a mechanism should have caught?

**Classification (C2)**
- Where was this miss *born* (not where it was caught)? What does the origin-to-catch distance say about which layer has the hole?
- Am I defaulting the category, or choosing it?

**Routing (C3)**
- Why can't this land one layer higher than my first instinct?
- Should an existing mechanism have caught this? (Then sharpen it — don't build its sibling.)
- What is the *smallest* mechanism that closes this hole?

**Landing (C4)**
- Has the new mechanism been seen catching its own motivating miss?
- Does it block any legitimate work I actually do?
- Is the correction shipping with its mechanism, or is the mechanism "later"?

**Escalation (C5)**
- Is this the third of its class? Then which layer — spec, plan, harness, process — actually failed?
- Did this recur *after* a fix? Then what was wrong with the original diagnosis (not just the original patch)?

**Pruning (C6)**
- What does this mechanism cost forever, and what has it caught?
- Never-fired: deterrent or dead? Would deleting it plausibly let the miss recur?
- Which memory line no longer survives the mechanization filter?

**Telemetry (C7)**
- Can every keep/retire decision I just made cite an observation?
- Would the current harness still catch last quarter's misses? (Run the replay, don't assume.)

**Harvesting (C8)**
- Which of this period's lessons would the next project otherwise pay for again?
- Which "global" rule is really this project's rule wearing a global tag?

**Governance (C9)**
- Is the loop alive (log growing during real work) and proportional (small fraction of build time)?
- Which tunable does the evidence say to move — and which am I moving out of restlessness?

---

## Part 5 — Output Artifact Templates

### 5.1 The miss log

```
# MISS LOG — [PROJECT_NAME]
Tally: intent [n] | scope [n] | assumption [n] | build [n] |
verification [n] | process [n] | staleness [n]     REGRESSED: [list]

| Date | Miss (one line) | Origin | Caught by | Mechanism landed | Rec? |
|------|-----------------|--------|-----------|------------------|------|
| ...  | ...             | build  | review    | test: [name]     |      |
| ...  | ...             | process| human     | Stop-gate item   | R!   |

Rules: one row per miss, logged in-session. One origin each, chosen
deliberately. "Rec?" marks recurrence after a landed mechanism —
re-escalate on first recurrence, do not re-count to three.
```

### 5.2 Amendment-log entry (in HARNESS.md)

```
[DATE] — ADDED [mechanism, layer] — motivated by miss [date/desc] —
proven by violation: [what was replayed] — cost note: [latency/context]

[DATE] — SHARPENED [existing mechanism] — miss [ref] slipped past it
because [gap] — now catches [what it didn't]

[DATE] — RETIRED [mechanism] — reason: [dead rule: deleting cannot
plausibly re-admit the original miss / superseded by (ref)]

[DATE] — REPLACED [failed mechanism] — original fix regressed on
[date]; re-diagnosis: [new understanding] — replacement: [mechanism]
```

### 5.3 The held-out check set

```
# HELD-OUT CHECKS — replay after significant harness changes
Each entry: the original miss, the replay step, and the mechanism
expected to catch it. All must catch; any slip is a harness
regression fixed before other work.

1. [MISS, date] — replay: [exact step/input] — expected catch:
   [mechanism @ rung]
2. ...
Keep small (a dozen or so of the most instructive); rotate in this
period's best new lessons, retire cases whose whole class is now
structurally impossible.
```

### 5.4 Maintenance-session report skeleton

```
# HARNESS MAINTENANCE — [DATE]
Escalations: [structural fixes landed / replacements of regressed fixes]
Telemetry: landed [n] | recurrence rate [x, trend] | never-fired [n,
  deterrent vs dead split]
Retirements: [each with reason]
Held-out replay: [all caught / regressions found+fixed]
Governance: [liveness, proportionality, tunable changes]
Harvest: [promotions to global, demotions to project]
VERDICT: compounding | accreting — [one line why; if accreting, the
subtraction planned]
```

---

## Part 6 — The Hardening Quality Checklist

Run at each maintenance session. A "no" names the stage to revisit.

1. Every miss observed this period has a logged row with a deliberately chosen origin class. (C1/C2)
2. The reinforcement pass fires mechanically at session closure, not by memory. (C0)
3. No lesson landed as a prompt patch when a higher layer could hold it; every below-top landing answered "why not higher?". (C3)
4. Sharpening was preferred over sibling-adding wherever an existing mechanism should have caught the miss. (C3)
5. Every landed mechanism was proven by replaying its motivating miss. (C4)
6. Corrections shipped with their mechanisms in the same session. (C4)
7. No origin category sits at three-plus without a structural fix. (C5)
8. Every recurrence-after-fix triggered immediate re-escalation and *replacement*, not a re-patch. (C5)
9. The advisory memory file is at or under budget and shrank or held this period. (C6)
10. Every retirement has a dated, reasoned entry; no silent deletions. (C6)
11. Never-fired mechanisms are split into deterrents (kept knowingly) and dead rules (retired) — none merely unexamined. (C6/C7)
12. No gate is being socially bypassed while technically alive. (C6)
13. Keep/retire/sharpen decisions cite observations, not impressions. (C7)
14. The held-out set was replayed after significant changes, and any slip was fixed first. (C7)
15. The three metrics are read as trends, and any wrong-way trend has a governance response. (C7/C9)
16. New mechanisms are tagged project vs portable at landing. (C8)
17. Proven portable mechanisms were promoted; single-project "globals" demoted. (C8)
18. Hardening time remains a small, steady fraction of build time. (C9)
19. The loop's own lapses (quiet log during active work, missed cadence) are logged as misses. (C9)
20. A stranger could reconstruct, from the ledgers alone, why the harness is shaped as it is. (C9)
21. The period's verdict — compounding vs accreting — was stated honestly, with subtraction scheduled if accreting. (C9)

**Anti-patterns this checklist exists to catch**
- *The prompt patch*: the miss becomes a sterner sentence in the memory file; it decays, the miss returns, and the file grows toward the instruction-decay cliff.
- *The absorbed miss*: "that's just how the agent is" — the sensor filters out exactly the recurring annoyances whose mechanisms would have been cheapest.
- *The later that never comes*: "I'll add the test after this feature" — the lesson evaporates in the gap between miss and landing.
- *The sibling factory*: every miss gets a brand-new rule beside the half-firing one that should have caught it, until two half-rules per topic decay each other.
- *The unproven guard*: mechanisms committed but never seen catching their own motivating miss — hardening as decoration.
- *The re-patched regression*: a fix that failed gets patched in its own broken framing, fails again, and the recurrence count restarts politely at zero.
- *The hoarder harness*: nothing is ever retired; cost climbs, false positives breed bypass habits, and the mechanism set becomes the new bloat.
- *The faith-based rule set*: no telemetry, so keep/retire is decided by fondness; deterrents get deleted and dead rules get honored.
- *The single-project immortal*: one project's quirk enshrined globally, taxing every future project with a rule that can never fire for them.
- *The meta-work spiral*: the taxonomy grows a taxonomy; logging the work displaces the work; the improvement system optimizes its own elegance instead of defects-prevented-per-minute.

---

## Part 7 — Usage Notes

**Where to run it.** The reinforcement pass (3.1) belongs wherever work happens — it is the closing move of every Step 3 build pass and every Step 4 session, bound to the closure protocol so it fires by mechanism rather than memory. The maintenance session (3.2) is its own scheduled session in the agentic environment, treated with the same seriousness as a build slice: it has an exit gate (the report and verdict) and no feature work rides along.

**Relation to the earlier steps.** Step 5 is the derivative of the whole system: Step 1 froze intent, Step 2 built the initial mechanism floor, Steps 3–4 generate the misses, and Step 5 converts those misses into a floor that rises. Its two prompts deliberately reuse the earlier steps' primitives — proof-by-violation from Step 2, the same-session coupling from Step 3's closure, the amnesia test from Steps 1 and 4 applied to the ledgers — because the reinforcement loop is not a sixth subsystem so much as the maintenance contract on the other five.

**Scaling down.** On a small or short project, the whole step compresses to three habits that cost minutes: log the miss when it happens (one line), land the smallest mechanism the same day (usually a test or a checklist line), and glance at the tally for threes. The two elements never safe to skip at any scale: **proof by violation** (an unproven mechanism is a decoration, and decorations are how the loop dies invisibly) and the **recurrence alarm** (a fix that didn't hold, treated as a fresh miss, silently converts the loop into a treadmill).

**The closing frame for the whole series.** Across the five steps, one pattern repeats at every scale: externalize the thing that would otherwise live in a mind or a conversation — intent into a spec, standards into mechanisms, verification into gates, state into artifacts, and finally *lessons* into a ledger-backed mechanism set — then trust only the externalized form, and maintain it as an asset. Step 5 is where the pattern becomes self-sustaining: a system that not only survives its workers' amnesia but gets permanently better every time it fails, which is the only kind of system worth building software with. The model provides the effort; the harness provides the memory; the hardening loop provides the direction — upward, compounding, one proven mechanism at a time.
