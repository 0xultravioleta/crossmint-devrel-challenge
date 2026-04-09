# PM Journal — Planning Phase

> **Role:** Planning Project Manager (coordinator, not executor)
> **Mandate:** Approve/reject HR's planner roster. Set non-negotiables. Write final hire list.
> **Output files:** `pm-journal.md` (this file), `decisions.md`
> **Author:** Claude (PM subagent, main conversation)

---

## 1. Phase start

- **Date/time:** 2026-04-09 ~14:04 EDT (H+5.5)
- **HR proposal state at spawn:** Empty. Planning dir existed, no file.
- **Poll cadence:** 3-minute intervals. HR delivered `hr-proposal.md` at 14:03 EDT (between poll 1 and poll 2).
- **Context files ingested before review:**
  - `_internal/phase-notes/BUILD-PLAN.md` (full, 685 lines — master skeleton)
  - `_internal/phase-notes/concept-lock-v2.md` (full — dual artifact concept)
  - `_internal/brainstorm/lobster-cash-deep-dive.md` (full — lobster.cash landscape)
  - `_internal/devils-advocate/kickoff.md` (full — the cargo-cult critique)
  - `_internal/decisions.md` (full — decision log including H+2.5 team cut, H+5 concept v2)
  - `_internal/planning/hr-proposal.md` (just delivered)

---

## 2. HR proposal review

### 2.1 Summary of HR's proposal

HR proposed a **5-planner roster**:

1. Research Planner — Phase 2A
2. Critical Path (Tool 4) Planner — Phase 2C
3. MCP Build & Skill Planner — Phases 2B+2D+2E+2F+2G (merged build track)
4. Content & Campaign Planner — Phase 3 + Phase 4 (merged writing track)
5. Integration, QA & Risk Planner — Phase 5 + Phase 6 + risk register + gates (merged)

HR also proposed rejected alternatives (3-planner too lean, 8-planner cargo-cult), a dependency graph, and spawn instructions per role.

### 2.2 What I accept without modification

- **The count: 5.** Defensible against both the 3-planner lean version (loses gate clarity between research and execution) and the 8-planner bloated version (directly contradicts DA kickoff critique which I re-read specifically to check). HR correctly anchored against DA and cited the exact passages.
- **Research as a standalone planner.** Research is a judgment task with a hard gate at H+7. Merging it into critical path would force the Tool 4 planner to write conditional trees (if Plan A then X, if Plan B then Y) that triple plan size. Kept separate.
- **Critical Path (Tool 4) as standalone planner.** Tool 4 is the single highest-risk deliverable. Separating it from the broader build track is correct because the pivot cadence (hour 1, 2, 3 triggers across Plans A/B/C/D) is unique and needs hour-resolution granularity that no other phase needs.
- **Content + Campaign merged.** Per DA kickoff section 2: "DevRel Strategist and Technical Writer are split on the wrong seam. The format decision and the writing are the same cognitive task." HR honored this. Good.
- **Integration + Risk merged.** HR's argument holds: "risks crystallize exactly at the gates this planner owns." Splitting would produce two documents with overlapping mitigation language and the same gates. Also, the DA kickoff demanded a "reviewer-reproduction gate" as a FIRST-CLASS deliverable, not a checkbox — merging keeps that gate in the same document as the risk that it mitigates.
- **Dependency graph + spawn order.** #1 and #4 parallel; #3 sequential after #1; #2 sequential after #3 (*wait — reversed vs HR; HR has #3 MCP Build after #2 Critical Path — I agree with HR, my read is correct*); #5 last after #3 and #4. Sound.

### 2.3 What I cut / reject

**Nothing is fully cut.** HR's roster survived review. But I am **rejecting HR's casual scope of planner #3** and applying a hard scope cap (see §4 non-negotiables below). The MCP Build & Skill Planner is owning 5 build phases in one document and that is the single biggest plan-quality risk. I accept the merge but with a tighter constraint than HR proposed.

### 2.4 What I merge / consolidate further

**No further merges.** HR already merged appropriately. Going tighter risks the same "Build → prose writing cognitive break" that HR flagged in the 3-planner alternative.

### 2.5 What I tighten (non-negotiables not in HR proposal)

HR's plan is strong but missing:
1. **Hard word/page caps on every plan document** — without caps, planners over-produce and PM has to re-review bloat.
2. **Mandatory "Open questions for PM / Research" section in every plan** — so planners surface their assumptions explicitly instead of burying them.
3. **"What this plan is NOT" section** — DA kickoff's biggest critique was that plans creep because nobody writes down the out-of-scope line. Every planner must draw it.
4. **No nested subagents.** Planners plan, they do not spawn research agents. Direct Claude execution only.
5. **Single file output, no sibling docs.** Each planner produces exactly one `.md` file at the approved path. No appendices in separate files.
6. **Explicit Framing Discipline reference.** Every plan must include a 3-line reminder of the facilitator-agnostic rule + no Ultravioleta branding rule + MCP-server-as-companion positioning.

---

## 3. Final planner roster

APPROVED. 5 planners. Owner = "Claude (direct execution in main conversation)" for all five, because API 529 is still the dominant constraint per decision log H+1. If API recovers during planning window, the orchestrator MAY spawn planners as subagents, but fallback is direct execution.

### Approved planners (numbered, with output file and spawn order)

| # | Role | Owner | Output file | Spawn order | Spawn mode |
|---|---|---|---|---|---|
| 1 | Research Planner | Claude (direct) | `_internal/planning/plans/01-research-plan.md` | FIRST (immediate, parallel with #4) | Sequential blocker for #2 |
| 2 | Critical Path (Tool 4) Planner | Claude (direct) | `_internal/planning/plans/02-critical-path-plan.md` | SECOND (after #1 draft exists) | Sequential blocker for #3 |
| 3 | MCP Build & Skill Planner | Claude (direct) | `_internal/planning/plans/03-mcp-build-and-skill-plan.md` | THIRD (after #2 draft exists) | Sequential, feeds #5 |
| 4 | Content & Campaign Planner | Claude (direct) | `_internal/planning/plans/04-content-and-campaign-plan.md` | FIRST (immediate, parallel with #1) | Parallel with #1/#2/#3, feeds #5 |
| 5 | Integration, QA & Risk Planner | Claude (direct) | `_internal/planning/plans/05-integration-qa-and-risk-plan.md` | LAST (after #3 and #4 drafts exist) | Sequential, closes planning |

### Rejected planners (with reason)

- None from HR's proposal. HR already pre-cut the cargo-cult version (8-planner alt) and the lean version (3-planner alt). PM accepts HR's cuts wholesale.

### Merged (from HR proposal, accepted)

- Content + Campaign → single planner (#4) — per DA kickoff section 2
- Phases 2B + 2D + 2E + 2F + 2G → single Build planner (#3) — shared TypeScript codebase and imports
- Phase 5 + Phase 6 + Risk register + Decision gates → single Integration planner (#5) — same gates the risks crystallize at

---

## 4. Non-negotiables for ALL planners

Every plan must comply with these or I reject on review.

### 4.1 Scope rules

1. **PLAN ONLY, NEVER EXECUTE.** No planner writes code, prose, or executes research. Planners write numbered phase/task plans that a downstream executor can follow without ambiguity.
2. **One file, one planner.** Output file path is fixed (see roster table). No sibling files, no appendices in separate docs.
3. **Single markdown document.** Each plan is ONE `.md` file. Embedded diagrams as ASCII or links.
4. **Hard page cap:** 1,200 lines max per plan document. Anything longer = bloat, PM rejects and asks for tightening. Target 600-900 lines.
5. **"What this plan is NOT" section is mandatory.** Every plan has a 5-10 bullet section explicitly listing out-of-scope items. If a reviewer could confuse the plan's scope with a neighboring planner's scope, the out-of-scope line is not drawn sharply enough.
6. **Framing Discipline reminder is mandatory.** Every plan has 3-line block at the top referencing:
   - Facilitator-agnostic (Faremeter default, Ultravioleta facilitator not mentioned)
   - No Ultravioleta branding in code/README/content/campaign
   - The MCP server is the MCP-native companion to lobster.cash, the skill is a certified lobster.cash skill — both position Saul as ecosystem builder

### 4.2 Format rules

1. **Structure = "Phase N → Task 1, 2, 3".** Every plan uses explicit numbered phases, each phase has numbered tasks. No bullet-soup planning.
2. **Every task must include:**
   - File path(s) the task creates, modifies, or reads (absolute Windows paths per CLAUDE.md)
   - Exact command(s) the task runs (shell, not prose)
   - URL(s) if the task fetches external info
   - Time budget (in minutes, not "short" or "quick")
   - Fallback strategy if the task fails (one line minimum)
3. **Every phase must include a GATE at the end** — a single binary success criterion that determines whether execution moves to the next phase.
4. **"Open questions for PM / Research" section is mandatory.** Anything the planner assumes but can't verify goes here, not buried inline. Max 10 items.
5. **"Dependencies on other plans" section is mandatory.** Every plan declares which upstream plans it reads and which downstream plans depend on its output.
6. **No placeholder values.** No `<endpoint-url>`, `<api-key>`, or `<some address>`. If the planner doesn't know the value, they put it in Open Questions, not inline.

### 4.3 Quality gates (PM rejection criteria)

PM will **reject and force a rewrite** if any plan:

- Exceeds 1,200 lines
- Contains any executable code (TypeScript, Python, Rust) beyond single-line shell commands
- Contains any prose for the blog post, README, or email (that's Content Planner's scope only, and even Content Planner plans the writing, does not write it)
- Uses placeholders anywhere in task definitions
- Spawns subagents (planners plan, they do not orchestrate)
- Misses the "What this plan is NOT" section
- Misses the Framing Discipline reminder
- Misses per-task time budgets or fallback strategies
- Makes claims that contradict BUILD-PLAN.md or concept-lock-v2.md without flagging the contradiction in Open Questions

### 4.4 Security constraints

Per CLAUDE.md and global security rules:
- **NO hardcoded private keys, API keys, or secrets** in ANY plan document (not even as examples).
- **NO real wallet addresses** that belong to Saul or the LLC. Use `0xDEAD...BEEF` placeholders if an example is needed, and flag it.
- **NO screen-shareable sensitive info.** Assume Saul is streaming during execution.

---

## 5. Specific instructions per planner

### 5.1 Planner #1 — Research Planner

**Sources to read before planning:**
- `_internal/phase-notes/BUILD-PLAN.md` §2 (Phase 2A)
- `_internal/phase-notes/BUILD-PLAN.md` Appendix A (Unknowns)
- `_internal/phase-notes/concept-lock-v2.md` "The critical path" section
- `_internal/brainstorm/lobster-cash-deep-dive.md` (for lobster-cli context)

**Must include in the plan:**
- Decomposition of R1, R2, R3, R4 into concrete fetch/parse/verdict tasks
- Primary source + fallback source per research task
- Exact grep queries to run on GitHub source (e.g., `grep -rn "sign(" src/`)
- **"Inconclusive verdict" handling** — what happens if a research task cannot produce YES/NO. This is NOT in BUILD-PLAN.md and is a critical gap. Add a "Plan selection decision rule when R1+R2 both inconclusive" fallback.
- Decision synthesis task: a single task at the end that writes the Plan A/B/C/D verdict to `_internal/decisions.md` in the exact format "Tool 4 will use Plan [A/B/C/D] because [reason]."
- Per-task time budget summing to ≤ 2 hours (Phase 2A budget)
- Hard gate: research files exist AND Plan decision is written to decisions.md by H+7

**Must NOT include:**
- Any actual research findings (that's execution, not planning)
- Any code to run the research
- Any recommendations about which plan should be chosen (the planner plans the decision PROCESS, not the decision itself)

**Target length:** 400-700 lines.

---

### 5.2 Planner #2 — Critical Path (Tool 4) Planner

**Sources to read before planning:**
- `_internal/phase-notes/BUILD-PLAN.md` §4 (Phase 2C)
- `_internal/phase-notes/concept-lock-v2.md` "The critical path" + "Cuts if we hit a blocker"
- `_internal/planning/plans/01-research-plan.md` (Planner #1's draft)
- `_internal/phase-notes/BUILD-PLAN.md` §14 risks R01, R02, R03 (Tool 4 specific)

**Must include in the plan:**
- Pre-build setup phase: Devnet wallet creation, USDC faucet URL (Circle's, with fallback), target x402 endpoint candidates (minimum 3 candidates ranked), env var list
- Plan A (direct SDK sign) decomposition: 6 tasks with an **explicit hour-1 pivot trigger** — if after 60 minutes Plan A has not returned a signed payload, move to Plan B immediately
- Plan B (lobster-cli wrap) decomposition: 5 tasks with an **explicit hour-2 pivot trigger**
- Plan C (REST API manual) decomposition: 5 tasks with an **explicit hour-3 pivot trigger**
- Plan D (transfer fallback) decomposition: 4 tasks + MANDATORY README caveat text for shipping with degraded mode
- Smoke test task with a specific endpoint to hit and a specific response shape to expect
- Commit protocol: one clean commit per plan attempt, with scoped conventional commit messages
- Gate at H+12: `pay_x402_endpoint` returns a valid response body from a Solana Devnet x402 endpoint
- **Fail-gate behavior:** if gate fails by H+12, what exactly does the pivot to "skill only" look like — plan the pivot path, don't just name it

**Must NOT include:**
- Any of the other 3 tools (that's Planner #3)
- Any MCP server wiring (that's Planner #3)
- Any blog post narrative (that's Planner #4)

**Target length:** 600-900 lines.

---

### 5.3 Planner #3 — MCP Build & Skill Planner

**This is the biggest planner. Scope discipline is critical.**

**Sources to read before planning:**
- `_internal/phase-notes/BUILD-PLAN.md` §§3, 5, 6, 7, 8 (Phases 2B, 2D, 2E, 2F, 2G)
- `_internal/phase-notes/concept-lock-v2.md` Artifact 1 + Artifact 2 sections
- `_internal/planning/plans/02-critical-path-plan.md` (Planner #2's draft — to understand what Tool 4 exposes that Tools 1-3 must match)
- `_internal/brainstorm/lobster-cash-deep-dive.md` Skill Compatibility Guide references

**Must include in the plan:**
- Phase 2B (Scaffold): 8-12 tasks covering both repos (`crossmint-wallets-mcp` and `crossmint-cpi-skill`). Explicit file list per repo. tsc gate.
- Phase 2D (Tools 1-3): 3 sub-phases, one per tool. Each tool: input schema, core function behavior, error handling, smoke test, commit. Tools 1-3 MUST reuse the same `CrossmintWallets` client initialization pattern that Tool 4 establishes.
- Phase 2E (MCP wiring): 5-7 tasks covering `src/mcp/server.ts`, Zod schemas in `src/mcp/tools.ts`, stdio transport, stderr logging (NOT stdout), tsc build, local test with stdin JSON-RPC.
- Phase 2F (Claude Desktop smoke test): npm publish prep, config install on Windows path (`%APPDATA%\Claude\claude_desktop_config.json`), 4 tool test prompts, screenshot protocol, video protocol (optional per BUILD-PLAN cuts).
- Phase 2G (lobster.cash skill): SKILL.md structure per compatibility guide, 3 required behaviors (wallet precheck, payment action description, payment status + error handling), README, install path test, draft of Fede Telegram message for cert request.
- Gate criteria at H+8 (scaffold compiles), H+15 (all 4 tools individually work), H+17 (MCP server responds to initialize/tools/list/tools/call), H+19 (Claude Desktop end-to-end), H+22 (skill + cert request ready).
- **Scope cut rules:** which tasks get dropped first if Planner #3's own track runs over time. Reference BUILD-PLAN §14 R10 and concept-lock-v2 "Cuts" hierarchy.

**Must NOT include:**
- Tool 4's implementation plan (Planner #2 owns that — Planner #3 only wires Tool 4 into the MCP server)
- Research on SDK APIs (Planner #1 owns that)
- Blog post / campaign / email (Planner #4)
- Reproduction test / final polish (Planner #5)

**Hard scope cap:** 1,200 lines. If the planner needs more, it means the build track should have been split — in that case planner must return to PM and request re-architecture. PM will reject silently-bloated plans.

**Target length:** 900-1,200 lines. This is the single biggest plan, and the tightest budget.

---

### 5.4 Planner #4 — Content & Campaign Planner

**Sources to read before planning:**
- `_internal/phase-notes/BUILD-PLAN.md` §§9, 10 (Phase 3 content + Phase 4 campaign)
- `_internal/phase-notes/concept-lock-v2.md` "Shared artifacts (content + campaign)" section
- `_internal/devils-advocate/kickoff.md` sections 1, 3, 4 (content/campaign critique)
- `_internal/decisions.md` Non-negotiables section

**Must include in the plan:**
- Title selection protocol: 3 title candidates, voice check criteria, lock time
- Blog post outline: 7 sections from BUILD-PLAN §9.1 with word budget per section, ONE task per section. Voice rules (Saul's voice, no AI-tone banned words: "seamlessly," "elegant," "unlock," "empower," "at scale" — per DA kickoff section 6 red flag #3).
- Twitter thread plan: 8-10 tweets, each stands alone, final tweet links repo. Thread voice check.
- Video demo script plan: shot list, 60-90 second beats, install demo, final frame.
- Campaign 1-pager plan: 4 sections (why, what, outcomes, channels). Real handles mandatory: @crossmint_io, @alfongj, @cryptofede, @0xultravioleta, Filippos. MCP Registry, Claude Desktop listings, lobster.cash/skills, x402hackathon Discord, blog.crossmint.com. No generic channel names.
- **Saul voice pass protocol:** what to send him, what to expect back, cutoff time if Saul is unavailable
- **Fede quote permission protocol:** if we use "x402 está en abandono un poco," the plan must include an explicit task to confirm Fede is OK with public attribution (or anonymize to "Crossmint team lead")
- Gate at H+27 (content draft complete) and H+29 (campaign 1-pager complete, 1 page max)

**Must NOT include:**
- Any actual prose (no blog sentences, no tweet text, no campaign sentences — plan the WRITING PROCESS only)
- Build track content (Planner #3)
- Risk playbook (Planner #5)

**Target length:** 500-800 lines.

---

### 5.5 Planner #5 — Integration, QA & Risk Planner

**Sources to read before planning:**
- `_internal/phase-notes/BUILD-PLAN.md` §§11, 12, 14, 15 (Phase 5 + Phase 6 + risks + gates)
- `_internal/phase-notes/concept-lock-v2.md` "Cuts if we hit a blocker"
- `_internal/devils-advocate/kickoff.md` section 3 (reviewer reproduction gate)
- `_internal/planning/plans/03-mcp-build-and-skill-plan.md` (Planner #3's draft)
- `_internal/planning/plans/04-content-and-campaign-plan.md` (Planner #4's draft)

**Must include in the plan:**
- Top-level README rewrite plan: 4-5 tasks for `z:\crossmint-devrel-challenge\README.md`
- Cross-references plan: MCP repo → skill repo, skill repo → MCP repo, both → submission entry
- Clean commit history audit: what "clean" looks like, how to squash if needed, no Claude co-author rule enforcement
- **Fresh-machine reproduction test as a FIRST-CLASS phase** (per DA kickoff section 3, R01): fresh clone, fresh node_modules, fresh Claude Desktop config, 4 tool end-to-end, failure recovery. This is NOT a checkbox. This is its own phase with 6+ tasks.
- Saul voice pass phase + DA final review phase — both as formal gates, not tasks
- Public GitHub push: 2 repos, incognito verify (Saul's OpenClaw or incognito browser), permission check
- npm publish: auth check, publish command, `npx crossmint-wallets-mcp --version` verification from fresh shell, rollback plan if name conflict
- Email draft plan: subject, body skeleton (3 paragraphs), recipient + CC list (Adolfo, Fede, Filippos mentioned), links audit
- **Risk playbook:** 15 risks R01-R15 from BUILD-PLAN §14. ONE task per risk. Each task has: trigger detection rule, mitigation command(s), time budget for mitigation, fallback if mitigation fails.
- **Decision gates consolidation:** 10 gates G1-G10. ONE task per gate. Each task: time check, criterion check, pass/fail action.
- Gate at H+33 (clean smoke test green, voice pass done, DA cleared), H+36 (ship), H+40 (hard cutoff)

**Must NOT include:**
- Actual risk mitigations executed (planner plans the playbook, executor runs it)
- Any build task (Planner #3)
- Any content task (Planner #4)
- Any research (Planner #1)

**Target length:** 900-1,200 lines. This planner has the widest surface area.

---

## 6. Timing

**Planning window:** 2026-04-09 14:05 EDT (now) → 15:45 EDT (~1.5 hours)

This fits inside the Phase 2A research window (BUILD-PLAN §2, H+5 → H+7 = 11:30 → 13:30 EDT, adjusted for the H+5.5 drift) **plus a 30-minute planning slippage budget**. If we overrun, execution Claude starts Phase 2A research on the roster that's ready, and the late plans catch up in parallel.

### Spawn timing

| Planner | Spawn time | Draft due | Review by PM |
|---|---|---|---|
| #1 Research | 14:05 EDT (immediate) | 14:25 EDT (20 min) | 14:30 EDT |
| #4 Content & Campaign | 14:05 EDT (immediate, parallel #1) | 14:35 EDT (30 min) | 14:40 EDT |
| #2 Critical Path | 14:30 EDT (after #1) | 15:00 EDT (30 min) | 15:05 EDT |
| #3 MCP Build & Skill | 15:05 EDT (after #2) | 15:50 EDT (45 min) | 15:55 EDT |
| #5 Integration, QA & Risk | 15:55 EDT (after #3 and #4) | 16:35 EDT (40 min) | 16:40 EDT |

**Total planning wall clock:** ~2 hours 35 minutes if everything runs ideal. Worst case 3 hours.

**Budget slippage allowed:** +30 minutes on any single planner before PM extends deadline OR kills and replans. No planner gets more than 1 hour wall clock.

**Hard deadline for ALL plans ready:** 2026-04-09 17:00 EDT (H+8).

### Execution window impact

BUILD-PLAN §2 budgets Phase 2A research at H+5 → H+7 (2 hours). With plans due at H+8 (worst case), execution research Phase 2A would start at H+8 instead of H+5. That's 3 hours of drift against BUILD-PLAN's original H+5 research start.

**Decision:** Acceptable. The drift buys better plans, which saves execution time downstream. Net trade is favorable because the Critical Path plan alone, if done well, de-risks ~4 hours of Phase 2C flailing. Trading 3h of research drift for 4h of Tool 4 de-risking is a win.

**Mitigation:** If planners deliver faster than budgeted (e.g., #1 in 15 min instead of 20), execution Claude can start Phase 2A research in parallel with later planners. Planners #3-#5 can finish while research is already in flight, and their outputs inform execution Phase 2B onward.

---

## 7. Next actions

1. **Write `decisions.md`** with the approved roster, rejected items, merges, and spawn instructions for Claude orchestrator.
2. **PM journal ENDS with step 1.** Orchestration of planners is the main conversation's job, not this subagent's job.
3. **Orchestrator spawns planners per the table in §6.** Planners #1 and #4 go first in parallel. Then the chain #2 → #3 → #5.
4. **Each plan gets PM review** upon draft completion. PM rejects and forces rewrite if quality gates (§4.3) are violated.
5. **Planning phase ends** when all 5 plans are in `_internal/planning/plans/` and PM has signed off on all 5.
6. **Execution phase begins** at the EARLIEST of: (a) all 5 plans approved, or (b) Planner #1 + #2 approved and execution Phase 2A can start with Phase 2B-onwards plans catching up in parallel.

---

**End of PM journal. PM signs off on HR proposal (with tightening per §4-§5). Handing back to orchestrator.**
