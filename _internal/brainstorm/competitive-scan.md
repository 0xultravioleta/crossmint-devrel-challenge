# Competitive Scan — Crossmint DevRel Challenge

> Compiled 2026-04-09 by Competitive Scanner agent
> Mission: map what already exists in the public domain for "Crossmint + agents + x402 + Solana + MCP + AI-native DevRel content" so the team picks a NET NEW concept.

---

## 1. Executive Summary

**The white space is narrower than the FINDINGS.md hypothesized.**

Three things changed the picture since FINDINGS.md was written:

1. **A relai.fi blog post titled "Solana Smart Wallet Payments with x402" already exists** (March 2, 2026, by web3luka). It explicitly names Crossmint as a supported smart wallet provider and shows the integration code for `@relai-fi/x402@0.5.19`. It is NOT a 5-minute quickstart — it is an architecture explainer — but it does occupy the "Solana smart wallet + x402 + Crossmint" topic in Google.

2. **Solana's official x402 guide** (`solana.com/developers/guides/getstarted/intro-to-x402`) explicitly states "Crossmint is currently in progress" for x402, which means the Solana Foundation is publicly waiting for Crossmint's official tutorial. This is a direct invitation.

3. **lobster.cash already has a published skill ecosystem with 12 certified skills** (lobster.cash/skills) and `clawcredit-lobster` is a real, published, working skill from t54-labs. The "build a lobster.cash skill" white space is already partially closed — but importantly, **none of the 12 skills target a developer-facing x402 quickstart for the Crossmint smart wallet itself**, and the lobster.cash skill format is a documented developer surface (`/skills` page advertises "Developing Agent Skills? Get Paid").

**Net conclusion:** The strongest white space is no longer "x402 quickstart for Crossmint smart wallets on Solana" in the abstract — that topic now has a relai.fi blog post. The strongest white space is one of three sharper angles:

- **(A) The official 5-minute clone-and-run repo** that the Solana Foundation guide is explicitly waiting for ("Crossmint is currently in progress")
- **(B) A Crossmint MCP server for agent wallets** — the only existing Crossmint MCP server is `mcp-crossmint-checkout` (1 star, last updated May 2025, only does Amazon/Shopify purchases via Crossmint credits — does NOT expose smart wallet primitives, does NOT support x402, does NOT support Solana)
- **(C) A lobster.cash skill that bridges OpenClaw agents to a public x402 facilitator on Solana** (e.g. Saul's facilitator), filling the gap that the existing 12 skills don't cover — namely, paying ANY x402-paywalled API from a lobster.cash wallet

The recommendation at the end of this document picks among these three.

---

## 2. Scan 1 — Crossmint MCP Landscape

### Findings

**Official Crossmint MCP server: exists, but is narrow and stale.**

- Repo: https://github.com/Crossmint/mcp-crossmint-checkout
- Stars: **1**
- Last commit: **May 2, 2025** (≈11 months stale as of April 2026)
- Scope: takes an item identifier, executes a purchase via Crossmint credits, defaults to Amazon (US Prime) and Shopify
- **Does NOT** expose smart wallet creation, signing, balance, or transfer primitives
- **Does NOT** mention Solana
- **Does NOT** mention x402
- No accompanying blog post or tutorial

**There is no general-purpose Crossmint MCP server** that exposes the Crossmint Wallets API as MCP tools (createWallet, getBalance, signTransaction, addDelegatedSigner, etc.). The existing one is checkout-only and based on the legacy "Crossmint credits" abstraction, not the smart wallet abstraction Crossmint now markets as the agent primitive.

**Other agent framework integrations for Crossmint:**

| Framework | Crossmint integration exists? | Source |
|---|---|---|
| **GOAT SDK** | YES — `@goat-sdk/wallet-crossmint` includes `SolanaSmartWalletClient.ts`. **Does NOT support x402 in this client** (verified by reading the file — no `402`, `payment`, or `paymentRequired` references) | https://github.com/goat-sdk/goat |
| **LangChain** | YES — via GOAT plugin (`python/examples/langchain/crossmint/solana_smart_wallet_example.py`). NFT minting only, not x402. | github.com/goat-sdk/goat |
| **Eliza (ElizaOS)** | YES — via `@elizaos/plugin-goat`. Inherits GOAT's Crossmint support. No dedicated x402 integration. | npmjs.com/package/@elizaos/plugin-goat |
| **CrewAI** | NO direct Crossmint plugin found. GOAT SDK is the bridge. | — |
| **AutoGen** | NO direct Crossmint plugin found. | — |
| **OpenClaw** | YES — via lobster.cash (Crossmint's purpose-built integration). | lobster.cash |
| **MCP** | Only `mcp-crossmint-checkout` (stale, narrow). No agent-wallets MCP server. | github.com/Crossmint/mcp-crossmint-checkout |

### Conclusion

**The Crossmint agent-wallets MCP space is wide open.** A modern MCP server that exposes Crossmint smart wallets (Solana included) as MCP tools — including x402 payment as an MCP tool — does not exist. The only existing MCP server is checkout-credits-only, has 1 star, and has not been touched since May 2025.

---

## 3. Scan 2 — lobster.cash and OpenClaw Implementations

### Findings

**lobster.cash is a real, shipped product with 12 certified skills.** Source: https://www.lobster.cash/skills

| # | Skill | Function | x402? |
|---|---|---|---|
| 1 | BlockRun | Pay-per-request AI model access | references x402 |
| 2 | ClawCredit (t54) | Credit lines repaid from lobster.cash wallet | "x402-powered" |
| 3 | Clawpay | Payment requests / checkout URLs | — |
| 4 | ClawPump | Token launches on Solana | — |
| 5 | Jupiter | DeFi swaps / lending | — |
| 6 | Lando | Subscription URLs via Tributary | — |
| 7 | OOBE | Multi-agent coordination on Solana | "x402-powered agent-to-agent payments" |
| 8 | OpenTask | Agent marketplace | — |
| 9 | Purch | E-commerce checkout | — |
| 10 | Seedstr | Task marketplace | — |
| 11 | Unbrowse | API-native browser automation (mentions x402-gated routes via lobster.cash) | yes |
| 12 | LI.FI | Cross-chain routing | — |

**Open SDK question:** lobster.cash advertises a "Developing Agent Skills? Get Paid" page and a "Skill Compatibility Guide", but the skill SDK / format documentation could not be retrieved publicly. This matches Saul's earlier observation in FINDINGS.md (Candidate 3) that "lobster.cash skill format is not publicly documented."

**Verified existing assets:**

- `clawcredit-lobster` skill: published on https://lobehub.com/skills/t54-labs-lobster-cash-skill, sourced from `t54-labs/clawcredit-sdk` on npm, links ClawCredit credit lines to lobster.cash wallets via micro-transfer wallet proof + repayment.
- `@crossmint/lobster.cash` is a published npm package — confirms an SDK surface exists even if the skill format itself is undocumented externally.
- `openclaw/lobster` is a separate workflow shell for OpenClaw — **NOT a payment integration**. It is a typed pipeline/macro engine. Confirmed via direct repo read: zero references to Crossmint, x402, Solana, payments, or wallets.

**OpenClaw + Crossmint general state:**

- Cointelegraph announcement (Dec 2025?): "Crossmint introduces lobster.cash as open payment standard enabling secure transactions for OpenClaw agents, powered by Visa, Solana, Circle and Stytch"
- This is the only OpenClaw <-> Crossmint integration. It IS lobster.cash.
- BlockRunAI's `ClawRouter` (https://github.com/BlockRunAI/ClawRouter) routes LLM requests to cheapest model and accepts USDC via x402 on Base or Solana — **does not specify which facilitator** and **does not mention Crossmint**. Framework-agnostic with an OpenClaw plugin.

### Conclusion

The lobster.cash skill ecosystem is real and crowded with 12 skills, but **there is no skill that specifically pays an arbitrary x402-paywalled endpoint on Solana through a third-party facilitator** (the existing skills are first-party services like ClawCredit, BlockRun, Jupiter — not generic x402 payment skills). The "lobster.cash skill that consumes any x402 API from any facilitator" is a real white space, but it is gated by the undocumented skill format.

---

## 4. Scan 3 — x402 + Crossmint Solana Tutorials

### Findings

**Tutorials that exist:**

| Source | Title | Crossmint? | Solana? | Smart wallet? | 5-min quickstart? |
|---|---|---|---|---|---|
| **relai.fi blog (Mar 2 2026)** | "Solana Smart Wallet Payments with x402" by web3luka | **YES — explicitly names Crossmint** | YES | YES (lists Squads, MPC clusters incl. Crossmint, SWIG, PDAs) | **NO — architecture explainer, not runnable in 5 min** |
| **solana.com/developers** | "How to get started with x402 on Solana" | NO ("Crossmint is currently in progress") | YES | NO (raw Keypair / EOA only) | NO (manual Express.js + verification flow) |
| **blog.crossmint.com** | "What is x402?" (Oct 2 2025, updated Dec 4 2025) | n/a | NO | n/a | NO — conceptual post, no code |
| **blog.crossmint.com** | "Introducing Solana Embedded Smart Wallets" (Feb 20 2025) | YES | YES | YES | **Does NOT mention x402 at all** |
| **crossmint-agentic-finance** monorepo | `ping-faremeter-crossmint-solana` subfolder | YES | YES | YES (delegated wallet pattern) | NO — buried in monorepo, no standalone repo, no walkthrough |
| **crossmint.com/quickstarts** | "Agentic commerce on Base mini-app + XMTP" | YES | NO (Base/Eth/Polygon/Arbitrum only) | Embedded wallet | YES (5 min) — but Base only, not Solana |
| **`x402-solana` npm** (separate package) | Reusable x402 v2 implementation for Solana | provider-agnostic | YES | works with any wallet | n/a — library, not a tutorial |

**Critical observation:** The relai.fi post is the closest thing to an existing "Crossmint smart wallet + x402 + Solana" tutorial, and it lists Crossmint as one of six supported smart wallet providers — NOT as the headline. It also requires the developer to bring their own wallet integration. It is **explicitly NOT a 5-minute quickstart**.

The Solana Foundation's own developer guide says **"Crossmint is currently in progress"** under x402 SDK support for Solana — this is a public, dated, on-the-record statement that the canonical Crossmint x402 Solana SDK / quickstart does not yet exist.

The crossmint-agentic-finance monorepo's `ping-faremeter-crossmint-solana` exists but uses Faremeter (`facilitator.corbits.dev`), is a subfolder (not a standalone repo), and has no blog post or video walkthrough.

### Conclusion

**The 5-minute clone-and-run quickstart that the Solana Foundation is publicly waiting for does NOT exist.** The relai.fi post is provider-agnostic and architecture-focused. Crossmint's own blog has a "what is x402" conceptual post and a "Solana smart wallets" announcement, but **these two posts have never been merged into a single Crossmint-branded x402-on-Solana-smart-wallet quickstart**.

This is a real, narrow, time-sensitive gap.

---

## 5. Scan 4 — Agentic Commerce State of the Art (April 2026)

### x402 Hackathons in 2026

| Event | Date | Notable winners |
|---|---|---|
| **SF Agentic Commerce x402 Hackathon** | Q1 2026, 324 hackers, 86 builds, 275K+ on-chain txs | 1st: World of Geneva (MMORPG with autonomous agents), 2nd: Moltbet (prediction market for agents) |
| **SF SKALE track** | 2nd: Pixie (trustless infra for agents) | |
| **SF Coinbase track** (Agentic Tool Usage on x402) | 1st: **Superpage** (autonomous payments in real-world commerce), 2nd: Pincer (ad subsidies as task subsidies) | |
| **SF Virtuals track** (Trading/DeFi/Agents) | 1st: Moltbet, 2nd: **Legasi** (credit + reputation layer for agents) | |
| **SF Google track** (AP2 Integration) | 1st: **RequestTap — "Open Source x402 Router for AI Agents"**, 2nd: Civiclens | |
| **Cronos x402 PayTech Hackathon** | 191 teams globally | (with Crypto.com) |
| **Algorand x402 Ideathon Berlin** | March 5 2026, 45 builders, 15 teams | micropayment infra + autonomous AI agent transactions |
| **Berlin Agentic Commerce Hackathon** | April 10-12 2026 | (upcoming as of writing) |

### Submission categories observed at the SF hackathon

- Autonomous gameplay agents (MMORPG)
- Prediction markets for agents (programmatic, no GUI)
- Credit + reputation layers for agents
- Ad-subsidy economics
- LLM routing (cheap model selection) — multiple teams (RequestTap, Pincer, ClawRouter)
- Real-world commerce (Amazon, retail) via agents
- Civic / public-goods rewards
- Trustless agent infrastructure (Pixie)

### ERC-8004 trustless agents

- **ERC-8004 went live on Ethereum mainnet on January 29, 2026** — three lightweight registries (Identity, Reputation, Validation)
- Authors: Marco De Rossi, Davide Crapis, Jordan Ellis, Erik Reppel
- DevConnect Buenos Aires (Nov 2025) hosted a "Trustless Agents Day" with prototypes for DeFi trading, code review, and gaming
- **No Crossmint involvement found** in ERC-8004 implementations
- This is also the focus of Saul's `add-erc8004-network` skill in his x402-rs facilitator — Saul has direct ERC-8004 backend experience

### Conclusion

**By April 2026 the field is crowded.** Three categories are saturated: LLM routing (RequestTap, ClawRouter, x402-OpenRouter), agent-to-agent payment demos (lots of monorepo subfolders), and "trustless agent" reputation layers (ERC-8004 implementations). The categories that still feel underdeveloped:

- **Developer-facing 5-minute quickstarts** (most submissions are end-user demos or infra, not "clone this repo and your agent pays an x402 endpoint in 60 seconds")
- **MCP servers that expose wallet primitives** (the few existing MCP servers like Phantom's are read-only or checkout-only)
- **Cross-framework parity** (most demos lock to one framework — there is no "same Crossmint x402 example, written four ways for LangChain, CrewAI, Eliza, and raw MCP")

---

## 6. White Space Map — Things That DO NOT Exist

A 48h submission could fill any of these:

- **WS1.** A standalone, runnable-in-5-minutes repo at `npx create crossmint-x402-solana` (or similar) that scaffolds a Crossmint smart wallet on Solana and pays a paywalled endpoint via x402. *(The Solana Foundation is publicly waiting for this.)*
- **WS2.** A Crossmint MCP server that exposes the Wallets API as MCP tools — `create_wallet`, `get_balance`, `transfer`, `pay_x402_endpoint`, `add_delegated_signer`. (The only existing Crossmint MCP server is the stale checkout one.)
- **WS3.** A blog post titled "Why Solana smart wallets need CPI inner instruction scanning to pay x402" — explaining the technical reason Crossmint smart wallets require special facilitator handling, and showing Saul's facilitator as the working implementation. (No public post explains this nuance.)
- **WS4.** A lobster.cash skill that pays ANY x402 endpoint via a configurable facilitator (rather than first-party services like ClawCredit/BlockRun). *(Gated by undocumented skill format — risky.)*
- **WS5.** A four-framework parity demo: the same Crossmint smart wallet + x402 payment, written for LangChain, CrewAI, Eliza, and a raw MCP client. (Framework lock-in is the dominant pattern in existing demos.)
- **WS6.** A Crossmint MCP server that wraps the lobster.cash wallet specifically — bridging the OpenClaw / lobster.cash ecosystem to the broader MCP ecosystem (Claude Code, Cursor, Continue.dev, etc.).
- **WS7.** A "Crossmint smart wallet pays an ERC-8004 registered agent" demo — combining the two newest standards (ERC-8004 mainnet Jan 29 2026 + Crossmint smart wallets). No such demo exists publicly. Plays directly to Saul's `add-erc8004-network` facilitator skill.
- **WS8.** A `npx create-crossmint-mcp` scaffold that bootstraps an MCP server that uses Crossmint smart wallets — plus a tutorial on how to publish that server to the MCP Registry.

## 7. Saturation Warnings — Things That DO Exist (Don't Duplicate)

- **SAT1.** Conceptual "What is x402" blog posts (Crossmint already has one, plus dozens elsewhere). DO NOT write another explainer.
- **SAT2.** Generic "Solana smart wallets explained" posts (Crossmint announcement post Feb 2025, Helius blog, alchemy blog). DO NOT recap this.
- **SAT3.** Solana smart wallet + x402 architecture explainer — the **relai.fi post (Mar 2 2026)** already covers this, including Crossmint as a supported provider. DO NOT publish a similar architecture-only post.
- **SAT4.** A monorepo subfolder demo for x402 + Crossmint + Solana — **`ping-faremeter-crossmint-solana` already exists** in crossmint-agentic-finance. DO NOT add another monorepo subfolder.
- **SAT5.** An LLM router that uses x402 micropayments — **RequestTap, ClawRouter, x402-OpenRouter** all exist. SF hackathon winner already shipped this.
- **SAT6.** A credit/reputation layer for agents — **Legasi, ClawCredit** already exist.
- **SAT7.** A prediction market for agents — **Moltbet** already exists.
- **SAT8.** An autonomous Amazon-buying agent — **Crossmint's worldstore-agent** already exists, plus the checkout MCP server.
- **SAT9.** GOAT SDK Solana smart wallet examples (NFT mint, etc.) — already in `goat-sdk/goat`.
- **SAT10.** Eliza plugin for onchain payments — `@elizaos/plugin-goat` already wraps Crossmint via GOAT.
- **SAT11.** A "first lobster.cash skill" demo — **clawcredit-lobster** by t54-labs is already published on lobehub. The "first skill" framing is taken.
- **SAT12.** "Agentic commerce on Base in 5 minutes" — Crossmint already publishes this quickstart for the Base mini-app + XMTP path.

---

## 8. Recommendation

**Combine WS1 + WS2 + (touch of WS3) into a single submission with three artifacts.**

The single highest-leverage target is:

> **"Crossmint Smart Wallet x402 MCP Server + 5-minute Solana quickstart"**

A submission with three artifacts:

1. **Artifact 1 — Standalone repo `crossmint-x402-solana-quickstart`** (fills WS1).
   `npx create crossmint-x402-solana@latest my-app` -> paste Crossmint API key -> `pnpm dev` -> Crossmint smart wallet on Solana pays an x402-paywalled endpoint and prints the tx signature. Uses Saul's facilitator (which has the only working CPI inner instruction scanning for Crossmint smart wallets on Solana). 5 minutes wall-clock.

2. **Artifact 2 — `crossmint-mcp-wallets` MCP server** (fills WS2).
   An MCP server that exposes Crossmint smart wallet primitives as tools: `create_wallet`, `get_balance`, `transfer_sol`, `transfer_spl`, **`pay_x402_endpoint`**, `add_delegated_signer`. Works in Claude Code, Cursor, Continue.dev. The `pay_x402_endpoint` tool is the killer feature — it makes Crossmint smart wallets agent-callable from any MCP client without writing code.

3. **Artifact 3 — Blog post + 60-second video**: "How I made my MCP client pay an x402 API with a Crossmint smart wallet on Solana in 5 minutes" — touching on WS3 (the CPI inner instruction nuance) only as a footnote / "why this matters" sidebar, not as the headline.

**Why this is the best target:**

- **It fills the Solana Foundation's publicly stated gap** ("Crossmint is currently in progress" for x402 on Solana). Filippos at Crossmint can cite the Solana Foundation guide as the gap, and Saul's submission as the answer.
- **It is NOT redundant.** The relai.fi post is provider-agnostic architecture; this is Crossmint-branded runnable code. The crossmint-agentic-finance monorepo has a similar demo, but buried in a subfolder with no MCP layer and no standalone install. The existing Crossmint MCP server (`mcp-crossmint-checkout`) is checkout-only and stale.
- **It hits five evaluation axes simultaneously**: runnable demo (artifact 1), MCP-native AI tooling (artifact 2), narrative content (artifact 3), uses Crossmint's preferred Solana stack, and demonstrates a NET NEW capability (an MCP-callable smart wallet that pays x402).
- **It plays to Saul's unique assets**: facilitator with CPI inner instruction scanning, x402hackathon mentor credibility, ERC-8004 backend experience, public testnet+mainnet facilitator on 19 chains. No one else can ship this in 48 hours.
- **It is forward-compatible with Filippos's stated priority** ("focus on agents, make it compatible for multiple frameworks"): the MCP server is by definition framework-agnostic — works in Claude Code, Cursor, Continue.dev, Cline, and any other MCP client. This is structurally better than locking to one of LangChain/CrewAI/Eliza.

**Secondary options (in case primary is rejected):**

- **Plan B**: WS7 (Crossmint smart wallet pays an ERC-8004 registered agent on Saul's facilitator). Riskier because ERC-8004 audience is small and the demo needs a fake "agent" on the receiving end. But uniquely defensible because Saul has the `add-erc8004-network` skill and the facilitator already supports it.
- **Plan C**: WS5 (four-framework parity demo). Lower technical risk, less impressive narrative. Use only if MCP path is blocked.

**Things to explicitly avoid in the submission** (per the saturation warnings):

- Do not write another "what is x402" explainer
- Do not add another subfolder to crossmint-agentic-finance
- Do not pitch a lobster.cash skill (gated by undocumented format + clawcredit-lobster already exists)
- Do not pitch an LLM router (RequestTap won this category at the SF hackathon)
- Do not pitch a credit/reputation layer (Legasi, ClawCredit own this)
- Do not pitch an Amazon-buying agent (worldstore-agent + mcp-crossmint-checkout cover this)

---

## Sources

### Crossmint properties
- [Crossmint MCP Checkout Server (the only existing Crossmint MCP server)](https://github.com/Crossmint/mcp-crossmint-checkout)
- [crossmint-agentic-finance monorepo](https://github.com/Crossmint/crossmint-agentic-finance)
- [awesome-crossmint](https://github.com/Crossmint/awesome-crossmint)
- [Crossmint blog: What is x402?](https://blog.crossmint.com/what-is-x402/)
- [Crossmint blog: Introducing Solana Embedded Smart Wallets](https://blog.crossmint.com/solana-embedded-smart-wallets/)
- [Crossmint quickstart: Agentic commerce on Base + XMTP](https://www.crossmint.com/quickstarts/agentic-commerce-base-mini-app-x402)
- [Agent Wallets compared (Crossmint Learn)](https://www.crossmint.com/learn/agent-wallets-compared)
- [Agentic payments protocols compared (Crossmint Learn)](https://www.crossmint.com/learn/agentic-payments-protocols-compared)
- [npm @crossmint/lobster.cash](https://www.npmjs.com/package/@crossmint/lobster.cash)

### lobster.cash + OpenClaw
- [lobster.cash skills index](https://www.lobster.cash/skills)
- [clawcredit-lobster skill on lobehub](https://lobehub.com/skills/t54-labs-lobster-cash-skill)
- [t54-labs/clawcredit-sdk on npm](https://www.npmjs.com/package/@t54-labs/clawcredit-sdk)
- [openclaw/lobster (NOT a payment integration — workflow shell)](https://github.com/openclaw/lobster)
- [BlockRunAI/ClawRouter (LLM router via x402)](https://github.com/BlockRunAI/ClawRouter)
- [Cointelegraph: Crossmint introduces lobster.cash for OpenClaw](https://x.com/Cointelegraph/status/2022159044941676895/photo/1)

### x402 + Solana tutorials (existing)
- [relai.fi: Solana Smart Wallet Payments with x402 (March 2 2026)](https://relai.fi/blog/solana-smart-wallet-payments-x402)
- [Solana developers: How to get started with x402 on Solana](https://solana.com/developers/guides/getstarted/intro-to-x402)
- [x402-solana on npm](https://www.npmjs.com/package/x402-solana)

### GOAT SDK
- [goat-sdk/goat](https://github.com/goat-sdk/goat)
- [GOAT Solana smart wallet TS client](https://github.com/goat-sdk/goat/blob/main/typescript/packages/wallets/crossmint/src/wallets/SolanaSmartWalletClient.ts) — verified no x402 support
- [GOAT LangChain Crossmint Solana smart wallet example](https://github.com/goat-sdk/goat/blob/main/python/examples/langchain/crossmint/solana_smart_wallet_example.py)
- [@elizaos/plugin-goat (Eliza framework -> GOAT -> Crossmint)](https://www.npmjs.com/package/@elizaos/plugin-goat)
- [Introducing GOAT (Crossmint blog)](https://blog.crossmint.com/introducing-goat-great-onchain-agent-toolkit/)

### Hackathons + ecosystem
- [SF Agentic Commerce x402 Hackathon recap (SKALE)](https://blog.skale.space/blog/san-francisco-agentic-commerce-x402-hackathon-recap-winners)
- [DoraHacks SF x402 hackathon BUIDL list](https://dorahacks.io/hackathon/x402/buidl)
- [Algorand x402 Ideathon Berlin recap](https://algorand.co/blog/x402-ideathon-berlin-recap-web3-builders-exploring-agentic-commerce)
- [Cronos x402 AI Hackathon winners](https://blockchainreporter.net/cronos-reveals-x402-ai-hackathon-winners-in-collaboration-with-crypto-com)

### ERC-8004
- [ERC-8004 EIP](https://eips.ethereum.org/EIPS/eip-8004)
- [awesome-erc8004 list](https://github.com/sudeepb02/awesome-erc8004)

### MCP general
- [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
- [awesome-crypto-mcp-servers](https://github.com/badkk/awesome-crypto-mcp-servers)

---

## Uncertainty Flags

- **lobster.cash skill format**: I could not retrieve the public skill SDK / format documentation. The "Skill Compatibility Guide" link exists but its contents were not fetched. This is a known unknown — building a lobster.cash skill blind has implementation risk.
- **lobster.cash article on crossmint.com/learn (agent-wallets-compared)**: WebFetch returned 403 (blocked). The structured comparison data above is from search snippets only, not the full article. There may be additional Crossmint product details I missed.
- **Crossmint internal/private MCP work**: I cannot see private repos. Filippos may already have an MCP server in development that I cannot detect publicly. This is the highest residual risk for the recommendation — Saul should ask Filippos directly: "is anyone at Crossmint already shipping a wallets MCP server?" before locking the concept.
- **`ping-faremeter-crossmint-solana` README contents**: I did not re-fetch the subfolder's README this round. If it has been recently updated to include a standalone install path, it may close more of WS1 than I assumed.
- **The `crossmint-402-starter` repo**: appeared in one search result with the URL `github.com/Crossmint/crossmint-402-starter` but the canonical name is `crossmint-agentic-finance`. May be a redirect/rename or a brand-new repo. Worth verifying directly.
