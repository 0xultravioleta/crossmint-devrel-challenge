# Concept Lock — Phase 1 Output

> **Role:** Concept Architect (Claude direct, not a subagent)
> **Date:** 2026-04-09 H+3
> **Status:** LOCKED. Demo Builder may now proceed on README.

---

## The Concept (one sentence)

**"Hello x402: the 5-minute quickstart for paying from a Crossmint smart wallet that docs.crossmint.com should have but doesn't."**

---

## The Opinion Sentence (< 280 chars, Twitter-length rubric)

> *Crossmint's x402 story lives in an 11-demo monorepo and an n8n node. A developer landing on docs.crossmint.com looking for "my first x402 payment" finds nothing. Here's the quickstart that should exist — one repo, one command, one agent payment.*

**263 characters.** Passes the rubric.

---

## The Clear Opinion in the Brief's Template

The brief asks for the template: *"this concept is confusing because X, so I approached it as Y."*

> **X** (the confusion): Crossmint has production-grade x402 support and internal champions (Fede, Alfonso) talking about it publicly, but the official documentation has zero dedicated x402 tutorials. A developer hearing "Crossmint + x402" on Twitter or at the x402 hackathon has three poor options:
> 1. Clone the `crossmint-agentic-finance` monorepo and spelunk through 11 unrelated subdemos
> 2. Read an n8n node reference and try to reverse-engineer the protocol
> 3. Give up and reach for Coinbase's reference implementation instead
>
> **Y** (the approach): A single standalone repo with one command that creates a Crossmint smart wallet on Solana Devnet, pays a paywalled endpoint via x402, and prints the response — in under 5 minutes of wall clock time for a developer who has never touched either product before. The repo is the missing page of the docs, shaped as working code.

---

## Why This Concept Wins

### Against the evaluation axes

| Axis | How the concept hits it |
|---|---|
| **Technical judgment** | Picks a real gap (zero x402 tutorials in docs), addresses it with a focused minimal demo, does NOT try to boil the ocean. The demo is end-to-end runnable, not a toy. |
| **Communication** | A developer can understand "what does x402 + Crossmint mean for me" faster than reading the monorepo or the n8n node reference. The README is literally shorter than both. |
| **Audience instinct** | The target audience is x402 hackathon builders and agentic AI devs — active, specific communities with known distribution channels (x402hackathon Discord, @alfongj, Fede's Twitter, Solana dev Telegrams). |
| **AI fluency** | The submission was orchestrated by a multi-agent team (HR, PM, DA, Docs research) with explicit role separation. Saul uses AI to write, review, and ship — but keeps the opinion and the voice pass as human work. The submission process itself is a signal about how Saul will operate at Crossmint. |
| **Taste** | Small. Focused. Runs. Ships with a clear opinion. No logo branding, no vanity repo name, no self-promotion. Just the page the docs should have, ready to publish. |

### Against the alternative concepts (see decisions.md for full list)

- **Beats "delegated signers explainer"**: that has a bigger gap but requires a full consent-flow UI demo, which is too much scope for 48h.
- **Beats "AI agents intro rewrite"**: pure content, no runnable demo, fails the demo requirement.
- **Beats "Lobster Cash + Execution Market skill"**: Filippos already saw Execution Market, showing it again is stale signal (per DA critique).
- **Beats "wallet-as-a-signer pattern"**: too abstract, no clean "5-minute run it yourself" moment.

---

## Scope Rules (non-negotiable)

### In scope

1. **One chain:** Solana Devnet (not mainnet — avoids funding friction for reviewers).
2. **One wallet type:** Crossmint smart wallet, created via `@crossmint/wallets-sdk`.
3. **One facilitator:** Faremeter (public endpoint `https://facilitator.corbits.dev`). This is Crossmint's preferred facilitator per Filippos's own words in the interview.
4. **One paywalled endpoint:** a toy `/weather` or `/fortune` style endpoint, hosted as part of this repo OR reusing the public one from `crossmint-agentic-finance/weather`.
5. **One command to run the demo:** `pnpm start` (or `npm start`) after `pnpm install` and a single `.env` copy step.
6. **One piece of content:** blog post + Twitter thread, explaining what the demo does and why it matters. No separate video script.
7. **One 1-pager:** campaign proposal aimed at Crossmint's DevRel team, naming real channels and metrics.

### Out of scope (PM veto on any attempt to add)

- Multi-chain support. One chain only. Solana.
- Mainnet examples. Devnet only.
- Multiple facilitator support. Faremeter only, with Ultravioleta as a one-line footnote.
- A new facilitator built from scratch. Nope, we use an existing one.
- A new paywall server written from scratch. Either use `crossmint-agentic-finance/weather` as the paywall or write the tiniest possible Express server with one endpoint.
- UI components. No React, no Next.js, no Vite app. This is a CLI-only demo.
- Screencasts, videos, recorded demos. Text-only submission. (Video is a nice-to-have if time allows in Phase 5, not a commitment.)
- Discussion of Ultravioleta, Execution Market, MeshRelay, or any other Saul projects.
- Any comparison to Coinbase, Alchemy, or competitors. This is constructive, not competitive.
- Any sentence that begins with "seamlessly," "unlock," "empower," or "at scale."

---

## Facilitator Choice: Why Faremeter, Not Ultravioleta

This is the single most important framing decision in the submission.

**Rationale:**

1. **Filippos said in the interview:** *"Facility-wise, Faremeter is the one we want to have in place. It's not like we're exclusive, but we've been focused on that, for example, versus doing a Coinbase one."* This is a direct quote from the debrief. Using Faremeter signals that Saul listened to the interview.
2. **Neutral framing:** If Saul uses his own facilitator, the submission reads as self-promotion. If Saul uses Faremeter, the submission reads as an agnostic Crossmint tutorial.
3. **Reviewer validation:** Faremeter's public endpoint (`facilitator.corbits.dev`) is already in the `crossmint-agentic-finance` repo, so the reviewer can verify it works without needing new infra.
4. **Ultravioleta gets a footnote:** The README will include one sentence at the bottom: *"This demo also works with the Ultravioleta x402 facilitator (`facilitator.ultravioletadao.xyz`), which supports 19 mainnets and is the only public facilitator with CPI inner-instruction scanning for Crossmint smart wallets on Solana."* This plants the seed without making the submission about Saul.

---

## Demo Specification

### What the demo does

1. Reads a Crossmint API key from `.env` (reviewer provides their own, or uses the test key the README explains how to create)
2. Creates a Crossmint smart wallet on Solana Devnet using `@crossmint/wallets-sdk`
3. Funds the wallet with Devnet USDC (via Circle faucet, documented in README)
4. Sends an x402 payment to a paywalled endpoint
   - Primary: the existing `crossmint-agentic-finance/weather` endpoint on Devnet
   - Fallback: a tiny Express server included in the repo
5. Receives the response from the paywalled endpoint
6. Prints the transaction signature, the response content, and a link to the Solana Devnet explorer

### What the demo does NOT do

- Does not implement its own x402 facilitator
- Does not implement its own paywall server (beyond the smallest possible fallback)
- Does not require the reviewer to fund a mainnet wallet
- Does not use any Saul-specific infrastructure (Ultravioleta facilitator is optional footnote)
- Does not deploy anywhere — it is a local CLI

### Stack

| Layer | Choice | Justification |
|---|---|---|
| Runtime | Node.js 20+ | Matches Crossmint's own `fintech-starter-app` (the 120-star flagship) |
| Package manager | pnpm | Matches Crossmint's SDK examples |
| Language | TypeScript | Matches Crossmint's SDK, best type safety for SDK usage |
| Crossmint SDK | `@crossmint/wallets-sdk` | Official, documented in the Node.js quickstart we already fetched |
| x402 client | `@faremeter/sdk` or direct `fetch` with `X-PAYMENT` header | Whichever is simpler and more documented |
| Paywall server | Use existing `crossmint-agentic-finance/weather` if available, else minimal Express | Minimize surface area |
| Env config | `dotenv` | Standard |
| Output | `console.log` + `chalk` (optional) | CLI-only, no UI |

### File structure

```
demo/
├── README.md               # The contract. Written FIRST.
├── package.json
├── pnpm-lock.yaml          # Committed for reproducibility
├── tsconfig.json
├── .env.example
├── .gitignore              # Ignores .env
└── src/
    ├── index.ts            # Main entry point. Creates wallet, pays, prints.
    ├── createWallet.ts     # Isolated: wallet creation flow
    ├── sendPayment.ts      # Isolated: x402 payment flow
    └── (optional) server.ts # Fallback paywall if external one is unreachable
```

### The 5-minute promise: setup steps

1. `git clone <repo>` (20 seconds)
2. `cp .env.example .env` and paste API key (30 seconds + 2 minutes to create key on crossmint.com/console for first-time users)
3. `pnpm install` (60 seconds)
4. `pnpm start` (30 seconds runtime)

Total: ~4 minutes for a first-time Crossmint user, ~2 minutes for someone who already has an API key.

---

## Content Piece Specification

### Format

**Primary: blog post (markdown),** target length 1200-1800 words.
**Secondary: Twitter/X thread (markdown),** 8-12 tweets.

Why blog post as primary: the brief says "pick the format based on the audience you're targeting." For Crossmint's target developer (fintech builders, AI agent devs), the blog post lives forever, is indexable by AI code assistants, and gets shared in Discord + Twitter naturally. The Twitter thread is the distribution layer.

### Structure (blog post)

1. **Hook (100 words).** A developer story: *"I wanted my AI agent to pay for an API call. Crossmint and x402 seemed perfect. Here's what I hit when I tried, and here's the quickstart I wish had existed."*
2. **What x402 is, in 2 sentences.** No more. Anyone who needs more can click through.
3. **Why Crossmint smart wallets are a natural x402 payer.** Gas sponsorship, key rotation, agent-ready permissions. Three bullets max.
4. **The CPI inner instruction nuance (plain English).** Why Solana smart wallets break naive facilitator scans. One paragraph, no Rust code, no assembly. Analogy: "it's like the difference between a check you sign yourself and a check signed by your bank on your behalf — the payment happened, but different signatures show up on the transaction."
5. **The quickstart walkthrough.** Copy-paste commands, expected output, troubleshooting for the one most common failure (wrong Node version or unfunded wallet).
6. **What's next.** One line: *"This is the quickstart the docs should have. If you're the Crossmint team and you want it published, the repo is MIT-licensed and ready to go."*

### Structure (Twitter thread)

Same beats as the blog, but in 8-12 tweets. Each tweet stands alone. Thread ends with a link to the repo.

---

## Campaign Proposal Specification

Delegated to the DevRel Campaign Strategist. Specification:

- **Audience:** Crossmint DevRel team + Alfonso (co-founder, per his `@alfongj` public thesis on agentic commerce).
- **Why:** Fills a real docs gap, validated by a clean site search showing zero x402 tutorials in `docs.crossmint.com`.
- **What:** Publish the quickstart as a dedicated page under `/solutions/ai-agents/x402/` or `/payments/x402/`. Distribute via Crossmint blog + Twitter + x402hackathon Discord.
- **Outcomes (metrics):** time-to-first-successful-x402-payment (from signup), repo clones, hackathon project mentions, x402-related GitHub issues mentioning Crossmint (baseline vs. 30-day post-publication).
- **Length:** 1 page maximum. Coworkers are busy. They decide in 60 seconds.

---

## Next Handoff

### To Demo Builder (directly next)

**Mandate:** Write `demo/README.md` FIRST, before any code. The README is the contract. Every sentence you commit to in the README is a requirement the code must satisfy.

**Success criteria:**
- README has a Prerequisites section (Node 20+, pnpm, Crossmint API key URL, Devnet USDC faucet link)
- README has a 4-step Quickstart section with copy-paste commands
- README has an Expected Output section showing what the developer will see
- README has a Troubleshooting section with the 2-3 most likely failures
- README does NOT mention Ultravioleta, Execution Market, or any non-essential project
- README is under 300 lines

**After README:** write the TypeScript code to make the README truthful. Then write the blog post draft (`content/blog-post.md`). Then the Twitter thread (`content/twitter-thread.md`).

### To DevRel Campaign Strategist

**Mandate:** Write `campaign/proposal.md` as a single-page document. Start as soon as the README draft exists (you need to know what the demo is before you can campaign it).

### To Devil's Advocate (mid-phase critique)

**Mandate:** After the README exists, stress-test it. Try to break the demo purely by reading the README. Find the failure modes before Integration Lead does.

---

## Commitment

This concept is locked at H+3 (concept lock target was H+4, one hour early). No more deliberation. Any attempt to re-open this decision before Phase 5 is scope creep and PM vetoes.

Signed (figuratively): Concept Architect (Claude) + PM approval recorded in decisions.md.
