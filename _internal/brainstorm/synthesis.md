# Brainstorm Synthesis — Final Concept Recommendation

> **Author:** Claude (main conversation, after reading outputs from Wild Innovator, Feasibility Scorer, and Competitive Scanner agents)
> **Date:** 2026-04-09 H+4
> **Purpose:** Merge 4 research perspectives into ONE binding concept decision.
> **Status:** RECOMMENDATION — PENDING SAUL APPROVAL

---

## The 4 inputs

| Source | Top pick | Hours | Key rationale |
|---|---|---|---|
| **Wild Innovator** | Claude Code Skill (`crossmint-skill`) | 14-16h | Filippos personally ships skills; no public Crossmint Claude Code skill; tightest "holy shit" video |
| **Feasibility Scorer** | `crossmint-x402-solana-starter` quickstart | 14-16h | Safest path, highest reuse, smallest variance — BUT this is essentially "Hello x402" which Saul rejected |
| **Competitive Scanner** | MCP server + quickstart combined | ~22h | Solana Foundation publicly waiting; no general-purpose Crossmint wallets MCP server exists; hits multiple frameworks at once |
| **Saul Asset Analysis** (Claude direct) | Crossmint MCP server (+ karmakadabra migration story) | ~18h | Saul's karmakadabra stack uses raw keys — real migration narrative; MCP patterns already built in execution-market and autojob |

**Convergence:** 3 of 4 inputs recommend some form of **MCP server or Claude Code skill** wrapping Crossmint wallets. Only the Feasibility Scorer recommends the quickstart — and the user has already vetoed that.

**Divergence:** The split is between (a) MCP server primary or (b) Claude Code skill primary.

---

## The killer intel from the Competitive Scanner

This changes the picture. Three facts that were not in FINDINGS.md:

### Fact 1: The Solana Foundation is PUBLICLY waiting for Crossmint's x402 story

`solana.com/developers/guides/getstarted/intro-to-x402` states **"Crossmint is currently in progress"** under x402 SDK support for Solana. This is a public, dated, on-the-record invitation. The Solana ecosystem developer audience is already looking at this page and wondering where Crossmint's tutorial is.

### Fact 2: The existing Crossmint MCP server is stale garbage

- Repo: `github.com/Crossmint/mcp-crossmint-checkout`
- Stars: **1**
- Last commit: **May 2, 2025** (11 months stale)
- Scope: checkout-only, defaults to Amazon US Prime, uses legacy Crossmint credits
- **Does NOT** expose smart wallet creation, signing, balance, transfer, x402, or Solana at all

**There is no general-purpose Crossmint Wallets MCP server.** Not at Crossmint, not in the community, not inside GOAT SDK. GOAT SDK wraps Crossmint for LangChain/Eliza but does NOT expose it as an MCP server.

### Fact 3: The relai.fi post kills the pure x402 quickstart angle

A relai.fi blog post "Solana Smart Wallet Payments with x402" (web3luka, March 2, 2026) already names Crossmint as a supported smart wallet provider and walks through an x402 architecture. It is provider-agnostic and not a 5-minute quickstart, but it does mean the pure "Hello x402 for Crossmint smart wallets" topic is no longer virgin territory.

**Conclusion:** The pure x402 quickstart is dead. The pure MCP server is wide open.

---

## The decision — THE WINNING CONCEPT

### Name

**Crossmint Wallets MCP Server (`crossmint-wallets-mcp`)**

### The opinion sentence (Twitter-length rubric)

> *Crossmint ships world-class smart wallets. The only Crossmint MCP server is a stale 1-star checkout wrapper from May 2025. Solana Foundation says our x402 SDK is "in progress." Filippos ships skills himself in his spare time. 2026 agents speak MCP. Here's the Crossmint Wallets MCP server Crossmint hasn't built yet.*

(308 chars — needs a small trim)

**Tightened (267 chars):**

> *Crossmint's only MCP server is a stale checkout wrapper from May 2025. Solana Foundation says our x402 SDK is "in progress." 2026 agents speak MCP, not SDK docs. Here's the Crossmint Wallets MCP server Crossmint hasn't built yet — every primitive, one install.*

Passes the rubric.

### What it is

A production-grade **Model Context Protocol server** that exposes the Crossmint Wallets API as MCP tools, installable in any MCP client (Claude Desktop, Claude Code, Cursor, Continue.dev, Cline, Codex CLI, OpenClaw agents, etc.) with a single config block.

### The 6 tools

| # | Tool name | What it does |
|---|---|---|
| 1 | `crossmint_create_wallet` | Create a Crossmint smart wallet for an email/identity on any supported chain (EVM or Solana) |
| 2 | `crossmint_get_balance` | Query token balances for a wallet across specified tokens |
| 3 | `crossmint_transfer_token` | Send USDC/SPL/ERC-20 from a Crossmint wallet to any address |
| 4 | `crossmint_pay_x402_endpoint` | **The killer tool.** Given a URL and a maxUsd cap, hit the endpoint, parse the 402 response, sign and send the payment via the Crossmint wallet, return the final response body. One call, one URL, done. |
| 5 | `crossmint_add_delegated_signer` | Grant a scoped signer to an agent with spending cap and expiration (fills the 404 docs gap Crossmint has today) |
| 6 | `crossmint_list_transactions` | Fetch transaction history for a wallet with status + tx signatures |

The `pay_x402_endpoint` tool is the unique differentiator. No existing Crossmint MCP server has it. It turns "pay an HTTP API" into a single function call for any AI agent.

### Chain support at launch

- **Solana mainnet** (the Solana Foundation gap)
- **Solana Devnet** (for the safe demo)
- **Base mainnet** (Crossmint's #1 EVM audience)

All three in the same MCP server, selectable via a `chain` argument to `create_wallet`.

### The 3 artifacts (matches the brief exactly)

**Artifact 1 — Demo / code artifact:**
- Public GitHub repo: `crossmint-wallets-mcp`
- Clean TypeScript, `@modelcontextprotocol/sdk` based
- Installable via `npx @<scope>/crossmint-wallets-mcp` (one npm publish)
- Works with a single Claude Desktop config block
- Includes an end-to-end script that Saul runs live on stream to prove it works

**Artifact 2 — Developer-facing content:**
- Blog post: *"Every AI agent in 2026 should have a Crossmint wallet. Here's the MCP server Crossmint hasn't built yet."*
- Technical depth: explains WHY Solana smart wallets require CPI inner instruction scanning (the nuance that makes the x402 tool hard to implement correctly), with the Faremeter facilitator cited as the reference implementation Crossmint has internally blessed
- Twitter thread (8-10 tweets) as the distribution layer

**Artifact 3 — Campaign proposal (1 pager):**
- Why: Solana Foundation publicly waiting; existing Crossmint MCP is stale; Filippos building skills personally
- What: publish as `@crossmint/wallets-mcp` under the Crossmint org; distribute via MCP registry + Claude Code skill marketplace + Cursor rules + Fede's Twitter + x402hackathon Discord + Solana Developer Relations channel
- Outcomes: concrete metrics — MCP registry installs, time-to-first-transaction from MCP client, GitHub stars, mentions in x402 hackathon submissions over next 30 days

### Why this beats the alternatives

| Alternative | Why I'm rejecting it |
|---|---|
| Wild Innovator's Claude Code Skill | Too narrow (Claude Code only). An MCP server is strictly more general — Claude Code can consume MCP servers natively. Ship the MCP server and the Claude Code skill manifest (10 lines) comes almost for free as a bonus. |
| Feasibility Scorer's `crossmint-x402-solana-starter` | This is Hello x402. User already rejected. The relai.fi post now occupies that topic. Dead. |
| My own `kk-crossmint` karmakadabra migration | Too narrow (OpenClaw only). Keeps Saul's focus on his own framework. An MCP server serves a bigger audience. BUT: the migration story can be the NARRATIVE of the blog post — "I rewrote my own agent swarm's wallet layer using this MCP server." Fold it in. |
| Wild Innovator's Delegated Signer consent UI | Higher strategic relevance (Visa bottleneck) but heavier UI work and API risk. Keep as Plan B if MCP server hits a blocker. |
| Wild Innovator's ChatGPT Apps SDK integration | Higher wow factor but OpenAI Apps SDK is unstable and would eat the 48h budget. Kill. |

### Why it beats "Hello x402" definitively

1. **It's not a quickstart.** It's a platform primitive. Reviewers don't say "I've seen this" because nobody has shipped it yet.
2. **It fills a gap the Solana Foundation publicly named.** The Solana Foundation guide explicitly lists Crossmint as "in progress." Saul can cite that in the campaign proposal.
3. **It's framework-agnostic.** Every MCP client wins immediately. The Claude Code skill is a 10-line bonus.
4. **It makes `pay_x402_endpoint` a one-liner.** This is the killer tool. Every x402 demo in the world right now requires multi-step client code. Saul's MCP server makes it one function call.
5. **It uses Saul's deepest asset (CPI scanning knowledge)** without showcasing the x402-rs facilitator as the subject. The facilitator is just a dependency; the subject is the MCP server.

---

## Time budget

| Phase | Task | Hours |
|---|---|---|
| Setup | Repo scaffold, TypeScript + `@modelcontextprotocol/sdk` + Crossmint SDK | 2h |
| Tool 1 | `crossmint_create_wallet` (Solana + EVM) | 1.5h |
| Tool 2 | `crossmint_get_balance` | 1h |
| Tool 3 | `crossmint_transfer_token` | 2h |
| Tool 4 | `crossmint_pay_x402_endpoint` — **hardest** — 402 parse + Crossmint signing + Faremeter submission + response | 5h |
| Tool 5 | `crossmint_add_delegated_signer` | 2h |
| Tool 6 | `crossmint_list_transactions` | 1h |
| Testing | End-to-end test on Solana Devnet + Base Sepolia, plus mainnet smoke | 3h |
| README | demo/README.md rewrite + Claude Desktop config example | 2h |
| Blog post | `content/blog-post.md` (1500-1800 words) | 3h |
| Twitter thread | `content/twitter-thread.md` (8-10 tweets) | 1h |
| Campaign | `campaign/proposal.md` (1 pager) | 1h |
| Video | Screen recording of Claude Desktop using the MCP server | 1h |
| Buffer | Integration, polish, deploy | 3h |
| **TOTAL** | | **28.5h** |

This is slightly over the 26h execution budget. Mitigations:

1. **Drop `crossmint_list_transactions`** if we are behind (saves 1h, tool is nice-to-have)
2. **Drop `crossmint_add_delegated_signer`** if the Crossmint SDK exposure is unclear (saves 2h, we can say "coming in v2")
3. **Ship with Solana only** if Base EVM support has integration bugs (saves 1-2h)

With drops, the minimum viable ship is 24-25h: tools 1, 2, 3, 4 + testing + content. That fits the budget.

---

## Risks

| Risk | Severity | Mitigation |
|---|---|---|
| `@crossmint/wallets-sdk` cannot sign an arbitrary x402 payload from a smart wallet (the SDK may only expose `transfer`, not raw signing) | **CRITICAL** — would break Tool 4 | Build Tool 4 FIRST. If it hits a wall, fall back to using `@crossmint/wallets-sdk` `transfer()` with the x402 destination address as the `to` — this works for direct-transfer x402 schemes but not for all x402 variants. Degraded but usable. |
| MCP server distribution model (npm package vs npx) is unfamiliar to Saul | Medium | Use AutoJob MCP as the template (already published and working) |
| Claude Desktop on Windows has known MCP transport issues | Medium | Test on macOS too; document both paths in README |
| Crossmint may have an internal MCP server under development that we can't see | Medium | Saul should ask Adolfo/Fede directly: "is anyone at Crossmint shipping a wallets MCP server?" Before locking. |
| Faremeter facilitator unresponsiveness during demo | Low | Include a local facilitator option in README as fallback |
| Ultravioleta facilitator self-promotion concern | Low | Use Faremeter as primary; Ultravioleta facilitator NOT mentioned in the MCP server code or README. Faremeter is Crossmint's stated preferred. |

**Critical path risk:** Tool 4 (`pay_x402_endpoint`). Build this first in the first 6-8h. If it works, the rest is easy. If it doesn't, we pivot to Plan B (Delegated Signer consent UI) within the first 8h and still have 20h to ship.

---

## Non-negotiables

1. **Subject is the MCP server.** Saul's x402-rs facilitator is NOT mentioned in the MCP server code, README, or blog post. It may appear as one alternative in a "fallback facilitators" paragraph, nothing more.
2. **Faremeter is the default facilitator.** Per Filippos's own words in the interview.
3. **Solana mainnet demo IS shippable.** No more Devnet-only. The video shows a real USDC transaction from a Crossmint smart wallet on Solana mainnet. This is the big demo.
4. **`pay_x402_endpoint` ships or we pivot.** Without that tool, this is just a Crossmint SDK wrapper. With it, it's a category claim.
5. **The blog post is the content piece.** Not a Twitter thread as primary. The Twitter thread is the distribution.
6. **Campaign proposal names real channels.** MCP registry, Claude Code skill marketplace, Fede @alfongj specific handles, Solana Developer Relations, x402hackathon Discord.

---

## What we need from Saul

1. **Approve the pivot from Hello x402 to the MCP server.** Everything else follows.
2. **(Optional but valuable):** ping Fede or Adolfo to ask *"Is anyone at Crossmint shipping a wallets MCP server?"* — if the answer is "yes, next week," we pivot to Plan B. If no, we ship.
3. **Budget confirmation:** 28.5h with drops to 24-25h if we hit blockers. Confirm this fits.

---

## Locked framing rules (forwarded from v1 concept lock)

These carry over unchanged:
- Framing is Crossmint-first, not Saul-self-promotion
- Real channels in the campaign, not generic
- Final voice pass by Saul before ship
- Devil's Advocate reviews at end of Phase 3 and Phase 5
- Ship at H+36 not H+48 (Adolfo is in Spain)
- Hard cutoff H+40

---

## If approved, next actions

1. Update `_internal/decisions.md` with the new concept lock
2. Update `_internal/pm-journal.md` with the pivot decision
3. Rewrite `demo/README.md` (it currently reflects the killed Hello x402 concept)
4. Start on Tool 4 (`crossmint_pay_x402_endpoint`) FIRST — de-risk the critical path
5. Scaffold the MCP server repo

This is the sharpest shot we have at landing a hire-on-the-spot submission in 48h.
