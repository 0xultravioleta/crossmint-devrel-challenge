# Wild Ideas — Crossmint DevRel Challenge

> **Role:** Wild Innovator. No self-censorship. Feasibility is somebody else's job.
> **Date:** 2026-04-09
> **Mandate:** 10 brutally ambitious concepts. Pitch them like Alfonso Gomez-Jordana is in the room.
> **Budget:** ~26 hours of execution inside 48h.

---

## Executive Summary — The 2026 DevRel Submission Thesis

A 2026 Crossmint DevRel submission cannot be a quickstart. Quickstarts are the product of a 2024 DevRel function. In 2026, every developer who lands on docs.crossmint.com is being pre-filtered by a Claude/Cursor/Codex/Devin sub-agent that already read the docs for them. The artifact that wins is the one **the agent reaches for**, not the one the human reads. So the submission must do exactly one of three things at industrial fidelity: (1) become a piece of agent infrastructure that Crossmint does not yet own (an MCP server, a Skill, a Claude Code plugin), (2) make a Crossmint primitive *demonstrably better than the competitor's* in a side-by-side video, or (3) reframe a Crossmint product as a primitive in a category Crossmint has not claimed yet (agentic identity, agent-to-agent payments, cross-org delegation). The boring but defensible move is #1. The audacious move is #3. Pick one with a clear opinion that Alfonso would screenshot. Everything else loses to "I already saw this from Saul."

---

## The 10 Ideas

---

### Idea 1 — `crossmint-mcp` — The Crossmint MCP server that Crossmint did not build

**Opinion:** *Crossmint's `lobstercash-cli-skills` repo is the right idea in the wrong shell — CLI skills are 2024 ergonomics, MCP servers are how 2026 agents discover tools. Crossmint's docs are a wallet for agents that can't read the docs, so I built the MCP server that turns every docs page into a callable tool.*

**What it does (3 bullets):**
- Exposes the entire Crossmint API (wallets, checkout, x402 paywall config, delegated signers, NFT minting, on/off-ramp, smart wallet creation) as MCP tools, auto-generated from the OpenAPI spec, tested live against Devnet.
- Includes a "docs grounding" RAG layer so when an agent calls `crossmint_help("how do I add a delegated signer with a spending limit?")`, it returns a working code snippet, not a marketing page.
- Ships with a ready-to-paste `.mcp.json` for Claude Code, Cursor, and Codex CLI, and a 60-second installer that lets a developer say `claude "create me a Solana smart wallet on devnet, fund it 10 USDC, then pay this x402 endpoint"` and watch it actually work.

**Crossmint products used:** Wallets SDK, Headless Checkout, x402 endpoints, Delegated Signers, On-ramp, Verifiable Credentials.

**Saul assets leveraged:** AutoJob MCP server experience (`autojob.cc/mcp`), Claude Code 1.4M-line operator credibility, Solana CPI knowledge for the smart-wallet tools, OpenClaw integration to demo agent-side consumption.

**Why this is NOT stale (vs Hello x402):** The `crossmint-agentic-finance` repo is a *human* surface area. There is no `crossmint-mcp` on GitHub.com/Crossmint. Filippos literally said "we need someone to focus on agents." An MCP server *is* the way agents now consume products. This is the missing primitive on top of every Crossmint product, not on top of one.

**Build time:** 18-22 hours (auto-generation from OpenAPI is the trick that makes it doable).

**Wow factor:** 9/10.

**Failure mode:** Crossmint's OpenAPI spec is incomplete or auth model can't be cleanly bridged into MCP, forcing manual tool definitions for the top 12 endpoints. Could blow the time budget.

---

### Idea 2 — `crossmint-skill` for Claude Code / Cursor / Codex — The agent-native onboarding

**Opinion:** *Crossmint thinks "developer reads docs, writes integration code, ships." Real 2026 developers say "Claude, integrate Crossmint" and tab over to Twitter. The first product Crossmint should ship into agent IDEs is a Skill that knows how to do the entire integration flow itself.*

**What it does (3 bullets):**
- A Claude Code Skill (and equivalent Cursor `.cursorrules` + Codex skill) that takes a user prompt like "add a Crossmint smart wallet to my Next.js app with delegated signer for an AI agent" and emits a working PR — environment variables, SDK install, wallet creation, auth flow, x402 paywall, all wired up.
- Includes a `verify` subcommand that runs the integration against Devnet end-to-end and returns a transaction signature as proof of liveness. No "looks good to me" — actual on-chain receipt.
- Demoed on video against three scaffolded apps (Next.js, FastAPI, Hono) — "watch me Crossmint-ify three apps in 60 seconds each." Then it ships as a public skill the Crossmint team can fork.

**Crossmint products used:** Wallets SDK, Headless Checkout, Delegated Signers, x402.

**Saul assets leveraged:** `.agents/skills/` and `.claude/skills/` patterns from consultoria-x and x402-rs (`add-network`, `add-erc8004-network`, `stablecoin-addition`), Claude Code Skills authoring expertise, OpenClaw/Karmakadabra fluency.

**Why this is NOT stale (vs Hello x402):** Filippos said in the interview *"I built this skill the other day... I tried the skill and my agent paid."* Skills are how Crossmint's leadership thinks about distribution now. There is **no public Crossmint Claude Code Skill**. This is the gap inside the gap.

**Build time:** 14-16 hours.

**Wow factor:** 8/10.

**Failure mode:** The Skill format is shallow and reads like a glorified system prompt — fails the "taste" axis. Mitigation: include the verify subcommand and the live video.

---

### Idea 3 — `xmint` — The 60-second wallet provisioning daemon for AI coding agents

**Opinion:** *Every coding agent (Claude Code, Codex, Devin, Cursor agent) hits the same wall the moment it needs to test a payment flow: it needs a wallet, it needs funds, it needs an address it can throw away. I built the side-loaded daemon that gives any local agent a fresh, funded, disposable Crossmint smart wallet in 60 seconds, hands-free.*

**What it does (3 bullets):**
- A tiny background daemon (`xmint serve`) that exposes a localhost HTTP endpoint where an agent can `POST /wallets/ephemeral` and get back a brand-new Crossmint smart wallet on Solana Devnet with 10 USDC pre-funded.
- Caches the wallet, exposes it as a `tools.wallet.payX402(url)` action via MCP, and auto-collects unused wallets back to a sweep address after 24h. Agents stop polluting your real wallet.
- Solves the *actual* friction every developer hits: the Crossmint API key bootstrap, the Devnet faucet dance, the keypair management. For coding agents, it makes Crossmint the "default disposable wallet provider for AI development."

**Crossmint products used:** Wallets SDK (smart wallets), API key flow, x402 (the sweep), faucets via Devnet (referenced in docs).

**Saul assets leveraged:** x402-rs facilitator (gas wallet management patterns), `add-network` skill philosophy (one-command provisioning), Settlement account expertise.

**Why this is NOT stale (vs Hello x402):** It is the *opposite* of a quickstart. A quickstart gets one wallet running once. This makes wallet creation a no-op for every test run forever. It is the kind of tool a DevRel hire would build in their first month at the company.

**Build time:** 12-15 hours.

**Wow factor:** 8/10.

**Failure mode:** Devnet rate limits or faucet flakiness break the "60 seconds" promise on demo day.

---

### Idea 4 — "The Delegated Signer Flow That Doesn't Exist Yet" — A working delegated-signer + spending-limit consent UI

**Opinion:** *Crossmint's `/wallets/guides/delegated-signers` page literally 404s. The closest existing page is about adding signers, not delegating them. So the "give your agent scoped access to your wallet" story — the single most important UX in agentic finance — has no entry point. I built it.*

**What it does (3 bullets):**
- A two-page Next.js app: page one is a user signing a delegation grant ("allow this agent to spend up to $50 in USDC over the next 24h on x402 endpoints in the `weather.*` domain"), page two is the agent acting under that grant and the user seeing each spend in a live activity feed.
- The grant is enforced *both* on the Crossmint smart wallet side (delegated signer with spending limit) *and* on a tiny policy server, so you can show that the same authorization model works whether the wallet is custodial or smart.
- Includes a video where I revoke the grant mid-stream and the agent gets a clean rejection — proving consent is reversible. This is the agentic-commerce demo Visa Intelligent Commerce wants but can't ship because of issuing-bank pushback.

**Crossmint products used:** Smart Wallets, Delegated Signers, spending limits, x402.

**Saul assets leveraged:** OFAC + risk rules expertise from Santander and the facilitator, Solana smart-wallet CPI knowledge for the spending-limit enforcement layer, frontend-design skill for a polished UI in 4 hours.

**Why this is NOT stale (vs Hello x402):** Filippos's biggest reveal in the interview was the **Visa issuing-bank bottleneck** — Chase is rejecting agent-card generation. Delegated signers + x402 are the *actual* path around that bottleneck and Crossmint has shipped the primitive but not the demo. This is filling a strategic gap, not a content gap.

**Build time:** 18-22 hours.

**Wow factor:** 9/10.

**Failure mode:** The spending-limit enforcement on Crossmint smart wallets isn't actually exposed via SDK yet — would need a server-side policy shim, which weakens the "the wallet enforces it" claim.

---

### Idea 5 — `crossmint-megaverse` — A Megaverse take-home that turns into a public tutorial about Crossmint's own auto-grading API

**Opinion:** *Crossmint hands every candidate the same Megaverse take-home and the internet has 200 mediocre solutions. The actual lesson the take-home should teach is how to design an idempotent, rate-limited, auto-graded onchain API — which is exactly what Crossmint had to build internally. I solved Megaverse, then wrote the post-mortem of Crossmint's API design choices, with their permission, as an onboarding tutorial for new candidates.*

**What it does (3 bullets):**
- A clean, agent-orchestrated solution to the Megaverse challenge using Crossmint's own MCP server (Idea 1) — proving the take-home itself can be solved by a Claude Code agent in <60 seconds.
- A blog post titled *"What the Megaverse take-home is actually trying to teach you about Crossmint's API"*, walking through every design decision the API forced on candidates — exponential backoff, idempotency keys, megaverse-state reconciliation.
- A "candidate companion" repo that future Crossmint applicants are *encouraged* to use (with internal blessing) so the take-home becomes about API design *literacy*, not API call grinding.

**Crossmint products used:** Megaverse API, indirectly the wallet/checkout APIs as analogs.

**Saul assets leveraged:** 18 years of infra, Santander API design experience, Claude Code at industrial scale (proven ability to orchestrate multi-step API workflows agentically).

**Why this is NOT stale (vs Hello x402):** The take-home is famous inside Crossmint and famously generic outside it. Turning the take-home into a Crossmint *content asset* instead of a hiring filter is a DevRel jujitsu move that signals product thinking + audience instinct + AI fluency in one shot.

**Build time:** 10-12 hours (the agent solves the Megaverse, you write the meta).

**Wow factor:** 7/10 (huge if Crossmint endorses it, weird if they don't).

**Failure mode:** Crossmint reads it as "this candidate cheated on the take-home" instead of "this candidate just elevated our take-home." Needs careful framing.

---

### Idea 6 — "x402 Reverse Paywall" — Get paid by AI agents to *answer* their Crossmint questions

**Opinion:** *Crossmint's docs assume the reader is a human paying for an SDK. In 2026 the reader is an AI agent that wants the answer fast and is willing to pay for it. So I inverted the docs: instead of free docs that humans read, I built a paid docs API that **earns USDC per query** on x402 — and proved a Claude Code agent will happily pay $0.001 per Crossmint docs lookup.*

**What it does (3 bullets):**
- A `docs-as-paywalled-API` server that wraps the Crossmint docs in an x402 endpoint: the agent posts a question, sends 1000 microUSDC, gets a grounded, citation-linked answer back.
- The reverse demo: a Claude Code session that solves a "how do I integrate Crossmint Solana smart wallets with a delegated signer?" task by paying its own way through the docs, then pays for the SDK install (it's free), then pays for the test wallet provisioning. End-to-end agent self-funded onboarding.
- Doubles as proof of *Crossmint earning revenue from its own documentation* — a category-shifting claim about what DevRel can be when the audience is paying agents.

**Crossmint products used:** Smart wallets (for the agent's wallet), x402 (the paywall standard).

**Saul assets leveraged:** x402-rs facilitator, x402r escrow scheme, settlement account support, OFAC compliance (a paid docs API needs OFAC), CPI inner-instruction scanning.

**Why this is NOT stale (vs Hello x402):** It is the *category mutation* of the quickstart concept. Instead of explaining x402, it weaponizes x402 against Crossmint's own docs in a way that produces revenue and a thinkpiece. Alfonso would screenshot this.

**Build time:** 16-18 hours.

**Wow factor:** 9/10.

**Failure mode:** Reads as a stunt unless paired with a sober campaign proposal. Needs the "why this is the future of DevRel" framing.

---

### Idea 7 — "Crossmint vs Privy vs Dynamic vs Turnkey: The Honest Smart Wallet Bake-Off" — A side-by-side benchmark video

**Opinion:** *Every wallet provider says they're the easiest. Devs trust nobody. So I built the same agentic checkout in four wallet stacks (Crossmint, Privy, Dynamic, Turnkey), timed each integration end-to-end on camera, and let the data say what marketing can't. Crossmint won three of four metrics. Here's the receipts.*

**What it does (3 bullets):**
- Four parallel implementations of the same agent-driven flow: "create wallet, fund with USDC, sign x402 payment, mint receipt NFT." Same input, four outputs.
- Recorded screencast measuring lines of code, time to first transaction, API call count, dependencies installed, error states encountered, and TTFB on each provider's RPC.
- A blog post + tweet thread with the raw timings, screenshots, and one chart that everybody shares. Crossmint wins or it doesn't — the credibility of the comparison comes from running it honestly.

**Crossmint products used:** Wallets SDK, Smart Wallets, x402, NFT minting.

**Saul assets leveraged:** 18 years of infra (objective benchmarking instinct), x402-rs (x402 layer), OpenClaw (the agent driving each flow).

**Why this is NOT stale (vs Hello x402):** Nobody at Crossmint can ship this — it would be self-marketing. A DevRel candidate can. It is the most viral content shape on Crypto Twitter in 2026 and Crossmint has zero head-to-head bake-off content. Maximum trust signal because it accepts the risk of losing.

**Build time:** 20-24 hours (the longest of all ideas, very risky for 48h).

**Wow factor:** 9/10 if Crossmint wins cleanly, 4/10 if it doesn't.

**Failure mode:** Crossmint loses on a metric and the campaign proposal becomes awkward. Mitigation: pick metrics where you know Crossmint wins from the SDK shape.

---

### Idea 8 — `crossmint-walletlink` — One-click "claim my Crossmint wallet from any GitHub repo"

**Opinion:** *Every Crossmint quickstart asks the dev to create an API key, copy it to .env, fund a wallet, then run the demo. That is four manual steps and three opportunities to bounce. I built the GitHub Action + browser flow that lets a developer click "Run on Crossmint" on any repo README and have a funded wallet handed to their fork in 30 seconds.*

**What it does (3 bullets):**
- A GitHub Action that, on workflow dispatch, provisions a Crossmint smart wallet, funds it on Devnet, writes the keys to repo Secrets, and comments on the issue with the wallet address — all from a `Run on Crossmint` button on the README.
- A companion OAuth-style browser flow at `walletlink.crossmint.dev` (or `walletlink.lxhxr.com` for the demo) that lets a non-CLI dev claim a wallet from their browser and have it installed into their dev environment.
- The whole flow is showcased in a 90-second video where I clone three real Crossmint demo repos and have them running with funded wallets in under 4 minutes total.

**Crossmint products used:** Wallets API, API key creation flow.

**Saul assets leveraged:** AWS infra background, GitHub Actions expertise, Frontend design skill for the OAuth-style flow.

**Why this is NOT stale (vs Hello x402):** It targets the exact moment in the funnel Filippos admitted Crossmint has no telemetry on. "Time to first on-chain transaction" is the metric Filippos cited but said *"we barely take the time to look at these metrics and change course."* This collapses that metric to under a minute.

**Build time:** 14-16 hours.

**Wow factor:** 8/10.

**Failure mode:** Crossmint's API key flow doesn't allow programmatic creation, so the "no manual step" promise has a manual step.

---

### Idea 9 — "Crossmint inside ChatGPT" — A native Apps SDK / OpenAI Action that does agentic checkout from inside a chat

**Opinion:** *Crossmint's headless checkout already exists. ChatGPT just shipped Apps SDK. Nobody has wired the two together yet. The first agentic commerce flow that runs inside a real ChatGPT conversation — buy this thing, route it through Crossmint, settle on Solana — is a category claim.*

**What it does (3 bullets):**
- An OpenAI Apps SDK app (or Custom GPT with Action, depending on what's stable that week) that lets a ChatGPT user say "buy me a domain via Crossmint" or "send 5 USDC to this address from my Crossmint wallet" and watch the chat orchestrate it.
- Uses Crossmint headless checkout for the purchase, smart wallets for the spend, x402 for the API monetization piece, all surfaced inline with confirmation cards.
- A 2-minute screen recording inside ChatGPT, plus the source repo, plus a campaign proposal for "Crossmint as the default agentic commerce stack inside the largest chat surface on earth."

**Crossmint products used:** Headless Checkout, Smart Wallets, x402, Verifiable Credentials (for receipt).

**Saul assets leveraged:** Claude Code expertise (cross-applies to OpenAI Apps SDK), Frontend design skill, OpenClaw fluency.

**Why this is NOT stale (vs Hello x402):** ChatGPT Apps SDK is the largest agentic surface in the world. There is no Crossmint integration on it. Filippos's #1 audience is OpenClaw — but the *largest* agentic surface is OpenAI's, and showing up there first is a flag-planting move that demonstrates audience instinct at strategic scale.

**Build time:** 18-22 hours.

**Wow factor:** 10/10.

**Failure mode:** OpenAI Apps SDK is too rough/locked-down to actually wire up in 48h, forced to fall back to a Custom GPT with Actions, which is much less impressive.

---

### Idea 10 — "The Crossmint Field Guide" — An agentic, auto-updating, AI-grounded docs companion with a paywall on the *expensive* parts

**Opinion:** *Crossmint has 100 blog posts, 300+ docs pages, and a 11-demo monorepo. Nobody can read all of that. So I built the field guide that an AI can — a structured, embedded, semantically-indexed snapshot of the entire Crossmint surface area, served behind a free MCP endpoint, with a $0.001 x402 paywall on the **deep** queries (architecture decisions, regulatory rationale, partner integration details).*

**What it does (3 bullets):**
- An MCP server + RAG index over the public Crossmint docs + blog + GitHub examples, served free for shallow queries and paywalled (via x402) for the cross-document, multi-hop reasoning queries.
- A weekly cron that re-indexes from source so the field guide is fresher than the docs themselves. Includes a public "diff log" of what Crossmint shipped each week.
- Demoed by having a Claude Code session use the field guide to write a complete Crossmint integration from scratch with zero hallucinations — verified against the Devnet smart wallet.

**Crossmint products used:** All of them, indirectly (this is *about* the docs).

**Saul assets leveraged:** Claude Code at 1.4M lines (RAG + grounding expertise), x402-rs facilitator, OpenClaw integration, IRC agent (MeshRelay) for the weekly re-index notification stream.

**Why this is NOT stale (vs Hello x402):** It treats the docs themselves as the product surface and extends them, instead of patching one missing page. It is the meta-move on the docs gap. And it earns money on day one via the paywall, which is a real demonstration of what Crossmint's own technology can do for content.

**Build time:** 22-26 hours (very tight).

**Wow factor:** 9/10.

**Failure mode:** RAG quality is mediocre and the demo session hallucinates on camera. Mitigation: hard-code the demo prompts to ones you've validated work.

---

## Top 3 Ranked

### Rank 1: **Idea 2 — `crossmint-skill` for Claude Code (and Cursor + Codex)**

The strongest combination of *audacity*, *strategic alignment*, and *48-hour feasibility*. Filippos *literally said in the interview* that he built a skill himself recently and his agent paid for the first time. Crossmint's leadership thinks in skills. There is no public Claude Code skill for Crossmint. A polished one — with a verify subcommand that produces an on-chain receipt as proof — is unanswerable. Saul has authored 8+ skills already and knows the format cold. The video demo (Crossmint-ifying three apps in 60 seconds each) is the kind of thing a DevRel hiring manager forwards to the founder.

### Rank 2: **Idea 1 — `crossmint-mcp` server**

Bigger surface area, bigger impact, but riskier on time. If the OpenAPI auto-generation works, this is the highest-leverage artifact in the list: Crossmint instantly becomes the most-integrable wallet provider for every coding agent on earth. Saul has shipped an MCP server before (`autojob.cc/mcp`), so the muscle memory exists. Risk is the auth model — if Crossmint's API auth doesn't translate cleanly into MCP, the time blows up.

### Rank 3: **Idea 4 — Delegated Signer + Spending Limits Consent UI**

The strategic kill shot. This is the demo Crossmint *needs* to ship to win the agentic commerce narrative against Visa Intelligent Commerce, and they haven't. Filippos handed Saul the strategic gap (Chase rejecting agent virtual cards) on a plate during the interview. Building this is the response to "we told you our biggest blocker, what would you do about it?" The risk is that the spending limits aren't fully exposed via SDK and the demo needs a shim — but even a shimmed version is a shipped opinion.

---

## The One I'd Bet My Career On

### **Idea 2: `crossmint-skill` for Claude Code, Cursor, and Codex.**

**Why this and not the others:**

The challenge brief evaluates five axes. Watch how this idea hits all five at maximum velocity.

1. **Technical judgment.** It picks the gap that *Crossmint itself does not know it has*: there is no agent-IDE-native onboarding for Crossmint, even though the leadership team is already shipping skills internally. The submission says "I noticed what your CEO is building in his free time and I built the production version of it."

2. **Communication.** The artifact is *self-explaining*. A developer doesn't read a tutorial, they install a skill and watch their AI integrate Crossmint for them. The "could a developer understand this faster than reading our docs" axis collapses to "yes, in 60 seconds, with zero reading."

3. **Audience instinct.** The audience is the OpenClaw / Claude Code / Cursor user. Filippos named OpenClaw as the #1 segment. Saul knows this audience because he *is* this audience (1.4M lines of Claude Code, 1962 commits, MCP server author). The campaign distribution goes through Crypto Twitter, the Claude Code subreddit, the Cursor Discord, and Fede's existing followers — every channel is a real handle, not a generic name.

4. **AI fluency.** The submission is itself a piece of AI-native infrastructure. Building it requires understanding skill discovery, prompt engineering as API design, and how coding agents actually make tool decisions. This is the literal measure of AI fluency the brief is asking for.

5. **Taste.** It is small, focused, opinionated, and *useful*. It has a one-line opinion ("Crossmint should distribute through Claude Code skills, not just docs"), a single artifact (the skill), a single demo (the 60-second video), and a single ask in the campaign (publish it as `@crossmint/claude-code-skill` on the Crossmint org).

**Why the failure mode is survivable:** The worst case is the skill is too thin and reads like a system prompt. The mitigation is the verify subcommand that produces an on-chain transaction signature. As long as the video shows a real Devnet TXID at the end, the skill cannot be dismissed as "AI slop." Saul's CPI inner-instruction work means he's the only person on earth who can guarantee that signature appears every single run on Solana.

**Why Alfonso would say "holy shit, hire this person":** Because the submission is not a piece of content explaining a Crossmint product. It is *a new Crossmint product*. The candidate is showing up on Day 1 with a deliverable that the company can ship to its top audience this week. That is exactly the muscle Filippos described as "weeks can be documentation, demos for enterprise, agentic development, eventos externos." This skill is all four in one.

**Why it beats the rest of the list:** Idea 1 is bigger but riskier. Idea 4 is more strategic but Crossmint already half-knows the play. Idea 9 is more audacious but depends on OpenAI's roadmap. Idea 7 is viral but has a face-plant scenario. Idea 2 has the smallest variance and the highest median outcome. In a 48-hour sprint, you bet on the median, not the long tail.

**The pitch sentence Saul should put in the submission cover:**

> *"I noticed Filippos shipped an x402 skill himself last month and his agent paid for the first time. Here is the production version of that pattern, packaged as a Claude Code skill, ready to drop into the Crossmint org on Monday."*

That is the sentence Alfonso forwards to the rest of the team. That is the hire.

---

— Wild Innovator
