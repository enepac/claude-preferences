# Writing-pattern knowledge base

A living instrument: the recurring patterns in Coco's prompt writing, the
diagnostic taxonomy that names them, and the targeted practice plan derived
from them. Source data is the "Prompt correction" sections logged across this
project's chats.

**Status: v1.** The corpus below is a partial sample (eleven corrected prompts
recovered by searching past project chats, not the complete history).
Frequencies are therefore directional, not exact counts. They will firm up as
the log grows. Treat the *ranking* as reliable and the *numbers* as estimates.

---

## How to use this file

1. Read section 3 first. It names the single habit that produces most of the
   errors. Fixing one root habit removes a plurality of mistakes, which is a
   far better return than chasing every category at once.
2. Use section 5 as the practice regimen. Each drill targets one named
   category with a built-in feedback loop.
3. Append new corrections to section 4 using the controlled-vocabulary tags in
   section 1, so the frequency table in section 2 stays measurable over time.
4. Update the tallies in section 7 when you log entries.

---

## 1. Pattern taxonomy (controlled vocabulary)

Eight categories, each with a stable tag. Every logged correction is tagged
with one or more of these. The tag set is the spine of the whole instrument:
free-text issue descriptions do not aggregate, tagged ones do.

| Tag | Category | What it covers |
|-----|----------|----------------|
| SVA | Subject-verb / verb-form agreement | Verb does not match its true subject in number ("process get" → "gets", "consensus use" → "uses"), or wrong verb form ("you can response" → "respond", "don't ignored" → "not ignore", "will takes" → "will take"). |
| FNW | Missing function word | A dropped article, preposition, or conjunction that the sentence needs ("for the purpose [of]", "give me [a] summary", missing "that" joining two clauses, "every [one] of"). |
| RUN | Run-on / sentence boundary | Two or more full clauses fused without the punctuation or conjunction that should separate them; one sentence carrying too many ideas. |
| NUM | Noun-number / compound-modifier | Article-noun number mismatch ("an artifacts", "an application products"), or a compound modifier that should be singular and hyphenated ("atomic-units level" → "atomic-unit level"). |
| TGT | Wordiness / needs tightening | The meaning is recoverable but the phrasing is loose, redundant, or vague and a tighter version reads cleaner ("beyond convention" → "beyond conventional use cases"). |
| PUN | Punctuation precision | Missing apostrophe ("audits worth"), missing comma before a participial phrase or a coordinating clause, missing terminal mark, inconsistent capitalization. |
| PRO | Pronoun-antecedent agreement | A pronoun that does not match its antecedent in number ("mistakes ... as it appeared" → "as they appeared"; "log it" where the antecedent is plural → "log them"). |
| QFM | Question-form construction | Auxiliary-verb inversion in questions ("What I must do" → "What must I do"). |

---

## 2. What the corpus shows (frequency)

Ranked across the eleven sampled prompts. A single prompt often carries more
than one tag, so counts sum to more than eleven.

1. **SVA — Subject-verb / verb-form agreement. ~7 of 11.** The dominant
   pattern by a wide margin.
2. **FNW — Missing function word. ~5 of 11.**
3. **RUN — Run-on / sentence boundary. ~4 of 11.**
4. **NUM — Noun-number / compound-modifier. ~4 of 11.**
5. **TGT — Wordiness / tightening. ~4 of 11.**
6. **PUN — Punctuation precision. ~2 of 11.**
7. **PRO — Pronoun-antecedent agreement. ~1 of 11** (emerging; first clear
   instance is the prompt that requested this file).
8. **QFM — Question-form construction. ~1 of 11.**

The shape that matters: the top item is roughly as frequent as the next two
combined, and the top four are all *agreement* problems of one kind or another
(verb to subject, noun to article, modifier to noun, pronoun to antecedent).
This is not eight scattered weaknesses. It is mostly one weakness wearing four
costumes.

---

## 3. The dominant signal — fix this first

One mechanism produces the plurality of the errors: **the agreement check is
not running.** When a sentence has words between the subject and its verb, or
shifts from singular to plural mid-thought, the link breaks. "The
precedent-first process get executed." The verb agreed with the nearest noun,
or with nothing, instead of with *process*.

This is good news, not bad. A learner with one root habit to fix improves
faster than a learner with a long unranked list, because the practice can be
concentrated. The target is specific and well-defined: **make the verb agree
with its true subject, and carry number agreement through the whole sentence.**

Everything in section 5 is built around hitting that target with reps and
immediate feedback, which is what actually moves a trainable skill, rather than
a general instruction to "write more carefully" (which gives the practice
nothing specific to grip).

---

## 4. The corpus (logged instances)

The data behind sections 2 and 3. Each entry is the corrected (target) version
plus the diagnosed tags. Newest first.

- *"...for the purpose of converting it into an artifact serving as a knowledge
  base of the way I write."* (the request that created this file)
  Tags: **PRO** (it/they, it/them), **FNW** (missing "of"; "every [one] of"),
  **NUM** ("an artifacts" → "an artifact"), **RUN** (overloaded first
  sentence), plus a typo ("aritifacts").

- *"User preferences are already pruned. Proceed with the next workstream, the
  /preflight slash command patch."*
  Tags: **SVA** ("User preference is" → "User preferences are").

- *"Nah, let's skip these steps. ... draft me an embeddable prompt I could
  append to all my prompts..."*
  Tags: **SVA** ("will takes" → "will take"; "you can response" → "respond";
  "don't ignored" → "not ignore"), **NUM** ("this steps" → "these steps"),
  **FNW** ("at the end in all" → "to all"), **TGT** (tightened a vague phrase).

- *"Here's another thing. We keep adding instructions ... I insist and urge you
  to find a fix for this recurring issue."*
  Tags: **FNW** (missing "that"), **RUN** (the missing "that" fused two
  clauses), **SVA** ("I insists and urges" → "I insist and urge").

- *"I think this prompt is best for learning. I am not sure it can be applied to
  solving problems like finding a job..."*
  Tags: **FNW** + missing verb (stray "what", dropped verb), **SVA**
  ("create an application products"), **RUN** (one run-on split into a parallel
  list).

- *"Can you give me a point-by-point summary of how the consensus uses
  Claude.ai beyond conventional use cases?"*
  Tags: **SVA** ("consensus use" → "uses"), **FNW** (missing "a"),
  **TGT** ("beyond convention" → "beyond conventional use cases").

- *"Design a custom writing-style voice based on William Zinsser's On Writing
  Well."*
  Tags: **NUM** (compound-modifier hyphenation), **PUN** (possessive),
  **TGT** (loose dash tightened), plus title italicization.

- *"Now, can you tell me how the precedent-first process gets executed?"*
  Tags: **SVA** ("process get" → "gets").

- *"What is the way to become a Claude Certified Architect? What must I do?"*
  Tags: **QFM** ("What I must do" → "What must I do").

- *"Draft a prompt to document the user preferences doc, covering how the
  pieces work together, grounded at the atomic-unit level."*
  Tags: **PUN** (comma before participial phrase), **NUM** ("atomic-units
  level" → "atomic-unit level").

- *"We have three audits' worth of new rules ... Which would you do first, and
  why?"*
  Tags: **RUN** (one run-on split into three), **PUN** (missing apostrophe in
  "audits worth", missing comma before "and why", missing question mark,
  sentence-start capitalization).

---

## 5. Targeted practice plan

The principle: deliberate practice works on a specific, well-defined weakness,
at the edge of current ability, with immediate feedback and many reps. A vague
goal ("improve my writing") gives the practice nothing to grip. A precise goal
("eliminate SVA errors") does. The drills below are ordered by the section 2
frequency, so effort lands where the errors are.

The feedback loop already exists: the prompt-correction block on every
substantive response is the immediate correction. The drills add the
*pre-send* check that turns passive correction into active reps.

**SVA (do this one first).** Before sending a prompt, find each main verb,
strip out the words between it and its subject, and read just *subject + verb*.
"The precedent-first process ... get" reads wrong instantly once the modifiers
are gone. A 20-second scan. Run it on every prompt for two weeks; the check
becomes automatic and the category should fall out of the top spot.

**FNW.** Read the prompt under your breath before sending. Dropped articles,
prepositions, and "that" are almost always *audible* as a stumble even when
they are invisible on the page. The ear catches what the eye skips.

**RUN.** First-draft rule: one idea per sentence. When you join two full
clauses with "and", "but", or nothing, decide deliberately whether they belong
in one sentence. If in doubt, split. Two clean sentences beat one fused one.

**NUM.** Whenever an article meets a noun, check they agree in number ("an
artifact", not "an artifacts"). For two-word modifiers in front of a noun,
default to singular and hyphen ("atomic-unit level", "first-class seat").

**TGT, PUN, PRO, QFM.** Lower frequency; let the correction block carry the
feedback for now rather than spending separate drill time. Promote one into the
active drill set only if section 7's tally shows it climbing.

---

## 6. Logging protocol

Keep this instrument self-consistent so the frequency data stays trustworthy.

- Every new prompt-correction instance gets appended to section 4: corrected
  version, then tags from section 1.
- Use the existing tags. Add a new tag only when a genuinely new category
  appears (and add its row to section 1 at the same time).
- Update section 7's tally when you log.

**Self-populating option (recommended enhancement).** The prompt-correction
rule in User Preferences currently ends each correction with a free-text
"Issues addressed:" line. Free text does not aggregate. If that line is
sharpened to *also* emit the controlled-vocabulary tags (e.g. "Issues
addressed: SVA, FNW — subject-verb agreement and a dropped article"), then
every correction is already log-ready and the tally can be maintained by
copy-paste rather than re-diagnosis. This is a sharpen of an existing rule, not
a new rule, so it routes through the normal Claude Code editing workflow.

---

## 7. Progress tracking

Running tally (update on each log entry). Starting point = the v1 corpus.

| Tag | Count (v1) | Latest | Trend |
|-----|-----------|--------|-------|
| SVA | ~7 | ~7 | — |
| FNW | ~5 | ~5 | — |
| RUN | ~4 | ~4 | — |
| NUM | ~4 | ~4 | — |
| TGT | ~4 | ~4 | — |
| PUN | ~2 | ~2 | — |
| PRO | ~1 | ~1 | — |
| QFM | ~1 | ~1 | — |

**Graduation criterion (per category).** A category is "controlled" when it
records zero new instances across ten consecutive logged prompts. When SVA
graduates, move the next-ranked category into the active drill slot in
section 5.
