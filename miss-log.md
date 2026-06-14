# Miss Log: Servo's learning half

## What this file is
The persistent error-sensing half of the task-completion system. Every time a delivered task misses (the user redirects, corrects, or rejects it), the miss is recorded here by category. Single misses get diagnosed and adjusted on the spot. Repeated misses in one category trigger a structural fix. The running count by category is the dashboard showing which stage of the system is leaking.

This file is Project Knowledge. Claude loads it on task-completion work, logs misses to it, and reads the tally to know where the system is weakest.

## The error taxonomy
A miss gets exactly one category, chosen by where the failure originated:

- TRIAGE: wrong size routing. Heavy process on a simple ask, or a thin answer to something that needed the full build.
- INTENT: decoded the wrong ask. Competent answer to an adjacent question.
- FORK: assumed when it should have asked, or asked when it should have assumed.
- SCOPE: defined "done" wrong, or missed a component that "done" required.
- ASSUMPTION: filled a gap with a wrong fact or wrong choice.
- BUILD: a component built wrong: unhappy path, unwired part, missing last mile.
- STALE: relied on a fact that was outdated or unverified.

## The record format
One row per miss in the log table below:
- Date: when.
- Task: one phrase naming the task.
- Category: one of the seven above.
- Miss: what was delivered versus what was wanted, one line.
- Root cause: which stage leaked, one line.
- Adjustment: the feed-back applied (spec update, calibration tweak, or checklist addition) and where it lands.

## How the loop closes
- Single miss: diagnose, log the row, apply the adjustment immediately.
- Pattern (3 or more of the same category, per the Part 3B threshold): trigger a structural fix to the responsible rule or spec, not just a point fix.
- Dashboard: the category tally below. Whichever category dominates is the stage to fix next. FORK-heavy means tighten the fork rule. INTENT-heavy means sharpen the architect phase.

## The log
| Date | Task | Category | Miss | Root cause | Adjustment |
|------|------|----------|------|------------|------------|
| 2026-06-13 | Servo file titles | BUILD | Created both file titles with em-dashes, against the no-em-dash convention | Applied default title styling without running the punctuation constraint at build time | Corrected both titles in this commit. Single instance, no structural fix (BUILD threshold is 3) |

## Category tally (the dashboard)
- TRIAGE: 0
- INTENT: 0
- FORK: 0
- SCOPE: 0
- ASSUMPTION: 0
- BUILD: 1
- STALE: 0

Last updated: 2026-06-13
