# Twitter Thread — Crossmint Wallets MCP Server + CPI Skill Launch

10 tweets, each under 280 chars.

---

## Tweet 1 — the hook

Crossmint ships world-class smart wallets.
lobster.cash ships the CLI that lets AI agents pay with them.
But Claude Desktop, Continue.dev, Cline, Codex — half the 2026 agent world — speak MCP, not CLI.
So I built the missing MCP server. And the skill lobster.cash is missing too.

`[278/280]`

---

## Tweet 2 — what lobster.cash is, what it doesn't reach

lobster.cash is Crossmint's agent payment CLI. Clean architecture, 13 certified skills, `@crossmint/lobster-cli@3.0.8` shipped today.

It installs into Claude Code, Cursor, OpenClaw via skills.

It does NOT install into MCP-native clients. That's the first gap.

`[279/280]`

---

## Tweet 3a — the MCP server, 4 tools (verbatim names)

Gap 1: the MCP server for Crossmint smart wallets.

4 tools: crossmint_create_wallet, crossmint_get_balance, crossmint_transfer_token, crossmint_pay_x402_endpoint.

One config block away.

`[187/280]`

---

## Tweet 3b — Claude Desktop install block

Install in Claude Desktop:

```
"crossmint-wallets": {
  "command": "npx",
  "args": ["-y", "crossmint-wallets-mcp"]
}
```

That's it. Restart. Done.

`[149/280]`

---

## Tweet 4 — the killer tool

The killer tool: `crossmint_pay_x402_endpoint`.

Give it any URL. It handles the 402, pays USDC from a Crossmint smart wallet, retries with X-PAYMENT, returns the body + the on-chain signature.

One tool call. Any MCP client. Any x402 endpoint.

`[258/280]`

---

## Tweet 5 — the real mainnet proof

I tested it on Solana mainnet with real USDC. Real wallets. Real paywall.

Payer 4xHkMCa...UmL8hM → Merchant Fxr4RtB...a1yqo
0.01 USDC, signed via CPI by Crossmint, gasless.

First signature:
https://explorer.solana.com/tx/KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg

`[215/280 — URL counted as 23 chars per Twitter rules]`

---

## Tweet 6 — the CPI nuance in plain English

Gap 2: the Solana CPI nuance no skill explains.

Crossmint smart wallets don't sign SPL transfers at the top level — the transfer happens in a nested CPI one level down.

Plain English: it's the difference between writing a check yourself and your bank writing it on your behalf.

`[277/280]`

---

## Tweet 7 — why the CPI nuance matters + the skill

Naive facilitators scan only top-level instructions. They miss the CPI transfer and reject the payment even though USDC moved.

Faremeter scans inner instructions and gets it right. So I wrote the lobster.cash skill that teaches agents this:

`npx skills add crossmint-cpi-skill`

`[279/280]`

---

## Tweet 8 — both are MIT, both open to fork

Both artifacts are MIT on my GitHub, ready to fork into the Crossmint org tomorrow:

- github.com/0xultravioleta/crossmint-wallets-mcp
- github.com/0xultravioleta/crossmint-cpi-skill

Registry: modelcontextprotocol.io/registry
Skills: lobster.cash/skills

`[263/280]`

---

## Tweet 9 — credit where it's due

Credit where it's due:
- Crossmint shipped the wallets
- lobster.cash shipped the CLI
- Faremeter handles the CPI on the facilitator side

I built the MCP bridge and the skill that explains the quirk.

Stronger together than any one of us alone.

`[258/280]`

---

## Tweet 10 — CTA

If you're on the Crossmint team — DMs open. Let's ship this as `@crossmint/wallets-mcp` and add the skill to the certified directory.

Everything is MIT, everything is tested on mainnet, everything is ready to fork today.

`[232/280]`
