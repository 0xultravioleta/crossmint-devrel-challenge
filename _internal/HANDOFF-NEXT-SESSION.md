# HANDOFF — Next Session Pickup Point

> **Context:** This conversation is ~90% full and needs to close out. The next Claude Code session will pick up execution from here.
> **Current time:** 2026-04-09 ~H+6.5 (~12:45 PM EDT)
> **Ship target:** 2026-04-10 ~18:30 EDT (H+36)
> **Hard cutoff:** 2026-04-10 ~22:30 EDT (H+40)
> **Absolute 48h spec deadline:** 2026-04-11 06:17 EDT

---

## TL;DR — Where we are

Phase 0 (coordination) + Phase 1 (concept lock) + Phase 2A (research) + Planning Phase (5 specialist plans) are **COMPLETE**.

**Next task:** Phase 2B (scaffold review) at H+7 → H+8, followed by Phase 2C (critical path Tool 4 build) at H+8 → H+12.

**The concept is locked. The plans are written. The research is done. Execution is the next move.**

---

## What the next session needs to read FIRST

Read these in order:

1. **`_internal/phase-notes/concept-lock-v2.md`** — what we're building
2. **`_internal/phase-notes/BUILD-PLAN.md`** — the master phased plan
3. **`_internal/planning/plans/02-critical-path-plan.md`** — Tool 4 build plan (most important for Phase 2C)
4. **`_internal/planning/plans/03-mcp-build-and-skill-plan.md`** — the main build track (Phase 2B through 2G)
5. **`_internal/research/crossmint-sdk-api.md`** — the SDK surface map (R1 verdict: Plan A confirmed)
6. **`_internal/decisions.md`** — running decision log
7. **`HANDOFF-NEXT-SESSION.md`** — this file (the summary)

**Don't read the brainstorm files unless the concept comes into question** — they're archived reasoning, not active plans.

---

## The concept in one paragraph

Build TWO artifacts that fill TWO gaps in Crossmint's agentic commerce stack:

1. **`crossmint-wallets-mcp`** — an MCP server exposing 4 Crossmint smart wallet tools (`create_wallet`, `get_balance`, `transfer_token`, `pay_x402_endpoint`) to any MCP-native client (Claude Desktop, Continue.dev, Cline, Codex CLI). Complements lobster.cash, which ships to Claude Code/Cursor/OpenClaw via the skill architecture but doesn't reach MCP-native clients.

2. **`crossmint-cpi-skill`** — a lobster.cash-compatible skill that teaches AI agents the Solana CPI inner instruction nuance when paying x402 endpoints from Crossmint smart wallets. None of the 13 certified lobster.cash skills cover this technical insight.

Both MIT licensed. Both ready for Crossmint to fork into the official org. Ship includes blog post + Twitter thread + video script + 1-page campaign proposal.

---

## Critical facts locked at research time

- **`@crossmint/wallets-sdk@1.0.7`** (published 2026-04-06, 3 days before research)
  - `SolanaWallet.sendTransaction({ serializedTransaction: base64 })` is the primitive for Tool 4 on Solana — confirmed
  - `EVMWallet.getViemClient()` escape hatch for EIP-712 typed data signing on EVM — confirmed
- **`@crossmint/lobster-cli@3.0.8`** (published 2026-04-09, 1 hour before research — fresh)
  - Has library exports but we will NOT wrap it (state isolation + MCP transport incompatibility)
- **`@modelcontextprotocol/sdk@1.29.0`** (published 2026-03-30, 10 days before research)
  - Use `McpServer` + `StdioServerTransport`
  - Peer dep: `zod@^3.25.0`
  - **Log to stderr only** — stdout is reserved for JSON-RPC transport
- **Saul's AutoJob MCP server** at `z:\ultravioleta\dao\autojob\` is the known-good stdio transport reference — read during Phase 2B before writing `src/mcp/server.ts`

---

## Repos and their state

### `z:\crossmint-devrel-challenge\` (coordination workspace, private)

- Git initialized, 17 commits
- All planning, research, brainstorm, decision log
- Will become the submission entry point after Phase 5 README rewrite

### `z:\crossmint-wallets-mcp\` (Artifact 1, will be public at H+34)

**Current state:** Partial scaffold exists from earlier in the conversation BEFORE the concept pivot. Review and update during Phase 2B:

- `package.json` — EXISTS, needs dep list refresh per concept-lock-v2.md
- `tsconfig.json` — EXISTS, fine
- `.gitignore` — EXISTS, fine
- `LICENSE` — EXISTS, MIT, fine
- `src/` — EMPTY, needs scaffold in Phase 2B
- `demo/` — EMPTY, needs smoke test skeleton
- `.env.example` — MISSING, create in Phase 2B
- `README.md` — MISSING, create in Phase 2B
- Git initialized but no commits yet

### `z:\crossmint-cpi-skill\` (Artifact 2, will be public at H+34)

- Does not exist yet
- Create in Phase 2B Task 1.10 per `03-mcp-build-and-skill-plan.md`

---

## Immediate next actions (Phase 2B scaffold — H+7 → H+8, 60 min)

**Follow `_internal/planning/plans/03-mcp-build-and-skill-plan.md` Phase 1 (tasks 1.1 through 1.10) exactly.**

Summary of what to do:

1. **Verify existing scaffold** — `ls -la z:/crossmint-wallets-mcp/ && cat z:/crossmint-wallets-mcp/package.json` (5 min)
2. **Update `package.json`** to include:
   - `@modelcontextprotocol/sdk@^1.29.0`
   - `@crossmint/wallets-sdk@^1.0.7`
   - `@solana/web3.js@^1.95.0`
   - `@solana/spl-token@^0.4.9`
   - `zod@^3.25.0`
   - dev deps: `@types/node`, `tsx`, `typescript`
   - `"bin": { "crossmint-wallets-mcp": "dist/mcp/server.js" }`
   - `"type": "module"` (ESM)
3. **`pnpm install`** (5 min)
4. **Scaffold `src/core/`** — empty function stubs for `createWallet`, `getBalance`, `transferToken`, `payX402Endpoint`, plus `client.ts` and `types.ts` (15 min)
5. **Scaffold `src/mcp/`** — minimal `server.ts` + `tools.ts` + `errors.ts` stubs (10 min)
6. **Create `.env.example`** with the 4 required env vars (5 min)
7. **Create `demo/smoke-test.ts` skeleton** (5 min)
8. **`pnpm tsc --noEmit`** must pass (5 min)
9. **Initialize `z:\crossmint-cpi-skill\`** with README + SKILL.md + package.json + LICENSE skeletons (10 min)
10. **Commit Phase 2B completion** in both repos

**Phase 2B gate:** `pnpm tsc --noEmit` passes on `crossmint-wallets-mcp`, skill repo initialized. Proceed to Phase 2C.

---

## Phase 2C — Critical path (H+8 → H+12)

**Follow `_internal/planning/plans/02-critical-path-plan.md` exactly.**

Key hour-resolution pivot triggers:

- **H+9:30** — if `src/core/pay-x402-endpoint.ts` transaction construction isn't complete, pivot decision
- **H+10:30** — if smoke test isn't green on Plan A, pivot to Plan B (wrap `@crossmint/lobster-cli`)
- **H+11:30** — if Plan B isn't green, pivot to Plan C (Crossmint REST API direct)
- **H+11:45** — if Plan C isn't green, pivot to Plan D (degraded transfer fallback)
- **H+12** — hard deadline: SOMETHING ships as Tool 4

**Plan A is expected to succeed** per R1 verdict. The other plans are mitigations, not expectations.

**Build Tool 4 FIRST, before Tools 1-3**, to de-risk the critical path. Tools 1-3 are simpler and can slot in during Phase 2D (H+12 → H+16).

---

## What NOT to do in the next session

- **Do NOT spawn more subagents** unless API 529 has clearly recovered. Default: direct Claude execution.
- **Do NOT re-open the concept decision.** It is LOCKED at concept-lock-v2.md. Pivot only if Tool 4 fails all 4 plans.
- **Do NOT read the brainstorm files** unless something is fundamentally broken. The brainstorm is archived reasoning.
- **Do NOT add scope.** The 4-tool MCP server + the lobster.cash skill is the FULL scope. Do not add tools, do not add chains beyond Solana (+ stretch Base), do not add UI components.
- **Do NOT name Ultravioleta, Execution Market, or MeshRelay** in any public file, code comment, README, blog post, or campaign proposal.
- **Do NOT hardcode secrets or private keys** in any committed file.
- **Do NOT write the blog post before the demo runs.** Per documentation-driven development, README first, but the blog post waits for screenshots of the real demo.

---

## Cut hierarchy if behind schedule

1. Drop video recording (script exists, recording optional)
2. Cut Twitter thread from 10 to 6 tweets
3. Cut blog post from 1500 to 1000 words
4. Drop Base EVM support (Solana only)
5. Drop `crossmint_transfer_token` (Tool 3) — ship 3 tools instead of 4
6. Drop `crossmint-cpi-skill` (Artifact 2) entirely — ship MCP server only
7. **LAST RESORT:** Drop Tool 4 — ship "Crossmint wallets MCP server without x402" with v0.2 roadmap

---

## Active TODO stack (next 28 hours)

| Hour | Activity | Owner |
|---|---|---|
| H+7 → H+8 | Phase 2B scaffold review | Next session |
| H+8 → H+12 | Phase 2C critical path Tool 4 | Next session |
| H+12 → H+15 | Phase 2D Tools 1-3 | Next session |
| H+15 → H+17 | Phase 2E MCP server wiring | Next session |
| H+17 → H+19 | Phase 2F Claude Desktop smoke test | Next session |
| H+19 → H+22 | Phase 2G lobster.cash skill | Next session |
| H+22 → H+27 | Phase 3 content (blog + thread + video script) | Next session + Saul voice pass |
| H+27 → H+29 | Phase 4 campaign 1-pager | Next session + Saul voice pass |
| H+29 → H+33 | Phase 5 integration + reproduction test | Next session |
| H+33 → H+34 | Phase 5 Saul voice pass + DA final review | Next session + Saul |
| H+34 → H+35 | Phase 6 public GitHub push + npm publish | Next session + Saul |
| H+35 → H+36 | Phase 6 email draft + send | Next session + Saul (sends) |

---

## Required from Saul for the next session

1. **Crossmint server API key** with scopes: `wallets.create`, `wallets.read`, `wallets:transactions.create`, `wallets:transactions.sign`, `wallets:balance.read`
2. **A "recovery secret" string** (any random 32+ character string, kept in `.env` only, never committed)
3. **Devnet USDC** — 2 USDC on Solana Devnet for the test wallet (get via Circle faucet during Phase 2C Task 1.4)
4. **GitHub `0xultravioleta` write access** — for creating public repos
5. **npm publish permissions** — for `crossmint-wallets-mcp@0.1.0` publish during Phase 7
6. **Claude Desktop installed** — for the Phase 2F smoke test

---

## Files the next session must NOT touch without care

- `_internal/phase-notes/concept-lock-v2.md` — LOCKED
- `_internal/phase-notes/BUILD-PLAN.md` — reference, do not modify
- `_internal/planning/plans/*.md` — reference, do not modify (they are the execution blueprints)
- `_internal/decisions.md` — append-only log, add new entries at the bottom but do not rewrite history

---

## The one sentence to remember

> *"Crossmint ships the wallets. lobster.cash ships the CLI. I shipped the MCP bridge — and the skill the CLI is missing."*

This is the locked narrative. Everything else serves it.

---

## Handoff complete

- 5 plans written, 2,805 lines of planning documentation
- 4 research files confirm the critical path
- 17 commits in the coordination workspace
- Partial scaffold in `z:\crossmint-wallets-mcp\` ready for Phase 2B review
- Clean decision log
- Ship target: H+36 (2026-04-10 18:30 EDT)

**Next session starts with `pnpm install` on `z:\crossmint-wallets-mcp\` after reviewing `03-mcp-build-and-skill-plan.md` Phase 1. Total budget to ship: ~29 hours of wall clock time.**

Good luck, next session. Ship clean.
