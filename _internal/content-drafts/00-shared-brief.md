# 00 — Shared Content Brief

> **Audience:** All 4 content specialists (marketing, blog, thread, video)
> **Read this BEFORE writing anything.** This is the single source of truth for framing, facts, and constraints.
> **Authority:** Overrides any assumptions from the master plan. If the plan and this brief disagree, this brief wins.

---

## The locked narrative (one sentence)

> **"lobster.cash is Crossmint's payment engine for AI agents — shipped as a CLI that installs into Claude Code, Cursor, and OpenClaw via skills. But Claude Desktop, Continue.dev, Cline, and Codex CLI speak MCP, not skills. And the nuance of how Crossmint smart wallets sign Solana transactions via CPI is not explained anywhere. I built the MCP server that fills the first gap and the lobster.cash skill that fills the second."**

This is the load-bearing sentence. Every artifact you write must be compatible with it.

---

## The two artifacts (what was actually built and shipped)

### Artifact 1 — `crossmint-wallets-mcp`

An MCP server (TypeScript, MIT, npm-publishable) that exposes Crossmint smart wallet primitives as MCP tools. Works in any MCP-native client via a single JSON config block. Verified working end-to-end on Solana mainnet.

### Artifact 2 — `crossmint-cpi-skill`

A lobster.cash-compatible skill (MIT, repo `crossmint-cpi-skill`) that teaches agents the Solana CPI inner instruction nuance and routes x402 payments through `@crossmint/lobster-cli` on Crossmint smart wallets. Format per lobster.cash Skill Compatibility Guide.

Both artifacts ship together. Same narrative, same blog post, same campaign.

---

## The 4 MCP tools (EXACT names, do not paraphrase)

| # | Tool name | What it does |
|---|---|---|
| 1 | `crossmint_create_wallet` | Creates a Crossmint smart wallet on Solana or Base. Takes `chain`, optional `owner` (user locator like `email:x@y.com` or `COMPANY`), optional `alias` (makes creation deterministic). |
| 2 | `crossmint_get_balance` | Returns native + USDC balances for a Crossmint wallet. Read-only, no signer required. Takes `address`, `chain`, optional `tokens`. |
| 3 | `crossmint_transfer_token` | Sends tokens from a Crossmint wallet to any address. Uses the gasless relayer, no native gas required. Amount in decimal human units (`'0.01'`). |
| 4 | `crossmint_pay_x402_endpoint` | **The killer tool.** Fetches an HTTP URL, handles the 402 challenge, pays USDC from a Crossmint wallet, returns the paid response + on-chain tx signature. Supports Solana mainnet in v0.1. |

---

## Real evidence (anchor every technical claim to one of these — DO NOT INVENT FACTS)

### The verified smoke-test transaction

- **Signature:** `KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg`
- **Explorer:** https://explorer.solana.com/tx/KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg
- **Network:** Solana mainnet
- **Amount:** 0.01 USDC
- **Payer wallet:** `4xHkMCaKVBGw4GtdpeKoNZhGFDMi1tMCJDvXvxUmL8hM` (Crossmint smart wallet, alias unset, company-owned)
- **Merchant wallet:** `Fxr4RtBPzU5ZJL2Wj57MZNuChBNbZzK3RHrXDwa1yqo` (Crossmint smart wallet, alias=merchant)
- **Result:** HTTP 402 → X-PAYMENT header → HTTP 200 round trip, verified
- **Runs completed:** 2 successful payments moved the merchant from 0 → 0.02 USDC, verified on-chain

### The verified demo flow (from `demo/smoke-test.ts`)

1. Load `.env` via dotenv (keeps recovery secret out of repo tree)
2. `createWallet` on Solana mainnet → returns real address, caches to `demo/wallet-cache.json`
3. `getBalance` on payer → prints balances to stderr
4. Start local paywall server (`demo/paywall-server.ts`) — hand-rolled express, ~150 lines, no `@x402/express` middleware
5. `payX402Endpoint` → GET the paywall URL → receive HTTP 402 with canonical `PaymentRequired` body → parse `accepts[0]` (`scheme=exact`, `network=SOLANA_MAINNET_CAIP2`, `asset=USDC_MAINNET_ADDRESS`, `amount=10000` atomic, `payTo=merchant address`) → call `Wallet.send(merchant, 'usdc', '0.01')` → Crossmint signs via CPI + pays gas → receive tx signature → base64-encode payload `{payload: {transactionSignature: "..."}}` → retry GET with `X-PAYMENT` header → paywall verifies tx on mainnet RPC (checks `postTokenBalances` delta on merchant ATA) → returns HTTP 200 with demo JSON
6. `getBalance` on merchant → verify funds landed

### The verified MCP handshake

- JSON-RPC handshake verified against `dist/mcp/server.js` via stdio
- `initialize` works
- `tools/list` returns all 4 tools with their zod schemas
- `tools/call` successfully invokes each tool and returns results

### Package versions (pinned, from real `package.json`)

| Package | Version |
|---|---|
| `@crossmint/wallets-sdk` | `^1.0.7` |
| `@modelcontextprotocol/sdk` | `^1.29.0` |
| `@crossmint/lobster-cli` | `3.0.8` (external reference, not a dependency) |
| `@solana/web3.js` | `^1.95.0` |
| `@solana/spl-token` | `^0.4.9` |
| `@x402/core` | `^2.9.0` |
| `@x402/svm` | `^2.9.0` |
| `zod` | `^3.25.0` |

### Claude Desktop config block (copy-paste ready)

```json
{
  "mcpServers": {
    "crossmint-wallets": {
      "command": "npx",
      "args": ["-y", "crossmint-wallets-mcp"],
      "env": {
        "CROSSMINT_API_KEY": "sk_...",
        "CROSSMINT_RECOVERY_SECRET": "hex...",
        "DEFAULT_CHAIN": "solana"
      }
    }
  }
}
```

### SDK workarounds we discovered (real, document-worthy technical nuggets)

1. **`createWallet` rejects free-form owner strings.** Valid formats: the literal `"COMPANY"`, or prefixed user locators (`email:x@y.com`, `userId:abc`, `x:handle`, `phoneNumber:+123`).
2. **`getWallet(locator, args)` crashes `useSigner` in SDK 1.0.7** with `Cannot read properties of undefined (reading 'startsWith')`. Workaround: pass `recovery: { type: "server", secret }` through a cast.
3. **`createWallet` without `owner`/`alias` is NOT deterministic.** Each call creates a fresh wallet. Use an explicit `alias` for idempotent addressing.
4. **`Wallet.send(to, token, amount)` is the recommended path**, not hand-rolling SPL TransferChecked. It wraps the SPL transfer in a CPI inner instruction, signs via the recovery signer, and pays gas via the gasless relayer automatically.

### The CPI inner instruction nuance (the unique technical content)

- A Crossmint smart wallet on Solana does not sign SPL token transfers directly at the top level of a transaction
- It signs a top-level "execute this inner instruction" call against its own program, which then performs the SPL transfer via a Cross-Program Invocation
- Naive x402 facilitators that only scan the **top-level** instructions of a transaction see an opaque smart-wallet program call and miss the USDC transfer entirely
- Facilitators that scan **inner instructions** (like Faremeter) correctly see the nested SPL transfer and verify the payment
- **Plain English analogy:** "It's like the difference between writing a check yourself and your bank writing it on your behalf — the money moves, but different signatures show up on the receipt."
- No public Crossmint docs, no lobster.cash skill, no Solana Foundation guide explains this today. This is the gap the `crossmint-cpi-skill` artifact fills.

---

## Framing discipline — ABSOLUTE rules (violating any of these is a PM flag)

### Non-negotiable positioning

1. **MCP server is the MCP-native COMPANION to lobster.cash.** Never a replacement. Never a competitor. The language is "fills the gap," "extends," "complements," "reaches the clients lobster.cash doesn't."
2. **Faremeter is the preferred facilitator.** It is mentioned respectfully as existing infrastructure that correctly handles the CPI nuance. Treat it as already-shipped, working, and recommended.
3. **lobster.cash is REAL and SHIPPED.** This is non-competitive framing. Do not attack, diminish, or compare unfavorably. The frame is: "Crossmint ships wallets. lobster.cash ships the CLI. I shipped the MCP bridge and the skill the CLI is missing."
4. **Crossmint is the incumbent we are extending.** Respectful, builder-to-builder tone. Never aspirational or pitch-deck.

### Hard prohibitions (will get you rejected from PM review)

- **NEVER name:** Ultravioleta, Execution Market, MeshRelay, or any Ultravioleta-adjacent project
- **NEVER name:** Saul's `x402-rs` facilitator fork (it exists but is NOT the subject)
- **NEVER quote Fede's private Telegram** ("x402 esta en abandono un poco") without explicit permission — use public signals only
- **NEVER quote Filippos's interview directly** — use public signals (Solana Foundation guide, stale `mcp-crossmint-checkout` repo) instead

### Banned words (DA veto list — do NOT use any of these anywhere)

- `seamlessly`
- `unlock`
- `empower`
- `at scale`
- `leverage` (as a verb)
- `ecosystem` (overused — name specific frameworks instead)
- `revolutionize`
- `game-changer`
- `paradigm shift`
- `synergy`

### Banned openers

- Any sentence starting with "In today's fast-paced..."
- Any sentence starting with "In the rapidly evolving..."
- Any variant of those

### Voice requirements

- **First person.** Saul wrote this. Not "the team," not "we," not "developers should" — "I built," "I hit," "I shipped."
- **Conversational, not corporate.** Twitch-stream voice, not enterprise blog.
- **Every claim anchored in a real file, URL, or command.** No handwaving. If you say "it works," link the signature or the file.
- **No AI-sounding transitions.** No "furthermore," "moreover," "in conclusion," "it is worth noting that."
- **Sentence variety.** Don't start three sentences in a row with the same word.

---

## Mandatory inclusions (every long-form artifact must include all of these)

The blog post and campaign proposal must include all of the below. The thread and video script should include as many as naturally fit.

- The 4 exact tool names
- The Claude Desktop JSON config block (blog post + thread + video end card)
- The verified transaction signature OR the full explorer URL
- The package name `@crossmint/lobster-cli@3.0.8` (fresh signal — show the version string)
- The MCP Registry URL: `modelcontextprotocol.io/registry`
- The lobster.cash directory URL: `lobster.cash/skills`
- The Solana Foundation x402 guide URL: `solana.com/developers/guides/getstarted/intro-to-x402`
- The CPI nuance analogy in plain English (no Rust code)
- A reference to lobster.cash as Crossmint's existing, shipped, respected CLI/skills play
- A reference to Faremeter as the preferred facilitator (respectful, one mention is enough)

---

## Output file paths (each specialist writes ONE file)

| Specialist | Output file | Target size |
|---|---|---|
| marketing-strategist (narrative) | `Z:\crossmint-devrel-challenge\_internal\content-drafts\01-narrative-lock.md` | ~300 words (title + hook + 1-sentence narrative + rationale) |
| blog-writer | `Z:\crossmint-devrel-challenge\_internal\content-drafts\02-blog-post.md` | 1300-1500 words |
| thread-writer | `Z:\crossmint-devrel-challenge\_internal\content-drafts\03-twitter-thread.md` | 8-10 tweets, each under 280 chars |
| video-script-writer | `Z:\crossmint-devrel-challenge\_internal\content-drafts\04-video-script.md` | 60-90 seconds of shot-listed script |
| marketing-strategist (campaign) | `Z:\crossmint-devrel-challenge\_internal\content-drafts\05-campaign-proposal.md` | 500 words (1 page) |

---

## Sign-off

If you're reading this as a specialist: do not start writing until you've read your specific plan section (Phase 1/2/3/4/5 in `04-content-and-campaign-plan.md`) AND this brief. When you're done, write the file, then return a brief report summarizing word count and any decisions you had to make.
