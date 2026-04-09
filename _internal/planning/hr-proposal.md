# HR Proposal — Planning Specialist Roster

> **Author:** Planning HR Recruiter Agent
> **Date:** 2026-04-09 (H+5)
> **Purpose:** Propose the minimum viable team of PLANNER agents that will write deep phase-by-phase plans for the Crossmint DevRel Challenge submission.
> **Scope:** These specialists PLAN, they do not EXECUTE. Each produces one plan document.
> **Status:** DRAFT — pending PM approval

---

## 1. Executive Summary

I recommend a **5-planner roster**: a Research Planner, a Critical Path (Tool 4) Planner, an MCP Build Planner, a Content & Campaign Planner (merged), and an Integration, QA & Risk Planner (merged). This composition mirrors the actual dependency seams of BUILD-PLAN.md — research gates everything, Tool 4 is the single highest-risk task and deserves its own planner, the other MCP tools + wiring + skill are a cohesive build track, and content/campaign and polish/risk are each writing-heavy tracks that merge cleanly.

The alternative 3-planner version strips too much specialization off the critical path; the 8-planner version replicates the cargo-cult ceremony the Devil's Advocate already shredded in `devils-advocate/kickoff.md`. Five is the defensible number.

---

## 2. Proposed Planner Roster (5)

### 2.1 — Research Planner

- **Mandate:** Plan how the 4 research tasks (R1–R4) get executed, with source-ranked fallbacks and a hard decision rule feeding Tool 4's Plan A/B/C/D selection.
- **Phases/areas owned:** Phase 2A (Research — H+5 → H+7), including Appendix A "Unknowns that could kill the plan" from BUILD-PLAN.md.
- **Plan output:** `z:\crossmint-devrel-challenge\_internal\planning\plans\01-research-plan.md`
- **Why necessary:** The research gate at H+7 is the single gate that determines whether the critical path even exists. If R1/R2 plans are sloppy or fallback-less, Tool 4 fails silently. This planner writes the exact URLs to fetch, the exact grep queries on SDK source, the exact verdict template, and what happens if a research task returns "inconclusive" (which is not currently handled in BUILD-PLAN.md). This cannot merge into any downstream planner because those planners consume its output.
- **Deliverable structure (phases → tasks):**
  - Phase 1: R1 (wallets-sdk surface) — 4 tasks (primary fetch, GitHub fallback, grep for `sign`, verdict write)
  - Phase 2: R2 (lobster-cli library exports) — 4 tasks
  - Phase 3: R3 (MCP SDK patterns) — 3 tasks
  - Phase 4: R4 (x402 payload format) — 3 tasks
  - Phase 5: Decision synthesis — 1 task (write the Plan A/B/C/D decision to `decisions.md`)
  - Fallback matrix + time budget per task

---

### 2.2 — Critical Path (Tool 4) Planner

- **Mandate:** Plan the `pay_x402_endpoint` build across all 4 fallback paths (Plans A/B/C/D), including the pivot rules if Plan A fails at hour 1, Plan B at hour 2, Plan C at hour 3.
- **Phases/areas owned:** Phase 2C (H+8 → H+12), plus the "Critical path" and "Cuts if we hit a blocker" sections of concept-lock-v2.md.
- **Plan output:** `z:\crossmint-devrel-challenge\_internal\planning\plans\02-critical-path-plan.md`
- **Why necessary:** Tool 4 is the single highest-risk deliverable in the entire submission. BUILD-PLAN §4 gives it 4 hours and 4 fallback plans, but does not decompose what "Plan A attempt" means in concrete steps. This planner writes the exact wallet creation commands, the exact Devnet USDC faucet URL, the exact target x402 endpoint candidates (Faremeter test, `crossmint-agentic-finance/weather`, Saul's own x402-rs test endpoints), the exact `X-PAYMENT` header construction code pattern, and — critically — the pivot trigger rules: if Plan A's first 3 sign-method probes fail, move to Plan B without burning another hour. Separating this from the broader MCP build planner is necessary because the risk profile, the research dependency, and the pivot cadence are all unique to Tool 4.
- **Deliverable structure (phases → tasks):**
  - Phase 1: Pre-build setup — 4 tasks (wallet create, Devnet USDC fund, target endpoint lock, env vars)
  - Phase 2: Plan A attempt (direct SDK sign) — 6 tasks with hour-1 pivot trigger
  - Phase 3: Plan B attempt (lobster-cli library wrap) — 5 tasks with hour-2 pivot trigger
  - Phase 4: Plan C attempt (REST API manual payload) — 5 tasks with hour-3 pivot trigger
  - Phase 5: Plan D fallback (transfer degraded mode) — 4 tasks + mandatory README caveat
  - Phase 6: Smoke test + error handling + commit — 3 tasks
  - Gate verification criteria for H+12

---

### 2.3 — MCP Build & Skill Planner

- **Mandate:** Plan the full MCP server build (scaffold, 3 remaining tools, MCP wiring, Claude Desktop smoke test) AND the lobster.cash skill build as a single cohesive track, because both artifacts share the same Crossmint wallet infrastructure.
- **Phases/areas owned:** Phases 2B, 2D, 2E, 2F, 2G from BUILD-PLAN.md (scaffold through lobster.cash skill, H+7 → H+22).
- **Plan output:** `z:\crossmint-devrel-challenge\_internal\planning\plans\03-mcp-build-and-skill-plan.md`
- **Why necessary:** This is the main build track. It owns ~14 hours of execution (the single biggest block). Merging scaffold + tools 1-3 + MCP wiring + smoke test + skill into one planner is correct because they share the same codebase context (`src/core/`, `src/mcp/`, plus the skill that references the MCP repo), the same TypeScript + `@modelcontextprotocol/sdk` + `@crossmint/wallets-sdk` stack, and the same Crossmint API key and Devnet funding. A single planner writing this track will produce internally consistent file names, import paths, and test fixtures across all 5 phases. Splitting this into "Scaffold Planner + Tools 1-3 Planner + MCP Wiring Planner + Skill Planner" would create 4 documents that must then be reconciled, which the Devil's Advocate explicitly flagged as cargo-cult waste.
- **Deliverable structure (phases → tasks):**
  - Phase 1: Scaffold (2B) — 12 tasks (package.json verify, tsconfig, stub files, .env.example, README skeleton, skill repo scaffold)
  - Phase 2: Tool 1 `create_wallet` (2D) — 5 tasks
  - Phase 3: Tool 2 `get_balance` (2D) — 4 tasks
  - Phase 4: Tool 3 `transfer_token` (2D) — 5 tasks
  - Phase 5: MCP server wiring (2E) — 7 tasks (server.ts, tools.ts, errors.ts, Zod schemas, stdio transport, stderr logging, tsc build)
  - Phase 6: Claude Desktop smoke test (2F) — 10 tasks (npm publish prep, config install, 4 tool test prompts, screenshots, video)
  - Phase 7: Lobster.cash skill (2G) — 8 tasks (SKILL.md structure, 3 required behaviors, README, install path test, Fede message draft)
  - Gate verification criteria for H+8, H+15, H+17, H+19, H+22

---

### 2.4 — Content & Campaign Planner

- **Mandate:** Plan the blog post, Twitter thread, video script, AND the 1-page campaign proposal as a single writing track. All four artifacts share the same narrative (two gaps, two fixes, Fede's quote, CPI nuance) and the same tone constraint (Saul voice, no Ultravioleta branding).
- **Phases/areas owned:** Phase 3 (Content, H+22 → H+27) + Phase 4 (Campaign, H+27 → H+29).
- **Plan output:** `z:\crossmint-devrel-challenge\_internal\planning\plans\04-content-and-campaign-plan.md`
- **Why necessary:** The Devil's Advocate kickoff explicitly flagged that splitting "DevRel Strategist and Technical Writer on the wrong seam" — format and writing are the same cognitive task. Same principle applies here: the blog post, thread, video script, and campaign proposal all derive from one narrative and must share one voice. A single planner writes the outline decisions, the title lock, the word budgets, the real-handles list (@crossmint_io, @alfongj, @cryptofede, @0xultravioleta), the Saul voice pass protocol, and the real distribution channels (MCP Registry, Claude Desktop listings, lobster.cash/skills, x402hackathon Discord, Crossmint blog). Merging content + campaign also prevents the failure mode the DA flagged where "Phase 4 (Campaign) is starved … treated as an afterthought."
- **Deliverable structure (phases → tasks):**
  - Phase 1: Title + outline lock — 3 tasks (title selection, structure confirm, hook draft)
  - Phase 2: Blog post (3.1) — 7 tasks (one task per section per BUILD-PLAN §9.1)
  - Phase 3: Twitter thread (3.2) — 4 tasks (draft, standalone check, link append, voice pass)
  - Phase 4: Video script (3.3) — 4 tasks (shot list, beats, install demo, final frame)
  - Phase 5: Campaign 1-pager (Phase 4) — 5 tasks (why, what, outcomes, channels, half-page fallback)
  - Phase 6: Saul voice pass protocol — 2 tasks (what to send him, what to expect back)
  - Gate verification criteria for H+27 and H+29

---

### 2.5 — Integration, QA & Risk Planner

- **Mandate:** Plan Phase 5 (polish + clean smoke test + DA final review), Phase 6 (public push + npm publish + email), AND the full risk register with concrete mitigation playbooks and pivot triggers across ALL phases.
- **Phases/areas owned:** Phase 5 (H+29 → H+33), Phase 6 (H+33 → H+36), and risk register §14 + decision gates §15 from BUILD-PLAN.md.
- **Plan output:** `z:\crossmint-devrel-challenge\_internal\planning\plans\05-integration-qa-and-risk-plan.md`
- **Why necessary:** The Devil's Advocate explicitly called out that BUILD-PLAN has "no explicit reviewer reproduction gate until Phase 5" and that's "the single most likely failure mode." This planner owns the clean-state reproduction test as a first-class deliverable, not a checkbox. It also owns the 15 risks in §14 — each needs a concrete trigger detection rule and a pre-written mitigation recipe so that when R01 fires at H+7, execution Claude is not improvising. Merging integration/polish/submission + risk into one planner is correct because the risks crystallize exactly at the gates this planner owns (clean smoke test at H+33, npm publish at H+36, email delivery confirmation). Splitting into "Integration Planner + Risk Planner" would produce two documents with the same gates and overlapping mitigation language.
- **Deliverable structure (phases → tasks):**
  - Phase 1: Top-level README rewrite (5.1) — 4 tasks
  - Phase 2: Cross-references between repos (5.2) — 3 tasks
  - Phase 3: Clean commit history audit (5.3) — 3 tasks
  - Phase 4: Fresh-machine reproduction test (5.4) — 6 tasks (fresh clone, fresh node_modules, fresh Claude Desktop config, 4-tool end-to-end, failure recovery)
  - Phase 5: Saul voice + DA final review (5.5, 5.6) — 4 tasks
  - Phase 6: Public GitHub push (6.1) — 3 tasks (both repos, incognito verify)
  - Phase 7: npm publish (6.2) — 4 tasks (auth check, publish, npx verification, rollback plan)
  - Phase 8: Email draft + send (6.3, 6.4, 6.5) — 5 tasks
  - Phase 9: Risk playbook — 15 tasks, one per risk R01–R15, each with (trigger detection + mitigation commands + time budget for mitigation + fallback if mitigation fails)
  - Phase 10: Decision gates consolidation — 10 tasks, one per gate G1–G10
  - Gate verification criteria for H+33, H+36, H+40

---

## 3. Alternative Roster A — Shorter (3 planners)

**Composition:**
1. Research + Critical Path Planner (merged 2.1 + 2.2)
2. Build Planner (MCP + Skill + Content + Campaign, merged 2.3 + 2.4)
3. Integration, QA & Risk Planner (same as 2.5)

**Why rejected:**
- Merging Research + Critical Path loses the clean gate at H+7. The research planner's job ends when the Plan A/B/C/D decision is written; the critical path planner's job starts when that decision is in. Putting them in one document means the planner is writing conditional plans ("if Plan A then X, if Plan B then Y") which triples the plan size without tripling the value. Research is a judgment task, critical path is an execution task — different cognitive modes.
- Merging Build + Content + Campaign creates a 30+ task plan document that crosses a hard medium break (TypeScript build → prose writing). A single planner holding both in their head is where quality drops. The Devil's Advocate already flagged this exact failure mode for the execution team ("Writer cannot finalize anything until the demo actually runs — the overlap is aspirational"). Splitting Build from Content/Campaign means Content can start planning in parallel without waiting on the build track.
- 3 planners leaves no slack for one planner failing, going off-scope, or being a poor fit.

**Verdict:** Too lean. The saved coordination cost is eaten by plan-quality risk.

---

## 4. Alternative Roster B — Longer (8 planners)

**Composition:**
1. Research Planner
2. Critical Path (Tool 4) Planner
3. Scaffold & Core Tools Planner (Phases 2B + 2D)
4. MCP Server Wiring Planner (Phase 2E)
5. Claude Desktop Smoke Test Planner (Phase 2F)
6. Lobster.cash Skill Planner (Phase 2G)
7. Content Strategist Planner (Phase 3)
8. DevRel Campaign Planner (Phase 4)
9. (plus an Integration/QA/Risk Planner if we're being honest — which pushes it to 9)

**Why rejected:**
- Directly contradicts the Devil's Advocate critique: *"Eight agents plus a human for three deliverables in 48 hours is cargo-cult scale."* That critique was aimed at the execution team; it applies doubly to the planning team.
- Phases 2B, 2D, 2E, 2F, 2G are one cohesive build track with shared repo state, shared imports, shared TypeScript compilation. Splitting them into 5 separate plan documents means 5 sets of assumptions about file paths and package versions that must be reconciled — the PM becomes a reconciliation bottleneck.
- Splitting Content from Campaign repeats the "DevRel Strategist vs Technical Writer on the wrong seam" error from BUILD-PLAN's original team architecture that the DA shredded.
- A dedicated Claude Desktop Smoke Test Planner is a 2-hour phase with 10 tasks — not enough surface to justify its own planner. Merges cleanly into the MCP Build track.
- Coordination cost: with 8-9 planners, HR/PM review of each plan is 15+ minutes × 8 = 2+ hours, which is 7% of the effective execution window spent on plan reviews alone.

**Verdict:** Cargo-cult scale. Ceremony masquerading as rigor.

---

## 5. Dependency Graph

```
                      [2.1 Research Planner]
                              │
                              │ (research verdicts feed Plan decision)
                              ▼
                   [2.2 Critical Path Planner]
                              │
                              │ (Tool 4 plan feeds build track)
                              ▼
                [2.3 MCP Build & Skill Planner]
                              │
                              │ (build track + artifact outputs feed polish)
                              ▼
                [2.5 Integration, QA & Risk Planner]


         [2.4 Content & Campaign Planner]  ◄── runs PARALLEL to 2.3
                              │
                              ▼
                (feeds into Integration 2.5)
```

**Sequential (must wait):**
- 2.1 → 2.2: Critical path plan depends on research verdicts
- 2.2 → 2.3: MCP build plan depends on Tool 4's chosen plan (A/B/C/D)
- 2.3 → 2.5: Integration plan depends on build track outputs existing as files
- 2.4 → 2.5: Integration plan depends on content track outputs as files

**Parallel (can run simultaneously):**
- 2.1 + 2.4 can start together (Content planner doesn't need research verdicts to outline)
- 2.2 + 2.4 can run together (Critical path + Content independent)
- 2.3 + 2.4 can run together (MCP build + Content independent — and this is the longest parallel window, roughly 60% of the planning time)

**Critical path for planning:** 2.1 → 2.2 → 2.3 → 2.5 (sequential chain of 4). This is the serialization bottleneck and should be where planner quality is highest.

---

## 6. Final Hire List for PM Approval

| # | Role | Spawn Order | Spawn Mode | Spawn Instruction for Claude (Orchestrator) |
|---|---|---|---|---|
| 1 | Research Planner | FIRST (immediate) | Sequential — blocks #2 | Spawn with mandate: "Read BUILD-PLAN.md §2 (Phase 2A) and Appendix A. Write `_internal/planning/plans/01-research-plan.md` with phases and numbered tasks for R1–R4, source-ranked fallbacks, inconclusive-verdict handling, and the decision synthesis step that writes to decisions.md. Time budget per task. Do NOT execute research, only plan it." |
| 2 | Content & Campaign Planner | FIRST (parallel with #1) | Parallel — independent | Spawn with mandate: "Read BUILD-PLAN.md §9 + §10, concept-lock-v2.md 'Shared artifacts' section, and the DA kickoff critique about content/campaign seams. Write `_internal/planning/plans/04-content-and-campaign-plan.md` with phases for blog post, Twitter thread, video script, and 1-page campaign proposal. Lock title, structure, word budgets, real handles, Saul voice pass protocol, distribution channels. No writing yet — plan only." |
| 3 | Critical Path (Tool 4) Planner | SECOND (after #1 drafts) | Sequential — needs research plan as input context | Spawn with mandate: "Read BUILD-PLAN.md §4, concept-lock-v2.md 'Critical path' + 'Cuts' sections, and the Research Planner's draft at `01-research-plan.md`. Write `_internal/planning/plans/02-critical-path-plan.md` decomposing Plans A/B/C/D with hour-by-hour pivot triggers, concrete commands, target x402 endpoint candidates, and gate criteria for H+12. Do NOT execute, only plan." |
| 4 | MCP Build & Skill Planner | THIRD (after #3 drafts) | Sequential — needs Tool 4 plan decisions as input | Spawn with mandate: "Read BUILD-PLAN.md §§3, 5, 6, 7, 8, concept-lock-v2.md Artifact 1 + Artifact 2 sections, and the Critical Path Planner's draft at `02-critical-path-plan.md`. Write `_internal/planning/plans/03-mcp-build-and-skill-plan.md` covering scaffold, Tools 1-3, MCP wiring, Claude Desktop smoke test, and lobster.cash skill. One cohesive build track with shared file paths and imports. Numbered tasks, time budgets, gate criteria for H+8/H+15/H+17/H+19/H+22." |
| 5 | Integration, QA & Risk Planner | FOURTH (after #3 and #4 drafts) | Sequential — needs build + content plans as input | Spawn with mandate: "Read BUILD-PLAN.md §§11, 12, 14, 15, and the drafts from planners #2, #3, #4. Write `_internal/planning/plans/05-integration-qa-and-risk-plan.md` covering polish, clean-state reproduction test (as a FIRST-CLASS deliverable, not a checkbox — per DA critique), npm publish, email draft, PLUS the full 15-risk playbook with trigger detection and mitigation commands, PLUS the 10 decision gates G1–G10. This is the safety net for the entire submission." |

**Total planners: 5.**
**Planning window:** ~1.5 hours wall clock if #1 and #2 run parallel, then #3, #4, #5 chain with ~20 min overlap at each handoff.
**Fits inside the Phase 2A research window without burning execution time from the main plan.**

---

## 7. Notes to PM

- Every planner's output is ONE markdown file. No planner writes code, writes prose for the blog, or executes any of the submission artifacts.
- Every plan must name real files, real commands, real URLs, real package names — no placeholders like `<endpoint-url>`. If a planner doesn't know a URL, they flag it as a dependency on the Research Planner's output, not a blank.
- Every plan must include time estimates per task AND a fallback strategy for each phase.
- The Research Planner is the only planner whose output materially changes the downstream plans. If Research Planner's verdict comes back "all 4 research tasks need more time," the PM may need to extend the planning window by 30 minutes and have the Critical Path Planner replan. Budget for this.
- No planner is a reviewer. The PM is the reviewer. The Devil's Advocate is the shredder. These 5 planners write plans and stop.

**End of HR proposal.**
