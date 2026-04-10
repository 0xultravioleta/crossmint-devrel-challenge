# Crossmint ships the wallets. lobster.cash ships the CLI. I shipped the MCP bridge — and the skill the CLI is missing.

*by Saul — DevRel candidate, Miami, hackathon mentor at x402hackathon.com*

---

## The gap I kept tripping over

I spent the last two weeks pointing hackathon teams at Crossmint's smart wallets and lobster.cash. Most of them asked the same follow-up question and I did not have a clean answer:

*"Cool, how do I wire this into Claude Desktop?"*

Or Continue.dev. Or Cline. Or Codex CLI. Every one of those clients speaks MCP — the Model Context Protocol — and none of them install skills. lobster.cash is Crossmint's payment engine for AI agents, and it is genuinely great, but it ships as a CLI plus a skills directory that installs into Claude Code, Cursor, and OpenClaw. Half the 2026 agent world cannot use it. On top of that, the single most important technical nuance of paying from a Crossmint smart wallet on Solana — the CPI inner instruction quirk — is not explained anywhere public. Not in Crossmint docs, not in the lobster.cash skill directory, not in the Solana Foundation x402 guide at [solana.com/developers/guides/getstarted/intro-to-x402](https://solana.com/developers/guides/getstarted/intro-to-x402).

So I built two MIT-licensed artifacts to close both gaps. This post walks through what each one is, why it matters, and how to install them in under a minute.

## What lobster.cash actually is (not the gap I'm filling)

Before I get into what I built, I want to be specific about what lobster.cash is, because my whole pitch depends on being honest about it.

lobster.cash is Crossmint's CLI for AI agents that need to pay for things. The package `@crossmint/lobster-cli@3.0.8` went to npm roughly an hour before I started writing this post — I watched the version tick up in real time. It works by exposing a `skills` directory: you run `npx skills add <url>`, the CLI installs a skill into your agent's context, and the skill teaches the agent when and how to call out to `lobster-cli` to execute a payment. The architecture is clean, the CLI is well-engineered, and there are already 13 certified skills in the [lobster.cash/skills](https://lobster.cash/skills) directory.

What lobster.cash does NOT do:

1. **It doesn't reach MCP-native clients.** Claude Desktop, Continue.dev, Cline, Codex CLI — they all load tools through the Model Context Protocol, not through a skills file. You cannot install a lobster.cash skill into Claude Desktop today.
2. **It doesn't ship a skill that explains the Solana CPI inner instruction nuance.** None of the 13 certified skills teach developers why Crossmint smart wallet payments look invisible to naive x402 facilitators on Solana — and that single piece of knowledge is what separates a payment that verifies from a payment that silently fails.

Those are the two gaps. Let me show you the fixes.

## Gap 1 — the MCP server

MCP — Model Context Protocol — is the standard Anthropic introduced for wiring tools into AI agents. Every serious MCP-native client loads tools from a tiny JSON config block. Paste one stanza into `claude_desktop_config.json`, restart, and the client has new capabilities. No skill installer, no per-agent surgery.

The server I built, `crossmint-wallets-mcp`, exposes four tools:

- `crossmint_create_wallet` — creates a Crossmint smart wallet on Solana or Base, optionally with an `alias` so creation is idempotent
- `crossmint_get_balance` — returns native + USDC balances for any Crossmint address, read-only
- `crossmint_transfer_token` — sends tokens from a Crossmint wallet to any recipient, uses the gasless relayer so the wallet does not need SOL for gas
- `crossmint_pay_x402_endpoint` — the killer one. Give it a URL. It handles the HTTP 402 challenge, pays the USDC, retries with the `X-PAYMENT` header, and returns the response body plus the on-chain transaction signature

Install is one JSON block:

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

I tested the full flow on Solana mainnet with real USDC. The smoke test creates a payer wallet, creates a merchant wallet, boots a local paywall server that returns canonical x402 `PaymentRequired` bodies, and then calls `crossmint_pay_x402_endpoint`. The payer smart wallet at `4xHkMCaKVBGw4GtdpeKoNZhGFDMi1tMCJDvXvxUmL8hM` transfers 0.01 USDC to the merchant at `Fxr4RtBPzU5ZJL2Wj57MZNuChBNbZzK3RHrXDwa1yqo`, Crossmint signs via CPI internally, the gasless relayer pays the fee, the paywall verifies the signature against a mainnet RPC, and the retry returns HTTP 200. First real signature: [`KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg`](https://explorer.solana.com/tx/KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg). Two runs moved the merchant balance from 0 to 0.02 USDC. Verified on-chain.

The stack is boring on purpose: TypeScript, `@modelcontextprotocol/sdk@1.29.0`, `@crossmint/wallets-sdk@1.0.7`, zod for schemas, `@solana/web3.js` for RPC lookups. The MCP JSON-RPC handshake — `initialize`, `tools/list`, `tools/call` — I verified end-to-end against `dist/mcp/server.js` via stdio, so dropping it into Claude Desktop is the same process as any other MCP server the user has already installed.

## Gap 2 — the Solana CPI inner instruction skill

Here is the thing that took me a full day to figure out when I first shipped x402 on Solana, and that I could not find explained in plain English anywhere: **Crossmint smart wallets do not sign SPL token transfers at the top level of a transaction.**

A normal Solana transfer has a top-level SPL `TransferChecked` instruction. The sender signs it, the receiver is listed in the accounts, and anyone scanning the transaction sees the transfer directly. A Crossmint smart wallet does something different. The top level of the transaction is an "execute this inner instruction" call against the smart wallet's own program. The actual SPL transfer happens inside a Cross-Program Invocation — a CPI — nested one level down.

Plain English analogy: it is the difference between writing a check yourself and your bank writing it on your behalf. The money moves either way, but different signatures show up on the receipt.

This matters because naive x402 facilitators only scan top-level instructions. They see an opaque "smart wallet program" call, cannot find an SPL transfer that matches the 402 requirements, and reject the payment as invalid — even though the USDC has already moved. Facilitators that correctly scan CPI inner instructions — Faremeter is the one Crossmint itself uses and recommends — find the nested transfer and verify it without a problem.

The skill I wrote, `crossmint-cpi-skill`, is a lobster.cash-compatible skill that teaches agents this exact decision tree. It follows the Skill Compatibility Guide — `SKILL.md` with a wallet precheck, a payment action description, and payment-status error handling — and it shells out to `@crossmint/lobster-cli@3.0.8` for execution so it is never reimplementing payment logic. It is MIT and installable via the lobster.cash skill directory path:

```bash
npx skills add https://github.com/0xultravioleta/crossmint-cpi-skill --global --yes
```

The skill's job is business intent. When an agent needs to pay an x402 endpoint from a Solana Crossmint smart wallet, the skill tells it: check the wallet exists, describe the payment, run the command, handle the known error modes (CPI vs top-level transfer, settlement account vs direct, mainnet vs devnet fallback). None of the other 13 certified skills teach this — I checked every one of them.

## How to use both

If you are on an MCP-native client (Claude Desktop, Continue.dev, Cline, Codex CLI), paste the JSON config block above into your client config and you have Crossmint wallets as tools.

If you are on a skill-architecture client (Claude Code, Cursor, OpenClaw), run the `npx skills add` line above and your agent gains the CPI-aware decision tree for paying x402 endpoints from Crossmint smart wallets.

They are complementary, not competitors. Different clients, different install paths, same underlying Crossmint wallet. The MCP server handles the tool plumbing for MCP clients; the skill handles the knowledge layer for lobster.cash clients. Neither one replaces lobster.cash. Neither one replaces Crossmint. Both fork cleanly into the Crossmint org the moment the team decides they want to adopt them.

## What's next

Both artifacts are MIT-licensed on my personal GitHub, and both are drafted to be republished at `@crossmint/wallets-mcp` and as a `crossmint/skills` entry the moment the team wants them. The MCP server is ready for the [modelcontextprotocol.io/registry](https://modelcontextprotocol.io/registry) submission. The skill is ready for Fede's certification review into [lobster.cash/skills](https://lobster.cash/skills). If there is a path forward, the next conversation is about shipping it as `@crossmint/*` and updating the Solana Foundation's "Crossmint is currently in progress" note on their x402 guide to "shipped."

DMs open.

— Saul
