# Session record: Servo, the Constant, DO-NOT-REPEAT, em-dash structural fix
Date: 2026-06-14
Status: all systems built, deployed, and verified in fresh chats.

## What was built (all global, in User Preferences, so active across every project)

### Servo (self-correcting task-completion + learning loop)
- miss-log.md (repo) created: seven-category error taxonomy (TRIAGE, INTENT, FORK, SCOPE, ASSUMPTION, BUILD, STALE), record format (Date | Task | Category | Miss | Root cause | Adjustment), a category tally dashboard, and the loop rule (single miss = log + fix; 3+ same category = structural fix per the Part 3B threshold).
- spec-library.md (repo) created: reusable done-specs for recurring task types, loaded at the Completion contract's define-done step, updated when a SCOPE or INTENT miss hits a recurring type.
- "Learning loop (miss logging and structural feedback)" rule added to user-preferences.md (Part 2): senses every miss, logs to miss-log.md by category, applies the immediate correction, escalates a recurring category to a structural fix, and (step 6) promotes a non-identity recurring error to DO-NOT-REPEAT. Reciprocal interaction notes written into Failure-as-data, Part 3B, Gate 6, and the Local artifact editing workflow. spec-library wired into the define-done step and the reference catalogs.

### The Constant (global personal profile, authoritative over drifted memory)
- profile.md (repo) created, titled "The Constant: Coco's canonical profile." Full profile, corrected by the user from source documents (work permit, RDP transcript, IQAS, EE audit).
- "ABOUT ME (global profile)" block deployed in User Preferences (after the title heading, before Part 1): compact always-available copy with an Authority/precedence clause making the verified profile win over Claude's memory on conflict (e.g., "Software Developer" not "founder"; celpip-prep is a practice app, not "speaking-prep").

### DO-NOT-REPEAT (active suppressions) block
- Deployed in User Preferences (after ABOUT ME): compact global suppression list for NON-identity recurring errors only (identity facts stay with the Constant). Fed by the Learning loop's step-6 promotion branch. Only entry so far: the em-dash backstop.

### Em-dash structural fix
- Full sweep of user-preferences.md removed every em-dash (U+2014), preserving en-dashes and hyphens, verified by grep returning empty. Root cause: reproduced format templates (voice line, prompt-correction line, title examples) carried em-dashes, so reproducing a template faithfully reproduced the violation. Point-fixing templates one at a time kept missing instances; the comprehensive sweep was the actual structural fix. Logged as the third BUILD strike. Verified clean in a fresh-chat retest: voice line, title, body, and correction line all dash-free.

## miss-log tally at session close
TRIAGE 0 | INTENT 0 | FORK 0 | SCOPE 1 | ASSUMPTION 1 | BUILD 3 | STALE 0. Last updated 2026-06-14.

## Key decisions and learnings
- A "pointer in preferences to external storage" does not give a global profile; the only truly global layer is the User Preferences Settings field, plus connector sync for project depth. The Constant is built on that.
- Deployment reality: knowledge files go live on connector re-sync; behavior rules (user-preferences.md) require a manual re-paste into Settings to activate. Several retest failures traced to a skipped or stale Settings paste.
- Servo reduces and relocates iteration; it does not achieve literal one-pass perfection. Honest framing held throughout.
- The three-strike rule worked as designed: it escalated the em-dash issue from point fixes to a comprehensive sweep once point-fixing demonstrably kept missing instances.

## Still open (not touched today)
- Deferred-findings batch: 4 findings (Test 9 B/E, Test 10 B, Test 11 C).
- Curriculum lessons 15-19 of 19 undelivered.
- Lesson 3 anomaly: curriculum revision overdue.
- spec-library.md created but unpopulated; fills as recurring task types appear.
- The actual PR goal (Alberta tech offer, CRS gain, CELPIP Reading band) is untouched. Everything today was tooling.
