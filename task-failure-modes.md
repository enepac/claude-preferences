# Task Failure Modes
## Why a single-pass build fails to satisfy the requester

Purpose: the reference catalog of failure surfaces a task-build can hit. It is
the source of truth that the Completion contract step 4 (failure-surface
checklist), /blueprint step 4 (critical path and bottleneck), and /battery
step 4 (name the failure modes) check against, so those rules stop re-deriving
failure surfaces from memory on each run.

Companion files: task-success-moves.md (the positive target) and
task-build-pipeline.md (the 9-step procedure). The three read the same process
from three angles: why builds fail, what a great build looks like, and the
steps between a rough prompt and a finished result. (Companion files are
created as files 2 and 3 of this set; references are forward-looking until then.)

## The structure

A single-pass build can fail at exactly three points: the target was defined
wrong (architect phase), the build fell short of a correct target (engineer
phase), or the result could not be proven or received (delivery). The 25 modes
group under those three points.

## Point 1 — Target failures (architect phase)
"Done" was wrong or never defined before anything got built.

1. Wrong question answered: the literal prompt got a competent answer, the intended one did not.
2. Scope mismatch: too narrow (missed assumed parts) or too broad (built unwanted things).
3. Implicit requirement missed: the unstated "obviously it also has to..." never surfaced.
4. Success criteria never pinned: no observable definition of done, so done was a guess.
5. Wrong objective optimized: built for speed when durability was wanted, polish when a draft was wanted.
6. Audience or register wrong: right content, wrong reader.
7. Constraint ignored: a real limit (budget, platform, policy, time, environment) was not designed around.

## Point 2 — Construction failures (engineer phase)
The build is incomplete or defective against a correct target.

8. Happy path only: works on expected input, breaks on the unexpected.
9. Unwired component: a part exists but is not connected to the rest.
10. Last mile dropped: 90% present, the final usable stretch missing.
11. Setup or environment unaddressed: correct in theory, cannot be run or deployed.
12. Partial coverage: did some of a set (3 of 5 cases, 4 of 7 sections) and stopped.
13. Factual or logic error: a defect in the content itself.
14. Stale facts: right at training time, wrong now (the Gate 5 failure).
15. Invented specifics: hallucinated an API, citation, number, or rule.
16. State, persistence, or security holes: breaks across sessions, leaks, or fails under real access.
17. Integration break: works in isolation, fails inside the real system.

## Point 3 — Delivery failures (verification and handoff)
The build may be right, but it is not proven or cannot be received.

18. Unverified: "should work" shipped as "works," no self-test.
19. Confidence performed: certainty asserted over an unchecked assumption.
20. No proof: claims done, shows no evidence.
21. Silent omission: what could not be done was not named; the requester finds the gap by hitting it.
22. Trade-off swallowed: a choice that was the requester's to make got decided silently.
23. Unusable form: correct but hard to operate (wrong format, no instructions, poor structure, no next step).
24. Quality ceiling too low: generic first-plausible output when a sharper version was reachable.
25. No durability: works once, no version control, breaks on next change, future maintainer is stuck.

## Root cause

Nearly all 25 collapse into one thing: the gap between what the requester
pictured and what got built was never measured before delivery. Single-pass
failure is almost always a missing measurement, not a missing skill.
