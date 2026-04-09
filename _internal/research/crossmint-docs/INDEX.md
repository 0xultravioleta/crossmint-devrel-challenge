# Crossmint Docs — Research Index

> Fetched via WebFetch in main conversation (docs-scraper agent failed with API 529).
> Date: 2026-04-09

## Site Map (from docs.crossmint.com)

### Main Sections

- **Introduction**
  - `/introduction/about-crossmint` — Platform Overview
  - `/introduction/getting-started` — Create an Account
  - `/introduction/ai-assistants` — AI Assistants

- **Wallets**
  - `/wallets/introduction` — Embedded Wallets
  - `/wallets/overview` — Wallets Overview (live demo)
  - `/stablecoin-orchestration/treasury-wallet/overview` — Treasury Wallets

- **Payments & Onramps**
  - `/stablecoin-orchestration/overview` — Stablecoin Orchestration
  - `/onramp/overview` — Onramps
  - `/payments/introduction` — Token Checkout
  - `/payments/headless/overview` — Agentic Checkout (headless)

- **Tokenization**
  - `/minting/introduction` — Tokenization Tools

- **Solutions**
  - Fintech: `/solutions/fintech/remittances`, `/solutions/fintech/neobanks`, `/solutions/fintech/global-payroll`, `/solutions/fintech/stablecoin-payouts`
  - AI Agents: `/solutions/ai-agents/agent-wallets/overview`, `/solutions/ai-agents/introduction`, `/solutions/n8n/overview`

- **CLI**
  - `/cli/install` — Crossmint CLI

## Files Saved

| Section | URL | Local file | Status |
|---|---|---|---|
| Platform overview | /introduction/platform-overview | introduction/platform-overview.md | saved |
| Wallets overview | /wallets/overview | wallets/overview.md | saved |
| Embedded wallets intro | /wallets/introduction | wallets/introduction.md | saved |
| AI agents intro | /solutions/ai-agents/introduction | agentic/introduction.md | saved |
| Agent wallets overview | /solutions/ai-agents/agent-wallets/overview | agentic/agent-wallets.md | saved |
| Headless checkout | /payments/headless/overview | payments/headless-checkout.md | saved |

## Early Gap Observations

1. **AI Agents intro page does NOT explain** x402, AP2, agent wallets architecture, or delegated signers — despite these being the foundational concepts of agentic commerce.
2. **Agent Wallets overview references x402** as an accepted payment standard but never explains what x402 is or how it works.
3. **Wallets intro does not mention** how smart wallets interact with external payment protocols (x402, Stripe, etc.) or any CPI implications on Solana.
4. **No code examples on agent wallets overview** — the page redirects readers to lobster.cash for a reference implementation instead of showing inline examples.
5. **The "clear 5-minute quickstart" for agents** mentioned in payments/headless points to "USDC checkout in 5 minutes" but the depth of that quickstart is unknown.
