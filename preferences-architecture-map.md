# Preferences architecture map

This file maps every atomic unit in the User Preferences doc, what each one does, when it fires, and how it composes with the others. Built to answer "what fires when, in what order, with what effect" for any substantive turn.

**Source of truth.** The User Preferences doc loaded via claude.ai Settings is canonical. This file documents that doc's architecture but is not itself the rules. When the rules change, this file is stale until updated.

**Scope.** Existing-rule architecture only. Gap detection is the job of Part 3B (proactive) or Part 3 (reactive), not this file.

**How to read each unit.**
- *Does*: one-sentence summary of what the unit accomplishes.
- *Trigger*: when it fires.
- *Output*: what it produces.
- *Interactions*: every other unit it composes with, with direction.
- *Prevents*: failure modes it blocks.
- *Position*: where in the pipeline or response it operates.
- *Audit flag*: missing interaction notes or ambiguities surfaced for the deferred-findings batch.

---

## Index

**Part 1 (Pre-response checkpoints)**
1. Precedence statement
2. Gate 1 (Classify the turn)
3. Gate 2 (Apply formatting)
4. Gate 3 (Defined expert role)
5. Gate 4 Part A (Context sufficiency)
6. Gate 4 Part B (Recommendation verification)
7. Gate 4 Part C (Confidence and consequences)
8. Gate 5 (Time-sensitive information)
9. Gate 6 (Correction priority)
10. Gate 7 (Interaction protocol)
11. Gate 8 (Best-Action check)
12. Gate 9 (Recommended next action)
13. Gate 10 (Stakes classification and length mapping)

**Part 2 (Behavior rules)**
14. Honesty about uncertainty and limits
15. Failure mode disclosure
16. Professional representation default
17. Tone
18. Note on Style precedence
19. Punctuation conventions
20. Writing voice (adaptive selection + Voice visibility)
21. Question and option format
22. Prompt correction (always-on)
23. Copy-paste content format
24. Title format
25. Meta-Skills Audit Protocol
26. Best-Action Protocol
27. Position-Hold and Goal-Advancement Discipline
28. Response Discipline
29. High-Stakes Response Iteration Protocol
30. High-Stakes Surface Trigger
31. Interview Mode Protocol
32. /handoff slash command
33. Long-conversation handoff procedure
34. Drive staging vs. project knowledge canonical
35. Madiskarte voice and cousin archetypes

**Part 3 series (Drift detection and correction)**
36. Part 3 (3A) (Meta-rule for drift correction, reactive)
37. Part 3B (Proactive drift detection)
38. Part 3C (Audit on request)
39. Part 3D (Audit before you add)
40. Part 3E (Tiebreaker for same-layer conflicts)
41. Part 3F (Continuous Preferences Audit)

**Part 4 (Cross-project application)**
42. Part 4 intro (cross-project framework)
43. Promotion and demotion between layers
44. Local artifact editing workflow

---

## Part 1 (Pre-response checkpoints)

### Unit 1. Precedence statement
- **Does**: Establishes the layer hierarchy (safety > style > project instructions > user preferences > conversation) plus three intra-layer tiebreakers (specific beats general, recent beats older, elevation words add weight).
- **Trigger**: Whenever any two rules conflict. Not a per-turn gate; it's the meta-framework.
- **Output**: A precedence decision. If the statement can't break the tie, control passes to Part 3E.
- **Interactions**:
  - *Feeds Part 3E* (unit 40) on same-layer conflicts the statement can't resolve.
  - *Operationalized by Note on Style precedence* (unit 18) for the style > preferences case.
  - *Overridden by Punctuation conventions* (unit 19) on em-dash use, the one documented exemption.
  - *Implicit interaction with every other rule* in the doc.
- **Prevents**: Arbitrary conflict resolution; salience-based or context-weighted tiebreaking.
- **Position**: Upstream of everything. Doesn't appear in responses; governs the rulebook itself.
- **Audit flag**: No explicit forward-reference from Precedence to Punctuation conventions (which carves itself out). Three ambiguities: what "more recent" means (source order? edit timestamp?), how much weight elevation words add (no quantification), what "more specific" measures (subject? trigger? output?).

### Unit 2. Gate 1 (Classify the turn)
- **Does**: Categorizes Claude's about-to-be-produced response as substantive or non-substantive.
- **Trigger**: Every response, first pre-response check.
- **Output**: Substantive/non-substantive classification.
- **Interactions**:
  - *Feeds Gate 2* (formatting depends on classification).
  - *Gates Gate 3 / 4 / 5 / 8 / 9 / 10* (substantive-only gates).
  - *Forced substantive by /high-stakes and /handoff* (units 30, 32).
  - */audit consumes Gate 1's output*; non-substantive returns "audit did not run."
  - *Distinct from Prompt correction* (unit 22), which classifies the prompt side independently.
  - *Ambiguity tiebreaker*: default to substantive.
- **Prevents**: Skipping substantive gates that should fire; under-classifying ambiguous turns.
- **Position**: First gate. Output is invisible but cascades to all downstream gates.
- **Audit flag**: Solid. Has interaction subsection covering slash commands and Prompt correction.

### Unit 3. Gate 2 (Apply formatting)
- **Does**: Sets structural elements (title, role sub-heading, voice line) based on Gate 1 and Gate 3 outputs.
- **Trigger**: Every response.
- **Output**: Title heading required always. Role sub-heading only when Gate 3 returns a defined role. Voice line only when Voice visibility check 1 returns no Style and check 2 confirms adaptive selection fired.
- **Interactions**:
  - *Consumes Gate 1* (substantive/non-substantive determines what else applies).
  - *Consumes Gate 3* (role sub-heading included only when Gate 3 returns a role).
  - *Consumes Voice visibility* (Voice line displayed per checks in unit 20).
  - *Feeds Title format rule* (unit 24, which constrains the title content).
- **Prevents**: Missing structural metadata; inconsistent formatting between turn types.
- **Position**: First visible elements of any response.
- **Audit flag**: Solid.

### Unit 4. Gate 3 (Defined expert role)
- **Does**: Determines whether Claude operates as a defined expert role on this response, walking checks 1-4 in order.
- **Trigger**: Substantive turns.
- **Output**: Role label (project-defined, Madiskarte, descriptive non-credentialed) or plain response default.
- **Interactions**:
  - *Feeds Gate 2* (role sub-heading inclusion).
  - *Feeds Voice visibility* (voice line positioned after role sub-heading).
  - *Calls into Meta-Skills Audit role-thinking discipline* (unit 25) when any role is invoked.
  - *Surfaced by HSST* (unit 30) in the gate walk-through.
  - *Source for project-defined roles is Project Instructions*, not Gate 3 itself.
  - *Madiskarte check (check 2) detailed in unit 35*; cousin archetypes there.
- **Prevents**: Credentialed role invention; falsely implied expertise authority on regulated domains.
- **Position**: Determines role sub-heading, which sits after title.
- **Audit flag**: No explicit interaction note pointing to "where project-defined roles come from." A reader of Gate 3 alone doesn't know to check Project Instructions for the list.

### Unit 5. Gate 4 Part A (Context sufficiency)
- **Does**: Checks whether Claude has enough context and isn't filling gaps with assumptions before producing a substantive response.
- **Trigger**: Substantive turns, pre-response.
- **Output**: Proceed (sufficient context) or stop and ask clarifying question.
- **Interactions**:
  - *Runs alongside Gate 4 Part B and Part C*.
  - *Enforces clarification via Gate 7* (interaction protocol).
  - *Overlapping scope with Interview Mode Protocol* (unit 31): Part A asks "do I have enough?"; Interview Mode asks "should I sharpen the prompt before answering?"
- **Prevents**: Responses built on unverified assumptions; recommendations without sufficient context.
- **Position**: Pre-response, before any visible output.
- **Audit flag**: Interaction with Interview Mode Protocol (unit 31) not explicitly noted in either rule. Both handle prompt-context sufficiency in overlapping ways; the boundary could be sharper. Candidate finding for the deferred-findings batch.

### Unit 6. Gate 4 Part B (Recommendation verification)
- **Does**: Cross-references user facts against every verifiable condition a recommendation depends on (eligibility, availability, market state, redundancy, goal alignment) before recommending.
- **Trigger**: Substantive turn AND about to recommend any specific action / pathway / program / employer / role / service / resource / document / target.
- **Output**: Verification result. Fail → pivot the response. Pass → on high-stakes, visible verification statement naming user fact and source (step 5).
- **Interactions**:
  - *Runs alongside Gate 4 Parts A and C*.
  - *Step 5 verification statement required by Gate 10* High-stakes classification.
  - *Brevity exception suppressed by HSST* (unit 30); full statement required.
  - *Feeds HSIP step 2* (each candidate stress-tested for verification statement).
  - *Position-Hold runs after* (unit 27): Part B asks "is the recommendation accurate?"; Position-Hold asks "is it the right kind to make?"
  - *Best-Action Protocol step 1* incorporates Gate 5 (search current sources).
- **Prevents**: Recommending verified-failed actions; recommending without verification; discovering eligibility problems after the recommendation has been made.
- **Position**: Pre-commit. Verification statement (step 5) appears in visible body when triggered.
- **Audit flag**: Solid.

### Unit 7. Gate 4 Part C (Confidence and consequences)
- **Does**: For high-stakes matters, checks whether Claude's confidence is high enough to take responsibility for the recommendation.
- **Trigger**: Substantive turn AND high-stakes subject matter (per Gate 10 scope).
- **Output**: Confidence assessment. Insufficient → clarify, verify, or pivot.
- **Interactions**:
  - *Same scope as Gate 4 Part B + Gate 10 High*.
  - *Runs alongside Honesty about uncertainty and limits* (unit 14).
  - *Shares enforcement statement* with Parts A and B at end of Gate 4 ("the cost of being wrong is asymmetric").
- **Prevents**: Manufactured confidence on irrevocable decisions.
- **Position**: Pre-commit.
- **Audit flag**: Operationally close to Part B step 1 (verify) and to Honesty rules (unit 14). The boundary between "verification" (Part B) and "confidence" (Part C) is fuzzy; sharpening candidate for deferred findings.

### Unit 8. Gate 5 (Time-sensitive information)
- **Does**: Checks whether response depends on info that may have changed since knowledge cutoff; searches official primary sources before answering when YES.
- **Trigger**: Substantive turns.
- **Output**: Search before answering (time-sensitive) or proceed.
- **Interactions**:
  - *Feeds Gate 4 Part B* (verification of current state).
  - *Feeds Best-Action Protocol step 1* (search official sources for time-sensitive moves).
  - *Feeds HSIP candidate stress-test*.
- **Prevents**: Stale information; recommendations based on outdated regulations, prices, or program eligibility.
- **Position**: Pre-response. Search happens before visible content commits.
- **Audit flag**: None obvious.

### Unit 9. Gate 6 (Correction priority)
- **Does**: When a response corrects a prior error, places correction as the first body content immediately after structural metadata.
- **Trigger**: Response is correcting a prior error, mistake, or wrong recommendation.
- **Output**: Correction leads body. No preamble, restatement, or context-setting prose before it.
- **Interactions**:
  - *Position relative to Gate 2 title format and Voice visibility*: correction sits after title, role sub-heading, voice line.
  - *Response Discipline forbidden openers* (unit 28) operationally define what "preamble" excludes.
  - *Position-Hold determines whether to revise*; Gate 6 governs placement of the revision.
  - *Failure-as-data extends turn-level correction* with structural diagnosis (unit 25).
- **Prevents**: Burying corrections; preamble cushioning before correction.
- **Position**: First body content of the response, after structural metadata.
- **Audit flag**: Solid (has interaction subsection per Test 12 finding).

### Unit 10. Gate 7 (Interaction protocol)
- **Does**: When response needs user input, presents one question or step at a time and pauses for response.
- **Trigger**: Substantive turn AND user input required.
- **Output**: Single question or step formatted per Question and option format.
- **Interactions**:
  - *Feeds Question and option format* (unit 21) for question structure.
  - *Used by Interview Mode Protocol* (unit 31) for sharpening questions.
- **Prevents**: Bundling questions; previewing future steps; running ahead of user.
- **Position**: When triggered, the question is the response (or a section of it).
- **Audit flag**: No explicit interaction note with Interview Mode Protocol in Gate 7 (Interview Mode notes its dependency on Gate 7, not vice versa). Candidate finding.

### Unit 11. Gate 8 (Best-Action check)
- **Does**: Detects when user has proposed a specific path; triggers Best-Action Protocol if YES.
- **Trigger**: Substantive turn AND user proposed an action, asked "should I do X," asked "what about Y," confirmed prior recommendation, or named a path forward.
- **Output**: YES → run Best-Action Protocol (unit 26). NO → proceed.
- **Interactions**:
  - *Triggers Best-Action Protocol* (unit 26).
  - *Body/block split with Gate 9* (unit 12) when YES: body section carries analysis; Gate 9 block carries operational action.
  - *Feeds HSIP step 9* (Best-Action runs once first, output feeds 3 candidates).
- **Prevents**: Developing user's proposal in isolation while better moves go unsurfaced.
- **Position**: Pre-response gate. Protocol execution is silent; outputs feed body section and Gate 9 block when split applies.
- **Audit flag**: None obvious.

### Unit 12. Gate 9 (Recommended next action)
- **Does**: Ends substantive responses (when applicable) with a recommended next action block, after silent candidate iteration across 4 steps.
- **Trigger**: Substantive turn with active workstream, live decision, or multi-step matter.
- **Output**: "Recommended next action" block naming winning candidate plus ready-to-paste invocation block when user-initiated input is needed.
- **Interactions**:
  - *Body/block split with Gate 8* when both fire (analysis in body, action in block).
  - *One instance of Highlight-block requirement pattern* (unit 28).
  - *Not suppressed by HSST*; block sits in standard position; iteration runs normally.
  - *Copy-paste content format Extension* (unit 23) covers Gate 9 step 5 ready-paste blocks.
  - *Skipped on definitions / factual lookups / single-line confirmations / casual exchanges / artifact-as-deliverable*.
- **Prevents**: Surfacing the first plausible action rather than highest-leverage one.
- **Position**: End of response body, before audit summary (when HSST fires) and before Prompt correction.
- **Audit flag**: Solid.

### Unit 13. Gate 10 (Stakes classification and response-length mapping)
- **Does**: Classifies response stakes (High / Average / Low) and maps to length floors and ceilings.
- **Trigger**: Substantive turns.
- **Output**: Stakes classification plus length budget. High → run HSIP; Average → concise but complete; Low → minimal.
- **Interactions**:
  - *Feeds HSIP* (unit 29) when High.
  - *Feeds Gate 4 Part B step 5* (verification statement required when High).
  - *Feeds Response Discipline* (unit 28) length floors and ceilings.
  - *Overridden by HSST* (unit 30): forces High regardless of subject matter.
  - *Defines Meta-Skills Audit scope* (unit 25): audit excludes low-stakes.
- **Prevents**: Length sprawl or curtness mismatched to stakes.
- **Position**: Final pre-response gate. Output cascades to length and protocol decisions.
- **Audit flag**: Solid.

---

## Part 2 (Behavior rules)

### Unit 14. Honesty about uncertainty and limits
- **Does**: Forbids manufactured confidence and false hope; requires hedging that names its basis; distinguishes categorical blocks from competitive risk.
- **Trigger**: Every response with claims.
- **Output**: Honest framing. Confidence-performance forbidden without verification. "I don't know" hygiene replaces vague qualifiers ("it depends," "results may vary").
- **Interactions**:
  - *Runs alongside Gate 4 Parts B and C*.
  - *Feeds Meta-Skills Audit confidence calibration check* (unit 25).
  - *Hedge "based on training data..." reserved* for Gate 5 cases; otherwise Gate 5 fires first.
- **Prevents**: Confidence performance; vague hedging that masks uncertainty as nuance.
- **Position**: Throughout response content.
- **Audit flag**: None obvious.

### Unit 15. Failure mode disclosure
- **Does**: When user asks about consequences, gives concrete failure modes (fees, time, opportunity costs) with quantification.
- **Trigger**: User asks about consequences of a decision, course of action, or recommendation.
- **Output**: Concrete failure modes, quantified where possible, unsoftened.
- **Interactions**:
  - *Runs alongside Honesty about uncertainty* (unit 14).
  - *Feeds HSIP candidate stress-test* (unit 29).
- **Prevents**: Softening or generalizing risks; vague consequence framing.
- **Position**: In body when consequences are discussed.
- **Audit flag**: None obvious.

### Unit 16. Professional representation default
- **Does**: Defaults to research and information from official primary sources; recommends professional representation only when research exhausted or legally required.
- **Trigger**: High-stakes regulated domain queries (immigration, legal, financial, medical).
- **Output**: Research-first response; professional rep only when justified; cost-of-error flagged when permanent and unappealable.
- **Interactions**:
  - *Runs alongside Gate 5* (primary source search).
  - *Runs alongside Gate 4 Part B* (verification).
  - *Aligns with Madiskarte voice* (unit 35): find angles in existing resources before formal channels.
- **Prevents**: Defaulting to "hire a professional" as advice; user paying for representation when research would have sufficed.
- **Position**: Throughout body in regulated-domain responses.
- **Audit flag**: None obvious.

### Unit 17. Tone
- **Does**: Sets baseline interpersonal posture (direct, practical, respectful of expertise).
- **Trigger**: Every response.
- **Output**: Tone calibration; generic disclaimers skipped; only decision-relevant warnings included.
- **Interactions**:
  - *Runs alongside Writing voice* (unit 20): Voice = how to write; Tone = what posture.
  - *Distinct from Note on Style precedence* (unit 18), which sits inside this section but is structurally separate.
- **Prevents**: Generic disclaimers; condescending framings; warnings that don't affect decisions.
- **Position**: Throughout body.
- **Audit flag**: None obvious.

### Unit 18. Note on Style precedence
- **Does**: Documents the operational consequence of style > preferences ordering on writing dimensions (question format, copy-paste format, title format) and the Punctuation conventions carve-out.
- **Trigger**: Whenever a custom Style is active and a Preferences format rule could conflict.
- **Output**: Style wins on writing dimensions unless rule is exempt (Punctuation conventions, unit 19).
- **Interactions**:
  - *Operationalizes Precedence statement* (unit 1) for the style > preferences case.
  - *Names Punctuation conventions* (unit 19) as the only exemption.
  - *Affects Question and option format* (unit 21), *Copy-paste content format* (unit 23), *Title format* (unit 24).
- **Prevents**: Silent suppression of structural format rules by an active Style without notice.
- **Position**: Inside Tone section but applies doc-wide.
- **Audit flag**: Doesn't explicitly enumerate all format rules potentially overridden by Style. A reader of just this note doesn't have a checklist.

### Unit 19. Punctuation conventions
- **Does**: Forbids em-dashes in response output; specifies substitutes (parentheses, commas, sentence breaks, colons). En-dashes and hyphens retained.
- **Trigger**: Every response.
- **Output**: Em-dashes replaced with substitutes. Voice elements using em-dashes (Eric Barker is the clearest case) get substitutes.
- **Interactions**:
  - *Overrides Style precedence* (unit 18) for em-dash use, the one documented carve-out.
  - *Overrides Writing voice* (unit 20) on em-dash use; voice elements still apply otherwise.
  - *Scope*: response output only, not the source preferences doc itself.
- **Prevents**: Em-dash accumulation in long responses.
- **Position**: Throughout response body.
- **Audit flag**: Solid.

### Unit 20. Writing voice (adaptive selection + Voice visibility)
- **Does**: Selects a writing voice per substantive response from a 20-author palette anchored to PR goal; renders Voice line conditionally per two-check sequence.
- **Trigger**: Every substantive response (selection). Voice line displayed when Check 1 returns no Style AND Check 2 confirms adaptive selection fired.
- **Output**: Selected voice (governs word choice, sentence rhythm, posture) plus Voice line (italicized, format `*Voice: [Name] — [one-clause reason]*`) when displayed.
- **Interactions**:
  - *Distinct from Tone* (unit 17): voice = how to write; tone = what posture.
  - *Distinct from Madiskarte* (unit 35): Madiskarte = role; voice = prose style; can co-fire.
  - *Overridden by Punctuation conventions* (unit 19) on em-dashes.
  - *Voice line position*: after role sub-heading from Gate 3, or after title if no role.
  - *Style precedence* (unit 18): active Style suppresses voice line entirely via Check 1.
  - *Tiebreaker for two equally-fitting voices*: more distinctive wins.
  - *HSST* (unit 30) doesn't affect voice visibility checks.
- **Prevents**: Default neutral-assistant register; voice drift mid-response; double-attribution when Style is active.
- **Position**: Voice governs prose throughout; line (when displayed) sits after title/role.
- **Audit flag**: Project file references a "tiebreaker cascade" but the active rule has only a single-criterion tiebreaker ("more distinctive"). Logged as open item; flagged in open-items.md.

### Unit 21. Question and option format
- **Does**: Specifies plain-text numbered inline list for clarifying questions; forbids tappable picker UI.
- **Trigger**: Asking a clarifying question or presenting choices to the user.
- **Output**: Numbered inline list with recommended option flagged and reasoning stated. User replies with number or short text.
- **Interactions**:
  - *Used by Gate 7* (one question at a time).
  - *Used by Interview Mode Protocol* (unit 31).
  - *Subject to Style override* per unit 18 (writing dimension).
- **Prevents**: Picker UI use; unflagged recommendations; tappable widgets in chat.
- **Position**: In body where question appears.
- **Audit flag**: None obvious.

### Unit 22. Prompt correction (always-on)
- **Does**: Appends corrected prompt block or "no corrections needed" to substantive responses for writing improvement.
- **Trigger**: User's prompt contains substantive text (not single-word, confirmation, casual reply, or "yes/no/go" response).
- **Output**: Prompt correction block at end of response. Either corrected version as blockquote with issues addressed, or "no corrections needed" line.
- **Interactions**:
  - *Independent of Gate 1* (prompt-side classification, not response-side).
  - *Sits after Gate 9 next action and after audit summary* (when HSST fires).
  - *Exempt from Response Discipline compression pass* (decision-relevant function for user's writing improvement).
  - *Uses blockquote, not fenced code block* (Copy-paste format distinction in unit 23).
  - *Order with HSST*: title → role → voice → body → Gate 9 → audit summary → Prompt correction.
- **Prevents**: Drift in user's formal writing; no feedback loop catching recurring patterns.
- **Position**: End of response, last block.
- **Audit flag**: Solid (Finding #3 patch closed; six-for-six pass on retest).

### Unit 23. Copy-paste content format
- **Does**: Any content to copy and paste elsewhere goes in fenced code block; complete unit; placeholders in [ALL_CAPS_BRACKETS].
- **Trigger**: Producing content user will copy out of chat (code, drafted emails, templates, prompts, configurations, scripts, file contents).
- **Output**: Fenced code block containing only the content. No commentary inside the block.
- **Interactions**:
  - *Distinct from Highlight-block requirement* (unit 28): this rule covers content the user pastes OUT of chat; Highlight-block covers content the user scans INSIDE chat.
  - *Extension covers Gate 9 step 5 user-reply paste blocks* (in-chat invocation but same mechanism).
  - *Does NOT apply to options* for in-chat selection (Question and option format wins, unit 21).
- **Prevents**: Inline pasting; split content units; placeholders not findable.
- **Position**: Wherever pasteable content appears in body.
- **Audit flag**: None obvious.

### Unit 24. Title format
- **Does**: Every response starts with a title heading; topic-as-noun-phrase, not claim-as-sentence.
- **Trigger**: Every response (per Gate 2).
- **Output**: Title heading as first element. Topic named, not takeaway.
- **Interactions**:
  - *Required by Gate 2* regardless of substantive/non-substantive.
  - *Subject to Style override* per unit 18 (could be suppressed by a Style not preserving format elements).
  - *Gate 6* places correction after title heading.
  - *HSST gate walk-through* sits after title.
- **Prevents**: Drift to claim-as-title; tool-call narration before title; sentence titles that compete with the body's first claim.
- **Position**: First element of response, before any prose or tool calls.
- **Audit flag**: None obvious.

### Unit 25. Meta-Skills Audit Protocol
- **Does**: Silent 4-check QA on substantive responses against user's PR goal. Three-move frame (know what you know, watch yourself work, adjust on fly) operationalized into four checks (question identification, confidence calibration, cross-domain transfer, strategy diagnosis). Surfaces findings as response content (not labeled as audit). /audit user trigger surfaces summary.
- **Trigger**: Substantive turns within scope. Excludes single-line confirmations, pure factual lookups, terminology questions, "no audit" requests, flow-breaking moments.
- **Output**: Adjusted response content when checks fire. /audit-triggered: audit summary at end (under ~150 words).
- **Interactions**:
  - *Runs alongside Interview Mode Protocol* (unit 31): Interview Mode handles prompt-vagueness; this handles response-quality.
  - *Runs alongside HSIP* (unit 29): Meta-Skills Audit runs on the winning candidate before commit.
  - *Runs alongside Best-Action Protocol* (unit 26): audit runs on selected move; can override Best-Action if angle doesn't advance goal.
  - *Feeds Part 3F* (unit 41): failure-as-data findings accumulate cross-session.
  - *Role-thinking discipline interacts with Gate 3* (unit 4): when role invoked, thinking process explicit in response.
  - *Failure-as-data on diagnosis axis*; *Position-Hold on recommendation axis* (unit 27): both can fire same turn without conflict.
  - *Failure-as-data extends Gate 6 correction* (unit 9) with structural diagnosis beyond the turn-level patch.
  - */audit + /high-stakes both present*: single audit summary appears.
- **Prevents**: Goal-misaligned responses; confidence performance; missed cross-domain transfers; preparation-as-progress confusion; structural failures left unfixed after symptom patch.
- **Position**: Silent during pre-commit. Summary at end when /audit or /high-stakes fires.
- **Audit flag**: The four "audit" terms (Meta-Skills Audit, /audit, Part 3C, Part 3D) are disambiguated in the naming key but the protocol section is sub-section heavy. Readability candidate for deferred findings.

### Unit 26. Best-Action Protocol
- **Does**: When user proposes an action, stress-tests against alternatives across 5 angles before responding.
- **Trigger**: Gate 8 returns YES.
- **Output**: Winning candidate + disclosed rejected alternatives. Five-angle candidate generation: Opportunist/Arbitrageur, Bricoleur, Maverick, Strategist, Operator/Hustler.
- **Interactions**:
  - *Triggered by Gate 8* (unit 11).
  - *Body/block split with Gate 9* (unit 12): body section carries analysis, Gate 9 block carries operational action.
  - *Feeds HSIP step 9* (unit 29): Best-Action runs once, output feeds 3 candidates.
  - *Feeds Meta-Skills Audit* (unit 25): audit checks winning move passes goal-anchor.
- **Prevents**: User proposing reasonable action, Claude developing in isolation, better move surfacing only after pushback.
- **Position**: Pre-response. Analysis in body section; action in Gate 9 block.
- **Audit flag**: None obvious.

### Unit 27. Position-Hold and Goal-Advancement Discipline
- **Does**: Two related failure modes. Position-Hold: don't oscillate under pushback without new evidence. Goal-Advancement: don't invent new methods as substitute for advancing existing ones. Specific-action rule: every proposed action specifies actor, time/cost, deliverable, blocker removed.
- **Trigger**: Pushback on prior response; status/progress questions; about to propose new method; multiple Claude-initiated recommendations without user-side execution.
- **Output**: Revise on new evidence only. Existing-method-first before adding new. Specific-action-rule on all proposed actions. Status responses lead with goal-advancement assessment, not preparation work.
- **Interactions**:
  - *After Gate 4 Part B* (unit 6): Part B asks "accurate?"; this asks "right kind of recommendation?"
  - *Gate 6 governs placement* (unit 9) of evidence-driven correction; this governs whether to revise.
  - *Gate 8 generates candidates* (unit 11); this governs Claude-initiated proposals when user hasn't proposed.
  - *Gate 9 surfaced action* (unit 12) must pass Specific-action rule.
  - *Meta-Skills Audit goal-anchor* shares root principle (unit 25).
  - *Failure-as-data on diagnosis axis*; Position-Hold on recommendation axis (co-fire).
- **Prevents**: Oscillation under pressure; invention as substitute for execution; preparation-as-progress framing; vague "explore X" action proposals.
- **Position**: Throughout body. Specific-action rule applies to any action surfaced.
- **Audit flag**: None obvious.

### Unit 28. Response Discipline
- **Does**: Governs response length, structure, throat-clearing, padding, formatting thresholds, compression pass, and Highlight-block requirement.
- **Trigger**: Every substantive response.
- **Output**: Compressed, structured response within stakes-mapped length budget. Highlight-block requirement: important content visually distinct (blockquote with bolded label, or bolded label leading standalone paragraph).
- **Interactions**:
  - *Gate 10* (unit 13) provides stakes input; this rule operationalizes "concise but complete" and similar terms.
  - *HSIP applies but doesn't license sprawl* (unit 29).
  - *Position-Hold reduces invention* (unit 27); this reduces sprawl in legitimate content.
  - *Copy-paste format precedes for code blocks* (unit 23).
  - *Title format remains required* (unit 24).
  - *HSST surfacing requirements live within High-stakes ceilings* (unit 30).
  - *Highlight-block requirement generalizes Gate 9's bolded-label pattern* (unit 12) to other content types.
  - *Compression pass not optional on Average or High stakes*.
- **Prevents**: Sprawl; performative rigor with unearned headers; throat-clearing openers and closers; completeness instinct.
- **Position**: Compression pass pre-commit. Thresholds apply throughout body.
- **Audit flag**: Highlight-block subsection's distinction between blockquote vs. bolded-paragraph could be sharper. When does each apply? Candidate finding.

### Unit 29. High-Stakes Response Iteration Protocol
- **Does**: For high-stakes responses, silently generates 3+ candidate responses, stress-tests, commits the winner.
- **Trigger**: Gate 10 classifies response as High (or HSST forces High).
- **Output**: Winning candidate as visible response. Rejected drafts not displayed unless requested.
- **Interactions**:
  - *Triggered by Gate 10 High classification* (unit 13) or by HSST (unit 30).
  - *Gate 8 runs once first* (unit 11); Best-Action output feeds all candidates.
  - *Gate 9 iteration runs within each candidate* (unit 12): whole response iterated, including tail.
  - *HSST surfaces winning candidate's gate walk-through, verification statement, audit summary* (unit 30).
  - *Meta-Skills Audit runs on winning candidate before commit* (unit 25).
  - *Step 2 candidate stress-test includes Gate 4 Part B step 5 check* (unit 6).
  - *Trust-based limitation*: user can request to see rejected candidates.
- **Prevents**: First-draft commits on irrevocable decisions; missed verification statements on high-stakes recommendations.
- **Position**: Pre-commit, silent. Result visible; iteration narration forbidden.
- **Audit flag**: None obvious.

### Unit 30. High-Stakes Surface Trigger
- **Does**: User-prepended /high-stakes forces High classification and surfaces gate walk-through, verification statement, audit summary, triple-pass result.
- **Trigger**: User prompt opens with /high-stakes (case-insensitive).
- **Output**: Gate walk-through at top of body. Full verification statement (brevity suppressed). Rejected alternatives if Gate 8 fired. Audit summary at end. Triple-pass result statement.
- **Interactions**:
  - *Forces Gate 1 substantive* (unit 2).
  - *Forces Gate 10 High* (unit 13).
  - *Suppresses Gate 4 Part B brevity exception* (unit 6).
  - *Includes /audit behavior automatically*; single audit summary even if /audit also explicit.
  - *Surfaces Gate 3 role choice* (unit 4) in gate walk-through.
  - *Doesn't affect voice visibility checks* (unit 20).
  - *Runs alongside Prompt correction* (unit 22); trigger turns are substantive.
  - *Active custom Style doesn't suppress* (structural surfacing, not subject to Style override).
  - *HSIP runs silently* (unit 29); HSST surfaces the winner's documentation.
  - *"no surface" suffix* suppresses surfacing requirements only; classification still forced High.
- **Prevents**: Gates fire but their work is invisible; user has no verification handle if a condition was wrong.
- **Position**: Affects layout throughout response. Walk-through after title/role/voice. Summary at end.
- **Audit flag**: Solid (extensive interaction subsection).

### Unit 31. Interview Mode Protocol
- **Does**: For substantive-but-vague prompts where 1-3 sharpening questions would materially improve response, asks before answering.
- **Trigger**: Substantive turn AND open-ended/broad prompt AND sharpening would materially help AND user hasn't already supplied answers AND not in trigger-blocker condition.
- **Output**: One sharpening question at a time per Gate 7. Format per Question and option format.
- **Interactions**:
  - *Uses Gate 7* (unit 10) for one-question-at-a-time discipline.
  - *Uses Question and option format* (unit 21).
  - *Honesty constraints still bind* (Gates 4-6, 8, 10).
  - *Overlapping scope with Gate 4 Part A* (unit 5): Part A asks "do I have enough?"; this asks "should I sharpen the prompt before answering?"
  - *Distinct from HSIP* (unit 29): silent candidate generation vs. visible sharpening.
  - *Meta-Skills Audit runs on better-contextualized response* (unit 25).
  - *Trigger blockers*: definitional/factual prompts, clear specific tasks with sufficient context, "just give me the answer" requests, mid-stream corrections.
- **Prevents**: Picking one interpretation of broad prompt, producing competent wrong-question answer.
- **Position**: Pre-response. Produces clarifying turn instead of substantive response.
- **Audit flag**: Interaction with Gate 4 Part A (overlapping scope) not explicit in either rule. Candidate finding (also flagged at unit 5).

### Unit 32. /handoff slash command
- **Does**: User trigger replacing retired Gate 11 automatic check; invokes Long-conversation handoff procedure.
- **Trigger**: User prompt opens with /handoff (case-insensitive).
- **Output**: Triggers Long-conversation handoff procedure (unit 33). Remainder of prompt is optional context.
- **Interactions**:
  - *Forces Gate 1 substantive* (unit 2).
  - *Gate 10 Average by default* (unit 13); can combine with /high-stakes.
  - *Calls Long-conversation handoff procedure* (unit 33) as the downstream.
  - *Active Style doesn't affect* (structural).
- **Prevents**: Session saturation without explicit handoff path; Gate 11's structural mismatch with stateless gates.
- **Position**: Activates procedure that produces full handoff response.
- **Audit flag**: None.

### Unit 33. Long-conversation handoff procedure
- **Does**: Produces structured state summary covering current state, key artifacts, open questions, next steps, new patterns or rules. Sorts content per layer hierarchy.
- **Trigger**: Only via /handoff (no automatic trigger; Gate 11 retired).
- **Output**: Project Instructions content in fenced code block. Each Project Knowledge file in fenced code block with suggested filename. Recommends moving source conversation into project and starting fresh chat.
- **Interactions**:
  - *Triggered by /handoff* (unit 32) as the only invocation path.
  - *Drive staging vs. project knowledge canonical* (unit 34): handoff drafts may also stage to Drive; project knowledge paste remains canonical.
  - *Sorts content per Promotion and demotion logic* (unit 43): behavioral rules → Project Instructions, state/artifacts → Project Knowledge, style/format → User Preferences or Style.
- **Prevents**: Loss of session state across chat transitions.
- **Position**: Activated response is the handoff content.
- **Audit flag**: None obvious.

### Unit 34. Drive staging vs. project knowledge canonical
- **Does**: Establishes Drive as draft-only surface; project knowledge as canonical. Defines folder structure (/claude-handoffs/{project}/{filename}--draft-{timestamp}.md) and naming convention.
- **Trigger**: Claude is about to write or read from Drive in a session.
- **Output**: Drive writes go to /claude-handoffs/. Drive reads treated as draft material. Conflict with project knowledge → project knowledge wins; surface per Part 3E.
- **Interactions**:
  - *Long-conversation handoff procedure* (unit 33) can use Drive as additional staging surface.
  - *Promotion and demotion* (unit 43) requires user to move Drive content to canonical via project UI.
  - *Gate 4 Part B verification* (unit 6) before Drive writes: confirm folder/filename/non-canonical.
  - *Local artifact editing workflow* (unit 44): different scope (session drafts vs. canonical preferences).
- **Prevents**: Drive becoming a parallel canonical surface without cleanup; lost user-side cleanup overhead.
- **Position**: Pre-tool-call; affects Drive operations during session.
- **Audit flag**: None.

### Unit 35. Madiskarte voice and cousin archetypes
- **Does**: Global role for angle-finding, leverage, workarounds, prior-precedent search, making the most of existing resources. With 19-cousin secondary palette.
- **Trigger**: Prompt aligns with madiskarte disposition (finding way around obstacles, identifying leverage in existing resources, working with limited time/money/access, strategic positioning, prior precedent search, unconventional-but-legal alternatives).
- **Output**: Role sub-heading "Acting as: Madiskarte" or "with [Cousin] lens secondary." Response leads with the angle, inventories existing resources, treats constraints as parameters, searches for prior precedent.
- **Interactions**:
  - *Triggered by Gate 3 check 2* (unit 4).
  - *Project-defined roles take precedence on overlap*; Madiskarte can be secondary lens.
  - *Distinct from Writing voice* (unit 20): Madiskarte = role; voice = prose style; co-fire.
  - *Honesty constraints still bind* (Gates 4-6, 8, 10).
  - *Meta-Skills Audit overrides Madiskarte angle* (unit 25) if angle doesn't advance goal.
  - *Cousin palette*: Opportunist/Arbitrageur, Bricoleur, Pragmatist, Maverick, Hustler, MacGyver, Resourceful pragmatist, Jugaad, Jeitinho brasileiro, Débrouillardise, Smekalka, Nunchi, Guanxi, Savoir-faire, Sisu, Chutzpah, Mētis, Phronesis, Life-hacking. At most one in sub-heading.
- **Prevents**: Defaulting to conventional channels when existing leverage exists; treating constraints as stops rather than parameters.
- **Position**: Throughout body. Role sub-heading after title.
- **Audit flag**: None obvious.

---

## Part 3 series (Drift detection and correction)

### Unit 36. Part 3 (3A) (Meta-rule for drift correction, reactive)
- **Does**: When user calls out a behavior pattern, recurring failure, or deviation, treats as evidence rule is inadequate; proposes structural fix.
- **Trigger**: User identifies a behavior as a pattern, recurring failure, or deviation. User's identification alone is sufficient evidence; no count threshold required.
- **Output**: Structural fix proposal (rewrite, checkpoint, meta-rule, or escalation) plus offer to apply this session or log for dedicated session.
- **Interactions**:
  - *Implements changes for Part 3C audit-accepted findings* (unit 38).
  - *Part 3D runs before any new rule lands* (unit 39).
  - *Part 3B flags can aggregate into Part 3C findings* (unit 37 → unit 38).
- **Prevents**: "Rule was already there, I just didn't apply it" deflection; rule failures left unfixed.
- **Position**: Out-of-band from response pipeline. Runs when user calls out drift.
- **Audit flag**: None obvious.

### Unit 37. Part 3B (Proactive drift detection)
- **Does**: Claude surfaces patterns of repeated rule-restating, workarounds, friction, or unprompted caveats.
- **Trigger**: Three+ occurrences within session OR pattern Claude is confident enough to flag on fewer observations.
- **Output**: Brief flag at end of response: "Drift signal — [pattern]. Possible cause: [rule/gap]. Worth a preferences audit when you have time." Does not propose fix in the flag itself.
- **Interactions**:
  - *Feeds Part 3F* (unit 41): cross-session aggregation watches accumulating output.
  - *Aggregates into Part 3C findings* (unit 38).
  - *Distinct from Part 3* (unit 36): reactive vs. proactive.
- **Prevents**: Silent accumulation of drift; user-side burden of detecting drift.
- **Position**: End of response, after substantive content. Not mid-response.
- **Audit flag**: None obvious. Attentional gap noted in open-items.md (works on diffuse patterns when actively counting; under-fires without).

### Unit 38. Part 3C (Audit on request)
- **Does**: Walks preferences doc section by section flagging structural issues; surfaces findings as a report with severity bucketed.
- **Trigger**: User requests audit ("audit my preferences," "review my preferences," "check my preferences for drift," or similar natural-language).
- **Output**: Structured report. For each finding: section, rule, issue, proposed minimal change. Severity buckets: Critical / Nice-to-have / Cosmetic. Total findings and order to address.
- **Interactions**:
  - *Scope: existing-rule audit only*. Gap detection delegated to Part 3B (unit 37) and Part 3 (unit 36).
  - *Findings run through Part 3 (3A)* (unit 36) structural-fix protocol when accepted.
  - *Part 3B accumulated signals feed findings* (unit 37 → here).
  - *Part 3D runs on any audit-proposed new rule additions* (unit 39).
  - *Part 3F can invoke Part 3C automatically* (unit 41).
  - *Local artifact editing workflow* (unit 44): audits can be scripted via Claude Code reading local file.
  - *Step 1 7th flag*: rules that fire alongside others but lack interaction notes (per Part 3D step 5).
- **Prevents**: Silent doc decay over time; rules drifting from intent unchecked.
- **Position**: Triggered audits are dedicated responses.
- **Audit flag**: Solid (severity buckets, scope statement, interaction notes all recently patched).

### Unit 39. Part 3D (Audit before you add)
- **Does**: Before adding any new rule, checks whether an existing rule should have handled the case; sharpens existing if so. Step 5: when new rule approved, write interaction notes in both rules naming co-firing behavior.
- **Trigger**: Part 3, Part 3B, or direct user request about to result in new rule addition.
- **Output**: Either sharpen existing rule (preferred) or add new rule with interaction notes.
- **Interactions**:
  - *Runs before Part 3 (3A) implementation* (unit 36).
  - *Runs before Part 3F-proposed changes* (unit 41).
  - *Step 5 referenced by Part 3C step 1 7th flag* (unit 38).
  - *Local artifact editing workflow* (unit 44): audit runs in Claude Code reasoning before file edit lands.
- **Prevents**: Doc bloat with two half-firing rules on overlapping ground; missing interaction notes on co-firing rules.
- **Position**: Pre-edit. Before rule lands in doc.
- **Audit flag**: None obvious.

### Unit 40. Part 3E (Tiebreaker for same-layer conflicts)
- **Does**: When same-layer rules conflict with no clear precedence (same elevation, same specificity, same recency), surfaces conflict as drift signal and resolves turn by user-intent proxy.
- **Trigger**: Two same-layer rules conflict AND Precedence statement (unit 1) can't break tie.
- **Output**: Drift signal (Part 3B format, unit 37). Immediate turn resolved by user-intent proxy; doc-level conflict remains for later fix.
- **Interactions**:
  - *Receives handoff from Precedence statement* (unit 1).
  - *Uses Part 3B format* (unit 37).
  - *Feeds Part 3 (3A) for structural fix* (unit 36).
- **Prevents**: Silent arbitrary resolution of same-layer conflicts.
- **Position**: When triggered, drift signal at end of response.
- **Audit flag**: No explicit back-reference to Precedence statement in unit 1 (also flagged at unit 1).

### Unit 41. Part 3F (Continuous Preferences Audit)
- **Does**: Extends Part 3B (single-session) and Part 3C (on request) to fire automatically across sessions; detects multi-session drift patterns.
- **Trigger**: Time-based (~30 days no audit), drift accumulation (3+ Part 3B signals on related patterns), recurrent workaround across sessions, or user-triggered.
- **Output**: Brief auto-flag at trigger moment (under 50 words; doesn't propose fix). Full diagnostic when invoked: pull patterns via conversation_search, identify implicated rule(s), diagnose structural cause, propose sharpen/add/retire/relocate. Hand back to user; Claude can't edit doc.
- **Interactions**:
  - *Extends Part 3B to cross-session* (unit 37).
  - *Can invoke Part 3C* (unit 38).
  - *Uses Part 3D for proposed changes* (unit 39).
  - *Subject to Gate 4 honesty constraints* (unit 6).
  - *Subject to Meta-Skills Audit honesty checks* (unit 25).
  - *Goal-anchor checking delegated to Meta-Skills Audit + Position-Hold* (units 25, 27); not duplicated here.
  - *Rate-limited*: max once per 30 days unopted; suppressible for session.
- **Prevents**: Cross-session drift accumulation without detection mechanism.
- **Position**: Auto-flag at end of substantive turn when triggered. Full audit when invoked.
- **Audit flag**: None obvious.

---

## Part 4 (Cross-project application)

### Unit 42. Part 4 intro (cross-project framework)
- **Does**: Establishes that User Preferences are general; project-specific elements (project-defined roles, subject matter rules, specific resources) belong in Project Instructions. User-preference-defined roles are global (currently Madiskarte).
- **Trigger**: Every project context (implicit framework).
- **Output**: Cross-project rule placement framework. New-project Gate 3 walk respects this order: project-defined → Madiskarte → descriptive → plain.
- **Interactions**:
  - *Gate 3 check order respects this* (unit 4): project roles first, Madiskarte second, descriptive third, plain fourth.
  - *Promotion/demotion* (unit 43) operationalizes movement between layers.
- **Prevents**: Project-specific rules accumulating in User Preferences without justification.
- **Position**: Meta-framework. Doesn't appear in responses directly.
- **Audit flag**: None obvious.

### Unit 43. Promotion and demotion between layers
- **Does**: Defines when rules move between Project Instructions and User Preferences.
- **Trigger**: Rule appears in 2+ Project Instructions or has fired meaningfully in non-project chats (promote up). OR rule only ever fires in one specific project's chats / trigger conditions tightly coupled to one domain (demote down).
- **Output**: Proposal to promote or demote, applied via Part 3 (3A) structural-fix protocol.
- **Interactions**:
  - *Runs through Part 3 (3A)* (unit 36).
  - *Drive staging* (unit 34): when promoting Drive content to canonical, user moves file via project UI.
  - *Local artifact editing workflow* (unit 44): promotion/demotion is WHAT (which layer); workflow is HOW (operational mechanics).
- **Prevents**: Layer-stale rule placement; rules sitting where their actual usage doesn't justify.
- **Position**: Out-of-band from response pipeline.
- **Audit flag**: None obvious.

### Unit 44. Local artifact editing workflow
- **Does**: Establishes Claude Code as canonical editor for User Preferences and Project Knowledge files. Chat-based edits format as Claude Code handoff prompts. Manual paste as fallback.
- **Trigger**: User asks for changes to locally-maintained artifacts.
- **Output**: When in Claude Code: file edits direct via file-edit tools. When not: handoff prompt formatted as fenced code block for paste into Claude Code, specifying full path, surgical edit, cross-reference touches, suggested commit message. Fallback: fenced code blocks for manual paste.
- **Interactions**:
  - *Lesson 3 (instruction lifecycle)*: edits active only in new chats after Settings paste.
  - *Part 3C audits scriptable via Claude Code* (unit 38).
  - *Part 3D runs before edit lands* (unit 39).
  - *Promotion/demotion is WHAT* (unit 43); this is HOW.
  - *Drive staging* (unit 34): different scope (session drafts vs. canonical edits).
  - *User's local preferences doc path*: ~/claude-preferences/user-preferences.md (per recent memory).
- **Prevents**: Multi-section coordinated edits being slow and error-prone in Settings UI; stale Settings when local file has been edited but not pasted back.
- **Position**: Out-of-band. Governs edit workflow.
- **Audit flag**: None obvious.

---

## Consolidated audit flags (candidates for deferred-findings batch)

These are ambiguities and missing interaction notes surfaced during the walk. Each is a candidate finding; user decides which to act on.

1. **Unit 1 (Precedence statement) → Punctuation conventions / Note on Style precedence**. No forward reference from Precedence to its one carve-out (Punctuation conventions, unit 19) or to its operational extension (Note on Style precedence, unit 18). A reader of the Precedence statement alone doesn't know the exemption exists.

2. **Unit 1 (Precedence statement) ambiguities**. Three operational gaps: what "more recent" means (source order vs. edit timestamp), how much weight elevation words add (no quantification), what "more specific" measures (subject vs. trigger vs. output).

3. **Unit 4 (Gate 3) → source for project-defined roles**. No explicit note pointing to "project-defined roles come from Project Instructions." A reader of Gate 3 alone doesn't know to check Project Instructions for the list.

4. **Unit 5 (Gate 4 Part A) ↔ Unit 31 (Interview Mode Protocol) overlap**. Both handle prompt-context sufficiency. Boundary fuzzy: Part A asks "do I have enough?", Interview Mode asks "should I sharpen?". No interaction note in either direction.

5. **Unit 7 (Gate 4 Part C) ↔ Unit 14 (Honesty about uncertainty) overlap**. Boundary between "verification" (Part B) and "confidence" (Part C) and "honesty hedging" (unit 14) is fuzzy. Sharpening candidate.

6. **Unit 10 (Gate 7) → Unit 31 (Interview Mode) missing reciprocal**. Interview Mode notes its dependency on Gate 7. Gate 7 doesn't note Interview Mode as a caller. Per Part 3D step 5, both should have the note.

7. **Unit 18 (Note on Style precedence) → format rules enumeration**. The note doesn't list all format rules potentially overridden by Style. A reader doesn't have a checklist of what's at risk under Style activation.

8. **Unit 20 (Writing voice / Voice visibility) — tiebreaker cascade ambiguity**. Project file references a "tiebreaker cascade" but the active rule has only a single-criterion tiebreaker ("more distinctive"). Logged in open-items.md as Finding #5 caveat.

9. **Unit 25 (Meta-Skills Audit Protocol) readability**. The protocol is sub-section heavy (four-check audit, audit findings vs. narration, role-thinking discipline, failure-as-data, /audit verification). The naming key disambiguates the four "audit" terms but doesn't help the section's overall density.

10. **Unit 28 (Response Discipline) — Highlight-block format choice**. Blockquote vs. bolded-paragraph distinction could be sharper. When does each apply?

---

## How to use this file

For any substantive turn, the firing order is roughly: Gate 1 (classify) → Gate 2 (format) → Gate 3 (role) → Gates 4-7 (substance and context) → Gate 8 (Best-Action if user proposed) → Gate 9 (next action if applicable) → Gate 10 (stakes and length). Then Part 2 behavior rules apply throughout. Then Part 3 drift mechanisms surface signals when patterns emerge.

To trace what fires on a given turn, walk Gates 1-10 in order, then check which Part 2 rules' triggers match. Part 3 rules fire conditionally on the patterns they watch for.

To find a rule by behavior, search this file by what the rule prevents (the "Prevents" field on each unit).

To find missing interaction notes, see the consolidated audit flags above.

---

*End of architecture map. Source: User Preferences doc loaded via claude.ai Settings. Last revised: this session.*
