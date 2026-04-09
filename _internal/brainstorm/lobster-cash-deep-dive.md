# lobster.cash Deep Dive — What It Is, What It Isn't

> **Author:** Claude (main conversation, via WebFetch)
> **Date:** 2026-04-09 H+5
> **Purpose:** Resolve the concern from Fede's Telegram: "estamos armando eso nosotros, que no es lo que dices pero un approach a algo asi"

---

## Executive Summary

**lobster.cash is Crossmint's shipped payment layer for AI agents.** It is a real product with a CLI, an npm package, 13 certified skills, and an official install path for Claude Code, Cursor, and OpenClaw.

**Key finding:** lobster.cash is **CLI-based with a skills architecture**. It does **NOT** have an MCP server. It does not use the Model Context Protocol at all. Confirmed by explicit absence in both the Skill Compatibility Guide and the install documentation.

**Implication for our concept:** An MCP server for Crossmint wallets is **NOT duplicative of lobster.cash**, but it should be positioned explicitly as the MCP-native companion, not as a competitor. Fede's exact words — *"no es lo que dices pero un approach a algo asi"* — confirm the distinction.

---

## What lobster.cash IS

### Product description (from landing page)

> *"The Payment Solution for Agents. Agents can pay for anything — domains and subscriptions, x402 endpoints, physical products online. Humans approve spending with limits. Agents complete purchases without touching credit card details or private keys."*

### Architecture

- **Payment engine:** Crossmint-built, closed-source backend handling wallet operations, virtual card issuance, x402 signing, and Visa Intelligent Commerce
- **Distribution layer 1:** `@crossmint/lobster-cli` — npm package with commands like `crypto x402 fetch <url>`, agent management (`register`, `list`, `set-active`), USDC transfers, and balance checks
- **Distribution layer 2:** Skills directory at `github.com/Crossmint/lobstercash-cli-skills` — a `skills/` folder with decision-tree markdown files (`SKILL.md`) that tell agents WHEN to call the CLI and WHICH commands to use
- **Install mechanism:** `npx skills add https://github.com/Crossmint/lobstercash-cli-skills --global --yes` (the Claude Code / Cursor / OpenClaw skill install pattern)
- **Storage:** `~/.lobster/agents.json` for local agent registration

### Explicit supported clients (from install page)

- OpenClaw agents (via clawhub.ai/crossmint/lobstercash)
- Claude Code (via `npx skills add ... --global`)
- Cursor (same install pattern)
- "Other agents" — generic, implying any agent that can shell out to CLI

### The skill compatibility philosophy

From the Skill Compatibility Guide:

> *"Skills/plugins should describe business intent and payment requirements. lobster.cash should implement payment execution."*

This is a CLEAR separation: **skills = business logic / intent; lobster.cash = execution engine.** Skills are thin adapters; the wallet, card, and x402 operations all happen inside lobster.cash infrastructure.

**Required skill behaviors:**
1. Wallet precheck (does a wallet exist?)
2. Payment action description (merchant, amount, reason)
3. Payment status + error handling (wallet not configured, insufficient balance, failure, pending confirmation)

**Forbidden skill behaviors:**
- Generating wallets or touching private keys
- Hardcoding lobster.cash internal action names
- Skipping the payment status confirmation

### The 13 certified skills (as of April 2026)

| Skill | Function | Saul-adjacent? |
|---|---|---|
| BlockRun | AI model access / pay-per-request | — |
| ClawCredit | Credit for agentic economy (x402-powered) | — |
| Clawpay | Payment requests / shareable URLs | — |
| ClawPump | Token launches on Solana | — |
| Jupiter | DeFi swaps / lending | — |
| Lando | Subscription URLs via Tributary | — |
| OOBE | Multi-agent coordination on Solana (x402-powered) | — |
| OpenTask | Agent marketplace | **Adjacent to Execution Market** |
| Purch | E-commerce product discovery | — |
| Seedstr | Task marketplace | **Adjacent to Execution Market** |
| Unbrowse | Browser automation (x402-gated routes) | — |
| LI.FI | Cross-chain routing | — |
| ClawRouter (mentioned elsewhere) | LLM router | — |

**Saturation warning:** OpenTask and Seedstr both occupy the "agent marketplace / task marketplace" niche that Execution Market also occupies. Building another one would duplicate.

**Gap observations:**
- NO skill about **ERC-8004 agent identity / reputation** (Jan 29 2026 mainnet)
- NO skill about **facilitator selection / multi-facilitator routing** for x402
- NO skill about **delegated signer + spending limit workflow** (the Visa bottleneck answer)
- NO **multi-chain x402 skill** that pays across Solana + Base + Polygon from a single lobster.cash wallet
- NO **CPI scanning / smart wallet on Solana** explainer skill (Saul's deepest wheelhouse)
- NO MCP server bridge for MCP-native clients (Claude Desktop, Continue.dev, Cline, Codex CLI)

### The DevRel contact

The Skill Compatibility Guide explicitly names **Fede (DevRel) on Telegram** as the official integration review contact. **This is the same Fede (@cryptofede) who is Saul's internal champion at Crossmint**. Building a lobster.cash-certified skill is literally asking Fede to review his own friend's work.

---

## What lobster.cash IS NOT

Confirmed absences (verified by fetching the relevant pages):

1. **NOT an MCP server.** Zero mentions of MCP, Model Context Protocol, `@modelcontextprotocol/sdk`, or JSON-RPC transport in any lobster.cash page or the CLI README.
2. **NOT available in Claude Desktop (the consumer chat app).** Claude Desktop only speaks MCP — there's no skill architecture in Claude Desktop. lobster.cash requires shell access to run its CLI. Claude Desktop does not shell out.
3. **NOT available in Continue.dev, Cline, or Codex CLI.** These MCP-native clients have no equivalent of the `skills/` directory pattern that lobster.cash uses.
4. **NOT a multi-framework SDK.** Unlike GOAT SDK (which wraps Crossmint for LangChain and Eliza), lobster.cash is a client-level product, not an embeddable library for agent frameworks.
5. **NOT documented in docs.crossmint.com.** The docs reference is `docs.lobster.cash` which 404s, and the internal `docs/skill-compatibility-guide` is hosted on `www.lobster.cash/docs/...` separately from the main Crossmint docs.
6. **NOT in the Crossmint blog's x402 tutorials.** The "What is x402" post on blog.crossmint.com does not reference lobster.cash.

---

## The White Space Map (Revised)

Given everything we now know, here are the 3 sharpest concept angles that do NOT duplicate lobster.cash:

### Angle A — `crossmint-wallets-mcp` (MCP-native companion to lobster.cash)

**Thesis:** lobster.cash serves OpenClaw, Claude Code, and Cursor via the skill architecture. But Claude Desktop, Continue.dev, Cline, and Codex CLI speak MCP natively — they cannot install a lobster.cash skill. The gap is the MCP-native companion.

**Demo:**
- MCP server exposes 6 tools: `create_wallet`, `get_balance`, `transfer_token`, `pay_x402_endpoint`, `add_delegated_signer`, `list_transactions`
- Under the hood: wraps `@crossmint/lobster-cli` library exports (the CLI has published library exports for programmatic use) OR calls Crossmint SDK directly
- Installable in Claude Desktop / Continue.dev / Cline with one config block
- Demo video: Claude Desktop paying an x402 endpoint from a Crossmint wallet with no shell, no CLI

**Why not duplicative:**
- Different transport (MCP vs CLI)
- Different audience (MCP-native clients not served today)
- Can literally wrap the lobster.cash CLI, making it a LAYERED extension, not a replacement
- Fede confirmed: "no es lo que dices" — not the same thing

**Risk:** The Claude Desktop + Continue.dev + Cline audience might be smaller than the Claude Code / Cursor / OpenClaw audience lobster.cash already serves. The submission has to pitch this as a strategic extension, not a "me too" clone.

**Build time:** ~22h (wrapping the CLI is faster than raw SDK integration)

---

### Angle B — A new certified lobster.cash skill that fills a real gap

**Thesis:** Build a skill that directly contributes to lobster.cash's own ecosystem. Fede (the DevRel contact for certification) is Saul's internal champion — this is literally asking Fede to certify a skill from his friend. The skill becomes an asset for Crossmint's existing platform, not a competitor.

**Best skill candidates (net new in the 13-skill ecosystem):**

1. **`lobster-agentcred`** — An ERC-8004 reputation-gated payment skill. Agents build on-chain reputation from their lobster.cash payment history. Services can accept x402 payments ONLY from agents with reputation > N. Uses Saul's ERC-8004 backend experience (x402-rs `add-erc8004-network` skill).
2. **`lobster-chainpick`** — A multi-chain x402 router skill. When an agent needs to pay an x402 endpoint that accepts multiple chains, it picks the cheapest + fastest chain based on gas and USDC availability in the agent's lobster.cash wallets. Uses Saul's 19-mainnet facilitator as backend reference.
3. **`lobster-cpicheck`** — A developer-facing skill that takes any Solana smart wallet x402 payment and verifies it was scanned correctly via CPI inner instructions. Educational skill, teaches the Solana smart wallet nuance. Saul's deepest wheelhouse.

**Why not duplicative:**
- Net new skills that lobster.cash does not have
- Fede literally runs the certification process — he can endorse on arrival
- Certified skills get listed at lobster.cash/skills — permanent distribution
- Uses Saul's unique assets as backend, not as subject

**Risk:** The skill format is proprietary. The Skill Compatibility Guide is behavioral, not technical. Saul might hit a blocker if the skill SDK has undocumented constraints.

**Build time:** ~16-20h depending on which skill

---

### Angle C — Pivot to a totally different concept (Delegated Signer + Visa Bottleneck)

**Thesis:** From the Filippos debrief, the Visa Intelligent Commerce integration is stuck because issuing banks (Chase) are rejecting agent virtual cards. This is a REAL strategic problem Filippos shared in confidence. The fix is a delegated signer + spending limits flow that works WITHOUT Visa. No lobster.cash overlap because lobster.cash uses Visa and stablecoins both.

**Demo:**
- A Next.js consent UI where a user grants an AI agent a delegated signer with a $50 USDC spending cap for 24h on x402 endpoints in a whitelisted domain
- Agent pays within the grant, user sees each payment in a live activity feed
- User revokes mid-stream, agent gets rejected on next payment
- Pure stablecoin path — no Visa, no issuing bank dependency

**Why not duplicative:**
- Totally different product line (consent UI + delegation, not payment execution)
- Directly addresses Filippos's stated blocker (Visa bottleneck)
- Fills the 404 docs page on delegated signers

**Risk:** The Crossmint SDK might not expose spending limit enforcement at the wallet level — would need a server-side policy shim, which weakens the claim.

**Build time:** ~20-22h (UI work adds time)

---

## The Recommendation

**Ship Angle A — `crossmint-wallets-mcp` — with explicit framing as the MCP-native companion to lobster.cash.**

**Rationale:**

1. **Fede already confirmed the gap.** His exact words: *"no es lo que dices pero un approach a algo asi"* — lobster.cash is NOT an MCP server. The MCP-native gap is real.
2. **Fede also said x402 is "en abandono un poco"** — the x402 space is wide open for new contributions.
3. **The build risk is lower than Angle C** (no UI, no spending limit enforcement) and the audience is broader than Angle B (every MCP client vs just lobster.cash users).
4. **The framing is the critical win:** the MCP server is pitched as an EXTENSION that promotes lobster.cash to new clients. Fede endorses it because it grows HIS product's reach. Filippos endorses it because it shows Saul thinks in ecosystems.
5. **The CLI can be wrapped.** `@crossmint/lobster-cli` has "Library exports for programmatic use" per its README. The MCP server can literally call the CLI's library, which saves build time AND makes it officially dependent on lobster.cash's infrastructure.

**BUT:** there is a strong alternative worth the user's consideration.

**Alternative ship: Angle B — build a new lobster.cash skill.** Specifically `lobster-cpicheck` — the Solana smart wallet CPI scanner skill. This has:
- Lower risk (no MCP transport work)
- Saul's deepest domain (CPI scanning is literally his specialty)
- Direct certification path via Fede
- Distribution via the lobster.cash/skills directory (permanent)
- Smaller build window (~16h vs ~22h)
- PM veto note: the subject is the skill + the Solana smart wallet nuance, not Saul's facilitator

The tradeoff is reach: the MCP server wins broader adoption, the skill wins directly within lobster.cash. Both are non-duplicative.

---

## What I need from Saul

The decision is now binary:

1. **Angle A: `crossmint-wallets-mcp`** — ship as MCP-native companion, wrap lobster-cli, 22h build, reach = all MCP clients
2. **Angle B: `lobster-cpicheck` skill (or similar)** — ship as a new lobster.cash-certified skill, 16h build, reach = lobster.cash skill directory

Or: do a fast-track decision where I commit to A, build Tool 4 (`pay_x402_endpoint`) first to de-risk, and if lobster-cli wrapping fails, pivot to B within the first 8 hours.

Give me the word and I start.
