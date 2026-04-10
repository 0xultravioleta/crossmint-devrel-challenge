# Crossmint Wallets MCP Server + lobster.cash CPI Skill — launch proposal

*A 1-pager for the Crossmint DevRel team. ~500 words.*

## Why this campaign

The 2026 AI agent landscape is MCP-native. Claude Desktop, Continue.dev, Cline, and Codex CLI all load tools through the Model Context Protocol, and all of them are growing fast. lobster.cash ships to Claude Code, Cursor, and OpenClaw via its skills architecture — `@crossmint/lobster-cli@3.0.8` landed on npm an hour before this proposal was drafted — but it does not yet reach the MCP-native half of the market. On top of that, Solana Foundation's public x402 guide at [solana.com/developers/guides/getstarted/intro-to-x402](https://solana.com/developers/guides/getstarted/intro-to-x402) literally says "Crossmint is currently in progress." That is a public invitation. And the only pre-existing Crossmint MCP server on GitHub (`mcp-crossmint-checkout`) is a 1-star checkout-only repo from May 2025 with no Solana and no x402. Stale.

## Proof it works

The MCP server ships 4 tools: `crossmint_create_wallet`, `crossmint_get_balance`, `crossmint_transfer_token`, `crossmint_pay_x402_endpoint`. The smoke test runs against Solana mainnet with real USDC — first confirmed signature [`KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg`](https://explorer.solana.com/tx/KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg) on-chain today. The CPI skill teaches the one thing every Crossmint-on-Solana developer hits: the USDC transfer happens one level down in a cross-program invocation — like writing a check yourself versus your bank writing it on your behalf — and Faremeter is the facilitator that verifies it correctly by scanning inner instructions.

## What needs to happen

**For the Crossmint team** (~4h review + 2h distribution):

- Fork `github.com/0xultravioleta/crossmint-wallets-mcp` into the Crossmint org, rename to `@crossmint/wallets-mcp`, publish to npm
- Submit it to the MCP Registry at [modelcontextprotocol.io/registry](https://modelcontextprotocol.io/registry)
- Add it to the Claude Desktop community MCP listings
- Publish the attached blog post draft at `blog.crossmint.com` (it is 1300 words, already voice-passed)
- Tweet the 9-tweet thread from `@crossmint_io` (draft attached)

**For the lobster.cash team** (Fede):

- Review `github.com/0xultravioleta/crossmint-cpi-skill` for certification
- If certified, add to the [lobster.cash/skills](https://lobster.cash/skills) directory
- The CPI inner instruction scanning fix has already been implemented and battle-tested in a production facilitator since March 2, 2026. The Faremeter SDK focuses on top-level instruction scanning today — extending it to also scan CPI inner instructions would benefit every Faremeter-based facilitator in the ecosystem.

**For DevRel distribution:**

- Announce in the x402hackathon Discord `#projects` channel (I mentor there)
- Cross-post to the Solana DevRel channel — they are the ones publicly waiting for Crossmint's x402 guide to land
- Cross-post to the Claude Desktop and Cursor community Discords
- Include in the next Crossmint weekly developer update

## Expected outcomes

**30-day metrics (measurable):**

- MCP Registry install count for `@crossmint/wallets-mcp`
- GitHub stars on both repos (baseline 0)
- lobster.cash/skills directory count moves from 13 to 14+
- x402hackathon project submissions mentioning either artifact
- Blog page views on `blog.crossmint.com`
- Twitter thread reach and reply engagement

**60-day strategic outcomes:**

- First wallet infra with BOTH an official MCP server and a certified lobster.cash skill
- Solana Foundation's "Crossmint is currently in progress" note updated to "shipped"
- Claude Desktop users pay x402 endpoints natively with Crossmint wallets — a first-party integration with the largest MCP-native client

## Real people and real channels

- Crossmint team: `@crossmint_io`, `@alfongj`, `@cryptofede`, `@filipposjim`
- MCP Registry: [modelcontextprotocol.io/registry](https://modelcontextprotocol.io/registry)
- lobster.cash skills directory: [lobster.cash/skills](https://lobster.cash/skills) (Fede's certification gate)
- x402hackathon Discord: `#projects`
- Solana DevRel: via their "in progress" guide update

---

*Both artifacts are MIT-licensed and ready to fork today. If there is a path forward, the next conversation is about shipping them as `@crossmint/*` this week.*
*— Saul*
