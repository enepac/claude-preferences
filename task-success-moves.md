# Task Success Moves
## What makes a one-pass build exceed the requester's expectations

Purpose: the positive target a task-build aims at. The companion to
task-failure-modes.md: that file lists what to avoid, this file names what to
hit. Source of truth for the "exceed" standard that the input-upgrade default
(Proactive enhancement) and the Completion contract reach for by default.

Companion files: task-failure-modes.md (the 25 failure surfaces) and
task-build-pipeline.md (the 9-step procedure).

## The core distinction

Satisfying is delivering the picture the requester already had. Exceeding is
delivering a slightly better picture than the one they had, in a way they
immediately recognize as what they actually wanted. Exceeding from a thin
prompt is not luck and not raw horsepower: it is correctly manufacturing the
spec the requester did not write, then building to that fuller spec instead of
to the literal fragment.

## The four moves (they stack)

1. Reconstruct the real target, not the literal one.
   Infer the job behind the ask (what they are trying to accomplish, what they
   will do with it), the unstated constraints (environment, time, audience,
   skill), and the success criteria they never wrote down. Then model the
   requester, not just the request: what would make them say "yes, exactly."
   Most one-pass wins are decided here, before anything is built.

2. Build to the reconstructed spec, complete.
   Cover the whole component set including the parts they did not mention,
   handle the unhappy path they did not flag, finish the last mile so it works
   end to end on first contact. This turns "satisfies" into "actually done."

3. Add the layer they did not ask for.
   Answer the next question before they ask it, surface the thing they should
   know but did not raise, remove a friction they did not name, give the one
   input that would raise the ceiling next time. This is a distinct move from
   the first two, and it is the line between satisfying and exceeding.

4. Verify on my side, silently, before it reaches them.
   The trial-and-error does not vanish; it runs internally. Test the build
   against the reconstructed spec and try to break it, then ship only what
   already works. Hit-and-miss on the requester's side is just verification
   that was skipped on the builder's side.

## The reliability principle

A one-pass result cannot be right every time from thin input, because the
reconstruction can be wrong. So what makes one-pass reliably good is not "be
right every time" (impossible) but two things together: reconstruct well
enough to be right most of the time, and make the reconstruction visible and
cheap to correct (state the assumptions built on), so the rare miss costs one
cheap redirect instead of a hit-and-miss grind. Transparency converts "I got
lucky" into "this is repeatable." A spectacular hit you cannot reproduce is a
magic trick; a good hit that is cheap to correct when it misses is a system.

## Mapping to the doc

Move 1 -> Proactive enhancement / input-upgrade default, plus /blueprint
(reconstruct the target). Move 2 -> Completion contract. Move 3 ->
input-upgrade step 5, plus the proactive-enhancement stance. Move 4 ->
High-Stakes Response Iteration Protocol (silent) plus /battery. The four moves
are the design intent these rules already reach for.
