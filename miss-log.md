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
- Recurrence: a new miss in a category that already received a structural fix is a recurrence, not a fresh single miss. The prior fix did not hold. Re-trigger escalation on the first recurrence (do not wait for three more), reopen and replace the failed fix via Part 3 / Part 3D rather than point-patching it again, and mark the category [REGRESSED] in the dashboard until the replacement fix is verified. Every recurrence feeds the recurrence-rate metric below.
- Dashboard: the category tally below. Whichever category dominates is the stage to fix next. FORK-heavy means tighten the fork rule. INTENT-heavy means sharpen the architect phase.

## The log
| Date | Task | Category | Miss | Root cause | Adjustment |
|------|------|----------|------|------------|------------|
| 2026-06-13 | Servo file titles | BUILD | Created both file titles with em-dashes, against the no-em-dash convention | Applied default title styling without running the punctuation constraint at build time | Corrected both titles in this commit. Single instance, no structural fix (BUILD threshold is 3) |
| 2026-06-14 | Profile pre-fill | ASSUMPTION | Pre-filled the profile from memory: inferred generic [REDACTED: see private repo] status (actually [REDACTED: see private repo]), omitted the RDP capstone AiCare entirely (not in memory), and softened the experience framing; user corrected from documents | Filled profile facts from project-scoped memory instead of verifying against the user's own documents first | User corrected from source documents this turn; profile.md is now document-verified. The flag-and-verify step caught it. Single instance, no structural fix (ASSUMPTION threshold is 3) |
| 2026-06-14 | Profile vs memory blend | SCOPE | Deployed profile supplied correct facts, but stale memory leaked "founder" and "speaking-prep product" into the output; the profile did not override drifted memory | Profile's done-definition omitted a precedence clause making it authoritative over memory | Added a precedence clause to the ABOUT ME block and profile.md so the verified profile wins on conflict; mitigates, does not guarantee, memory leak. Single instance, no structural fix (SCOPE threshold is 3) |
| 2026-06-14 | No-em-dash rule slip (fresh-chat test) | BUILD | A fresh-chat reply used two em-dashes, violating the global no-em-dash rule; the Punctuation rule did not fire | Existing rule present but not firing reliably across chats | Promoted em-dashes to the DO-NOT-REPEAT block as a blunt backstop. Second BUILD instance; one more hits the three-strike structural threshold. Confirmed: DO-NOT-REPEAT entry fixed free-prose em-dashes (clean James Clear paragraph on retest). Residual traced to reproduced format templates (voice line, correction line, title examples), fixed by de-em-dashing those templates. |
| 2026-06-14 | Correction-line em-dash persisted after point fix | BUILD | After the template fix, the prompt-correction line still reproduced the old em-dash form of the no-corrections message, so the point fix missed an instance | Fixing reproduced templates one at a time keeps missing instances; the prior "no further fix needed" call was premature | Structural fix: swept ALL em-dashes out of user-preferences.md in one pass and verified with grep, so no template can reproduce one. Third BUILD instance, three-strike structural fix applied |
| 2026-07-06 | Repo-edit handoff block delivery | BUILD | Handoff block opened with a User-Preferences slash command (/refactor) as if it were a Claude Code CLI command; Claude Code returned "Unknown command" and treated the rest as args | Handoff block template used a Claude Code slash-command prefix, a convention from interactive CLI usage, instead of the plain-instruction convention required for User-Preferences repo-edit handoffs | Fix: repo-edit-handoff blocks never carry a slash-command prefix; plain instructions only. Fourth BUILD instance, and BUILD already carries a three-strike structural fix from 2026-06-14 — this is a recurrence, not a fresh single miss |
| 2026-07-07 | Cross-project fetch pointer mechanism | ASSUMPTION | Proposed raw-fetch pointer mechanism across multiple turns without testing Claude's own fetch tool against it first; found broken only after repo/PII/path work was built on top of it | Recommended a mechanism Claude itself controls without testing it in the turn it was proposed | Structural fix applied this commit: Gate 4 Part A mechanism-verification addition (self-triggering, tests Claude-controlled mechanisms before they are recommended), a DO-NOT-REPEAT entry, and the /probe slash command for on-demand re-checks. Second ASSUMPTION instance, below the three-strike threshold; status OPEN pending verification the fix holds |

## Category tally (the dashboard)
Each non-zero category carries a fix-status bracket: [OPEN] = misses logged, below threshold, point-fixed only; [FIXED] = reached the three-strike threshold and received a structural fix; [REGRESSED] = a new miss landed after that structural fix (a recurrence; the fix did not hold). Zero-count categories carry no bracket.

- TRIAGE: 0
- INTENT: 0
- FORK: 0
- SCOPE: 1 [OPEN]
- ASSUMPTION: 2 [OPEN]
- BUILD: 4 [REGRESSED]
- STALE: 0

Recurrence rate: 1 recurrence / 1 structural fix = 100%. (Post-fix misses divided by structural fixes applied. Tracks whether fixes hold; a rising rate means structural fixes are not sticking and the responsible rule or spec needs rework, not another point fix.)

Last updated: 2026-07-07
