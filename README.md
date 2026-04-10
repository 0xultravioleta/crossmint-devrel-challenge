# Crossmint DevRel Challenge — Submission

> **Candidate:** Saul Jaramillo
> **Position:** Senior Developer Relations Engineer
> **Submitted:** 2026-04-10
> **Challenge received:** 2026-04-09 06:17 AM EDT (48h deadline)

---

## The concept I picked

**Crossmint smart wallets as MCP tools + the Solana CPI inner instruction nuance that no public resource explains.**

lobster.cash is Crossmint's payment engine for AI agents. It ships as a CLI (`@crossmint/lobster-cli@3.0.8`) that installs into Claude Code, Cursor, and OpenClaw via skills. But Claude Desktop, Continue.dev, Cline, and Codex CLI speak MCP, not skills — and the single most important technical nuance of paying from a Crossmint smart wallet on Solana (the CPI inner instruction quirk) is not explained anywhere public. Not in Crossmint docs, not in the lobster.cash skill directory, not in the [Solana Foundation x402 guide](https://solana.com/developers/guides/getstarted/intro-to-x402) — which literally says "Crossmint is currently in progress."

I built two MIT-licensed artifacts to close both gaps.

---

## The three deliverables

### 1. Demo / Code artifact — two repos

**[crossmint-wallets-mcp](https://github.com/0xultravioleta/crossmint-wallets-mcp)** — An MCP server exposing 4 Crossmint smart wallet tools:

| Tool | What it does |
|---|---|
| `crossmint_create_wallet` | Create a smart wallet on Solana or Base |
| `crossmint_get_balance` | Read native + USDC balances |
| `crossmint_transfer_token` | Send tokens via gasless relayer |
| `crossmint_pay_x402_endpoint` | Handle HTTP 402 → pay USDC → return paid response + tx signature |

Install in Claude Desktop:

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

**[crossmint-cpi-skill](https://github.com/0xultravioleta/crossmint-cpi-skill)** — A lobster.cash-compatible skill that teaches agents the Solana CPI inner instruction nuance: why Crossmint smart wallet payments are invisible to naive x402 facilitators, and how to route through facilitators (like Faremeter) that correctly scan inner instructions.

```bash
npx skills add https://github.com/0xultravioleta/crossmint-cpi-skill --global --yes
```

### 2. Developer-facing content — [`content/`](content/)

- **[Blog post](content/blog-post.md)** (1310 words) — Full technical walkthrough with the CPI nuance explained in plain English
- **[Twitter thread](content/twitter-thread.md)** (10 tweets) — Standalone launch thread
- **[Video script](content/video-script.md)** (75s) — Terminal demo of real mainnet payment, shot-listed and ready to record

### 3. Campaign proposal — [`campaign/`](campaign/)

- **[Launch proposal](campaign/proposal.md)** (~500 words) — Distribution plan naming real channels, real people, and measurable 30/60-day outcomes

---

## Proof it works — real mainnet transaction

The MCP server was tested end-to-end on **Solana mainnet** with real USDC.

- **Payer:** `4xHkMCaKVBGw4GtdpeKoNZhGFDMi1tMCJDvXvxUmL8hM` (Crossmint smart wallet)
- **Merchant:** `Fxr4RtBPzU5ZJL2Wj57MZNuChBNbZzK3RHrXDwa1yqo` (Crossmint smart wallet)
- **Amount:** 0.01 USDC per run, 2 successful runs (0 → 0.02 USDC on merchant)
- **First tx signature:** [`KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg`](https://explorer.solana.com/tx/KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg)

The smoke test creates a payer wallet, creates a merchant wallet, boots a local x402 paywall server, calls `crossmint_pay_x402_endpoint`, and verifies the merchant balance increased on-chain. HTTP 402 → X-PAYMENT header → HTTP 200 round trip.

---

## How this was built

The challenge says *"AI is expected — we're evaluating your judgment."* This submission was built by a multi-agent team with explicit role separation:

- **3 coordinators** (HR Recruiter, Project Manager, Devil's Advocate) — meta only, no execution
- **4 specialists** (Docs Auditor, Technical Architect, Demo Engineer, Content Writers) — execution
- **1 human** (me) — strategic direction, concept lock, voice pass on content, submission signoff

The full process is documented in [`_internal/`](_internal/):

| File | What it shows |
|---|---|
| [`MASTERPLAN.md`](MASTERPLAN.md) | Phase-by-phase execution plan |
| [`TEAM.md`](TEAM.md) | Multi-agent team architecture |
| [`_internal/decisions.md`](_internal/decisions.md) | Running decision log with timestamps |
| [`_internal/hr-proposal.md`](_internal/hr-proposal.md) | HR's team composition rationale |
| [`_internal/pm-journal.md`](_internal/pm-journal.md) | PM's coordination journal |
| [`_internal/devils-advocate/`](_internal/devils-advocate/) | Every decision challenged before commit |
| [`_internal/content-drafts/`](_internal/content-drafts/) | Original drafts with PM fix logs |

This is not a prompt dump. It is an AI-augmented engineering workflow where every artifact was reviewed by at least two roles before promotion.

---

## The technical nuance (in one paragraph)

A Crossmint smart wallet on Solana does not sign SPL token transfers at the top level of a transaction. Instead, the top-level instruction is an "execute" call against the smart wallet's own program, and the actual SPL transfer happens inside a Cross-Program Invocation one level down. It is the difference between writing a check yourself and your bank writing it on your behalf. Naive x402 facilitators that only scan top-level instructions miss the transfer entirely and reject the payment — even though the USDC has already moved. Facilitators that scan CPI inner instructions (like Faremeter) find the nested transfer and verify it correctly. This is documented nowhere public today. The skill I wrote teaches agents this decision tree.

---

## Repos

| Repo | What | Status |
|---|---|---|
| [crossmint-wallets-mcp](https://github.com/0xultravioleta/crossmint-wallets-mcp) | MCP server — 4 tools, Solana mainnet verified | Published on npm |
| [crossmint-cpi-skill](https://github.com/0xultravioleta/crossmint-cpi-skill) | lobster.cash skill — CPI nuance + payment routing | Ready for certification |
| [crossmint-devrel-challenge](https://github.com/0xultravioleta/crossmint-devrel-challenge) | This submission repo | You are here |

---

*Both artifacts are MIT-licensed and ready to fork into the Crossmint org today.*

*— Saul*
