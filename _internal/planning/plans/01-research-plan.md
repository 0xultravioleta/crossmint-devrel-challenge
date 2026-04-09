# 01 — Research Plan

> **Role:** Research Planner
> **Owner:** Claude (direct execution)
> **Mandate:** Plan Phase 2A research + R4 just-in-time strategy
> **Scope:** Phase 2A (H+5 → H+7) + Appendix A unknowns
> **Author:** Claude (main conversation)
> **Status:** R1, R2, R3 already COMPLETE before this plan was written. This plan documents the verdict and plans R4 as just-in-time during Phase 2C.
> **Line target:** 200-400 (shortest of the 5 plans)

---

## Framing discipline (apply to all research outputs)

- No Ultravioleta branding in the SUBJECT of research. The x402-rs facilitator is infrastructure, not showcase.
- The MCP server is positioned as an MCP-native COMPANION to lobster.cash, never a competitor.
- Faremeter is the default x402 facilitator in all code and docs (per Filippos's own words).
- No hardcoded API keys, recovery secrets, or wallet addresses in any research file.

---

## What this plan is NOT

- NOT a plan to re-do R1, R2, R3 (they are already complete — see `_internal/research/crossmint-sdk-api.md`, `lobster-cli-library.md`, `mcp-sdk-patterns.md`)
- NOT a plan to execute the research (this document is ONLY the plan — the execution is tracked in the research files themselves)
- NOT a plan for the Solana x402 protocol implementation (that belongs to the Critical Path Planner, file `02-critical-path-plan.md`)
- NOT a plan to read Crossmint's blog or docs beyond what's needed to verify API surfaces
- NOT a plan for the content piece research (the Content & Campaign Planner owns that)
- NOT a backlog for future research after ship

---

## Dependencies on other plans

- **Provides to:** 02-critical-path-plan.md (R1 verdict unlocks Plan A), 03-mcp-build-and-skill-plan.md (R1 wallet SDK surface, R3 MCP SDK patterns, R2 lobster-cli library surface)
- **Depends on:** None — this is the upstream root of the planning chain

---

## Phase 1 — R1: `@crossmint/wallets-sdk` API surface (DONE)

### Status

COMPLETE. Findings at `_internal/research/crossmint-sdk-api.md`. Verdict: Plan A confirmed feasible.

### Task 1.1 — Document the R1 verdict for orchestrator

- **Status:** DONE
- **File:** `_internal/research/crossmint-sdk-api.md`
- **Summary:** `SolanaWallet.sendTransaction({ serializedTransaction: base64 })` is the primitive that enables `pay_x402_endpoint` on Solana. `EVMWallet.signMessage({ message })` + `EVMWallet.getViemClient()` provide the EVM path. SDK version `@crossmint/wallets-sdk@1.0.7`, Apache-2.0, 12,047 weekly downloads.
- **Time budget:** 0 minutes (already done)
- **Fallback:** N/A

### Task 1.2 — Formal Plan A/B/C/D verdict entry in decisions.md

- **Action:** Write single-line verdict to `_internal/decisions.md`: "Tool 4 uses Plan A (direct SDK signing via `SolanaWallet.sendTransaction` on Solana, `EVMWallet.getViemClient().signTypedData()` on EVM)."
- **File to update:** `z:\crossmint-devrel-challenge\_internal\decisions.md`
- **Time budget:** 5 minutes
- **Fallback:** Already captured in `crossmint-sdk-api.md` under "Critical decisions driven by this research" — decisions.md is a convenience consolidation, not load-bearing

### Task 1.3 — Supplementary chain list fetch (low priority)

- **Action:** Playwright navigate to `https://docs.crossmint.com/introduction/supported-chains` to confirm full Solana + Base + EVM chain list
- **Command:** `mcp__plugin_playwright_playwright__browser_navigate` with URL above
- **File:** append to `_internal/research/crossmint-sdk-api.md` under "Supported chains"
- **Time budget:** 10 minutes
- **Fallback:** Use the chains already referenced in the Node.js quickstart (`base-sepolia`, `solana`, `solana-devnet`) — sufficient for v0.1

### Phase 1 gate

- **Criteria:** R1 findings file exists AND the decisions.md verdict is written
- **Status:** PASSED at H+5.5 (already)

---

## Phase 2 — R2: `@crossmint/lobster-cli` library exports (DONE)

### Status

COMPLETE. Findings at `_internal/research/lobster-cli-library.md`. Verdict: Path B available but not needed for MCP server. Skill will reference `lobstercash crypto x402 fetch` as-is.

### Task 2.1 — Document R2 verdict

- **Status:** DONE
- **File:** `_internal/research/lobster-cli-library.md`
- **Key findings:**
  - Package `@crossmint/lobster-cli@3.0.8` published 2026-04-09 (1h before research)
  - Library exports: `parseConfig`, `getOrCreateWallet`, `ensureAuthenticated`, `withAuthenticatedApi`, `getWalletBalance`, `createTransaction`, `approveTransaction`, `runSetupFlow`
  - Repo: `github.com/Crossmint/lobster.cash` (not `lobstercash-cli-skills`)
  - Storage at `~/.lobster/agents.json` — would conflict with MCP server state if wrapped
  - MIT licensed
- **Time budget:** 0 minutes (already done)

### Task 2.2 — Record the "do NOT wrap lobster-cli" architectural decision

- **Action:** Already captured in `lobster-cli-library.md` under "Architecture decision recorded"
- **Decision:** MCP server uses `@crossmint/wallets-sdk` directly, not `@crossmint/lobster-cli`. Reasons: state isolation, clean transport (MCP stdio vs CLI stdout human-readable), no coupling.
- **Time budget:** 0 minutes
- **Fallback:** N/A

### Phase 2 gate

- **Criteria:** R2 findings file exists AND the architectural decision is recorded
- **Status:** PASSED at H+5.75 (already)

---

## Phase 3 — R3: `@modelcontextprotocol/sdk` minimal server pattern (DONE)

### Status

COMPLETE. Findings at `_internal/research/mcp-sdk-patterns.md`. Verdict: Pin `@modelcontextprotocol/sdk@^1.29.0` + `zod@^3.25.0`. Use `McpServer` + `StdioServerTransport`.

### Task 3.1 — Document R3 verdict

- **Status:** DONE
- **File:** `_internal/research/mcp-sdk-patterns.md`
- **Key findings:**
  - Package `@modelcontextprotocol/sdk@1.29.0` published 2026-03-30 (10 days ago)
  - MIT licensed, 40,815 dependents (very mature)
  - Primary imports: `McpServer` class + `StdioServerTransport` from `/server/stdio`
  - Required peer: `zod` (both v3 and v4 supported, internally uses v4)
  - Log to stderr only — stdout is reserved for JSON-RPC transport
  - Pattern: `server.tool(name, description, schema, handler)` returns `{ content: [{ type: "text", text: ... }] }`
- **Time budget:** 0 minutes

### Task 3.2 — Verify `McpServer` API signature against a known-good example during Phase 2B scaffold

- **Action deferred to Phase 2B:** Read Saul's existing AutoJob MCP server source at `z:\ultravioleta\dao\autojob\` and extract the exact `McpServer` constructor + `server.tool()` invocation pattern
- **File to create during Phase 2B:** `z:\crossmint-wallets-mcp\src\mcp\server.ts` (scaffold with verified pattern)
- **Time budget:** 15 minutes during Phase 2B (not Phase 2A)
- **Fallback:** If AutoJob uses a different MCP SDK version, fall back to the official examples at `github.com/modelcontextprotocol/typescript-sdk/tree/main/src/examples/server/`

### Phase 3 gate

- **Criteria:** R3 findings file exists AND the Phase 2B scaffold task is registered
- **Status:** PASSED at H+6 (already)

---

## Phase 4 — R4: x402 payload format on Solana (DEFERRED)

### Status

DEFERRED to Phase 2C (critical path build). Not a Phase 2A blocker because `SolanaWallet.sendTransaction` handles the signing side of x402; we only need to know the `X-PAYMENT` header shape that the target endpoint expects, which depends on the chosen endpoint.

### Task 4.1 — Pick the target x402 endpoint during Phase 2C

- **Action:** Critical Path Planner (`02-critical-path-plan.md`) identifies 2-3 candidate endpoints. During Phase 2C, the build Claude picks one and inspects its 402 response to learn the expected payload format.
- **Candidates (from research so far):**
  1. `crossmint-agentic-finance/ping-faremeter-crossmint-solana` subfolder's test endpoint (requires running their demo server, or use whatever public endpoint it exposes — verify at `github.com/Crossmint/crossmint-agentic-finance/tree/main/ping-faremeter-crossmint-solana`)
  2. Faremeter's public test endpoint at `facilitator.corbits.dev` (if they host one)
  3. Saul's own x402-rs test endpoints at `facilitator.ultravioletadao.xyz` — DO NOT USE in the public submission (framing discipline), but OK as a backup during development
  4. `github.com/Crossmint/crossmint-agentic-finance/tree/main/weather` — simpler Express paywall that we can run locally if needed
- **File to update:** `_internal/research/x402-payload-format.md` (create during Phase 2C)
- **Time budget:** 15-20 minutes during Phase 2C Task 2.1 (endpoint discovery)
- **Fallback:** If no public Solana x402 endpoint is reachable, clone `crossmint-agentic-finance` and run the `weather` subfolder locally as the target paywall

### Task 4.2 — Inspect the 402 response body structure

- **Action:** `curl -i <endpoint>` (or Playwright fetch) and capture the 402 body. Document the fields: `accepts`, `scheme`, `network`, `payTo`, `asset`, `maxAmountRequired`, `maxTimeoutSeconds`, `resource`, `description`, `mimeType`, `outputSchema`, `extra`
- **Reference:** x402 spec at `github.com/coinbase/x402` → `crates/x402/src/types.rs` or `packages/types/src/index.ts` depending on repo structure
- **Alternative:** Saul's own `x402-rs/src/types.rs` already implements the full x402 v1 spec — read that as the authoritative local reference
- **File:** `_internal/research/x402-payload-format.md` with: sample 402 response, expected `X-PAYMENT` request header structure, signing requirements
- **Time budget:** 20-30 minutes
- **Fallback:** If the spec is unclear, ask Saul (he wrote the x402-rs facilitator) to paste the expected payload shape directly

### Task 4.3 — Construct the `X-PAYMENT` header for the retry request

- **Action:** After payment is sent via `SolanaWallet.sendTransaction`, build the `X-PAYMENT` header value per the spec. On Solana, this is typically a base64-encoded JSON object with `scheme`, `network`, and `payload` fields where `payload` contains the signed transaction or a reference to the executed transaction signature
- **Implementation:** Lives in `z:\crossmint-wallets-mcp\src\core\pay-x402-endpoint.ts`
- **Time budget:** 30 minutes (part of Phase 2C Plan A build, not Phase 2A research)
- **Fallback:** Saul's x402-rs `src/chain/solana.rs` has working client-side payload construction — port the pattern from Rust to TypeScript if needed

### Phase 4 gate

- **Criteria:** During Phase 2C Task 2.3, the first successful `pay_x402_endpoint` call returns a valid HTTP 200 response from the target endpoint
- **Target time:** H+10 (2 hours into Phase 2C build)
- **Gate failure action:** Pivot to Plan B (wrap lobster-cli) per Critical Path Planner's Phase 3

---

## Phase 5 — Decision synthesis + handoff to Critical Path Planner

### Task 5.1 — Write formal Plan A/B/C/D verdict

- **Action:** Add an entry to `_internal/decisions.md` with the verdict
- **Template:**
  ```
  ## 2026-04-09 H+6: Tool 4 plan selection (Research verdict)
  Verdict: Plan A (direct SDK signing)
  Reason: R1 confirmed SolanaWallet.sendTransaction({ serializedTransaction }) and EVMWallet.getViemClient() both expose the primitives needed.
  Plans B, C, D retained as mitigations in 02-critical-path-plan.md.
  ```
- **Time budget:** 5 minutes
- **File:** `z:\crossmint-devrel-challenge\_internal\decisions.md`

### Task 5.2 — Hand off to Critical Path Planner

- **Action:** Critical Path Planner reads this plan + `crossmint-sdk-api.md` + `lobster-cli-library.md` + `mcp-sdk-patterns.md` and produces `02-critical-path-plan.md`
- **Handoff contract:**
  - Research has confirmed Plan A is feasible
  - Plans B, C, D are NOT expected to fire but MUST be documented as mitigations
  - R4 (x402 payload format) is a Phase 2C task, not a blocker for planning — Critical Path Planner plans R4 as the first 20 minutes of Phase 2C
- **Time budget:** 0 minutes (handoff is automatic)

---

## Inconclusive verdict handling (PM requirement)

If any research task returns "inconclusive" (e.g., an SDK method exists but its signature is ambiguous):

1. **Do not guess.** Record the ambiguity in the research file with a flag: `**UNVERIFIED:** <what's unclear>`
2. **Raise a PM blocker.** Write a line to `_internal/planning/pm-journal.md` under "Open questions": `<research task ID>: <question>`
3. **Continue with a named assumption.** Pick the most likely interpretation, document it as an assumption, and proceed
4. **Schedule a just-in-time recheck.** Add a task to Phase 2B scaffolding: "verify <assumption> by reading <exact file path or running <exact command>>"
5. **Pivot trigger:** If the just-in-time recheck invalidates the assumption, and the invalidation affects Tool 4 (critical path), immediately notify Critical Path Planner and trigger the next fallback plan (A→B→C→D)

**None of R1-R3 are inconclusive.** This handling exists for R4 and any surprises that emerge during Phase 2C.

---

## Gate verification criteria for H+7 (Phase 2A end)

- [x] R1 verdict documented (Plan A confirmed)
- [x] R2 verdict documented (Path B available, architectural decision: don't wrap lobster-cli)
- [x] R3 verdict documented (MCP SDK version + pattern locked)
- [ ] R4 deferred with clear just-in-time plan (this document)
- [ ] Decisions.md updated with formal Plan A verdict (Task 5.1, 5 minutes)

**Phase 2A gate: PASS once Task 5.1 is executed.** Everything else is already done.

---

## Open questions for PM / Research

1. **None blocking.** R1-R3 are resolved and R4 is deferred with a clear just-in-time plan.
2. **Minor:** The exact list of Crossmint-supported chains beyond `base-sepolia` and `solana` is not documented in the SDK README. Phase 1 Task 1.3 covers this as a 10-min Playwright fetch. Not a blocker.
3. **Minor:** Whether `@crossmint/wallets-sdk` v1.0.7 and `@modelcontextprotocol/sdk` v1.29.0 have dependency conflicts (both depend on zod versions) — will be verified during Phase 2B `pnpm install`. Not a research-phase blocker.

---

## Time budget summary

| Task | Status | Time |
|---|---|---|
| 1.1 Document R1 verdict | DONE | 0 min |
| 1.2 Formal Plan A verdict to decisions.md | TODO | 5 min |
| 1.3 Supplementary chain list fetch | OPTIONAL | 10 min |
| 2.1 Document R2 verdict | DONE | 0 min |
| 2.2 "Do not wrap lobster-cli" architectural decision | DONE | 0 min |
| 3.1 Document R3 verdict | DONE | 0 min |
| 3.2 Verify McpServer API in Phase 2B | DEFERRED TO 2B | 15 min (2B) |
| 4.1-4.3 R4 x402 payload (just-in-time) | DEFERRED TO 2C | 60-80 min (2C) |
| 5.1 Decision synthesis entry | TODO | 5 min |
| 5.2 Handoff to Critical Path Planner | AUTOMATIC | 0 min |

**Research phase active work remaining:** 10 minutes (Task 1.2 + Task 1.3 optional).

**Total Phase 2A elapsed time:** ~60 minutes (H+5 → H+6 already spent, plus 10 minutes to close out). Under the 2h budget by a large margin.

---

## Research planner sign-off

All R1, R2, R3 findings are in-hand. Plan A confirmed for Tool 4. R4 deferred cleanly. Critical Path Planner is unblocked. Handoff complete.
