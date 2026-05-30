# lesson-3-anomaly.md

# Lesson 3 anomaly — preferences refresh mid-chat

## Observation

Lesson 3 of the customization curriculum states: instructions load at chat start; edits to Settings don't propagate to active chats.

This has now been contradicted 13 times across sessions:

[Prior 5 observations from previous sessions]
1. Gate 9 step 5 patch deployed → visible in active chat immediately
2. Part 4 sharpening deployed → visible in active chat immediately
3. Finding #1 patch deployed → visible in active chat immediately
4. Finding #5 retest verification → updated rules visible in fresh-chat responses
5. Test 8 cleanup (15 reciprocal notes) deployed → visible in active chat immediately

[This session — 8 new observations]
6. Test 9 Finding A (Part 3C step 1 7th bullet) → visible in active chat after deploy
7. Test 10 Finding C (Gate 1 slash command interactions + Meta-Skills Audit reciprocal) → visible after deploy
8. Test 11 Finding A (Gate 3 cascade preamble) → visible after deploy
9. Test 12 Finding A (Gate 6 YES branch positioning) → visible after deploy
10. Cluster 1 batch (5 edits across Part 3C, Gate 3, Gate 6, HSST, Failure-as-data) → all 5 edits visible simultaneously after single Settings save
11. Test 10 Finding A (Gate 1 preamble + Prompt correction reciprocal) → visible after deploy
12. Test 10 Finding D (Gate 1 ambiguity tiebreaker) → visible after deploy
13. Test 9 Finding D (Part 3C scope paragraph) → visible after deploy

The Cluster 1 batch observation is particularly strong — five edits across five distinct rule locations propagated together via single Settings save.

## Drift signal status — RESOLVED

Drift signal fired across many sessions; the evidence base was overwhelming. Curriculum revision is now complete: Lesson 3 was rewritten with the corrected mid-chat-refresh model and marked REVISED in curriculum-state.md, and the deployed User Preferences (Part 4, /handoff) were corrected to match. Drift signal closed. The log stays open for genuinely new lifecycle observations, but the curriculum no longer depends on the falsified load-once model.

## Mechanism evidence — Opus 4.8 release (2026-05-28)

Source: Opus 4.8 announcement (user-uploaded, dated 2026-05-28), "Also launching today" section.

Capability described: the Messages API now accepts system entries placed inside the messages array, so a harness can revise Claude's instructions mid-task without a user turn and without invalidating the prompt cache.

What this establishes: mid-conversation instruction update is now a first-class, supported capability at the API layer. The behavior pattern logged above is no longer mechanically surprising; the platform demonstrably supports it.

What this does NOT establish:
- The feature is stated for the Messages API and developer harnesses. claude.ai is a harness built on the API, so it is plausibly the same mechanism family, but the announcement does not say claude.ai uses it.
- All logged observations (the prior 5 and this session's 8) predate this 2026-05-28 release. A capability announced today cannot be the cause of behavior observed before today.

Timing wrinkle (explicit): the refresh behavior was observed before this feature was announced. Two accounts survive, and this evidence does not disambiguate them:
  (a) claude.ai already reinjected the full system context (preferences and project knowledge included) on each new user message. This needs no new feature and is the simplest account.
  (b) The capability existed internally before today's API-surface announcement and was already in use by claude.ai.

Classification: corroborating evidence for the category in Explanation 2 (the platform can and does refresh instructions mid-conversation), NOT proof of the specific mechanism behind the logged observations.

## Possible explanations

1. **Lesson 3's model is inaccurate.** Actual behavior is that claude.ai reloads preferences on each new user message rather than only at chat start. Curriculum was simplified or wrong from the start.
2. **Platform behavior changed.** A recent claude.ai update modified the preferences lifecycle. Curriculum was correct when written but is now stale.
3. **Hybrid behavior.** Preferences refresh under certain conditions (explicit Settings save) but not others.

Re-weighting after the 2026-05-28 evidence:
- Explanation 1 (per-user-message reinjection) remains the most parsimonious account. It is consistent with every observation, including the "visible on the next user message" pattern, and it requires no new platform feature. The new evidence does not undermine it.
- Explanation 2 (platform behavior changed). The release raises the plausibility that mid-conversation refresh is a real, first-class capability. But the timing wrinkle cuts against it as the explanation for the logged observations: behavior present in prior sessions cannot be caused by a feature announced on 2026-05-28. Corroborates the category; does not confirm it as the mechanism here.
- Explanation 3 (hybrid). Unchanged. Not falsified, not advanced by this evidence.

Net: the 2026-05-28 evidence shifts no explanation decisively. It confirms the behavior is mechanically supported and removes "is this even possible" as a concern. It does not resolve which mechanism produced the logged observations. Explanation 1 remains the simplest fit.

## Next steps — status

1. DONE. Lifecycle tested via controlled sentinel-string experiments; a saved change became visible on the next user message in an active chat.
2. DONE. User Preferences refreshes and project knowledge refreshes both confirmed mid-chat.
3. DONE. Lesson 3 rewritten with the corrected model (curriculum-state.md, marked REVISED).
4. IN PROGRESS. Downstream-lesson audit: Lessons 14 and 4 updated; Lesson 5 revision is the remaining queued item.