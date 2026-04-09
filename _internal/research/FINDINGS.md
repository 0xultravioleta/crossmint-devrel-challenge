# Research Findings — Crossmint DevRel Challenge

> Compiled 2026-04-09 from direct WebFetch + WebSearch (docs-scraper + github-explorer agents failed with API 529)

---

## Executive Summary

**The strongest concept candidate is:** *"The missing x402 quickstart for Crossmint smart wallets on Solana."*

Crossmint has world-class x402 support via its agentic-finance repo and n8n integration, but **its official docs (docs.crossmint.com) have zero dedicated x402 tutorial**. The only x402 reference in docs is an n8n node type. Developers who hear "Crossmint + x402" from Fede on Twitter, from a talk, or from the x402 hackathon have nowhere to go to learn how to wire it up themselves.

Saul is uniquely positioned to fill this gap because:
1. He built the CPI inner instruction scanning that makes Crossmint smart wallets work with any x402 facilitator on Solana (upstream x402-rs does not have this code; Saul wrote it for Fede's lobster.cash use case)
2. He operates a public x402 facilitator (facilitator.ultravioletadao.xyz) on 19 mainnets with 5 stablecoins
3. He is a listed mentor at x402hackathon.com
4. He has a direct relationship with Fede (Crossmint DevRel)

---

## Key Gaps in Crossmint Docs

### Gap 1: No dedicated x402 guide

**Evidence:**
- `site:docs.crossmint.com x402` returns only: `solutions/n8n/nodes/wallets` (n8n node type reference) and `payments/headless/quickstarts/paying-usdc` (which is headless checkout, not x402).
- There is no page at `/payments/x402/*` or `/solutions/ai-agents/x402/*`.
- The `crossmint-agentic-finance` repo on GitHub has 11 x402 demos, but they are buried in a monorepo with no dedicated blog post or standalone tutorial per demo.
- The closest official doc is the `ping-faremeter-crossmint-solana` subfolder in the agentic-finance repo — it uses `facilitator.corbits.dev` and a delegated wallet pattern, but there is no walkthrough video, blog, or standalone repo for it.

### Gap 2: AI Agents Introduction page does not explain foundational concepts

**Evidence:** `/solutions/ai-agents/introduction` page does NOT explain: agent wallets architecture, delegated signers, x402, or AP2. It jumps straight to a 4-step workflow (delegate credentials -> create purchase order -> track fulfillment -> deploy). The foundational "what are these things" explanation is absent.

### Gap 3: Agent Wallets overview references x402 without showing code

**Evidence:** `/solutions/ai-agents/agent-wallets/overview` mentions x402 twice as an accepted payment standard ("onchain payments and x402", "credit cards, stablecoins, or x402 are accepted") but provides **no code example** of how an agent wallet would initiate an x402 payment. The page redirects developers to lobster.cash for a reference implementation instead of showing anything inline.

### Gap 4: Delegated Signers page does not explain delegated signers

**Evidence:** `/wallets/guides/delegated-signers` 404s. The signers overview page (`/wallets/core-concepts/signers`) also 404s. The closest existing page explains adding operational signers (passkey, device, external wallet) but does not cover the delegated signer + spending limits flow that would let an agent have scoped access to a user's wallet.

### Gap 5: The lobstercash-cli-skills repo is brand new

**Evidence:** `github.com/Crossmint/lobstercash-cli-skills` was last updated April 1, 2026 (8 days ago). It has 0 stars and no description beyond "lobstercash cli skills". This is a greenfield asset that is the exact category Filippos told Saul in the interview was Crossmint's top priority ("focus on agents, make it compatible for multiple frameworks").

---

## Candidate Concepts (Ranked)

### Candidate 1: "The missing x402 quickstart for Crossmint smart wallets on Solana" (WINNER)

**Gap filled:** No official x402 tutorial in Crossmint docs.

**Demo:** A standalone repo (not a monorepo subfolder) that lets a developer:
1. `pnpm create crossmint-x402-solana` (or similar)
2. Paste a Crossmint API key
3. Run one command
4. Watch a Crossmint smart wallet on Solana send an x402 payment to a paywalled endpoint
5. See the transaction signature and the API response

**Content:** Tutorial + blog post explaining:
- What x402 is in 2 sentences
- Why Crossmint smart wallets are the best x402 payers
- The CPI inner instruction nuance (simply explained)
- The 5-minute quickstart walkthrough

**Campaign:** Target Solana + agent builders via x402 hackathon Discord, Crossmint blog, Twitter thread.

**Why this wins:** Fills a real gap. Leverages Saul's unique position. Has a clear opinion ("your x402 story is incomplete without this quickstart"). Is runnable in 5 minutes. Matches all 5 evaluation axes.

### Candidate 2: "The AI Agents intro page that does not actually introduce AI agents"

**Gap filled:** Gap #2 above.

**Demo:** Rewrite of the intro page with inline explanations + interactive examples.

**Rejected because:** Pure content, no runnable code. Challenge says deliver 3 artifacts, and a docs rewrite makes the campaign pitch feel weak ("we need to ship a content piece" is less compelling than "we need to publish a working tutorial").

### Candidate 3: "The lobster.cash skill for Execution Market" (Filippos interview suggestion)

**Gap filled:** Net-new product proposal.

**Demo:** Working lobster.cash skill that posts bounties to Execution Market from OpenClaw agents.

**Rejected because:** Filippos already knows about Execution Market. Showing it in the challenge is redundant. Also, lobster.cash skill format is not publicly documented, so building it blind risks failing the "accurate and useful" axis.

---

## Saul's Asset Map

| Asset | Relevance to Candidate 1 | Notes |
|---|---|---|
| x402 facilitator (facilitator.ultravioletadao.xyz) | HIGH | Public, 19 mainnets, 5 stablecoins, includes Solana |
| CPI inner instruction scanning (solana.rs) | HIGH | Only implementation that supports Crossmint smart wallets on Solana via x402 |
| Settlement account support | MEDIUM | Specifically for Crossmint custodial wallets |
| x402hackathon mentor | HIGH | Credibility + distribution channel |
| Ultravioleta DAO streaming | MEDIUM | Distribution channel for Spanish-speaking devs |
| CryptoFede relationship | HIGH | Internal champion at Crossmint DevRel |
| Execution Market | LOW (for this submission) | Better left for post-hire |
| MeshRelay | LOW (for this submission) | Tangential |

---

## References

Docs pages fetched:
- https://docs.crossmint.com/ (landing)
- https://docs.crossmint.com/wallets/overview
- https://docs.crossmint.com/wallets/introduction
- https://docs.crossmint.com/wallets/quickstarts/nodejs (Node.js quickstart)
- https://docs.crossmint.com/solutions/ai-agents/introduction
- https://docs.crossmint.com/solutions/ai-agents/agent-wallets/overview
- https://docs.crossmint.com/payments/headless/overview

GitHub pages fetched:
- https://github.com/Crossmint (org listing)
- https://github.com/Crossmint/crossmint-agentic-finance
- https://github.com/Crossmint/crossmint-agentic-finance/tree/main/ping-faremeter-crossmint-solana

Searches:
- `site:docs.crossmint.com x402` (Google)

---

## Next Steps

1. Write hr-proposal.md (team composition)
2. Write pm-journal.md + decisions.md (ruthless PM review)
3. Write devils-advocate/kickoff.md (critique of plan)
4. Lock the concept in decisions.md
5. Start Phase 2 (demo development) with specialist agents or direct Claude execution
