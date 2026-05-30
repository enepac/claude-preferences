# curriculum-state.md

# Curriculum state — Mastering Claude Customization

## Lessons delivered (1–14)

1. The instruction layer model
2. How Claude receives instructions per turn
3. The instruction lifecycle (REVISED): User Preferences and project knowledge refresh on each new user message, not only at chat start. An active chat reflects a saved change on its next message. Behavior confirmed by sentinel tests; mechanism most likely per-message reinjection but not definitively resolved. Corrected model documented in lesson-3-anomaly.md.
4. User Preferences in depth
5. Project Instructions in depth (REVISED): testing guidance corrected to same-chat iteration per Lesson 3 (revised) and Lesson 14; demotion criteria added (when a User Preferences rule moves down to Project Instructions). Scope, roles, and routing content unchanged.
6. Style overrides
7. Memory and Past Chats
8. Writing instructions Claude can follow
9. Structural patterns
10. Anti-drift mechanisms
11. Honesty calibration
12. Role assignment without credential fraud
13. Resolving conflicts between layers
14. Testing and debugging instructions

## Lessons remaining (15–19)

15. Versioning and iterating preferences over time
16. Working with Claude's strengths and around its weaknesses
17. Instruction stability under pressure
18. Templating instructions across roles or for handoff
19. Capstone — designing a preferences doc from scratch

## Audits run

- Lesson 11 honesty calibration → integrated
- Lesson 10 anti-drift mechanisms → integrated
- Lesson 13 conflict resolution → integrated

## Session activity log

[Current session]: Lesson 3 (instruction lifecycle) rewritten with the corrected mid-chat-refresh model; curriculum-state index line updated to REVISED, resolving the dangling "corrected Lesson 3" references in the deployed User Preferences. Stale lifecycle claims in Part 4 and /handoff corrected to match. Lesson 3 anomaly workstream closed (drift signal resolved in lesson-3-anomaly.md). Lesson 5 (Project Instructions in depth) revised and delivered: same-chat iteration plus demotion criteria. Lifecycle-correction cascade from the Lesson 14 downstream audit now complete (Lessons 3, 4, 5, 14 all corrected).

[Prior session]: Audit-findings integration + Finding #1 redesign + Claude Code workflow setup.

[Session N-1]: Audit-findings full closure + Tier 3 Tests 7-8 + reciprocal notes cleanup + Lesson 3 drift signal.

[This session]: 12-test plan closed + Cluster 1 batch + 8 individual deferred-findings deploys.
- Tier 3 Test 9 (Part 3C audit-on-request) Class-2 read-through PASSED with 5 findings.
- Tier 4 Test 10 (Gate 1 turn classification) Class-2 read-through PASSED with 4 findings.
- Tier 4 Test 11 (Gate 3 role assignment) Class-2 read-through PASSED with 3 findings.
- Tier 4 Test 12 (Gate 6 correction priority) Class-2 read-through PASSED with 2 findings.
- 12-test plan CLOSED. Tier 1 through Tier 4 complete; structural baseline of preferences doc fully audited.
- 10 findings deployed via Part 3 (3A):
  * Test 9 Finding A: Part 3C step 1 interaction-notes flag category added.
  * Test 10 Finding C: Gate 1 slash-command interactions + Meta-Skills Audit reciprocal.
  * Test 11 Finding A: Gate 3 cascade preamble incoherence fixed.
  * Test 12 Finding A: Gate 6 YES branch positioning clarified.
  * Cluster 1 batch (5 edits): interaction subsections for Part 3C, Gate 3, Gate 6 + 2 reciprocals (HSST → Gate 3, Failure-as-data → Gate 6).
  * Test 10 Finding A: Gate 1 preamble response-side classification + Prompt correction reciprocal.
  * Test 10 Finding D: Gate 1 ambiguity tiebreaker.
  * Test 9 Finding D: Part 3C scope statement (Option A — narrow scope).
- 4 findings remain deferred (Test 9 B/E, Test 10 B, Test 11 C).
- Lesson 3 anomaly: 8 new observations this session (13 total across sessions).
- Session closed via /handoff slash command.