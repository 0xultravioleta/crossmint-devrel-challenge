# Decision Log — Crossmint DevRel Challenge

> Running log of binding decisions. Author for this phase: PM (ruthless), written directly by Claude after subagent 529 failures.

---

## 2026-04-09 H+0: Challenge accepted

- Received challenge brief from Adolfo Fernandez at 06:17 EDT
- Deadline: 2026-04-11 06:17 EDT (48h)
- Submission format: single GitHub repo
- Separate repo created at `z:\crossmint-devrel-challenge\`

---

## 2026-04-09 H+1: Agent orchestration strategy

- Launched parallel agent team: HR, PM, Devil's Advocate + docs-scraper + github-explorer
- First spawn batch: all 3 coordinators failed with API 529 overload
- Second spawn batch: same errors, 5 agents failed within seconds
- **Decision:** Pivot to direct Claude meta-analysis for coordinator roles (HR, PM, Devil's Advocate). Use WebFetch directly for research. Preserve subagent option for specialist roles in case API recovers.

---

## 2026-04-09 H+2: Team hiring decision (Phase 0) — SUPERSEDED

Initial roster was 5 specialists. After DA kickoff critique and PM review, roster cut to 4. See revised decision at H+2.5 below.

## 2026-04-09 H+2.5: Team hiring decision — FINAL after DA review

**Approved specialists (4 roles, spawn order):**

1. **Concept Architect** (Claude direct in main conversation — not a subagent) — outputs `_internal/phase-notes/concept-lock.md`, `demo/SPEC.md`, top-level `README.md` draft
2. **Demo Builder** (merged: Demo Engineer + Technical Writer) — outputs `demo/` source, `demo/README.md`, `content/blog-post.md`. One role because this is documentation-driven development: README first, code second, blog post is the expanded README.
3. **DevRel Campaign Strategist** — outputs `campaign/proposal.md` (1 pager)
4. **Integration Lead** — outputs final top-level `README.md`, QA log, cleanup, submission email draft, submission readiness check

**Rejected (with reason):**

- **Crossmint Docs Auditor** — FINDINGS.md already has the research.
- **Blog Style Reviewer** — Saul does the voice pass.
- **Distribution Specialist** — overlaps with DevRel Campaign Strategist.
- **Taste Reviewer** — Devil's Advocate covers this.
- **Technical Writer as separate role** — merged into Demo Builder under documentation-driven development model (per DA critique, accepted).

**Merged / consolidated:**

- Demo Engineer + Technical Writer = Demo Builder (single role, writes README first, then code, then expanded blog post)
- Integration Lead + Final QA + submission email ownership = one role (unchanged)

**Notes for Claude (orchestrator):**

- API 529 is the dominant constraint. Run all 4 roles as direct Claude execution in main conversation unless API clearly recovers.
- The Demo Builder role is the highest-tool-volume job. If API recovers, spawn it as a subagent. Otherwise direct.
- Concept Architect runs IMMEDIATELY next (in this conversation) and locks concept by H+4.

---

## 2026-04-09 H+2.5: Concept FINAL lock (pending Concept Architect execution)

**Concept statement (commitment):**

> **"The 5-minute x402 quickstart for Crossmint smart wallets that docs.crossmint.com does not have."**

**Opinion sentence (must be under 280 characters — Twitter-length rubric):**

> *"Crossmint's x402 story lives in an 11-demo monorepo and an n8n node. A developer landing on docs.crossmint.com looking for 'my first x402 payment' finds nothing. Here's the quickstart that should exist."*

(264 characters, passes the rubric)

**Framing rules:**

1. The demo is facilitator-agnostic. Primary facilitator: **Faremeter** (Crossmint's preferred, per Filippos interview). Secondary mention: Ultravioleta as a one-line footnote.
2. No "Ultravioleta" branding anywhere in the public-facing artifacts (demo, content, campaign). The repo can live under Saul's personal GitHub, but the content is Crossmint-first.
3. The demo code is NET NEW — written from scratch for this submission, even though it leans on Saul's domain knowledge of CPI inner instructions and Solana smart wallets.
4. The content piece explains the CPI inner instruction gap in plain English (why smart wallet payments are "invisible" to naive facilitator scans), but does NOT reference Saul's facilitator code specifically. The gap is a protocol concept, not a Saul asset.

**Why this concept won (vs. alternatives):**

- **vs. "Delegated Signers explainer"** — delegated signers docs page is a 404 in our research, which is a huge gap. But building a demo for delegated signers requires a full consent-flow UI, and that is too much scope for 48h. x402 quickstart is tighter.
- **vs. "Rewriting the AI Agents intro page"** — pure content, no runnable code. The brief requires a runnable demo. Rejected.
- **vs. "Lobster Cash + Execution Market skill"** — Filippos already saw Execution Market. Per DA's rule, stale signal. Rejected.
- **vs. "Wallet-as-a-Signer pattern"** — too abstract, no clear "5-minute run it yourself" moment.

**Why this concept has a clear opinion that fits the brief:**

The opinion sentence follows the rubric the brief gives literally: *"this concept is confusing because X, so I approached it as Y."* In our case: *"Crossmint's x402 story is scattered across a monorepo and a n8n node, so I wrote the single-page quickstart a developer actually needs."*

**What changes in the plan because of the concept lock:**

- Concept Architect deliverable becomes: `_internal/phase-notes/concept-lock.md` with the above + `README.md` draft (documentation-driven development per DA).
- Demo Builder starts on README at H+4, demo code at H+8, blog post at H+20.
- DevRel Campaign Strategist starts at H+12 with: *"who is the audience, what channels, what metrics for a Crossmint x402 quickstart?"*
- Integration Lead runs at H+30 for the first reproducibility smoke test.
- Ship target: **H+36, not H+48** (DA's timezone rationale).

---

## 2026-04-09 H+5: Concept v2 — DUAL ARTIFACT

Fede confirmed on Telegram that lobster.cash is Crossmint's play but "not what you're saying" — the MCP server angle is non-duplicative. Deep dive revealed lobster.cash is CLI + skills architecture, has 13 certified skills, and Fede is the certification gate.

User decision: "hagamoslo los dos" — ship both the MCP server AND a lobster.cash skill.

**Concept lock v2:**

> "The two things lobster.cash doesn't ship: a Crossmint Wallets MCP Server for MCP-native clients (Claude Desktop, Continue.dev, Cline, Codex), and a certified lobster.cash skill that fills the Solana CPI scanning gap only Saul can explain."

**Two artifacts, shared narrative, shared blog post, shared campaign proposal.**

**Artifact 1:** `crossmint-wallets-mcp` — MCP server with 4 tools (create_wallet, get_balance, transfer_token, pay_x402_endpoint). Ships via npm. Installable in any MCP-native client via JSON config. Built first.

**Artifact 2:** `crossmint-cpi-skill` — lobster.cash-compatible skill focused on the Solana CPI inner instruction nuance. Thin adapter, calls @crossmint/lobster-cli under the hood. Built second. Fede is the cert gate.

**Critical path:** pay_x402_endpoint tool in the MCP server. Must work by H+11 or we pivot. 4 fallback plans documented in concept-lock-v2.md.

**Full plan:** see `_internal/phase-notes/concept-lock-v2.md`

**Kills:** concept-lock.md v1 (Hello x402). Ship target remains H+36, hard cutoff H+40.

---

## 2026-04-09 H+2.5: Timeline revision

| Gate | Old time | New time | Change reason |
|---|---|---|---|
| Phase 0 end | H+2 | H+0.5 | DA: Phase 0 is ceremony, collapse it |
| Concept lock | H+6 (my tighten) | H+4 (DA push) | Concept is the load-bearing decision |
| Demo runs E2E (dev box) | H+20 | H+14 | Leave buffer for reviewer-machine issues |
| Reproducibility gate (clean env) | H+44 | H+30 | DA: this is the real fail mode |
| Content voice pass by Saul | H+36 | H+24 | Distribute Saul bandwidth earlier |
| Final Devil's Advocate review | H+44 | H+32 | Leave 4h for fixes |
| Ship | H+48 | H+36 | DA: Adolfo is in Spain, ship during EU business hours |
| Hard cutoff | H+48 | H+40 | Real buffer for the unexpected |

---

## 2026-04-09 H+2: Concept lock decision (PROVISIONAL, pending Concept Architect + Devil's Advocate review)

Based on FINDINGS.md, the provisional winning concept is:

> **"The missing x402 quickstart for Crossmint smart wallets on Solana"**
>
> *"Crossmint has world-class x402 support in its agentic-finance repo and n8n integration — but there is zero dedicated x402 tutorial in docs.crossmint.com. Developers who hear 'Crossmint + x402' on Twitter or at the x402 hackathon have nowhere to land. I am building the 5-minute quickstart that should exist."*

**This is PROVISIONAL.** The Concept Architect must defend or replace it in Phase 1. The Devil's Advocate must shred it in parallel. PM commits only after both reviews.

---

## Non-negotiables committed to the team

1. ONE concept. No hedging.
2. Demo runs in 5 minutes or less on a clean machine.
3. Content piece has an explicit opinion sentence.
4. Campaign proposal names real channels, handles, and people.
5. Saul does the final voice pass on content.
6. Devil's Advocate final review before ship.
7. Hard cutoff H+44 for development, H+46 for push, H+48 for email.

---

## Pending decisions

- **Concept final lock** — Phase 1, owner Concept Architect + Devil's Advocate + PM
- **Demo stack** — Phase 2, owner Demo Engineer (likely TypeScript + @crossmint/wallets-sdk)
- **Facilitator choice** — Phase 2, owner Demo Engineer (Ultravioleta facilitator vs Faremeter vs Corbits)
- **Content format** — Phase 3, owner Technical Writer (blog post primary, Twitter thread secondary)
- **Campaign channels** — Phase 4, owner DevRel Campaign Strategist

---

## 2026-04-09 H+6: Tool 4 plan verdict (Research close-out)

**Verdict: Plan A (direct SDK signing via `@crossmint/wallets-sdk`)**

**Why:** R1 research confirmed that `@crossmint/wallets-sdk@1.0.7` exposes all the primitives needed for `crossmint_pay_x402_endpoint` on both Solana and EVM:

- **Solana path:** `SolanaWallet.from(wallet).sendTransaction({ serializedTransaction })` accepts any base64-encoded Solana transaction. Build a TransferChecked instruction client-side, serialize, submit. Crossmint's smart wallet signs via CPI internally.
- **EVM path:** `EVMWallet.from(wallet).getViemClient()` returns a raw viem WalletClient for EIP-712 typed data signing (needed for EIP-3009 x402 payments).

**Plans B, C, D remain documented as mitigations in `_internal/planning/plans/02-critical-path-plan.md`** with hour-resolution pivot triggers, but are NOT expected to fire.

**Dropped from MCP server scope (confirmed by R1):**

- `crossmint_add_delegated_signer` tool — the SDK's signer model is recovery-only, not first-class delegation with spending caps. Can ship in v0.2 if Crossmint adds the API.
- `crossmint_list_transactions` tool — nice-to-have, dropped to stay under the 4-tool cap.

**Final tool list for v0.1:**

1. `crossmint_create_wallet`
2. `crossmint_get_balance`
3. `crossmint_transfer_token`
4. `crossmint_pay_x402_endpoint` ← critical path

**R2 verdict recorded:** `@crossmint/lobster-cli@3.0.8` has importable library exports (`parseConfig`, `getOrCreateWallet`, `createTransaction`, `approveTransaction`, etc.), BUT the MCP server will NOT wrap it. Reasons: state isolation (`~/.lobster/agents.json` would conflict across installs), clean MCP stdio transport (lobster-cli's human-readable stdout breaks JSON-RPC). Clean separation between the two Crossmint products.

**R3 verdict recorded:** `@modelcontextprotocol/sdk@1.29.0` pinned. Use `McpServer` + `StdioServerTransport`. Required peer: `zod@^3.25.0`. Log to stderr only — stdout is reserved for JSON-RPC.

**R4 deferred** to Phase 2C Task 1.2 (x402 payload format inspection during critical path build, not a Phase 2A blocker).

**Phase 2A gate: PASSED at H+6.**

---

## 2026-04-09 H+6: Planning phase complete

All 5 specialist plans delivered (2,805 lines total) at `_internal/planning/plans/`:

1. `01-research-plan.md` (266 lines)
2. `02-critical-path-plan.md` (487 lines)
3. `03-mcp-build-and-skill-plan.md` (981 lines, under 1200 cap)
4. `04-content-and-campaign-plan.md` (451 lines)
5. `05-integration-qa-and-risk-plan.md` (620 lines)

All 5 conform to the 6 PM tightenings: framing discipline, "What this plan is NOT" section, dependencies, open questions, real commands/paths/budgets, no secrets.

**Execution is cleared to begin.** Next session starts at Phase 2B (scaffold review, H+7 → H+8).
