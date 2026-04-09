# Saul's Asset Analysis — Karmakadabra + OpenClaw Discovery

> **Critical finding discovered while brainstorming agents run in parallel.**
> Author: Claude (main conversation)
> Date: 2026-04-09 H+3.5

---

## The karmakadabra/openclaw ecosystem — 11 production skills

Saul's OpenClaw agent framework (karmakadabra) ships **11 OpenClaw skills** in production, running on Base mainnet. This is a real agent swarm, not a toy.

| Skill | Function | Wallet model |
|---|---|---|
| `autojob` | Job matching from behavioral evidence | — |
| `execution-market` | Task marketplace (with MCP endpoint) | — |
| `kk-catalog` | Announce product offerings via IRC HAVE protocol | — |
| `kk-data` | Local data stores access | — |
| `kk-irc` | MeshRelay IRC connection | — |
| `kk-juanjumagalp` | Community buyer agent | Raw keys via HD derivation |
| `kk-marketplace` | EM task operations | — |
| `kk-reputation` | ERC-8004 reputation check | — |
| **`kk-wallet`** | USDC balance + HD derivation + EIP-3009 signing | **Raw keys from AWS Secrets Manager** |
| **`kk-x402`** | EIP-3009 signing for x402 payments on Base | **Raw keys from env var or AWS** |
| `meshrelay` | MeshRelay IRC MCP |

---

## THE GAP IN SAUL'S OWN STACK

**All wallet and payment operations in karmakadabra use RAW PRIVATE KEYS derived from an HD mnemonic stored in AWS Secrets Manager.** Every OpenClaw agent in the swarm uses this pattern.

This is:

1. **A real security liability.** Raw keys in agent frameworks are the exact thing lobster.cash was created to solve — per Crossmint's own blog post on the OpenClaw plugin.
2. **An upgrade path that maps 1:1 to Crossmint's value proposition.** Crossmint smart wallets eliminate raw key management entirely. Delegated signers let agents have scoped permissions without ever touching root keys. Gas sponsorship removes the "keep ETH in every agent wallet" tax.
3. **The exact failure mode Filippos described in the interview.** He said: *"When agents need to send or receive money, they hit a wall."* Saul has 11 skills that hit this wall — and his solution today is "put the key in AWS," not "use a smart wallet."

---

## The strategic insight

**The submission should NOT be another tutorial. It should be a migration.**

The story writes itself:

> *"I run a swarm of 11 OpenClaw agents in production on Base mainnet. Every agent manages payments using raw private keys derived from an HD mnemonic in AWS Secrets Manager. That's the pattern the lobster.cash blog post explicitly calls out as a liability. I thought about it and realized: my own production stack is the exact thing Crossmint smart wallets were built to fix. So I migrated. This is the kit you can use to migrate yours."*

This is a CLEAR OPINION, specific ("raw keys in agent frameworks are a liability"), net-new (Saul's never shipped this migration), and directly relevant to Crossmint's agentic commerce priority.

---

## Concept proposal: `crossmint-mcp` — the Crossmint MCP Server

### The name

**Crossmint MCP Server: Drop-in wallet infrastructure for any AI agent.**

### The opinion sentence (Twitter-length rubric)

> *OpenClaw agents, Claude Code, Cursor, Codex — every agent framework in 2026 speaks MCP. But to hold a wallet and pay for things, most of them still fall back to raw private keys in env vars. Crossmint smart wallets plus MCP fix this. Here's the server that makes it one config line.*

(296 chars — slightly over 280, needs trimming to pass rubric)

Tighter version:

> *Every 2026 agent framework speaks MCP, but most still hold raw private keys for payments. Crossmint smart wallets + MCP fix this in one config line. Here's the server that drops in to Claude Desktop, OpenClaw, Cursor, Codex, or anything MCP-native.*

(274 chars — passes)

### What it is

An **MCP (Model Context Protocol) server** that wraps the Crossmint SDK and exposes these tools to any MCP-compatible agent:

- `crossmint_create_wallet(email, chain)` — create a smart wallet for an identity
- `crossmint_get_wallet(email, chain)` — fetch existing wallet
- `crossmint_balance(email, tokens[])` — read token balances
- `crossmint_send_token(email, to, token, amount)` — send a token
- `crossmint_sign_x402_payment(email, paywall_response)` — sign a payment for an x402 paywall
- `crossmint_delegate_signer(email, delegate_address, spending_cap, expiration)` — create a scoped delegated signer for an agent
- `crossmint_list_transactions(email)` — transaction history

### The demo flow (5 minutes, video-recordable)

1. Saul installs the MCP server: `pnpm add @lxhxr/crossmint-mcp-server` (or npm)
2. Saul adds one block to his Claude Desktop config:
   ```json
   {
     "mcpServers": {
       "crossmint": {
         "command": "npx",
         "args": ["-y", "@lxhxr/crossmint-mcp-server"],
         "env": {"CROSSMINT_API_KEY": "sk_..."}
       }
     }
   }
   ```
3. Saul restarts Claude Desktop
4. Saul types in Claude: *"Create a Crossmint wallet for me on Solana mainnet and send 1 cent to the Circle hot wallet."*
5. Claude calls `crossmint_create_wallet`, then `crossmint_send_token`, reports the transaction signature
6. Saul shows it on the Solana explorer. Done.

### Crossmint products used

- `@crossmint/wallets-sdk` (required)
- Smart wallets (Solana + EVM)
- Delegated signers (via the `crossmint_delegate_signer` tool)
- Gas sponsorship (inherent to Crossmint wallets)
- Balance queries
- Token transfer API

### Saul's assets that feed in

- MCP server patterns already exist in `execution-market/mcp_server`, `enclaveops/packages/mcp-server`, `autojob`
- Deep familiarity with the Crossmint SDK (built CPI inner instruction scanning for their smart wallets)
- Knows the `@crossmint/wallets-sdk` API surface cold
- 11 OpenClaw skills give him real-world usage scenarios for the 7 tools
- Streams on Twitch — can record the demo video in one take

### Why this is NOT stale

- **No official Crossmint MCP server exists** (verified in competitive scan pending)
- **No public community MCP server for Crossmint** (verified in competitive scan pending)
- **MCP is the 2026 standard**, not 2025 — this is landing on the wave, not behind it
- **Lobster.cash is an OpenClaw payment plugin, not an MCP server** — different pattern, complementary, not competing
- **The "replace raw keys with smart wallets" migration is a story nobody has told yet** in the Crossmint ecosystem

### Estimated build time in 48h

| Task | Hours |
|---|---|
| MCP server scaffolding (TypeScript + fastmcp or @modelcontextprotocol/sdk) | 2 |
| `crossmint_create_wallet` tool | 1 |
| `crossmint_get_wallet` tool | 0.5 |
| `crossmint_balance` tool | 1 |
| `crossmint_send_token` tool | 2 |
| `crossmint_sign_x402_payment` tool | 3 (hardest — involves reading x402 response, constructing EIP-3009 via Crossmint, submitting) |
| `crossmint_delegate_signer` tool | 2 |
| `crossmint_list_transactions` tool | 1 |
| README (already written pattern — adapt from demo README) | 1 |
| Claude Desktop config example + video recording | 1 |
| Blog post | 3 |
| Campaign proposal 1-pager | 1 |
| **Total** | **~18.5 hours** |

Fits comfortably in the 26h execution budget with 7.5h buffer for debugging and integration.

### Wow factor (1-10)

**9.** Crossmint has not shipped an MCP server. Everyone is watching MCP. Saul drops it in Claude Desktop and sends a real transaction on stream. That is a DevRel hire-on-the-spot moment.

### Failure modes

1. **Crossmint SDK has an Api surface issue** that makes one of the tools fail → mitigation: ship 6 of 7 tools, document the 7th as TODO
2. **x402 payment signing via Crossmint smart wallet is tricky** (the smart wallet needs to sign the x402 payload, and the SDK may not expose a raw signing method) → mitigation: this is the highest-risk tool, build it FIRST so failure is caught early
3. **The video demo fails** → mitigation: write the transcript of what Claude should do, run it until it works, THEN record
4. **API 529 during development** → mitigation: the MCP server is Node/TypeScript, not an AI workflow — developed locally without agent calls

### What would kill this

Only two things:
1. An official Crossmint MCP server already exists. (Competitive Scanner agent will confirm.)
2. The `@crossmint/wallets-sdk` cannot sign an x402 payment directly from a smart wallet (would require deep SDK work).

---

## Alternative concept: `kk-crossmint` — migrate karmakadabra to Crossmint

### What it is

Instead of a general-purpose MCP server, ship ONE specific OpenClaw skill that replaces `kk-wallet` + `kk-x402` with Crossmint smart wallets. Write the migration story as the blog post.

### Why weaker than the MCP server

- Narrower audience (only OpenClaw users, not Claude Desktop / Cursor / Codex / every MCP client)
- Ties to karmakadabra, which is a Saul-specific framework — audience rightfully asks "why should I care?"
- Less wow factor

### Why still an option

- Closer to Saul's actual production code
- The migration narrative is real
- Cheaper to build (~10 hours)

### Verdict

**Fold it INTO the MCP server submission as a case study.** The blog post shows the MCP server being used by karmakadabra to migrate the 11-skill swarm. Best of both worlds.

---

## What I'm waiting on

1. Wild Innovator agent — may propose concepts I haven't considered
2. Feasibility Scorer — may flag risks in the MCP server concept
3. Competitive Scanner — CRITICAL — needs to confirm no official Crossmint MCP server exists

Until those land, treat this as the strongest working hypothesis.
