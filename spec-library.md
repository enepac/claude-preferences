# Spec Library — the compounding half of the task-completion system

## What this file is
The durable home for done-specs of recurring task types. When a build task arrives, Claude checks here at the architect phase's define-done step for a matching spec. A match loads the saved target instead of re-deriving "done" from scratch, so a task type done repeatedly gets faster and more consistent each time. This is the compounding half of the system; miss-log.md is the learning half.

This file is Project Knowledge. Claude reads it at the start of a build task and updates it when a recurring-type miss reveals a spec was incomplete.

## How a spec loads and grows
- Load: at the define-done step, match the task against the entries below. Match, load that spec and skip re-deriving done. No match, build from scratch and consider whether this type recurs enough to add.
- Born: add a new spec when a task type recurs (three or more times, the same threshold the rest of the system uses) or when the user says "save this as a spec."
- Updated: when a SCOPE or INTENT miss (per miss-log.md) lands on a task type that has a spec, update the spec so the gap does not recur. This is the recurring-type branch of the learning loop: a systemic pattern fixes a rule, a recurring-type pattern fixes the spec here.

## Entry format
Each spec uses these fields:
- Type key and match triggers: a short name, then the phrases or conditions that identify a task as this type.
- Done: the observable end-state for this type.
- Constraints: length, tone, format, and hard "must avoid" items.
- Success bar: what separates a strong result from a passable one.
- Known traps: failure modes seen before on this type, fed from the miss log.
- Updated: date.

## Entry template (copy this structure for each new spec)
### [type-key]
- Match triggers: [phrases or conditions]
- Done: [observable end-state]
- Constraints: [length, tone, format, must-avoid]
- Success bar: [strong versus passable]
- Known traps: [failure modes from the miss log]
- Updated: [date]

## Specs
(No specs yet. The first entry lands when a task type recurs or the user says "save this as a spec.")
