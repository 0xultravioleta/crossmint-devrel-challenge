# Planning Decisions Log

> **Scope:** Planning phase only (planner hiring and sign-off). Execution decisions continue in `_internal/decisions.md`.

---

## 2026-04-09 H+5.5: Planning team hiring decision

**Approved planners (spawn order):**

1. **Research Planner** — owner: Claude (direct execution) — output: `z:\crossmint-devrel-challenge\_internal\planning\plans\01-research-plan.md` — spawn: FIRST (parallel with #4)
2. **Critical Path (Tool 4) Planner** — owner: Claude (direct execution) — output: `z:\crossmint-devrel-challenge\_internal\planning\plans\02-critical-path-plan.md` — spawn: SECOND (after #1 draft)
3. **MCP Build & Skill Planner** — owner: Claude (direct execution) — output: `z:\crossmint-devrel-challenge\_internal\planning\plans\03-mcp-build-and-skill-plan.md` — spawn: THIRD (after #2 draft)
4. **Content & Campaign Planner** — owner: Claude (direct execution) — output: `z:\crossmint-devrel-challenge\_internal\planning\plans\04-content-and-campaign-plan.md` — spawn: FIRST (parallel with #1)
5. **Integration, QA & Risk Planner** — owner: Claude (direct execution) — output: `z:\crossmint-devrel-challenge\_internal\planning\plans\05-integration-qa-and-risk-plan.md` — spawn: LAST (after #3 and #4 drafts)

**Total planners:** 5. Matches HR proposal exactly. No cuts, no additions.

**Rejected (with reason):**

- **HR Alternative Roster A (3 planners)** — rejected by HR, upheld by PM. Merging Research + Critical Path loses the clean gate at H+7; merging Build + Content/Campaign crosses a hard cognitive medium break (TypeScript build ↔ prose writing) that DA kickoff already flagged.
- **HR Alternative Roster B (8-9 planners)** — rejected by HR, upheld by PM. Directly contradicts Devil's Advocate kickoff section 2: "Eight agents plus a human for three deliverables in 48 hours is cargo-cult scale." Applies doubly to the planning team. Splitting the build track into 5 separate plans creates a reconciliation nightmare with 5 sets of assumptions about file paths and package versions that PM would have to stitch together.
- **Dedicated Crossmint Docs Auditor Planner** — not in HR proposal, explicitly rejected by PM. FINDINGS.md already has the research. No separate planner needed.
- **Dedicated Taste Reviewer Planner** — not in HR proposal, explicitly rejected by PM. Devil's Advocate already covers this role for the execution phase. Adding it to planning would be Devil's Advocate theater.

**Merged (from HR proposal, accepted by PM):**

- **Phases 2B + 2D + 2E + 2F + 2G → single MCP Build & Skill Planner (#3)** — shared TypeScript codebase, shared imports, shared Crossmint API key. Splitting would produce 5 documents that the PM would have to reconcile. Accepted with hard 1,200-line scope cap enforced by PM at review.
- **Phase 3 + Phase 4 → single Content & Campaign Planner (#4)** — per Devil's Advocate kickoff section 2 ("DevRel Strategist and Technical Writer are split on the wrong seam. The format decision and the writing are the same cognitive task"). HR honored DA's critique; PM upholds.
- **Phase 5 + Phase 6 + Risk register §14 + Decision gates §15 → single Integration, QA & Risk Planner (#5)** — risks crystallize exactly at the gates this planner owns (clean smoke test at H+33, npm publish at H+36, email delivery at H+36). Also keeps the "reviewer-reproduction gate" (DA kickoff section 3, R01) in the same document as the risk it mitigates.

**Spawn instructions for Claude (orchestrator):**

1. **Spawn Planner #1 (Research) and Planner #4 (Content & Campaign) in parallel AT 14:05 EDT.** Both are independent — Research reads BUILD-PLAN §2 + Appendix A + concept-lock-v2 critical path; Content reads BUILD-PLAN §§9,10 + concept-lock-v2 shared artifacts + DA kickoff §§1,3,4. Planner #4 does NOT need to wait for Planner #1 because the narrative is already locked in concept-lock-v2.md.

2. **Wait for Planner #1 draft (expected 14:25 EDT, 20 min budget). PM reviews at 14:30 EDT.**
   - Reject criteria: violates any non-negotiable in `pm-journal.md` §4, or misses the "inconclusive verdict handling" requirement in §5.1.
   - If rejected: force one rewrite pass, no second. If still bad, PM writes the plan directly.
   - If accepted: spawn Planner #2.

3. **Spawn Planner #2 (Critical Path / Tool 4) at 14:30 EDT after Planner #1 approval.** Reads BUILD-PLAN §4 + concept-lock-v2 critical path + Planner #1's approved draft. Must cover all 4 fallback plans (A/B/C/D) with hour-resolution pivot triggers.

4. **Wait for Planner #2 draft (expected 15:00 EDT, 30 min budget). PM reviews at 15:05 EDT.**
   - Reject criteria: any pivot trigger is vague ("move on if stuck"), or Plan D fallback doesn't include a README caveat draft reference.
   - If accepted: spawn Planner #3.

5. **Spawn Planner #3 (MCP Build & Skill) at 15:05 EDT after Planner #2 approval.** Biggest planner. Reads BUILD-PLAN §§3,5,6,7,8 + concept-lock-v2 Artifact 1 + Artifact 2 sections + Planner #2's draft for Tool 4 integration seam. Hard scope cap: 1,200 lines. PM rejects on sight if over.

6. **Wait for Planner #3 draft (expected 15:50 EDT, 45 min budget). PM reviews at 15:55 EDT.**
   - Reject criteria: exceeds 1,200 lines, contains executable code beyond shell one-liners, omits Phase 2G lobster.cash skill plan.

7. **While Planner #3 is drafting (15:05-15:50), Planner #4 should already be in PM review or approved.** If not approved, that's a slippage signal — PM contacts orchestrator and re-budgets.

8. **Spawn Planner #5 (Integration, QA & Risk) at 15:55 EDT after Planner #3 approval AND Planner #4 approval.** Reads BUILD-PLAN §§11,12,14,15 + DA kickoff §3 (reviewer reproduction gate) + Planners #3 and #4 drafts. Must include fresh-machine reproduction test as its own phase, not a checkbox.

9. **Wait for Planner #5 draft (expected 16:35 EDT, 40 min budget). PM reviews at 16:40 EDT.**
   - Reject criteria: reproduction test is a single task instead of a phase, or risk playbook doesn't have 15 entries (one per R01-R15), or decision gates aren't all 10 (G1-G10).

10. **Hard deadline for ALL 5 plans ready: 17:00 EDT (H+8).** If any plan is still incomplete at 16:55 EDT, PM overrides: accepts what exists, flags gaps, execution starts.

11. **Parallel execution allowance:** If Planners #1 and #2 are approved before #3/#4/#5 are done, execution Claude MAY begin Phase 2A research at 15:05 EDT (right after Planner #2 approval). Phase 2B+ execution waits for Planner #3 approval.

12. **All planners run as DIRECT CLAUDE EXECUTION in the main conversation** unless API 529 clearly recovers AND the orchestrator confirms subagent spawn health. Default is direct. No exceptions during peak load window.

13. **No planner spawns sub-planners or sub-agents.** Planners plan. The orchestrator orchestrates. The PM reviews. Roles do not blur.

14. **Each plan lives at its designated path.** `_internal/planning/plans/` directory must exist before spawns — if not, orchestrator creates it first.

---

**Planning phase hiring decision LOCKED. Orchestrator is authorized to spawn per this schedule. PM ends here.**
