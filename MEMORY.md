# Project Knowledge Index

One line per knowledge file maintained in this working directory. Pointers only: the content lives in the linked files.

## Task-build catalog (the architect-then-engineer reference set)

Three companion files, read together, that the Completion contract, /forge, /blueprint, and /battery treat as their source of truth so failure surfaces and the success target are not re-derived from memory each run.

- [Task Failure Modes](task-failure-modes.md): the 25 ways a single-pass build fails, grouped as target (architect), construction (engineer), and delivery failures; the checklist the Completion contract step 4 and /battery check against.
- [Task Success Moves](task-success-moves.md): the four stacking moves that make a build exceed rather than just satisfy (reconstruct the real target, build complete, add the unasked layer, verify silently); the "exceed" standard for the input-upgrade default.
- [Task Build Pipeline](task-build-pipeline.md): the 9-step procedure from rough prompt to finished result, the architect/engineer phase boundary, and the mapping to each governing rule; the operational spine /forge runs end-to-end.

## Servo (task-completion learning loop) and canonical profile

The persistent stores the Learning loop rule and the Completion contract read and write, plus the document-verified personal profile that is authoritative over Claude's memory.

- [Miss Log](miss-log.md): Servo's learning store: the seven-category error taxonomy, the running tally dashboard, and one row per logged miss; read at the start of task-completion work to bias attention toward the dominant failure stage.
- [Spec Library](spec-library.md): reusable done-specs for recurring task types, loaded at the Completion contract's define-done step and updated when a SCOPE or INTENT miss hits a recurring type.
- The Constant (profile): Coco's canonical, document-verified personal profile, authoritative over drifted memory; the full source behind the ABOUT ME block deployed in User Preferences. Moved to `profile.md` in the private `claude-preferences-private` repo (2026-07-07) as part of the public/private split; not publicly linkable since that repo stays private.
- [Session record 2026-06-14](session-2026-06-14-servo-constant-build.md): build record: Servo, the Constant, the DO-NOT-REPEAT block, and the em-dash structural fix, 2026-06-14.
