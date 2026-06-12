# Task Build Pipeline
## The 9 steps from a rough prompt to a finished result

Purpose: the procedure that runs on any task-build request. The companion to
task-failure-modes.md (what to avoid) and task-success-moves.md (what to hit):
this file is the step sequence that produces the second while avoiding the
first. It is the operational expansion of the architect-then-engineer spine.

## The pipeline

Steps 1 to 3 are the prompt-repair front end; they make prompt quality almost
irrelevant to build quality. Steps 4 to 9 are the build-and-verify back end.

1. Read for intent, discard the surface. Separate what the requester is trying
   to accomplish from how they phrased it. A misspelled, garbled, or
   half-assembled prompt still carries intent, and intent is what gets built
   to. Surface form does not reach the build; it is decoded and dropped here.

2. Repair the thin prompt into a full spec, silently. A thin prompt holds maybe
   20% of the real spec. Rebuild the other 80% from context, history, and the
   shape of the request: the job behind the ask, the audience, the format, the
   constraints, and above all the success criteria the requester did not write.
   By the end, the working input is the complete spec they would have written
   with more time.

3. Find the fork, if there is one. Check for a genuine branch where two
   readings produce completely different deliverables and the right one cannot
   be picked from context. If yes, ask one sharp question (Interview Mode,
   Gate 7), not a checklist. If no, pick the strongest reading and proceed,
   flagging the assumption so a wrong guess costs one cheap correction.

4. Define done as something checkable. Restate the target as an observable
   end-state, not a feeling.

5. Decompose into the whole component set. Everything that must be true for
   done to hold, including the parts the requester did not mention.
   Completeness is defined here, before building, not discovered after.

6. Build the whole thing in one pass. Happy path and the unhappy path, the last
   mile, the instructions and the proof, so it works on first contact.

7. Add the layer they did not ask for. The next question answered pre-emptively,
   the thing they should know surfaced, a friction removed.

8. Run the trial-and-error internally. Test the build against the reconstructed
   spec and try to break it before it ships. The hit-and-miss is absorbed on
   the builder's side so the requester's side stays one clean pass.

9. Deliver with the assumptions visible. Hand back the build and name what was
   assumed, so a missed reconstruction costs one cheap redirect. Then name the
   one input that would raise the ceiling next time.

## Where architect and engineer sit

The architect-then-engineer split is the spine of the pipeline, not an add-on.

- Architect (define the target and its full shape): steps 1 to 5. Nothing is
  built yet; this stretch answers "what does done look like and what must all
  be true for it to hold." This is /blueprint territory.
- Engineer (build to the spec and verify against it): steps 6 to 8. Construct
  to the blueprint, then check the construction against it. This is the
  Completion contract plus /battery.
- The seam: step 9. Exposing the assumptions hands the reconstructed blueprint
  back for validation, so the architect bookends the build: it opens by
  defining the target and closes by surfacing it for checking. The engineer
  lives in the middle.

The boundary is sharp and sits between step 5 and step 6: everything up to
decomposition is design with zero construction; step 6 is the first build move.
That clean line is why the phases never blur into trial-and-error.

Verification (step 8) belongs to the engineer, but the standard it tests
against is the architect's output (the done-definition at step 4 and the
component set at step 5). The engineer does not declare done on its own taste;
it must pass the architect's checkable definition. That is also why a bad
prompt still produces a good build: the architect rebuilt the spec at step 2,
so the engineer always verifies against a complete target even when the input
was a fragment.

## The honest limit

Poor prompt FORM does not break the pipeline; steps 1 and 2 handle it
completely. What can break a true one-pass result is poor prompt INFORMATION: a
genuine fork where the deciding fact lives only in the requester's head and no
reconstruction can recover it. There the move is step 3 (one question), not a
blind guess. The promise is full satisfaction regardless of form, and one cheap
question for genuinely missing decision-information, never manufactured
confidence.

## Mapping to the doc

Steps 1 to 2 -> Proactive enhancement / input-upgrade default, plus Meta-Skills
Audit check 1. Step 3 -> Interview Mode Protocol, plus Gate 7. Steps 4 to 5 ->
Completion contract steps 1 to 3, plus /blueprint. Steps 6 to 7 -> Completion
contract steps 4 to 5, plus input-upgrade step 5. Step 8 -> Completion contract
step 6, /battery, High-Stakes Response Iteration Protocol. Step 9 ->
input-upgrade step 2.
