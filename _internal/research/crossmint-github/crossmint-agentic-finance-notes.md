# crossmint-agentic-finance — Deep Dive

> Source: https://github.com/Crossmint/crossmint-agentic-finance
> Fetched 2026-04-09

## Summary

This is the reference implementation repo showing AI agents making autonomous blockchain payments using Crossmint wallets.

## Glossary (from README)

- **x402**: HTTP payment protocol using `402 Payment Required` status code
- **A2A**: Agent-to-Agent protocol for AI agents to communicate and transact
- **EOA**: Externally Owned Account (traditional wallet controlled by private key)
- **MCP**: Model Context Protocol (standard for AI tool integration)

## Prerequisites

- Node.js 18.17.0+
- npm 9.0.0+
- Crossmint API key (crossmint.com/console)
- Testnet USDC via Circle faucet
- Basic understanding of smart contracts and wallets

## Folder Structure

| Folder | Difficulty | What it is |
|---|---|---|
| `ping` | Beginner | Basic x402 paywall on Base Sepolia (Express server) |
| `weather` | Beginner | Express server with parameterized paywall endpoint |
| `hello-eoa-a2a` | Beginner | Minimal A2A with EOA wallets using **EIP-3009 authorization** |
| `hello-crossmint-wallets-a2a` | Intermediate | A2A demo with Crossmint SDK and direct-transfer verification |
| `ping-crossmint` | Intermediate | React + Express with Crossmint smart wallet UI |
| `ping-faremeter-crossmint-solana` | ? | **Solana Devnet implementation — uses Faremeter facilitator + Crossmint smart wallet** |
| `send-tweet` | Intermediate | Twitter posting agent requiring USDC payment per tweet |
| `cloudflare-agents` | Advanced | Agent-to-agent payments on Cloudflare edge |
| `events-concierge` | Advanced | MCP-based event RSVP with autonomous payments using Durable Objects |
| `worldstore-agent` | Advanced | (submodule) XMTP agent for Amazon purchases with gasless USDC on Base |
| `x402-ad-agent` | Advanced | (submodule) Claude-powered agents bidding for ad space autonomously |

## Key Insight for DevRel Challenge

**`ping-faremeter-crossmint-solana` is the exact demo for Crossmint smart wallet + x402 on Solana.** This is critical because:

1. It exists but is buried in a monorepo with 11 other demos
2. It uses Faremeter as the facilitator (Crossmint's preferred one, per Filippos interview)
3. This is Saul's wheelhouse — his x402 facilitator was the first to ship CPI inner instruction scanning for Solana smart wallets (including Crossmint's)
4. The demo likely does NOT have a dedicated blog post or standalone tutorial explaining it
5. There is no mention of "lobster.cash" in the main README — suggesting lobster.cash is not yet integrated into this repo

## Gap Hypothesis

The crossmint-agentic-finance repo has the building blocks for Crossmint smart wallet + x402 on Solana, but:

- No standalone tutorial walking a developer through the 5-minute "my first x402 payment from a Crossmint smart wallet on Solana"
- No blog post explaining why Solana smart wallets are different (CPI inner instructions)
- No visible standalone repo that a developer can clone + run in 5 minutes for THIS specific use case
- The monorepo structure forces discovery through a README rather than a focused experience

## Technologies Referenced

- x402 HTTP payment protocol
- A2A (Agent-to-Agent)
- EIP-3009 authorization
- Cloudflare Workers
- MCP (Model Context Protocol)
- Solana smart wallets
- Crossmint wallets
- Durable Objects (Cloudflare)
- XMTP messaging
