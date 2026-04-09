# Feasibility-First Concept Picks

> Author: Feasibility Scorer agent
> Audience: Crossmint DevRel Challenge submission team
> Time budget: ~26h execution (48h minus sleep, meals, meta, buffer)
> Mode: engineering manager sizing tickets

---

## 1. Feasibility Criteria

I score every concept against the same five-axis filter: (1) **end-to-end demoable in <20h of coding** by one senior engineer with AI assistance, (2) **explainable in 1500 words** without hand-waving, (3) **reuses existing Saul-owned or upstream code** for at least 60% of the moving parts, (4) **does not block on third parties** (no waiting for a Crossmint API change, no waiting for a counterparty agent, no waiting for a mainnet audit), and (5) **gives the reviewer a 5-minute path to "I see it work"** — clone, set one env var, run one command, see a transaction or a screenshot. Anything that fails one of these axes is downgraded; anything that fails two is rejected.

---

## 2. Five Feasibility-First Concept Proposals

### Pick A — `crossmint-x402-solana-starter`

**One-liner:** A standalone, one-command starter that takes a Crossmint smart wallet on Solana from "API key" to "I just paid an x402 paywall" in under 5 minutes, including the CPI inner-instruction nuance that nobody else has documented.

**Estimated hours:** 14h total
- 6h: extract `ping-faremeter-crossmint-solana` from the agentic-finance monorepo, strip cruft, rewrite as a single-package repo with one `pnpm start` command
- 2h: wire it against Saul's existing public facilitator (`facilitator.ultravioletadao.xyz`) so the reviewer needs zero infra
- 2h: README with annotated trace of the CPI inner instruction (the actual gap)
- 3h: 1500-word blog post draft + 1-pager campaign doc
- 1h: end-to-end smoke test from a clean machine

**What exists to reuse:**
- `Crossmint/crossmint-agentic-finance/ping-faremeter-crossmint-solana` (working reference)
- `x402-rs/src/chain/` and `solana.rs` CPI scanner (Saul wrote it)
- `facilitator.ultravioletadao.xyz` mainnet+devnet endpoints
- Crossmint Wallets SDK Node.js quickstart (already documents wallet creation)

**What to build fresh:**
- The single-file glue that wraps the wallet → x402 client flow
- The CPI inner-instruction explainer (this is the *actual* DevRel artifact)
- A one-page Mermaid diagram showing how Crossmint's smart wallet differs from EOA + how the facilitator scans for it

**Hardest technical risk:** Crossmint smart wallet on Solana devnet returning a transaction shape the Faremeter client doesn't expect after a recent SDK update. Mitigation: pin SDK versions in `package.json` and document the version in the README.

**Mainnet demo?** Either. Devnet is the safe default for the README; mainnet 0.01 USDC trace as a bonus video.

---

### Pick B — `x402-paywall-in-a-tweet`

**One-liner:** A 40-line Express route + 30-line client that proves you can paywall any HTTP endpoint with x402 + a Crossmint smart wallet, copy-pasteable into anyone's existing Node project.

**Estimated hours:** 8h total
- 2h: minimal Express middleware (this already exists in Faremeter; the work is *trimming* it)
- 2h: client snippet using `@crossmint/wallets-sdk` + Faremeter client
- 2h: README with the "paste this into your own Express app" angle
- 2h: blog post + 1-pager (very short, the whole point is small surface area)

**What exists to reuse:**
- Faremeter Express middleware
- Crossmint SDK wallet creation primitive
- Saul's facilitator as the resolver

**What to build fresh:**
- The "tweet-sized" framing (this is 90% positioning)
- One screenshot of the 402 → 200 round trip in DevTools

**Hardest technical risk:** None real. The risk is *narrative* — too small to feel like a DevRel campaign on its own.

**Mainnet demo?** Either. Trivially mainnet-ready because the Crossmint smart wallet just signs a normalized payload.

---

### Pick C — `crossmint-mcp-x402-pay`

**One-liner:** A Model Context Protocol (MCP) server that exposes one tool — `pay_url(url, max_usdc)` — backed by a Crossmint smart wallet, so any MCP-aware AI client (Claude Desktop, Cursor, etc.) can pay an x402 paywall as a tool call.

**Estimated hours:** 16h total
- 4h: MCP server scaffold (Saul has MCP patterns from AutoJob to copy)
- 4h: Crossmint wallet + Faremeter client wiring inside the tool handler
- 2h: spending limit + max-USDC guardrail (in-process, not on-chain)
- 2h: README with screenshots of Claude Desktop calling the tool
- 3h: blog post + 1-pager (the angle: "the smallest agentic payment primitive")
- 1h: video of Claude Desktop paying a paywall live

**What exists to reuse:**
- AutoJob's MCP server skeleton (Python or TS, both work)
- Crossmint Wallets SDK
- Faremeter client
- Saul's facilitator

**What to build fresh:**
- The MCP tool definition + JSON schema for `pay_url`
- The Claude Desktop config snippet
- Screenshots/video of the LLM-driven call

**Hardest technical risk:** Claude Desktop's MCP transport quirks on Windows (Saul has hit these before with AutoJob). Mitigation: ship as `npx`-runnable so the install path is one command.

**Mainnet demo?** Either. Defaults to devnet for safety; mainnet flag for the demo video.

---

### Pick D — `crossmint-x402-receipts`

**One-liner:** A 100-line companion CLI that takes any Crossmint wallet address and prints a human-readable timeline of every x402 payment it has made, by querying the chain and decoding the inner instructions.

**Estimated hours:** 10h total
- 3h: CLI scaffold (`commander` or `clipanion`)
- 3h: chain query layer (Solana RPC + EVM RPC) — Saul has this pattern in his facilitator
- 2h: rendering layer (table output, JSON output)
- 2h: README + blog post

**What exists to reuse:**
- Saul's facilitator already does inner-instruction decoding for Solana
- Crossmint Wallets SDK for the address resolution
- Standard `viem` / `@solana/web3.js` for RPC

**What to build fresh:**
- The CLI ergonomics
- The "this is what an agentic payment ledger looks like" narrative

**Hardest technical risk:** Crossmint smart wallets emit slightly different log shapes than EOAs, and the decoder has to handle both. Saul has the code for this in `x402-rs`, so risk is low — just porting Rust logic to TS.

**Mainnet demo?** Yes — this is the only one that *needs* mainnet, because the demo only sings if it pulls a real wallet's history.

---

### Pick E — `crossmint-delegated-signer-quickstart`

**One-liner:** Fill the documented 404 — a 5-minute walkthrough of creating a Crossmint smart wallet, attaching a delegated signer with a USDC spending cap, and watching an agent spend exactly $0.50 against that cap.

**Estimated hours:** 12h total
- 4h: figure out the actual delegated-signer API surface from the SDK source (the docs page 404s, so this is *the* gap)
- 3h: write the demo script with a hard-coded scenario
- 2h: README with the spending-cap explainer
- 2h: blog post + 1-pager
- 1h: smoke test

**What exists to reuse:**
- Crossmint Wallets SDK (the API surface exists; only the docs are missing)
- Existing Node.js quickstart as a starting template

**What to build fresh:**
- The delegated-signer primitive walkthrough
- The narrative around "this is the missing primitive that makes agents safe"

**Hardest technical risk:** **The biggest risk on this list.** The 404 page might be 404 because the feature is actually shipping next week, or because the API shape is unstable, or because the PM who owns it changed jobs. Mitigation: confirm with Fede or Adolfo that the API is stable before committing 12 hours. If they say "in flux," skip this pick entirely.

**Mainnet demo?** Devnet only — spending caps need real funds and that's not the point of the demo.

---

## 3. Concept Evaluation Rubric

Apply this matrix to every Wild Innovator idea when it lands. Score 1–5 on each axis. Total possible: 25. Anything <16 is rejected; 16–19 is "rework"; 20+ ships.

| Axis | What it measures | 1 (bad) | 5 (great) |
|---|---|---|---|
| **Buildability** | Can it be coded + tested end-to-end in 20h? | 40h+, multiple unknowns, blocked on third party | <12h, all parts known, zero blockers |
| **Reuse leverage** | How much existing Saul/Crossmint code does it lean on? | <20% reuse, mostly greenfield | >70% reuse, glue work only |
| **Gap clarity** | Does it fill a *named* hole in Crossmint docs/repos? | "It would be cool if..." | "This URL 404s and here is the working version" |
| **Demoability in 5 min** | Can a busy reviewer experience it in 5 min? | Requires Docker + 3 env vars + a counterparty | One command, one env var, one screenshot |
| **Saul's edge** | Does it leverage something only Saul can credibly ship? | Anyone could build this | This requires Saul's CPI scanner / facilitator / 19-chain ops experience |

A concept that scores 5/5/5/5/5 is the platonic ideal but extremely rare. A realistic ship target is **4/4/4/4/3** (19) — high on the first four, acceptable on Saul's edge.

**Special veto criterion:** any concept that requires Crossmint internal data, an unreleased API, a mainnet audit, a Crossmint infrastructure fork, or a counterparty agent that has to be running at demo time is **vetoed regardless of score**.

---

## 4. Top Pick

**Pick A — `crossmint-x402-solana-starter`** is the recommended ship.

**Reasoning:**

1. **Highest gap clarity.** The research already proves Crossmint docs have *zero* dedicated x402 tutorial, and `ping-faremeter-crossmint-solana` is the existing reference but is buried in a monorepo. This is a "the 404 has Saul's name on it" moment.

2. **Highest Saul-edge.** Saul wrote the CPI inner-instruction scanning for Solana smart wallets. He is, on paper and in commits, the person on Earth with the most context about *why* a Crossmint smart wallet on Solana is harder to support than an EOA. Nobody else can write the explainer paragraph that goes between code blocks.

3. **Reuses ~70% of existing code.** The agentic-finance monorepo already has the working demo. The facilitator is already running on mainnet+devnet. The Crossmint SDK already creates the wallet. The work is *extraction, narration, and ergonomics* — exactly what DevRel is.

4. **Demoable in 5 minutes.** Clone, set `CROSSMINT_API_KEY`, run `pnpm start`, see a paywalled endpoint return 200. That's it. The reviewer doesn't need to spin up a facilitator, doesn't need a counterparty, doesn't need Docker.

5. **Defensible against Wild Innovator's swing.** Even if the Wild Innovator proposes something more ambitious, this pick is the safety floor — it can be built in parallel, and if the ambitious idea slips, this still ships and still hits all five evaluation axes from the brief.

6. **Aligned with Filippos's signal.** The debrief shows Filippos explicitly said Faremeter is Crossmint's preferred facilitator, agents are one of two company priorities, and the CPI example was the moment Saul moved from "talks well" to "actually builds." Shipping the CPI quickstart turns the interview moment into a public artifact.

**Estimated hours:** 14h coding/writing + 2h buffer = 16h, fitting comfortably inside the 26h budget with room for one of the smaller picks (B or D) as a bonus drop.

**The sweet spot answer to the user's key question:** Yes, there is something innovative *and* buildable. The innovation is not in inventing a new primitive — it is in being the first person to ship a clean, standalone, runnable explainer for a primitive that already works but has no documentation. The CPI inner-instruction angle is genuinely novel content (no other public write-up exists), and the running demo is genuinely the missing quickstart. That is not a "safe quickstart"; it is a *gap-fill quickstart with a technical opinion* — which is exactly what the brief asks for ("be critical, be specific").

**Recommended bonus drop if time permits (under 6 remaining hours):** Pick B (`x402-paywall-in-a-tweet`) — it's tiny, complementary, and creates a one-two punch: "here's the deep starter, and here's the 40-line version for people who already get it."
