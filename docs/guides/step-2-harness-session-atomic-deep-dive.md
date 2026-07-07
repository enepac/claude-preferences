# The Harness Session: Engineering "Build the Harness Before the App" at Atomic Resolution

**A complete process discovery and a paste-ready master prompt for Step 2 of AI-assisted app development: the machinery around the agent, built before any feature code exists.**

---

## Part 1 — Orientation: What the Harness Session Is and Why It Exists

### 1.1 Where this step sits

The harness session runs after the spec freeze (Step 1) and before the first feature slice (Step 3). Its single job: convert the frozen SPEC.md and PLAN.md into an **enforcement environment** — a repository where correct agent behavior is produced by mechanism, not by hoping the model follows instructions.

The governing formula is **Agent = Model + Harness**. The model is fixed; the harness is the entire variable under your control. Two engineers running the same model on the same codebase get radically different results, and the difference is almost entirely how much harness work each has done. The harness session is where that work happens — deliberately, once, up front — instead of accreting reactively while features are half-built.

### 1.2 The five layers plus the world around them

A complete harness has five layers, plus the environment that bounds them:

| Layer | Question it answers | Primary artifact |
|---|---|---|
| **1. Memory** | What does the agent always know? | CLAUDE.md |
| **2. Tools** | What can the agent reach? | MCP config, slash commands, subagents, skills |
| **3. Permissions** | What is the agent allowed to do? | settings.json |
| **4. Hooks** | What is enforced at runtime? | Lifecycle hook scripts |
| **5. Observability** | What can you see afterward? | Session logs, progress artifacts, verification loops |
| **(0.) Environment** | What world does the agent act in? | Sandbox, OS user, network egress, secrets handling |

Most builders configure only Layer 1 and stop. The harness session's purpose is to stand up all five, sized to the project's rigor budget, before feature work begins.

### 1.3 The operating principles of the whole step

1. **Mechanism over prompt.** Anything that can be enforced deterministically (a config, a permission, a hook, a lint rule) is never left as an instruction in CLAUDE.md. Prompts are advisory; the agent will eventually ignore them. Config is physics.
2. **Feedback goes to the fastest layer.** Push every quality check to the fastest place it can run: runtime hook (milliseconds) > pre-commit (seconds) > CI (minutes) > human review (hours). A check at a slow layer that could live at a fast one is wasted latency on every future iteration.
3. **The harness compounds.** One lint rule prevents that mistake in every future session. One test catches that regression forever. One hook removes a whole class of review comments. Harness investment is the only work in the project with a permanently increasing return.
4. **Executable artifacts only.** Agents grep and read everything in the repo and treat all text as equally authoritative — they have no intuition that a note is three months stale. The repo holds code, tests, configs, schemas, and CI definitions. Prose plans and stale notes either live outside the repo or are aggressively pruned.
5. **Start minimal, reinforce on miss.** Build the Minimum Viable Harness in this session; thereafter, every agent mistake during the build becomes a new mechanism (rule, hook, test) — never just a stronger sentence in a prompt. The harness session establishes the floor and the reinforcement habit, not the final state.
6. **Prove the harness before trusting it.** A gate that has never been seen to fire is a decoration. The session ends by deliberately violating each gate and watching it block.

### 1.4 What this session must produce

By the end, the repo contains, working and proven: the scaffold; a lean CLAUDE.md; settings.json with explicit permissions; the deterministic toolchain (lint, format, types, tests) wired into hooks; a Stop-condition gate; pre-commit and CI; the project's slash commands, subagents, and any MCP connections; the observability spine (PROGRESS.md protocol, session log habit); and a HARNESS.md documenting all of it for future sessions. Anything not mechanized in this session will have to be remembered by a future context window — which is to say, forgotten.

---

## Part 2 — The Atomic Process Map

Step 2 decomposes into ten stages, H0 through H9. Each is specified as **Purpose → Inputs → Atomic actions → Outputs → Exit criteria**. A stage may not begin until the prior stage's exit criteria hold.

---

### H0 — Preconditions and Rigor Budget

**Purpose.** Confirm the harness has something legitimate to enforce, and size the harness to the project instead of copying someone else's maximal setup.

**Inputs.** Frozen SPEC.md and PLAN.md from the spec session; the constraint sheet (time, budget, deadline); the target stack decision.

**Atomic actions.**
- H0.1 — Verify the spec freeze exists. A harness enforcing an unfrozen spec enforces a moving target; if SPEC.md is missing or draft, return to Step 1.
- H0.2 — Extract the enforcement-relevant facts from the spec: language/runtime, framework, test expectations, quality gates named in the done-definition, security floor (SR-n requirements), data-sensitivity rules (what must never persist or leak), and deployment target.
- H0.3 — Set the rigor budget. Map the spec's stakes to a harness tier: **prototype** (lint + format + one smoke test + minimal permissions), **portfolio/production-single-dev** (full five layers, CI, Stop gate), **multi-user production** (all of the above plus environment isolation, secrets scanning, dependency auditing). Over-harnessing a throwaway prototype is procrastination; under-harnessing production is negligence.
- H0.4 — Inventory harness-relevant assets already owned: existing configs from prior projects that can be ported, hook scripts, CI templates, slash command libraries, subagent definitions. Reuse beats re-derivation.
- H0.5 — Verify current-state facts that decay: tool versions, hook event names, platform capabilities. Harness documentation goes stale fast; the canonical reference for the runtime's hook events and config schema is checked now, not recalled.

**Outputs.** Enforcement-facts extract; rigor tier decision; reuse inventory; verified platform capability list.

**Exit criteria.** The tier is chosen and justified in one sentence against the spec's stakes; every platform fact the session will build on is verified current, not remembered.

---

### H1 — Repository Scaffold and Hygiene

**Purpose.** Create the physical world the agent will inhabit, structured so that everything the agent finds is true and executable.

**Inputs.** Stack decision; PLAN.md slice structure; rigor tier.

**Atomic actions.**
- H1.1 — Initialize the repo with version control from commit zero. Git history is harness infrastructure: it is the bisection search space, the rollback mechanism, and half of the cross-session memory.
- H1.2 — Lay the directory skeleton matching the stack's conventions (src/tests/scripts/docs or the framework's idiom). Conventional structure is free context: the agent's priors about where things live come true.
- H1.3 — Write .gitignore before anything else can be accidentally committed: dependencies, build output, environment files, secrets, editor state.
- H1.4 — Establish the freshness discipline: SPEC.md and PLAN.md enter the repo as the frozen references; any other prose is either executable-adjacent (README with run commands) or excluded. Delete-by-default for stale text, because the agent will believe whatever it greps.
- H1.5 — Create the run scripts as the single entry points (dev, test, lint, build). One canonical command per activity, scripted, so neither agent nor human ever improvises the invocation.
- H1.6 — Commit the scaffold as its own commit. From here on, the commit granularity discipline starts: one logical change per commit, message states intent.

**Outputs.** Initialized repo; skeleton; .gitignore; run scripts; frozen spec/plan checked in; first commits.

**Exit criteria.** A fresh clone plus one documented command reaches a running (empty) dev state; nothing in the repo is text the agent shouldn't trust.

---

### H2 — Memory Layer: CLAUDE.md

**Purpose.** Give every future session the minimum always-loaded context — and nothing else. This is the most misused layer, and the discipline is subtractive.

**Inputs.** Enforcement-facts extract (H0); scaffold (H1).

**Atomic actions.**
- H2.1 — Draft CLAUDE.md as a **pointer file, under ~50 lines**: stack and versions, the canonical run commands, the three to five project conventions that genuinely differ from defaults, and pointers to SPEC.md / PLAN.md / PROGRESS.md for depth. It is a map, not the territory.
- H2.2 — Run the mechanization filter on every candidate line: *could this instruction be a config, permission, or hook instead?* "Never commit secrets" → secrets-scanning hook, not a sentence. "Always run tests before finishing" → Stop gate, not a plea. "Don't touch the migrations folder" → permission deny rule. Every line that survives must be genuinely advisory context that no mechanism can carry.
- H2.3 — Purge instruction-shaped wishes. ALL-CAPS "NEVER/ALWAYS" lines in a memory file are evidence of a missing mechanism; each one found is converted (to H3/H5 work) or deleted.
- H2.4 — Verify every claim in the file against the actual repo — commands run as written, paths exist, versions match. A memory file that lies is worse than none: it is trusted and wrong.
- H2.5 — Note the maintenance rule inside the file itself: CLAUDE.md is updated when conventions change, and shrinks by default; it is reviewed whenever an agent visibly acts on stale memory.

**Outputs.** A verified, sub-50-line CLAUDE.md; a conversion list feeding H3 and H5.

**Exit criteria.** Every line passes the mechanization filter; every command in the file executes as written; nothing in the file duplicates what a config enforces.

---

### H3 — Permissions and Environment Layer

**Purpose.** Bound what the agent may do (permissions) and what world it can reach (environment) so that the worst plausible agent mistake is bounded before it happens.

**Inputs.** Security floor from the spec (SR-n); data-sensitivity rules; rigor tier.

**Atomic actions.**
- H3.1 — Write settings.json deliberately rather than accepting defaults: allow-list the routine operations the build will need constantly (edit within the project tree, run the scripted commands), so permission prompts don't train the human into reflexive approval.
- H3.2 — Deny-list the irreversibles and the sensitive: destructive filesystem operations outside the project, force-pushes, production credentials, deployment commands, and any path the spec marked as protected. Declarative deny rules are the cheap 80%; anything needing judgment becomes a hook (H5).
- H3.3 — Set harness-enforced behaviors that would otherwise be prompt-wishes: commit attribution, default model per context, tool availability. Deterministic settings beat instructions every time the two could disagree.
- H3.4 — Bound the environment: decide what OS user / container / sandbox the agent runs as, and what network egress it has. The floor and ceiling of what an unbounded agent can do are identical to what your OS user can do; environment work is what separates them.
- H3.5 — Establish secrets handling: env files ignored (H1.3), a .env.example template committed, real secrets injected at runtime only, and — at production tier — a scanning mechanism so a leaked key cannot reach a commit.
- H3.6 — Record every non-obvious permission decision and its reason in HARNESS.md (seeded now, completed in H9), because future-you will otherwise loosen a rule whose purpose was forgotten.

**Outputs.** settings.json; environment/sandbox decision; secrets protocol; seeded HARNESS.md.

**Exit criteria.** The routine build loop runs without permission friction; each denied category maps to a named risk; a secret placed in a tracked file cannot reach the remote.

---

### H4 — Deterministic Toolchain

**Purpose.** Install the non-negotiable mechanical judges — formatter, linter, type checker, test runner — whose verdicts are facts, not opinions. Everything in H5–H6 is plumbing that delivers these verdicts faster; the verdicts themselves are created here.

**Inputs.** Stack; rigor tier; quality gates named in the spec's done-definition.

**Atomic actions.**
- H4.1 — Install and pin the formatter; zero-config idiom where the ecosystem has one. Formatting disputes are the lowest-value text an agent can generate; the formatter deletes the entire category.
- H4.2 — Install the linter and tune it once: start from the recommended set, remove rules that fight the project's idiom, add rules encoding the spec's conventions. Every enabled rule is a permanent, silent reviewer.
- H4.3 — Enable the type layer at the strictness the tier warrants. Types are the cheapest large-scale error detector available to an agent working in unfamiliar code — which is every agent, every session.
- H4.4 — Stand up the test runner with one passing example test and one deliberately failing test (temporarily) to prove the runner distinguishes them. A test suite whose failure mode has never been observed is unverified plumbing.
- H4.5 — Wire all four into the run scripts from H1.5 (`lint`, `format`, `typecheck`, `test`) so every later layer (hooks, pre-commit, CI) calls the identical canonical commands. One definition of "passing," used everywhere.
- H4.6 — Pin dependency versions and commit the lockfile. Deterministic tooling on floating versions is deterministic until Tuesday.

**Outputs.** Configured formatter, linter, type checker, test runner; canonical script targets; lockfile.

**Exit criteria.** Each tool runs via its script target; each has been seen to both pass and fail correctly; configs are committed and pinned.

---

### H5 — Hooks Layer: Runtime Enforcement

**Purpose.** Attach the H4 judges (and any judgment-requiring guards) to the agent's lifecycle so feedback arrives in milliseconds, inside the loop, instead of at commit or review time.

**Inputs.** Toolchain (H4); conversion list from H2.2/H2.3; verified hook-event reference (H0.5).

**Atomic actions.**
- H5.1 — Map desired enforcements to lifecycle events using the verified event list. The workhorses: post-edit (format/lint the file just touched and feed failures straight back into context for immediate self-correction), pre-tool-use (block semantically dangerous actions that declarative deny rules can't express — hooks receive the proposed action as structured input and can run any script against it), and stop (refuse to let the turn end until the completion gate passes).
- H5.2 — Implement the post-edit format/lint hook first. This is the single highest-leverage mechanism in the entire harness: it converts every future style-and-lint mistake from a review comment into an instant, automatic correction.
- H5.3 — Implement the Stop gate: the turn cannot end while the canonical test/lint/typecheck targets fail. Choose its strictness deliberately — a hard gate suits unattended runs; a softer "warn and report" suits interactive work. Note the platform's escape behavior (gates typically yield after repeated consecutive blocks) so the gate is designed with, not against, it.
- H5.4 — Implement the guards converted from CLAUDE.md wishes (H2.2): the secrets-pattern block, the protected-path block, anything expressible as "inspect the proposed action, veto if it matches."
- H5.5 — Keep every hook script fast, deterministic, and silent on success. A slow hook taxes every single agent action; a chatty one pollutes context. Budget: the post-edit hook in milliseconds, the Stop gate in seconds.
- H5.6 — Test each hook by violation: write an unformatted file, attempt a forbidden action, try to stop with failing tests — and watch each block fire. Then commit the hook scripts; hooks are code and get versioned like code.

**Outputs.** Working post-edit hook, Stop gate, guard hooks; each proven by a witnessed block; scripts committed.

**Exit criteria.** Every hook has fired at least once against a deliberate violation; no hook adds perceptible latency to routine edits; hook behavior matches HARNESS.md's description.

---

### H6 — Pre-Commit and CI: The Slower Gates

**Purpose.** Add the second and third rungs of the feedback ladder — checks too heavy for per-edit hooks, and the clean-environment proof that "works on my machine" can't fake.

**Inputs.** Canonical script targets (H4.5); rigor tier.

**Atomic actions.**
- H6.1 — Install the pre-commit framework and wire it to the canonical targets: format check, lint, typecheck, fast tests, plus secrets scan at the tier that requires it. Pre-commit catches what per-edit hooks defer (cross-file consistency, the full fast suite).
- H6.2 — Keep pre-commit under a human patience budget (seconds, not minutes); anything slower migrates to CI. A pre-commit gate that developers bypass out of frustration enforces nothing.
- H6.3 — Stand up CI running the identical canonical targets in a clean clone, plus the heavy checks: full test suite, build, dependency audit at production tier. CI's unique value is the fresh environment — it is the only gate that proves the repo is self-sufficient.
- H6.4 — Make CI required on the integration branch (at any tier where a remote exists), so the gate has teeth rather than advice.
- H6.5 — Run the full ladder once end-to-end and verify each layer catches what it should: introduce a lint error (hook catches), a failing test (pre-commit/Stop gate catches), a clean-clone-only breakage like a missing dependency declaration (only CI catches).

**Outputs.** Pre-commit config; CI pipeline; branch protection where applicable; a witnessed catch at each rung.

**Exit criteria.** All three rungs proven by deliberate violations; total pre-commit time within budget; CI green on the scaffold.

---

### H7 — Tools Layer: Commands, Subagents, Skills, MCP

**Purpose.** Extend what the agent can reach and pre-package the project's repeated workflows, so future sessions spend context on the problem instead of re-deriving procedure.

**Inputs.** PLAN.md (which workflows will repeat); reuse inventory (H0.4); rigor tier.

**Atomic actions.**
- H7.1 — Identify the inner-loop workflows this project will run many times (implement-a-slice, review-then-fix, update-progress, debug-with-repro) and encode each as a slash command checked into the repo's command directory. The threshold: anything that will be prompted more than once a day earns a command.
- H7.2 — Define the project's subagents — feature-specific and narrowly-toolscoped rather than generic personas — for work that should run in an isolated context: the verification/review subagent (so the agent doing the work is never the one grading it), plus any heavy-read specialist the plan implies. Each definition names its tools, its model, and its single job.
- H7.3 — Wire the verification subagent into the completion path: the review runs in a fresh context against the slice's exit gate from PLAN.md, and is instructed to flag only gaps affecting correctness or stated requirements — an uncalibrated reviewer generates findings forever and drives over-engineering.
- H7.4 — Connect only the MCP servers the plan actually needs, each with least-privilege scope; every connected tool is attack-and-distraction surface, so absence is the default.
- H7.5 — Add or author skills for domain procedures the project will need repeatedly (the progressive-disclosure principle: heavyweight instructions load only when their trigger matches, keeping baseline context lean).
- H7.6 — Commit all definitions. Commands, subagents, and skills are harness code: versioned, reviewed, portable to the next project.

**Outputs.** Slash command set; subagent definitions including the verification reviewer; minimal MCP config; skills; all committed.

**Exit criteria.** Each command runs; the verification subagent produces a correctly-scoped review on a toy diff; no tool is connected without a named need.

---

### H8 — Observability and Continuity

**Purpose.** Make the agent's work inspectable after the fact and survivable across context windows — the layer that turns isolated sessions into a continuous project.

**Inputs.** PLAN.md continuity protocol; scaffold.

**Atomic actions.**
- H8.1 — Instantiate PROGRESS.md from the plan's seed and bind the update contract into the workflow mechanically: the update-progress step lives inside the slice slash command (H7.1) and/or the Stop gate's checklist, not in a prompt-wish. Completed / current state / next — the fresh-session bootstrap.
- H8.2 — Establish the session-log habit at the tier that warrants it: where transcripts/logs are kept, and the rule that anomalies (a hook misfire, a permission surprise) are noted when seen, because they are invisible later.
- H8.3 — Define the goal-condition mechanism for longer runs: the slice exit gate expressed as a checkable condition that an evaluator re-checks each turn, so unattended work converges instead of drifting.
- H8.4 — Set the cost/usage watch appropriate to the tier — the observability question "what did this run consume" answered before it becomes a surprise.
- H8.5 — Write the reinforcement loop into HARNESS.md as the standing rule of the build phase: **every agent miss observed during development becomes a mechanism the same day** — a lint rule, a test, a hook, a permission — and never merely a stronger instruction. This single habit is what makes the harness compound instead of decay.

**Outputs.** Live PROGRESS.md wired into the workflow; logging/anomaly habit; goal-condition pattern; reinforcement rule documented.

**Exit criteria.** A cold-start simulation passes: a fresh session given only the repo can state where the project is and what's next, without asking; the progress update fires mechanically at end-of-work rather than by memory.

---

### H9 — Harness Validation, Documentation, and Freeze

**Purpose.** Prove the whole assembly, document it for the amnesiac future, and hand off to the build phase.

**Inputs.** Everything H1–H8 produced.

**Atomic actions.**
- H9.1 — Run the full violation battery in one pass — one deliberate breach per gate (format, lint, type, test, secret, protected path, premature stop, dirty commit, broken clean-clone) — and record that each was blocked at its intended rung. Any breach that sails through is a harness bug fixed now, at zero feature-cost, instead of discovered mid-build.
- H9.2 — Run the walking-skeleton compatibility check: execute PLAN.md's slice 0 (thinnest end-to-end path) *inside* the finished harness. This is the first real cohabitation of harness and product, and it flushes out gates that fight the actual workflow (an over-strict permission, a hook that mangles generated files) while the cost of adjusting is minutes.
- H9.3 — Finish HARNESS.md: the layer map, every non-default decision with its one-line reason, the canonical commands, the escape hatches (how to bypass a gate legitimately and what bypass costs), and the reinforcement rule. The test it must pass is the amnesia test: a competent stranger with only the repo could operate, trust, and extend this harness without asking anything.
- H9.4 — Calibrate friction honestly: anything that blocked legitimate work during H9.2 gets loosened deliberately and the reason recorded; anything that felt theatrical gets removed. A harness the builder starts bypassing is dead; pruning is maintenance, not defeat.
- H9.5 — Freeze: commit the complete harness state, tag it, and mark HARNESS.md with the date. Post-freeze harness changes follow the reinforcement loop (miss → mechanism → commit) with their own dated entries — the harness stays alive, but never drifts silently.
- H9.6 — Hand off: the build phase (Step 3) starts in a fresh session whose opening instruction is already defined by the plan's continuity protocol; the harness session's conversation is now disposable because the repo itself carries everything.

**Outputs.** Violation-battery record; slice-0-compatible harness; frozen, tagged, documented harness; handoff.

**Exit criteria.** Every gate witnessed blocking; slice 0 runs clean inside the harness; HARNESS.md passes the amnesia test; the tag exists.

---

## Part 3 — The Master Prompt (Paste-Ready)

Paste this as the first message of a dedicated harness session — ideally in the agentic coding environment with the frozen SPEC.md and PLAN.md present in (or pasted into) the workspace.

```
You are running a HARNESS SESSION — Step 2 of an architect/harness/build
workflow. Your single job in this session is to build and PROVE the enforcement
environment around the future application: memory, permissions, environment,
deterministic toolchain, hooks, pre-commit, CI, tools, and observability. You
will write NO feature code in this session except the throwaway violations used
to test gates and the slice-0 walking skeleton used to validate compatibility.

Your governing principles, binding for the whole session:
P1. Mechanism over prompt. Any rule expressible as config, permission, hook,
    lint rule, or test is NEVER left as an instruction in a memory file. If you
    catch yourself writing "NEVER" or "ALWAYS" in CLAUDE.md, stop and build the
    mechanism instead.
P2. Feedback to the fastest layer: runtime hook (ms) > pre-commit (s) > CI
    (min) > human review (h). Place every check at the fastest rung that can
    hold it.
P3. Executable artifacts only. The repo will be believed verbatim by future
    agents; stale prose is poison. Code, tests, configs, schemas, CI, and the
    frozen spec/plan — nothing else.
P4. Prove every gate. A gate that has never been witnessed blocking a
    deliberate violation does not exist. No gate ships unproven.
P5. Verify platform facts (hook event names, config schema, tool versions)
    against current canonical references before building on them. Do not build
    on recalled capabilities.
P6. Size to the rigor tier. Do not copy a maximal harness onto a prototype or
    a minimal one onto production.
P7. Do not proceed past a stage until its exit check passes. Announce stage
    transitions in one line.

EXECUTE THESE STAGES IN ORDER:

STAGE H0 — PRECONDITIONS AND RIGOR BUDGET.
Confirm SPEC.md and PLAN.md exist and are marked FROZEN; if not, stop and say
so. Extract the enforcement-relevant facts: stack, test expectations, quality
gates in the done-definition, security floor, data-sensitivity rules,
deployment target. Propose a rigor tier — prototype / single-dev production /
multi-user production — with a one-sentence justification, and list which
harness components that tier includes and excludes. Inventory reusable harness
assets I already have (configs, hooks, CI templates, commands from prior
projects). Verify current platform capabilities you will rely on.
Exit check: I confirm the tier before you build anything.

STAGE H1 — SCAFFOLD.
Initialize version control; lay the conventional directory skeleton for the
stack; write .gitignore FIRST (deps, build output, env files, secrets, editor
state); check in the frozen SPEC.md/PLAN.md; create canonical run scripts
(dev, test, lint, format, typecheck, build) as the single entry points; commit
in logical units with intent-stating messages.
Exit check: fresh clone + one documented command reaches a running empty dev
state; nothing in the repo is text a future agent shouldn't trust verbatim.

STAGE H2 — MEMORY (CLAUDE.md).
Draft CLAUDE.md as a pointer file UNDER 50 LINES: stack + versions, canonical
commands, the 3–5 conventions that differ from defaults, pointers to
SPEC/PLAN/PROGRESS for depth. Then run the mechanization filter line by line:
convert every rule-shaped line into H3/H5 work (list these conversions
explicitly) and delete it from the file. Execute every command in the file to
verify it is true.
Exit check: <50 lines; zero instruction-wishes; every stated command runs as
written; a conversion list exists for stages H3/H5.

STAGE H3 — PERMISSIONS AND ENVIRONMENT.
Write settings deliberately: allow-list the routine loop (project-tree edits,
canonical scripts) so approvals don't become reflexive; deny-list the
irreversibles (destructive ops outside the tree, force-push, prod credentials,
deploy, spec-protected paths). Move judgment-requiring guards to the H5 hook
list. Set deterministic behaviors that would otherwise be prompt-wishes
(attribution, model defaults). State the environment boundary (sandbox/user/
network egress) for my tier. Establish secrets handling: .env.example
committed, real secrets runtime-only, scanning at production tier. Record
every non-obvious decision + reason in a seeded HARNESS.md.
Exit check: routine loop runs friction-free; each denial maps to a named risk;
a secret in a tracked file cannot reach a commit.

STAGE H4 — DETERMINISTIC TOOLCHAIN.
Install and pin: formatter (ecosystem idiom), linter (recommended set, tuned
once: remove idiom-fighting rules, add spec-convention rules), type checking
at tier-appropriate strictness, test runner with one passing test AND one
temporarily failing test to prove the runner discriminates. Wire all four to
the canonical script targets so hooks/pre-commit/CI call identical commands.
Commit lockfile.
Exit check: each tool proven to both pass and fail correctly via its script
target; versions pinned.

STAGE H5 — HOOKS.
Using the VERIFIED current hook-event reference: (1) post-edit hook that
formats + lints the touched file and feeds failures back into context —
implement this first, it is the highest-leverage mechanism in the harness;
(2) Stop gate that blocks turn-end while canonical test/lint/typecheck fail —
state its strictness choice and the platform's escape behavior; (3) guard
hooks from the H2 conversion list (secrets patterns, protected paths,
semantically dangerous actions). Keep hooks fast (ms for post-edit), silent on
success, committed as code. Test EVERY hook by deliberate violation and report
each witnessed block.
Exit check: every hook seen blocking; no perceptible latency on routine edits.

STAGE H6 — PRE-COMMIT AND CI.
Pre-commit wired to canonical targets (format check, lint, typecheck, fast
tests, secrets scan per tier), total runtime within a seconds-level patience
budget — anything slower migrates to CI. CI runs identical targets in a clean
clone plus the heavy checks (full suite, build, dependency audit per tier);
required on the integration branch where a remote exists. Prove the ladder:
introduce (a) a lint error → caught at hook; (b) a failing test → caught at
Stop/pre-commit; (c) a clean-clone-only breakage → caught only in CI.
Exit check: one witnessed catch per rung; CI green on the scaffold.

STAGE H7 — TOOLS: COMMANDS, SUBAGENTS, SKILLS, MCP.
From PLAN.md, identify the workflows this project repeats (implement-slice,
review-fix, update-progress, debug) and encode each as a checked-in slash
command — threshold: anything done more than once a day. Define feature-
specific, narrowly-toolscoped subagents; REQUIRED: a verification subagent
that reviews a slice against its PLAN.md exit gate in a fresh context and is
instructed to flag only correctness/requirement gaps (calibrated, not
exhaustive). Connect ONLY the MCP servers the plan needs, least-privilege;
absence is the default. Add skills for repeated domain procedures. Commit all
definitions as harness code.
Exit check: each command runs; the verification subagent gives a correctly-
scoped review on a toy diff; no tool connected without a named need.

STAGE H8 — OBSERVABILITY AND CONTINUITY.
Instantiate PROGRESS.md from the plan's seed and bind its update MECHANICALLY
into the workflow (inside the slice command and/or Stop-gate checklist), never
as a prompt-wish. Establish the session-log/anomaly-note habit for my tier.
Define the goal-condition pattern: each slice's exit gate as a checkable
condition an evaluator re-checks per turn on unattended runs. Set the
cost/usage watch for my tier. Write the standing reinforcement rule into
HARNESS.md: every agent miss during the build becomes a mechanism (rule, test,
hook, permission) the same day — never merely a stronger instruction.
Exit check: cold-start simulation passes — a fresh session with only the repo
can state where the project is and what's next, unprompted.

STAGE H9 — VALIDATE, DOCUMENT, FREEZE.
(a) Full violation battery in one pass — one deliberate breach per gate
    (format, lint, type, test, secret, protected path, premature stop, dirty
    commit, clean-clone break) — report the blocking rung for each; fix any
    breach that sailed through before proceeding.
(b) Compatibility check: execute PLAN.md slice 0 (the walking skeleton) inside
    the finished harness; loosen or fix anything that fought legitimate work,
    recording each adjustment and reason.
(c) Complete HARNESS.md: layer map, every non-default decision + one-line
    reason, canonical commands, legitimate escape hatches and their cost, the
    reinforcement rule. It must pass the amnesia test — a stranger with only
    the repo could operate, trust, and extend the harness without questions.
(d) Freeze: commit, tag, date HARNESS.md. Post-freeze changes follow the
    reinforcement loop with dated entries only.
(e) Hand off: state the exact opening instruction for the fresh build-phase
    session (per PLAN.md's continuity protocol) and remind me this session is
    now disposable — the repo carries everything.

Begin now with Stage H0.

MY PROJECT CONTEXT: [PASTE_OR_CONFIRM_LOCATION_OF_FROZEN_SPEC_AND_PLAN, STACK,
AND ANY EXISTING HARNESS ASSETS TO REUSE]
```

---

## Part 4 — The Decision Bank

The questions each layer must answer or consciously default. Use when a stage needs deeper drilling.

**Rigor tier (H0)**
- Who is hurt, and how much, if the agent ships a defect unnoticed? (Only you → prototype tier. A pilot user → single-dev production. Strangers' data → full tier.)
- Will any run be unattended? (Yes → the Stop gate and goal-conditions move from optional to mandatory.)
- What is the longest the project will live? (Weeks → minimize; quarters → the compounding argument dominates and harness investment rises.)

**Memory (H2)**
- If a future agent knew nothing but this file plus the repo, what would it get wrong in its first ten minutes? (Whatever that is belongs in the file — nothing else does.)
- Which lines would still be true in three months untouched? (Lines that decay belong in generated or referenced artifacts, not in always-loaded memory.)

**Permissions/environment (H3)**
- What is the single worst action the agent could take in this repo, and is it mechanically impossible yet?
- Which approvals will I face so often that I'll stop reading them? (Those become allow-list entries now, before approval blindness sets in.)
- If a dependency install script were malicious, what could it reach? (The answer is the environment boundary's job description.)

**Toolchain (H4)**
- Which lint rules encode *this spec's* conventions, beyond the stock set?
- What strictness of typing will the project's dependencies actually tolerate?

**Hooks (H5)**
- For each rule-shaped wish: which lifecycle event is the earliest moment the violation is detectable?
- Hard gate or advisory, per check? (Unattended → hard; exploratory interactive → advisory, promoted later.)

**Gates (H6)**
- What is my honest patience budget for pre-commit before I start bypassing it?
- What breaks only in a clean environment? (Undeclared deps, absolute paths, uncommitted config — CI's exclusive catches.)

**Tools (H7)**
- Which prompts will I type more than once a day during the build? (Each is a command.)
- What review would I want from a colleague who hadn't watched me write the code? (That is the verification subagent's charter.)
- For each candidate MCP connection: what specific plan step needs it? (No answer → not connected.)

**Observability (H8)**
- If a session ended mid-slice right now, what would the next session need to know, and where would it look?
- What spend or usage number, if I saw it weekly, would change my behavior?

---

## Part 5 — Output Artifact Templates

### 5.1 CLAUDE.md skeleton (pointer discipline)

```
# [PROJECT_NAME]

Stack: [LANG/RUNTIME + VERSION], [FRAMEWORK], [DB], [KEY_SERVICES].

Commands (canonical — use these, never improvise):
- dev: [CMD]        - test: [CMD]
- lint: [CMD]       - format: [CMD]
- typecheck: [CMD]  - build: [CMD]

Conventions that differ from defaults:
1. [CONVENTION + ONE-CLAUSE WHY]
2. [CONVENTION + ONE-CLAUSE WHY]
3. [CONVENTION + ONE-CLAUSE WHY]

Depth: SPEC.md (what done means) · PLAN.md (slice order + gates) ·
PROGRESS.md (current state — read first) · HARNESS.md (how enforcement works).

Maintenance: this file shrinks by default; rules belong in configs/hooks,
not here.
```

### 5.2 HARNESS.md skeleton

```
# HARNESS — [PROJECT_NAME]        FROZEN [DATE], tag [TAG]

## Layer map
Memory: CLAUDE.md (<50 lines, pointer only)
Permissions: [FILE] — allow: [SUMMARY]; deny: [SUMMARY + RISK EACH]
Environment: [SANDBOX/USER/NETWORK BOUNDARY]
Toolchain: [FORMATTER] · [LINTER] · [TYPES @ STRICTNESS] · [TEST RUNNER]
Hooks: post-edit format/lint · Stop gate ([HARD/ADVISORY]) · guards: [LIST]
Gates: pre-commit ([RUNTIME]s) · CI ([REQUIRED ON BRANCH?])
Tools: commands [LIST] · subagents [LIST] · MCP [LIST + WHY EACH] · skills [LIST]
Observability: PROGRESS.md protocol · logs at [WHERE] · goal-conditions · cost watch

## Non-default decisions
[DECISION] — [ONE-LINE REASON]
...

## Escape hatches (legitimate bypasses and their cost)
[GATE] — bypass: [HOW] — cost/when justified: [WHY]

## The reinforcement rule (standing)
Every agent miss observed during development becomes a mechanism (lint rule,
test, hook, permission) the same day. Never only a stronger instruction.

## Violation battery record ([DATE])
[GATE] → breached by [X] → blocked at [RUNG]  (one line per gate)

## Amendment log
[DATE] — [CHANGE] — [MISS OR REASON THAT MOTIVATED IT]
```

### 5.3 Post-edit hook sketch (shape, not platform-specific)

```
# Fires after any file edit. Receives the touched path.
# 1. Run formatter on the file (write in place).
# 2. Run linter on the file.
# 3. On lint failure: emit the failures to the agent's context and exit
#    with the platform's "feed back and continue" status.
# 4. On success: exit silently. Success must produce zero output.
# Budget: total runtime in milliseconds. Anything heavier belongs in
# pre-commit or CI, not here.
```

### 5.4 Stop-gate sketch

```
# Fires when the agent attempts to end its turn.
# 1. Run canonical targets: typecheck, lint, test (fast suite).
# 2. All green → allow the stop.
# 3. Any red → block the stop, returning the failure summary so the agent
#    continues working on it.
# 4. Respect the platform's consecutive-block escape (design the gate
#    knowing it yields after N blocks; N failures in a row is itself a
#    signal to surface, not suppress).
# Checklist rider: PROGRESS.md updated this session? If not, block with
# that instruction — continuity is part of "done."
```

---

## Part 6 — The Harness Quality Checklist (used in H9)

Run every line. A "no" names the stage to revisit.

1. Rigor tier chosen and justified against the spec's stakes, not copied from a tutorial. (H0)
2. Platform facts (hook events, config schema) verified current, not recalled. (H0)
3. Fresh clone + one command reaches running state. (H1)
4. Nothing in the repo is prose a future agent shouldn't believe verbatim. (H1)
5. CLAUDE.md under 50 lines; every command in it executes as written. (H2)
6. Zero rule-shaped wishes in CLAUDE.md; each was converted to a mechanism or deleted. (H2)
7. Routine loop runs without permission friction; approvals are rare enough to still be read. (H3)
8. Every denial maps to a named risk in HARNESS.md. (H3)
9. A secret in a tracked file cannot reach a commit. (H3)
10. Formatter, linter, types, tests each witnessed both passing and failing. (H4)
11. One canonical definition of each check, called identically by hooks, pre-commit, and CI. (H4/H6)
12. The post-edit format/lint hook exists, fires in milliseconds, and is silent on success. (H5)
13. The Stop gate exists, its strictness was chosen deliberately, and its escape behavior is documented. (H5)
14. Every hook witnessed blocking a deliberate violation. (H5)
15. Pre-commit fits the patience budget; nothing in it tempts bypass. (H6)
16. CI proved the clean-clone catch that no other rung can make. (H6)
17. Every >once-a-day workflow is a checked-in command. (H7)
18. The verification subagent exists, runs in fresh context, and is calibrated to flag only correctness/requirement gaps. (H7)
19. Every connected MCP server has a named plan-step need. (H7)
20. PROGRESS.md updates fire mechanically, not by memory. (H8)
21. Cold-start simulation passes: a fresh session states where the project is, unprompted. (H8)
22. The reinforcement rule (miss → mechanism, same day) is written and standing. (H8)
23. Full violation battery run; every gate's block witnessed and recorded. (H9)
24. Slice 0 ran inside the harness; friction was loosened deliberately with reasons recorded. (H9)
25. HARNESS.md passes the amnesia test; the freeze is tagged and dated. (H9)

**Anti-patterns this checklist exists to catch**
- *The prompt-wish harness*: an eloquent CLAUDE.md full of NEVER/ALWAYS, and no mechanisms. Instructions decay; the first long session ignores them.
- *The unproven gate*: hooks and CI configured but never once witnessed blocking anything. Decoration, not enforcement.
- *The imported fortress*: a maximal production harness copied onto a weekend prototype; the builder starts bypassing it within days, which trains the bypass habit for when it matters.
- *The reflexive approve*: permissions so noisy that every prompt gets approved unread — indistinguishable from no permissions at all.
- *The novelist's memory file*: a 300-line CLAUDE.md that eats context every session and goes stale in a month, while the agent believes every word.
- *The trusting reviewer*: a verification subagent with no calibration instruction, generating findings forever and driving defensive over-engineering.
- *The harness that fought the app*: gates never tested against real work (no slice-0 check), discovered to block legitimate workflows three slices into the build.
- *The silent drift*: post-freeze harness edits with no amendment log, until nobody knows why a rule exists — or notices one vanished.

---

## Part 7 — Usage Notes

**Where to run it.** The harness session belongs in the agentic coding environment itself (not a chat tab), because most of its outputs are files, configs, and witnessed tool behavior. The spec session produced documents; the harness session produces a working machine, and the machine must be tested where it will run.

**Sequencing with Step 1.** The harness consumes the spec: the rigor tier comes from the stakes, the guard hooks from the security floor, the Stop gate's checklist from the done-definition, the slash commands from the plan's repeated workflows. Running the harness session before the spec exists forces guessing on all four — which is how imported-fortress and prompt-wish harnesses happen.

**Scaling down.** At prototype tier the whole session can compress to under an hour: scaffold, ten-line CLAUDE.md, default-plus-two permissions, formatter+linter, the post-edit hook, one smoke test, PROGRESS.md. The stages never disappear — they shrink. The two stages that are never safe to skip at any tier: H5.2 (the post-edit hook, the single highest-leverage mechanism per minute of setup) and H9.1's spirit (never trust a gate you haven't seen fire).

**The session after this one.** The build phase inherits a repo where the fast loop corrects style instantly, the Stop gate defines "finished," the reviewer is a fresh pair of eyes on demand, and the progress file survives every context death. From here on, harness work happens only through the reinforcement loop: a miss occurs, a mechanism lands, the harness compounds. That loop — not this session's initial construction — is where most of the harness's eventual value accumulates. This session's real product is not the configs; it is the floor under every future session and the habit that keeps raising it.
