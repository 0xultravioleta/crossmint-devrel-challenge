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

---

## 2026-04-09 H+7: Phase 2B scaffold complete

Scaffold review executed per `03-mcp-build-and-skill-plan.md` Phase 1, tasks 1.1 through 1.10. All 10 tasks landed within the 60-minute budget.

**Delivered:**

- `z:\crossmint-wallets-mcp\` — scaffold committed (root commit, author `0xultravioleta`, no Claude co-author)
  - `package.json` updated to locked deps: `@modelcontextprotocol/sdk@^1.29.0`, `@crossmint/wallets-sdk@^1.0.7`, `@solana/web3.js@^1.95.0`, `@solana/spl-token@^0.4.9`, `zod@^3.25.0`
  - `src/core/{client,types,create-wallet,get-balance,transfer-token,pay-x402-endpoint}.ts` — stubs (all 4 tool functions throw NotImplemented)
  - `src/mcp/{server,tools,errors}.ts` — `McpServer` + `StdioServerTransport` entry point wired, `registerTools` stub, standardized error shape
  - `.env.example` documents both direct and file-ref secret patterns, defaults `DEFAULT_CHAIN=solana-devnet`
  - `demo/smoke-test.ts` — config loader exercise, logs to stderr
  - Gates green: `pnpm install`, `pnpm tsc --noEmit`, `pnpm build` (produces `dist/mcp/server.js`), `pnpm demo` (scaffold mode)
- `z:\crossmint-cpi-skill\` — repo initialized (root commit, author `0xultravioleta`)
  - MIT LICENSE, `package.json`, `README.md` (framing the knowledge gap), `SKILL.md` (frontmatter + section stubs for Phase 2G)
  - `.gitignore`

**Deviations from plan worth flagging for Phase 2C:**

1. **Mainnet instead of devnet** — operator instruction at scaffold time. Local `.env` sets `DEFAULT_CHAIN=solana` and `SOLANA_RPC_URL=https://api.mainnet-beta.solana.com`. Implications for Phase 2C:
   - x402 smoke test will spend real USDC on mainnet, not devnet faucet USDC
   - Crossmint API key scopes must be enabled for mainnet (verify before first `createWallet` call)
   - Recovery secret protects real funds — loss = loss of access to wallet
   - `.env.example` (public) still defaults to `solana-devnet` so external users don't accidentally spend real funds on first try
2. **Secret file-ref pattern** — added `CROSSMINT_API_KEY_FILE` and `CROSSMINT_RECOVERY_SECRET_FILE` alongside the direct env vars. `client.ts` reads direct value first, falls back to file. Keeps secrets entirely outside the project tree (operator stores them at `z:/consultoria-x/.unused/` on this machine). Docker secrets / K8s secrets just work as a side benefit.
3. **`dotenv` + `rimraf` added as devDeps** — not in the plan's dep list but needed for `demo/smoke-test.ts` env loading and cross-platform `clean` script on Windows.
4. **Recovery secret generated locally** — 32 bytes of entropy from `crypto.randomBytes`, hex-encoded (64 chars), written to `z:/consultoria-x/.unused/crossmint-recovery-secret-0xultravioleta.txt` with no terminal echo. Never printed, never logged.

**Phase 2B gate: PASSED at H+7.** Phase 2C critical path (Tool 4 `pay_x402_endpoint`) is cleared to begin per `02-critical-path-plan.md`.

---

## 2026-04-09 H+8: Phase 2C Tool 4 complete — HAPPY PATH on mainnet

Tool 4 `pay_x402_endpoint` shipped ahead of the H+10:30 Plan A gate. First confirmed signature: `KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg` on Solana mainnet.

### Strategy pivot from the original plan

The original `02-critical-path-plan.md` Plan A expected us to hand-roll the SPL TransferChecked transaction client-side with `@solana/web3.js` + `@solana/spl-token`, serialize to base64, and submit via `SolanaWallet.sendTransaction`. During SDK inspection at H+7:30 we discovered `Wallet.send(to, token, amount)` — a high-level method that:

- wraps the SPL transfer in the Crossmint smart wallet's CPI inner instruction internally
- signs via the configured recovery signer automatically
- has Crossmint pay gas via the gasless relayer
- polls for confirmation

This is what Plan D ("degraded transfer fallback") was using, but the plan framed it as degraded because it wouldn't produce a facilitator-compatible canonical `ExactSvmPayloadV1` with base64 signed tx bytes. In practice our local paywall verifies via tx signature lookup on Solana RPC (delta on the merchant ATA's postTokenBalances), so the signature-only payload is sufficient for the demo.

**Result: Plan A via wallet.send() shipped at H+8 instead of H+10:30.** The raw SPL-transfer-with-CPI path is preserved as the "advanced alternative" content in the crossmint-cpi-skill artifact (Artifact 2) — the skill teaches WHY wallet.send is the recommended path and what the underlying CPI nuance is.

### SDK workarounds discovered and documented inline

1. **`createWallet(opts)` rejects free-form owner strings.** Valid formats: the literal `"COMPANY"`, or prefixed user locators like `email:x@y.com`, `userId:abc`, `x:handle`, `phoneNumber:+123`. Our core function conditionally spreads the owner field to omit it entirely when unset.
2. **`getWallet(locator, args)` crashes useSigner in SDK 1.0.7** with `Cannot read properties of undefined (reading 'startsWith')` because the TypeScript `WalletArgsFor` type omits `recovery` but the runtime `createWalletInstance` still reads `args.recovery` if present. Workaround: pass `recovery: { type: "server", secret }` through a cast so the returned Wallet has its internal `#recovery` field populated.
3. **`createWallet` without owner/alias is NOT deterministic.** Each call creates a fresh wallet with a new random seed. Use an explicit `alias` for any wallet you need to address idempotently across runs.

### x402 implementation choices

- Hand-rolled local paywall server in `demo/paywall-server.ts` — does not use `@x402/express` middleware (which assumes a facilitator client). Paywall returns a canonical `PaymentRequired` body and verifies X-PAYMENT by looking up the tx on Solana RPC and checking the merchant ATA's `postTokenBalances` delta. ~150 lines.
- Used constants from `@x402/svm`: `USDC_MAINNET_ADDRESS`, `SOLANA_MAINNET_CAIP2`.
- Used types from `@x402/core/types`: `PaymentRequired`, `PaymentRequirements`, `PaymentPayload`, `Network`.
- Did NOT use `@x402/svm`'s `ExactSvmScheme` — it requires a `@solana/kit` `TransactionSigner` (client-side signing), incompatible with Crossmint's server-side signing model.

### Scope additions beyond plan

- **Phase 2D Task 3.1 and Task 4.1 lifted forward** (createWallet + getBalance implementations) — needed during Phase 2B to produce the funding wallet and verify funding before Phase 2C.
- **Merchant wallet created** via `demo/create-merchant-wallet.ts` — a second Crossmint wallet with `alias="merchant"` to serve as the x402 payTo, so the payment is a real ledger move between two distinct addresses.

### Anomaly flagged for investigation in Phase 3

After two successful 0.01 USDC payments, the payer balance dropped from 2.0 to 1.818509 — a delta of ~0.18 USDC instead of the expected 0.02. Possible causes: Crossmint charges a service fee per gasless transfer, or the original 2 USDC funding transfer from the operator arrived with less than 2.0 due to an upstream fee. Does not block Phase 2C gate — core flow is verified working. Worth investigating during QA so the README can document expected fees.

### Dependencies added to crossmint-wallets-mcp

- Runtime: `@x402/core ^2.9.0`, `@x402/svm ^2.9.0`
- Dev: `@x402/express ^2.9.0` (unused in hand-rolled paywall but kept for future canonical-facilitator path), `express ^5`, `@types/express`
- Earlier in Phase 2B: `dotenv ^16.4.5`, `rimraf ^5.0.5`

**Phase 2C gate: PASSED at H+8.** Tool 4 shipped 2.5 hours ahead of the Plan A deadline. Proceeding to Phase 2D (Tools 1-3 remaining) and Phase 2E (MCP wiring) — we already have createWallet + getBalance complete, just need transferToken and MCP tool registration.

---

## 2026-04-09 H+8.5: Phase 2D + 2E complete — MCP server end-to-end

Delivered in a single push at H+8.5:

- **Phase 2D Task 5.1 (transferToken)** — general-purpose wrapper around `wallet.send()` using the same `getWallet + recovery cast` pattern as `pay-x402-endpoint.ts`. Dropped the plan's email-based signature in favor of `payerAddress`, consistent with the rest of the core layer.
- **Phase 2E (MCP wiring)** — all 4 tools registered via `McpServer.registerTool` (the 1.29 canonical API; `tool(...)` overloads are deprecated). Each tool has: title, verbose description that names the real gotchas (owner locator format, alias determinism, fee expectations), zod inputSchema with per-field descriptions for agent discoverability.
- **Error handling** — handlers catch thrown errors, route them through `classifyError + toolErrorResponse` so clients see structured error codes (`CONFIG_MISSING`, `WALLET_NOT_FOUND`, `INSUFFICIENT_BALANCE`, `X402_CHALLENGE_FAILED`, `NETWORK_ERROR`, `SDK_ERROR`, etc) instead of raw stack traces.
- **`maxUsdcAtomic`** comes in as a zod integer (JSON-safe, max safe 2^53-1) and is converted to `bigint` at the core boundary.

**Verified end-to-end via stdin JSON-RPC pipe against `dist/mcp/server.js`:**
1. `initialize` → protocolVersion `2025-06-18`, 4 tools, `listChanged: true` OK
2. `notifications/initialized` → handled
3. `tools/call crossmint_get_balance { address: "4xHk...", chain: "solana" }` → returned real on-chain balances: 0.015 native SOL + 1.807663 USDC

**Fee anomaly partially solved**: Crossmint charges ~0.001 USDC per `wallet.send` operation as the gasless relayer fee. Documented in the MCP server README.

**Phase 2E gate: PASSED at H+8.5.** `dist/mcp/server.js` exists, responds correctly to `initialize` + `tools/list` + `tools/call`. Ready for Phase 2F (npm publish + Claude Desktop smoke test — user-dependent steps).

---

## 2026-04-09 H+9: Phase 2F + 2G as far as possible

**Phase 2F (publish-ready, Claude Desktop prep):**
- README.md written for crossmint-wallets-mcp (303 lines) with install instructions, config blocks for 4 MCP clients (Claude Desktop, Continue.dev, Cline, Codex CLI), real smoke test output anchored to tx `KRjW2uK7LBioy...icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg`, env var reference, fee notes, companion-skill cross-link.
- `pnpm publish --dry-run` verified: 22.9kB tarball, 43 files, includes dist/ + README + LICENSE, excludes src/ + demo/ + .env + lockfile. Package name `crossmint-wallets-mcp` is unclaimed on npm (404 at registry.npmjs.org).
- What remains for Phase 2F is operator-dependent: `pnpm publish --access public`, install Claude Desktop, add config, restart, invoke tools from Claude's chat input. Cannot be executed without the operator's npm login + Claude Desktop install.

**Phase 2G (skill content):**
- SKILL.md rewritten (242 lines added) with the full CPI teaching content: plain-english corporate-treasury analogy, wrong-way code example with the common errors, right-way `wallet.send()` recipe, x402 payment loop recipe, facilitator verification guidance using `postTokenBalances` delta scan, quick decision tree for agents, common errors table mapping SDK 1.0.7 error messages to root causes.
- Cross-links `crossmint-wallets-mcp` as the reference implementation.
- README.md already covered the framing (kept from scaffold).

**Phase 2F + 2G gate: PASSED at H+9** (modulo the operator-dependent publish + install steps for 2F).

---

## 2026-04-09 H+9: Phase 3 content — HR-coordinated specialist team launched

Per operator direction, Phase 3 content work is being delegated to a specialist team coordinated by an HR agent and reviewed by a soulless PM agent. Structure:

- **HR agent** (running in background since H+9): reads project context, writes a shared brief at `_internal/content-drafts/00-shared-brief.md`, spawns 4 specialists in parallel:
  - marketing-strategist: narrative + title lock + campaign 1-pager
  - blog-writer: 1300-1500 word blog post
  - thread-writer: 8-10 tweet Twitter thread
  - video-script-writer: 60-90s shot list
  - Waits for all 4, returns consolidated hiring + delivery report
- **PM agent** (to be spawned after HR returns): reads all 4 drafts + HR report, cross-checks for consistency against the banned-words list + framing discipline + mandatory inclusions, produces a final consolidated plan WITHOUT rewriting any content.
- Working directory for drafts: `_internal/content-drafts/` (intentionally out of the public repos until integration)
- Progress as of H+9: shared-brief + 01-narrative-lock + 02-blog-post + 03-twitter-thread already exist; 04-video-script + 05-campaign-proposal in flight

This structure mirrors the PM/HR separation the operator specified: HR hires, PM coordinates, specialists execute, consolidated plan returned at the end.

---

## 2026-04-09 H+10: Phase 3 content closeout — PM plan returned + fixes applied

**HR delivered** all 5 content drafts in ~20 min of sequential writing passes (Task tool was NOT available in the subagent environment; HR adapted by running each specialist as a sequential single-purpose pass with the same briefs and constraints). Shared brief + 01-narrative-lock + 02-blog-post + 03-twitter-thread + 04-video-script + 05-campaign-proposal all landed in `_internal/content-drafts/`.

**PM (soulless) cross-reviewed** everything independently and returned a GO WITH FIXES verdict. Critical catches that HR missed:

1. **Twitter thread Tweet 3 paraphrased 3 of 4 tool names.** Shared brief rule was "EXACT names, do not paraphrase". PM caught it via independent grep, HR's QA pass didn't.
2. **Campaign proposal was missing 4+ mandatory inclusions.** HR's QA focused on word count and handle-naming; it did not audit the campaign against the shared-brief "blog post AND campaign proposal must include all of the below" list. The campaign had no tool names, no tx signature, no CPI analogy, and no Faremeter mention. This is the strongest single piece of proof we have and it was absent from the pitch document.
3. **Video end card had no JSON config block reference.** Minor.

PM resolved HR's 6 open questions (keep `0xultravioleta` handle, keep video at 75s, soften Tweet 10 CTA by dropping public lobster.cash tag, accept campaign under 500, keep narrative-lock rationale, keep video self-QA checklist for internal files), flagged HR misses, and recommended 5 mechanical fixes totaling ~30-40 minutes.

**Fixes applied directly** (2026-04-09 H+10):

- `03-twitter-thread.md`: Split former Tweet 3 into Tweet 3a (framing + 4 verbatim tool names) + Tweet 3b (install block). Thread is now 10 tweets instead of 9 — still inside 8-10 target. Verified all Twitter char counts under 280 (accounting for Twitter's 23-char URL collapse). Also softened the CTA in what is now Tweet 10 by dropping the public lobster.cash mention — Fede hasn't reviewed the skill yet, so don't publicly ping his team before the certification path is confirmed privately.
- `05-campaign-proposal.md`: Added a 3-sentence "Proof it works" section between "Why this campaign" and "What needs to happen" containing all 4 verbatim tool names, the tx signature with explorer link, the CPI analogy in one sentence, and the Faremeter reference. Trimmed the "60-day strategic outcomes" by ~20 words to stay under the 500-word ceiling. Final clean word count: 496.
- `04-video-script.md`: Added a "Claude Desktop / Cursor / Cline / Codex config block in README" line to the end card and the matching voiceover change. End card now has 6 lines instead of 5.

**Post-fix grep audit results:**

| File | Words | Banned | Ultravioleta | Tool names | URLs | Tx sig | lobster-cli |
|---|---|---|---|---|---|---|---|
| 01-narrative-lock | 468 | clean | URL-only | n/a | n/a | n/a | n/a |
| 02-blog-post | 1310 | clean | URL-only | 4/4 | 3/3 | present | present |
| 03-twitter-thread | 10 tweets, all ≤280 | clean | URL-only | 4/4 | 2/3 (Solana URL acceptable per PM) | present | present |
| 04-video-script | 871 | negative-assertion false positives only | URL-only | 3/4 (video scope) | n/a | present | n/a |
| 05-campaign-proposal | 496 | clean | URL-only | 4/4 | 3/3 | present | present |

**Phase 3 status: READY FOR SAUL VOICE PASS.** All 5 files technically correct, framing-clean, mandatory-inclusion-compliant, and narratively consistent. The Saul voice pass (plan Phase 6, ~30 min) is the remaining gate before publish.

**Open items for ship:**
- [ ] Saul voice pass on all 5 files (mandatory)
- [ ] Before promoting `04-video-script.md` to any public repo: strip the self-QA compliance checklist at lines 55-57 (contains banned words as negative assertions, trips naive grep)
- [ ] DM Fede privately about the skill cert path BEFORE the Twitter thread goes live (so the soft CTA in Tweet 10 doesn't leave him blindsided)
- [ ] Cross-check the "13 certified skills" count against live `lobster.cash/skills` directory before publishing anything
- [ ] Phase 2F user-dependent steps: `pnpm publish --access public`, install Claude Desktop, add config, invoke tools from Claude's chat
