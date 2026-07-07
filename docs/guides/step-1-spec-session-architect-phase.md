# The Spec Session: Engineering the Architect Phase at Atomic Resolution

**A complete process discovery and a paste-ready master prompt for Step 1 of AI-assisted app development: Spec Before Code.**

---

## Part 1 — Orientation: What the Spec Session Is and Why It Exists

### 1.1 Where this step sits

In the architect-then-engineer workflow, all building is split into two phases with a hard boundary between them:

- **Architect phase (this document):** decode intent, interview for the missing decision-information, define "done" as an observable state, decompose into vertical slices, register risks and unknowns, freeze the spec.
- **Engineer phase (a separate, fresh session):** execute the frozen spec, slice by slice, with verification gates.

The boundary is not cosmetic. The two phases fail differently and need different context:

| | Architect failure | Engineer failure |
|---|---|---|
| **What goes wrong** | The wrong thing gets built, correctly | The right thing gets built, incorrectly |
| **Root cause** | Target never defined, or defined from the literal prompt instead of the real intent | Context exhaustion, skipped verification, one-shotting |
| **Where it's caught** | Only after the build, expensively | During the build, cheaply, if gates exist |
| **The fix** | This spec session | Harness + loop engineering |

A model executing a vague prompt does not fail loudly. It fails by confidently building a plausible interpretation. The spec session exists to make the interpretation explicit, interrogated, and agreed **before** any code exists, when changing direction costs a sentence instead of a refactor.

### 1.2 The two artifacts this step must produce

Everything in the spec session converges on two frozen documents:

1. **SPEC.md** — what "done" means: problem, users, scope, non-goals, functional and non-functional requirements, acceptance criteria, constraints, assumptions, unknowns.
2. **PLAN.md** — how to get there: vertical slices in dependency order, each with its own tests and exit gate, plus the risk register and the handoff instructions for the engineer session.

Anything that doesn't end up encoded in one of these two files did not happen, because the engineer session starts with **zero memory** of the spec conversation. The spec session's real customer is not the human — it is a future context window with amnesia.

### 1.3 The operating rules of the whole step

These rules govern every stage below:

1. **Interview before asserting.** The spec is extracted from the requester, not invented for them. Assumptions are allowed only when labeled as assumptions.
2. **One question at a time.** Bundled questions get half-answered. Each answer can eliminate the need for the next planned question.
3. **Done is observable.** Every requirement must be phrased so a third party (or a test) could check it. "Fast" is not a requirement; "P95 response under 800ms on the practice-question endpoint" is.
4. **Decompose vertically, not horizontally.** Slices cross all layers (data → logic → interface) so every increment is end-to-end verifiable. A finished database with no UI is unverifiable progress.
5. **Unknowns are inventory, not embarrassment.** A spec with an honest UNKNOWN register is stronger than a clean-looking spec that hid its gaps.
6. **Freeze before build.** The spec session ends with an explicit freeze. Scope changes after the freeze are new spec-session turns, not silent drift.

---

## Part 2 — The Atomic Process Map

Step 1 decomposes into ten stages, S0 through S9. Each stage below is specified as: **Purpose → Inputs → Atomic actions → Outputs → Exit criteria**. A stage may not begin until the prior stage's exit criteria hold. This is the "end-to-end at atomic level" view: every action a spec session performs belongs to exactly one of these stages.

---

### S0 — Intake and Intent Decoding

**Purpose.** Separate what the requester *wrote* from what the requester *wants*. The literal prompt is evidence about intent, not the intent itself.

**Inputs.** The raw request, in whatever form it arrives (one sentence, a rambling paragraph, a half-spec).

**Atomic actions.**
- S0.1 — Restate the request in one sentence, in your own words, as the *job to be done* ("You want X so that Y").
- S0.2 — Classify the request type: new build / extension of existing system / rebuild-replacement / exploration-prototype. The type changes how much archaeology (S1) is needed.
- S0.3 — Identify the surface form vs. the underlying job. ("Build me a flashcard app" may be the surface form of "help my users retain speaking templates under time pressure.")
- S0.4 — Detect **forks**: points where two genuinely different products satisfy the literal prompt, and the deciding fact lives only in the requester's head. Each fork becomes a mandatory interview question in S2.
- S0.5 — Detect scale signals: words like "quick," "MVP," "production," "for my portfolio," "for paying users" — these set the rigor budget for every later stage.

**Outputs.** A one-sentence intent statement, a request-type classification, a fork list, a rigor budget.

**Exit criteria.** The intent statement is confirmed (or corrected) by the requester before any deeper questioning begins. If the intent restatement is wrong, everything downstream would have been wrong with it — this is the cheapest correction point in the entire pipeline.

---

### S1 — Context and Asset Inventory

**Purpose.** Never spec in a vacuum. What already exists constrains and accelerates what will be built.

**Inputs.** Confirmed intent statement; access to any existing repos, documents, prior decisions.

**Atomic actions.**
- S1.1 — Inventory existing assets: repos and their stacks, prior specs, design documents, data already collected, accounts and API keys already held, deployed infrastructure.
- S1.2 — Inventory the builder's real constraints: hours per week, budget ceiling, deadline (hard vs. soft), skill set actually held vs. willing to learn.
- S1.3 — Inventory the reuse surface: which existing components, patterns, or prior code can be ported instead of rebuilt.
- S1.4 — Mark every inventory item as VERIFIED (seen/read directly) or CLAIMED (asserted but not inspected). Claimed items that the plan depends on become unknowns in S8.
- S1.5 — If an existing codebase is in scope, do the archaeology now: read the entry points, the data model, and the build/run scripts before asking the requester to describe them from memory. Code beats recollection.

**Outputs.** HAVE / NEED / UNKNOWN inventory table; constraint sheet (time, money, deadline, skills).

**Exit criteria.** Every asset the plan will later rely on is either VERIFIED or explicitly flagged as an assumption. No "I think there's an endpoint for that already."

---

### S2 — The Structured Interview

**Purpose.** Extract the decision-information that exists only in the requester's head. This is the heart of the spec session and the stage most often skipped by impatient builders.

**Inputs.** Intent statement, fork list (S0), inventory gaps (S1).

**Atomic actions.**
- S2.1 — Order the question queue by *elimination power*: ask first the question whose answer prunes the most downstream questions. Forks from S0.4 always rank near the top.
- S2.2 — Ask exactly one question per turn. Offer 2–4 concrete options with a flagged recommendation where options are enumerable; leave open-ended where they aren't.
- S2.3 — After each answer, re-plan the queue: strike questions the answer mooted, add questions the answer surfaced.
- S2.4 — For every answer, capture not just the choice but the *reason* — the reason generalizes to future decisions the spec doesn't anticipate.
- S2.5 — Apply the stop rule: the interview ends when remaining questions would not change SPEC.md or PLAN.md. Interviewing past that point is procrastination wearing rigor's clothes.
- S2.6 — Close the interview by reading back the five to ten load-bearing decisions in one list for a single confirm-or-correct pass.

**Question domains that must be covered or consciously skipped** (the full question bank is in Part 4):
problem & user • success definition • smallest shippable version • scope and explicit non-goals • users/auth model • data model and residency • integrations • privacy and data sensitivity • non-functional requirements (performance, availability, accessibility) • budget/hosting • timeline • maintenance expectations after ship.

**Outputs.** Decision log: every question, its answer, and the reason.

**Exit criteria.** Every fork from S0.4 is resolved; every domain above is answered or has a written "consciously skipped because…" note; the read-back was confirmed.

---

### S3 — Problem and User Definition

**Purpose.** Pin who this is for and what pain it removes, in language sharp enough to arbitrate later scope disputes.

**Inputs.** Interview answers.

**Atomic actions.**
- S3.1 — Write the problem statement: current state → pain → desired state, one short paragraph, no solution language in it.
- S3.2 — Define the primary user as a concrete person-shape (context, skill level, device, patience level), not a demographic blur.
- S3.3 — Define secondary users and non-users (people the product deliberately does not serve — this powers non-goals in S4).
- S3.4 — Write the one-line value test: the sentence a real user would say after the product worked for them. This becomes the north star for every slice-priority argument later.

**Outputs.** Problem statement, user definitions, value test line.

**Exit criteria.** The requester agrees the problem statement would still be true if the currently-imagined solution turned out to be wrong. (If the problem statement smuggles in the solution, it can't arbitrate anything.)

---

### S4 — Scope Boundary and Non-Goals

**Purpose.** The spec's most protective section. What's *out* prevents more failure than what's in.

**Inputs.** Problem/user definitions, constraint sheet.

**Atomic actions.**
- S4.1 — Draft the v1 scope as the smallest set of capabilities that passes the value test from S3.4 end-to-end for the primary user.
- S4.2 — For every capability the requester mentioned but v1 excludes, write it into NON-GOALS with a one-clause reason ("v2: after first-user validation," "cut: contradicts budget ceiling").
- S4.3 — Run the subtraction pass: for each in-scope item ask "if this were cut, does v1 still pass the value test?" Cut everything that survives the question.
- S4.4 — Run the addition pass: is anything *missing* without which the in-scope items can't actually deliver value (auth for a multi-user feature, persistence for a progress feature)? Hidden dependencies enter scope explicitly now, not as mid-build surprises.
- S4.5 — Time-box sanity check: estimate scope against the constraint sheet. If the honest estimate exceeds the deadline or hour budget, the resolution happens here — cut scope or move the deadline — never by silently hoping.

**Outputs.** IN-SCOPE list, NON-GOALS list with reasons, feasibility verdict.

**Exit criteria.** Scope fits the constraint sheet with margin, and every cut is written down with its reason so it can't be re-litigated ambiently.

---

### S5 — Requirements Synthesis

**Purpose.** Convert scope into checkable statements: what the system must do (functional) and how well it must do it (non-functional), including the unhappy paths.

**Inputs.** Scope list, decision log.

**Atomic actions.**
- S5.1 — For each in-scope capability, write functional requirements in the form: *actor + trigger + behavior + observable result*. ("When a signed-in user submits a speaking response, the system returns a rubric-scored evaluation within one screen, without a page reload.")
- S5.2 — For each functional requirement, enumerate the unhappy paths: invalid input, empty state, network failure, permission failure, concurrent action, quota exceeded. Each gets its own required behavior — "shows a clear error and preserves the user's draft" is a requirement, not a nicety.
- S5.3 — Write non-functional requirements with numbers, not adjectives: latency targets, capacity assumptions, uptime expectations, accessibility floor, browser/device matrix, cost ceiling per month.
- S5.4 — Write the data requirements: entities, their relationships, what must persist, what must never persist (PII decisions land here), retention and deletion rules.
- S5.5 — Write the security floor: auth model, secrets handling, input validation posture, what an attacker must not be able to do.
- S5.6 — Trace every requirement back to an interview answer or a labeled assumption. Requirements with no traceable origin are inventions — delete them or take them back to the interview.

**Outputs.** Numbered requirement set (FR-1…, NFR-1…, DR-1…, SR-1…), each traceable.

**Exit criteria.** Every requirement is testable as written; no adjectives standing where numbers should be; unhappy paths covered for every user-facing flow.

---

### S6 — Done-Definition and Acceptance Criteria

**Purpose.** Compress the requirement set into the single observable end-state that means "ship," plus per-slice acceptance gates the engineer phase will be held to.

**Inputs.** Requirement set.

**Atomic actions.**
- S6.1 — Write the global done-definition as a scene, not a status: *who* does *what* and *what they observe*, on the deployed system. ("A new user signs up on the live URL, completes one full practice test with audio, sees their scored results, and their history persists on next login.")
- S6.2 — For each requirement cluster, write acceptance criteria in given/when/then or checklist form — the exact conditions the engineer session's verification step will check.
- S6.3 — Separate acceptance ("does what the spec says") from quality gates ("tests pass, lint clean, no console errors, deploys from a clean clone") — the engineer harness enforces the latter; the spec owns the former.
- S6.4 — Define the demo script: the 2-minute walkthrough that proves done. If the demo script can't be written now, "done" is still fuzzy — return to S6.1.

**Outputs.** Global done-definition, per-cluster acceptance criteria, demo script.

**Exit criteria.** A stranger with the demo script and the deployed product could verify done without asking anyone anything.

---

### S7 — Decomposition into Vertical Slices and the Phase-Gated Plan

**Purpose.** Turn "done" into an ordered sequence of small, end-to-end verifiable increments — the plan the engineer session will execute one slice at a time.

**Inputs.** Requirements, acceptance criteria, inventory (S1).

**Atomic actions.**
- S7.1 — Cut the build into vertical slices: each slice crosses every layer it touches (data → server → interface) and produces something a user could, in principle, touch. Reject horizontal slices ("all the models first") — they defer verifiability.
- S7.2 — Slice 0 is always the walking skeleton: the thinnest possible end-to-end path (one trivial request in, one trivial response out, deployed). It proves the plumbing before any feature exists and becomes the template every later slice extends.
- S7.3 — Order slices by dependency first, then by risk (riskiest assumptions earliest — if the core bet fails, fail in slice 1, not slice 7), then by user value.
- S7.4 — For each slice, write: what it delivers, which requirements it satisfies (by number), its own tests (unit + one end-to-end check), its exit gate (the acceptance criteria that must hold before the next slice starts), and its rough size (a slice should fit comfortably inside one working session / one context window — split anything bigger).
- S7.5 — Mark the checkpoints where a human must review before proceeding (typically: after slice 0, after the riskiest slice, before anything irreversible like a data-model freeze or a paid-service commitment).
- S7.6 — Attach the continuity mechanism: the plan names the progress artifact (e.g., `PROGRESS.md`) the engineer must update at the end of every session — what was completed, what state things are in, what's next — because the next engineer session starts blind.

**Outputs.** Ordered slice list with per-slice tests and gates; checkpoint map; continuity protocol.

**Exit criteria.** Every requirement number appears in exactly one slice; no slice exceeds one-session size; slice 0 is genuinely end-to-end.

---

### S8 — Risk, Assumption, and Unknown Register

**Purpose.** Make every gap explicit before it becomes a mid-build ambush.

**Inputs.** Everything above; especially the CLAIMED items from S1.4 and any assumptions labeled during S2–S7.

**Atomic actions.**
- S8.1 — List assumptions: statements the plan treats as true without verification. For each: the impact if false, and the cheapest verification step.
- S8.2 — List unknowns: questions nobody can currently answer. For each: which slice first collides with it, and whether it must be resolved before the build starts (blocking) or can be resolved in-slice (deferred).
- S8.3 — List external risks: third-party API limits/pricing/deprecation, platform policy, credential access, anything time-sensitive that should be verified against current sources rather than memory.
- S8.4 — Schedule the blocking unknowns: each becomes either a pre-build verification task or an interview question that reopens S2 for one turn.
- S8.5 — Write the kill/pivot criteria: the observable events that would mean the plan should be re-specced rather than pushed through (e.g., "the core API's latency makes the interaction unusable" → re-spec, don't grind).

**Outputs.** Assumption table, unknown table (blocking vs. deferred), risk table, kill criteria.

**Exit criteria.** Zero blocking unknowns remain unscheduled. A spec may ship with unknowns; it may not ship with *unacknowledged* ones.

---

### S9 — Spec Review, Freeze, and Handoff Packaging

**Purpose.** Prove the spec itself before anyone builds against it, then package it for a memoryless successor.

**Inputs.** Draft SPEC.md and PLAN.md assembled from S3–S8 outputs.

**Atomic actions.**
- S9.1 — Run the adversarial read: attempt to satisfy the spec's letter while violating its intent. Every loophole found becomes a tightened requirement. (A separate context window or a second model pass does this better than the author — the author reads what they meant, not what they wrote.)
- S9.2 — Run the completeness checklist (Part 6 of this document) line by line against the draft.
- S9.3 — Run the amnesia test: could a competent engineer with *only* these two files and the repo build the right thing without asking a single question? Every question they'd have to ask marks a spec hole — fix the file, not the conversation.
- S9.4 — Present the spec to the requester for the freeze decision. Changes requested now are cheap; fold them in and re-run S9.2–S9.3 on the changed sections.
- S9.5 — On approval, mark both files FROZEN with a date. Post-freeze changes are amendments with their own dated entries — never silent edits.
- S9.6 — Package the handoff: SPEC.md, PLAN.md, the empty PROGRESS.md seeded with "Slice 0 not started," and the opening instruction for the engineer session ("Read SPEC.md, PLAN.md, PROGRESS.md. Execute the next incomplete slice only. Update PROGRESS.md before ending."). The spec conversation itself is now disposable; the files are the product.
- S9.7 — Start the engineer phase in a **fresh session**. The spec session's context is spent; dragging it into the build session wastes the window and imports conversational noise into execution.

**Outputs.** Frozen SPEC.md, frozen PLAN.md, seeded PROGRESS.md, engineer-session opening prompt.

**Exit criteria.** The requester has approved the freeze, and the three files are sufficient on their own — the amnesia test passes.

---

## Part 3 — The Master Prompt (Paste-Ready)

What follows is the engineered prompt that executes S0–S9 end-to-end. Paste it as the first message of a dedicated spec session (chat or Claude Code plan mode), followed by your raw request. It is deliberately explicit; length is the price of a session that cannot skip stages.

```
You are running a SPEC SESSION — the architect phase of an architect-then-engineer
workflow. Your job in this entire session is to produce two frozen documents,
SPEC.md and PLAN.md, plus a seeded PROGRESS.md. You will write NO application code
in this session. If you feel the urge to start building, that urge is the signal
you have skipped a stage.

Your real customer is not me. It is a future session with zero memory of this
conversation that will build from your documents alone. Anything not written into
the files does not exist.

OPERATING RULES (bind for the whole session)
R1. Interview before asserting. Extract decisions from me; invent nothing silently.
    Any assumption you must make gets labeled ASSUMPTION and logged.
R2. One question per turn. Offer 2–4 concrete options with your flagged
    recommendation and one-clause reasoning when options are enumerable. After my
    answer, re-plan your remaining questions before asking the next.
R3. Every requirement must be observable/testable as written. Numbers over
    adjectives. If you write "fast," "simple," or "robust," replace it before
    continuing.
R4. Decompose vertically. Every plan slice crosses all layers it touches and ends
    in something demonstrable. No "all the backend first."
R5. Unknowns are inventory. Maintain a running UNKNOWNS list from the first turn;
    never paper over a gap to make the spec look finished.
R6. Verify time-sensitive facts (API pricing/limits, library status, platform
    policies) against current sources before the spec relies on them; mark
    anything unverifiable as a risk.
R7. Do not proceed past a stage until its exit check (stated per stage below)
    passes. Announce stage transitions in one line.

EXECUTE THESE STAGES IN ORDER:

STAGE 0 — INTENT. Restate my request as a one-sentence job-to-be-done ("You want
X so that Y"). Classify it: new build / extension / rebuild / prototype. List
every FORK — points where two different products satisfy my literal request —
and note the rigor level my wording implies (throwaway vs. production).
Exit check: I confirm or correct the intent sentence before you ask anything else.

STAGE 1 — INVENTORY. Ask me for (or read directly, if you have access): existing
repos/stacks, prior docs or decisions, assets and accounts already held, and my
real constraints — hours/week, budget ceiling, deadline and its hardness, skills
I actually have. Build a HAVE / NEED / UNKNOWN table. Mark each item VERIFIED
(you saw it) or CLAIMED (I asserted it).
Exit check: everything the plan will rely on is VERIFIED or explicitly flagged.

STAGE 2 — INTERVIEW. Maintain a queue ordered by elimination power; forks from
Stage 0 go first. Cover, or get my explicit permission to skip: problem & user •
success definition • smallest shippable version • scope & non-goals • users/auth •
data model & residency • integrations • privacy/data sensitivity • performance &
availability targets • accessibility floor • budget/hosting • timeline •
post-ship maintenance. One question per turn (R2). Log every answer WITH my
reason. Stop when further answers would not change SPEC.md or PLAN.md, then read
back the load-bearing decisions in one list for my confirm/correct.
Exit check: all forks resolved; all domains answered or consciously skipped in
writing; read-back confirmed.

STAGE 3 — PROBLEM & USER. Draft: a problem statement (current state → pain →
desired state, no solution language), a concrete primary-user definition,
secondary users and explicit non-users, and the one-line value test — the
sentence a user says when this worked.
Exit check: the problem statement stays true even if the imagined solution is wrong.

STAGE 4 — SCOPE. Draft the smallest IN-SCOPE set that passes the value test
end-to-end. Everything else I mentioned goes to NON-GOALS with a one-clause
reason. Run the subtraction pass (cut anything whose removal still passes the
value test) and the addition pass (surface hidden dependencies that must enter
scope now). Sanity-check scope against my constraint sheet; if it doesn't fit,
present the cut-or-extend choice to me explicitly.
Exit check: scope fits constraints with margin; every exclusion is written with
its reason.

STAGE 5 — REQUIREMENTS. Convert scope into numbered, traceable requirements:
- FR-n functional: actor + trigger + behavior + observable result.
- For every user-facing flow: its unhappy paths (invalid input, empty state,
  network/permission failure, quota) each with required behavior.
- NFR-n non-functional with numbers: latency, capacity, uptime, accessibility,
  device matrix, monthly cost ceiling.
- DR-n data: entities, persistence, what must NOT persist, retention/deletion.
- SR-n security floor: auth model, secrets handling, validation posture, what an
  attacker must not be able to do.
Every requirement cites the interview answer or labeled ASSUMPTION it came from.
Exit check: each requirement is testable as written; no orphan requirements.

STAGE 6 — DONE-DEFINITION. Write the global done-definition as an observable
scene on the deployed system, per-cluster acceptance criteria (given/when/then
or checklist), and a 2-minute demo script that proves done. Keep acceptance
("does what the spec says") distinct from quality gates ("tests pass, lint
clean, deploys from a clean clone") — list both.
Exit check: a stranger with the demo script and the live product could verify
done without asking anyone anything.

STAGE 7 — PLAN. Cut the build into vertical slices. Slice 0 is the walking
skeleton: thinnest end-to-end path, deployed. Order by dependency, then risk
(riskiest assumptions earliest), then value. For EACH slice specify: deliverable,
requirement numbers satisfied, its own tests (unit + one end-to-end check), its
exit gate, and size (must fit one working session — split anything larger).
Mark human-review checkpoints (after slice 0, after the riskiest slice, before
anything irreversible). Define the continuity protocol: the engineer updates
PROGRESS.md every session — completed / current state / next.
Exit check: every requirement number lands in exactly one slice; no oversized
slices; slice 0 is genuinely end-to-end.

STAGE 8 — RISKS, ASSUMPTIONS, UNKNOWNS. Tables for: ASSUMPTIONS (impact if
false + cheapest verification), UNKNOWNS (blocking vs. deferred, and which slice
collides with each), EXTERNAL RISKS (API pricing/limits/deprecation, platform
policy — verified current per R6), and KILL/PIVOT criteria (observable events
that mean re-spec, not grind).
Exit check: zero blocking unknowns left unscheduled.

STAGE 9 — REVIEW, FREEZE, HANDOFF.
(a) Adversarial read: try to satisfy the spec's letter while violating its
    intent; tighten every loophole you find.
(b) Amnesia test: list every question a competent stranger building from ONLY
    these files would still have to ask. Fix the files until the list is empty.
(c) Present both documents to me for the freeze decision; fold in my changes and
    re-run (a)–(b) on changed sections.
(d) On my approval: mark both files FROZEN with today's date, output final
    SPEC.md and PLAN.md in full, output PROGRESS.md seeded with "Slice 0: not
    started," and output the exact opening prompt for the fresh engineer
    session: "Read SPEC.md, PLAN.md, PROGRESS.md. Execute the next incomplete
    slice only. Verify its exit gate. Update PROGRESS.md before ending."
(e) Remind me to start the engineer phase in a NEW session — this one is spent.

Begin now with Stage 0 on the request that follows.

MY REQUEST: [PASTE_YOUR_RAW_REQUEST_HERE]
```

---

## Part 4 — The Question Bank

The interview (S2) draws from these domains. The master prompt requires each to be answered or consciously skipped. Use this bank when a domain needs deeper drilling than the default question.

**Problem & user**
- Walk me through the moment this problem last bit you (or your user). What happened, step by step?
- If this product vanished a month after launch, who would complain first, and what exactly would they miss?
- Who is explicitly NOT a user, even if they ask to be?

**Success & smallest version**
- What is the single interaction that, if it works end-to-end, makes v1 worth shipping?
- What would make you personally use this weekly rather than once?
- Which currently-imagined feature would you trade away first under deadline pressure?

**Scope & non-goals**
- Name three things adjacent products do that this one deliberately won't. Why?
- Is v1 for real users, one pilot user, or only you? (The answer moves the auth, error-handling, and polish floors.)

**Users & auth**
- Anonymous, accounts, or invited-only? What breaks if a stranger finds the URL?
- One role or several? What must role B never be able to see or do?

**Data**
- What must survive a browser refresh? A device change? An account deletion?
- What is the most sensitive datum the system will touch, and what's the rule for it?
- If a user asks "delete everything you have on me," what has to happen?

**Integrations & externals**
- Which third-party services are load-bearing? For each: what's the fallback when it's down, slow, or repriced?
- Any API keys/credentials that exist only in one person's head or inbox?

**Non-functional**
- How slow is too slow, in seconds, for the core interaction?
- How many users on day 1? Day 90? What breaks first if that's 10x wrong?
- Which devices/browsers must this work on? Which are explicitly unsupported?
- What's the monthly cost ceiling before this stops being worth it?

**Timeline & maintenance**
- What's the real deadline, and what happens if it slips a week?
- After ship: who fixes bugs, and within what response time?
- Should the codebase optimize for "I maintain this" or "anyone can maintain this"?

---

## Part 5 — Output Artifact Templates

### 5.1 SPEC.md skeleton

```
# SPEC — [PROJECT_NAME]        STATUS: DRAFT | FROZEN [DATE]

## 1. Intent
One-sentence job-to-be-done. Request type. Rigor level.

## 2. Problem & Users
Problem statement (no solution language). Primary user. Secondary users.
Non-users. Value test line.

## 3. Scope
IN-SCOPE: …
NON-GOALS: each with one-clause reason.

## 4. Requirements
FR-1 … (actor + trigger + behavior + observable result) [source: Q# / ASSUMPTION-n]
  Unhappy paths: …
NFR-1 … (numbers, not adjectives)
DR-1 … (data & persistence rules)
SR-1 … (security floor)

## 5. Done-Definition
Global done scene. Per-cluster acceptance criteria. Demo script.
Quality gates (tests/lint/clean-clone deploy).

## 6. Assumptions / Unknowns / Risks
ASSUMPTION-n: statement | impact if false | cheapest verification
UNKNOWN-n: question | blocking? | first slice affected
RISK-n: external risk | mitigation
KILL CRITERIA: …

## 7. Amendment Log
[date] — [change] — [reason]
```

### 5.2 PLAN.md skeleton

```
# PLAN — [PROJECT_NAME]        STATUS: DRAFT | FROZEN [DATE]

## Slice 0 — Walking Skeleton
Delivers: thinnest end-to-end path, deployed.
Satisfies: (plumbing only)
Tests: one end-to-end smoke check.
Exit gate: live URL returns the trivial round-trip.
Size: one session.
CHECKPOINT: human review before slice 1.

## Slice 1 — [NAME]
Delivers: …
Satisfies: FR-…, NFR-…
Tests: …
Exit gate: …
Size: one session.

[… remaining slices, dependency → risk → value order …]

## Checkpoints
After slice 0. After slice [riskiest]. Before [irreversible step].

## Continuity Protocol
Engineer updates PROGRESS.md at end of every session:
COMPLETED / CURRENT STATE / NEXT. Next session reads all three files first.
```

### 5.3 PROGRESS.md seed

```
# PROGRESS — [PROJECT_NAME]
Slice 0: not started.
CURRENT STATE: repo not initialized.
NEXT: execute Slice 0 per PLAN.md.
```

---

## Part 6 — The Spec Quality Checklist (used in S9.2)

Run every line. A "no" means return to the named stage.

1. Intent sentence confirmed by the requester verbatim or corrected. (S0)
2. Every plan-load-bearing asset VERIFIED or flagged as assumption. (S1)
3. Every fork from intake resolved by an interview answer, not a guess. (S2)
4. Decision log captures reasons, not just choices. (S2)
5. Problem statement contains zero solution language. (S3)
6. Every exclusion in NON-GOALS carries its reason. (S4)
7. Scope fits the constraint sheet with margin, in writing. (S4)
8. No requirement contains "fast," "simple," "robust," "user-friendly," or any
   other untestable adjective. (S5)
9. Every user-facing flow has unhappy-path requirements. (S5)
10. Every requirement traces to an answer or a labeled assumption. (S5)
11. The done-definition is a scene a stranger could verify. (S6)
12. The demo script exists and takes under ~2 minutes. (S6)
13. Slice 0 is end-to-end and deployable. (S7)
14. Every requirement number appears in exactly one slice. (S7)
15. No slice exceeds one-session size. (S7)
16. Blocking unknowns: zero unscheduled. (S8)
17. Kill/pivot criteria are observable events, not vibes. (S8)
18. Adversarial read performed; loopholes tightened. (S9)
19. Amnesia test passes: a stranger needs zero questions. (S9)
20. Freeze is explicit, dated, and amendments have a log. (S9)

**Anti-patterns this checklist exists to catch**
- *The eager build*: code appears before the freeze. (Violates the session's one job.)
- *The mirror spec*: the spec restates the prompt in longer words without extracting any new decision. (S2 was skipped.)
- *The horizontal plan*: "first all models, then all endpoints, then the UI." (Nothing is verifiable until everything is done.)
- *The clean-looking spec*: zero unknowns on a genuinely novel build. (The gaps were hidden, not resolved.)
- *The immortal conversation*: the spec lives in chat history instead of files, and dies with the context window.
- *The silent amendment*: scope changes mid-build with no dated entry, and the plan quietly stops matching reality.

---

## Part 7 — Usage Notes

**Where to run it.** The master prompt works in a plain chat session or in Claude Code's plan mode. In Claude Code, Stage 1's archaeology gets stronger — the session can read the actual repo instead of asking you to describe it.

**Model choice.** Spec work is judgment-dense and token-light compared to building; it is the natural place to spend your strongest model. The engineer phase can often run on a cheaper model precisely because the spec removed the judgment calls.

**Session hygiene.** One spec session per project. When it ends, the files are the product and the conversation is disposable. Start the engineer phase fresh — importing the spec conversation into the build session wastes context on already-crystallized reasoning.

**Re-entry.** When scope genuinely changes mid-build, do not renegotiate inside the engineer session. Open a short spec-amendment session, run only the affected stages (usually S2 for the new decision, S5–S7 for the ripple), date the amendment, and re-freeze.

**Scaling down.** For a genuinely small task, the stages compress but never disappear: intent sentence, three interview questions, five requirements, a done scene, two slices, one unknown table — a fifteen-minute spec session is still a spec session. The stage that is never safe to skip, at any size, is S0: confirming you are solving the right problem costs one sentence and prevents the only failure that no amount of engineering can recover from.
