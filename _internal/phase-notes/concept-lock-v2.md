# Concept Lock v2 — DUAL ARTIFACT

> **Status:** LOCKED — kills concept-lock.md v1 (Hello x402)
> **Date:** 2026-04-09 H+5
> **Approved by:** Saul ("hagamoslo los dos")

---

## The Concept (one sentence)

**"The missing two companions to lobster.cash: a Crossmint Wallets MCP Server for MCP-native clients, and a certified lobster.cash skill that fills the CPI scanning gap only Saul can explain."**

---

## Opinion Sentence (Twitter-length, 271 chars — passes rubric)

> *lobster.cash is Crossmint's payment engine for agents on Claude Code, Cursor, and OpenClaw. It does not reach Claude Desktop, Continue.dev, Cline, or Codex CLI — they speak MCP. And nobody has shipped the Solana CPI scanning skill lobster.cash needs. I built both.*

---

## Why dual artifact instead of one

1. **Ecosystem thinking beats point-solution thinking.** A DevRel hire who ships ONE thing solves ONE problem. A DevRel hire who ships TWO complementary things demonstrates they understand Crossmint's agentic commerce stack as a platform, not a feature.
2. **Two audiences, one codebase.** The MCP server covers MCP-native clients. The skill covers lobster.cash-compatible clients. They do not overlap; they complement.
3. **Fede is the skill certification gate AND the internal champion.** Building a skill gives Fede a reason to push it through the directory. Building the MCP server gives him a second reason to advocate internally.
4. **Shared infrastructure reduces build time.** Both artifacts can reuse the same Crossmint wallet integration code. Write it once, expose it twice.
5. **Shared narrative in one blog post.** Same story, same gap, two fixes. Reviewer reads one post and walks away understanding both.

---

## Artifact 1 — `crossmint-wallets-mcp`

### What it is

An MCP server that exposes Crossmint smart wallet primitives as MCP tools, installable via one JSON config block in any MCP-native client.

### Target clients

- **Claude Desktop** (the consumer chat app — not Claude Code)
- **Continue.dev**
- **Cline**
- **Codex CLI**
- **Any custom MCP-native agent** (LangChain via MCP bridge, CrewAI via MCP bridge, etc.)

### The 4 tools (cut from 6 for safety)

| # | Tool | What it does | Priority |
|---|---|---|---|
| 1 | `crossmint_create_wallet` | Create a Crossmint smart wallet for an email + chain | HIGH |
| 2 | `crossmint_get_balance` | Query token balances | HIGH |
| 3 | `crossmint_transfer_token` | Send USDC/SPL/ERC-20 to any address | HIGH |
| 4 | **`crossmint_pay_x402_endpoint`** | **KILLER.** Given a URL, parse 402, sign + send payment via Crossmint wallet, return response | **CRITICAL PATH — BUILD FIRST** |

**Cut for v2:** `crossmint_add_delegated_signer`, `crossmint_list_transactions`. Both are nice-to-haves that can ship in a v0.2 if we have buffer time.

### Chain support at launch

- **Solana mainnet + Devnet** (the Solana Foundation gap)
- **Base mainnet + Sepolia** (Crossmint's #1 EVM audience)

### Stack

- TypeScript
- `@modelcontextprotocol/sdk` (official MCP SDK)
- `@crossmint/wallets-sdk` (Crossmint official)
- Published as npm package `crossmint-wallets-mcp` (or similar — user's scope)
- Installable via `npx crossmint-wallets-mcp`

### Config example (for README)

```json
{
  "mcpServers": {
    "crossmint-wallets": {
      "command": "npx",
      "args": ["-y", "crossmint-wallets-mcp"],
      "env": {
        "CROSSMINT_API_KEY": "sk_...",
        "DEFAULT_CHAIN": "solana"
      }
    }
  }
}
```

---

## Artifact 2 — `crossmint-cpi-skill` (lobster.cash certified skill)

### What it is

A lobster.cash-compatible skill that teaches agents the Solana CPI inner instruction nuance and routes x402 payments through `@crossmint/lobster-cli` correctly on Solana smart wallets.

### Why this skill and not another

**Saul's deepest wheelhouse.** He wrote the only public implementation of CPI inner instruction scanning for Crossmint smart wallets on Solana (in `x402-rs`). Nobody else on Earth can write the authoritative explainer for this. lobster.cash does not currently have a skill that explains Solana smart wallet payment semantics to agents — it just executes them.

The skill's job is **business intent**: "when an agent needs to pay an x402 endpoint from a Solana Crossmint smart wallet, here is the decision tree, here is what to expect, here is how to handle the edge cases (CPI vs top-level transfer, settlement account vs direct transfer, fallback to devnet)."

### Format (per lobster.cash Skill Compatibility Guide)

- `SKILL.md` — decision tree for the agent
- `README.md` — developer onboarding
- Calls `@crossmint/lobster-cli` for payment execution (never implements payment logic itself)
- Includes the 3 required behaviors: wallet precheck, payment action description, payment status + error handling

### Install path (per lobster.cash docs)

```bash
npx skills add https://github.com/<user>/crossmint-cpi-skill --global --yes
```

### Certification path

After build: message Fede on Telegram for certification into the lobster.cash/skills directory. Fede is Saul's internal champion AND the official cert gate. Highest-probability fast approval.

---

## Shared artifacts (content + campaign)

### Blog post (one, shared)

- **Title:** *"The two things lobster.cash doesn't ship: an MCP server and a Solana CPI skill. I built both."*
- **Length:** 1500-1800 words
- **Structure:**
  1. Hook (Fede's quote: "x402 esta en abandono un poco" — with permission)
  2. What lobster.cash is and isn't (the two gaps)
  3. Artifact 1: the MCP server (architecture + install + demo)
  4. Artifact 2: the lobster.cash skill (architecture + install + cert path)
  5. The CPI inner instruction nuance in plain English (the unique technical content)
  6. What's next (what happens if Crossmint adopts both)

### Campaign proposal (one, shared, 1 page)

- **Why:** Two real gaps, both confirmed by Fede, both addressable with shared code
- **What:** Publish both artifacts under the Crossmint org; MCP server in MCP Registry + Claude Desktop skill marketplace; skill in lobster.cash directory via Fede's certification
- **Who:** Fede (cert + skill distribution), Filippos (product signoff for MCP Registry submission), Crossmint Twitter + blog (launch day)
- **Outcomes:** MCP server installs in first 30 days, lobster.cash skill certification, time-to-first-transaction from MCP client, developer inbound on x402hackathon Discord mentioning either artifact

---

## Architecture — shared core

```
crossmint-wallets-mcp/               # Main repo (Artifact 1)
├── src/
│   ├── core/                        # Pure functions, reusable
│   │   ├── createWallet.ts
│   │   ├── getBalance.ts
│   │   ├── transferToken.ts
│   │   └── payX402Endpoint.ts       # THE HARD ONE — build first
│   ├── mcp/                         # MCP server wrapping core/
│   │   ├── server.ts
│   │   └── tools.ts
│   └── index.ts                     # npm entry point
├── demo/                            # Runnable smoke test
│   └── run.ts
├── package.json
├── tsconfig.json
└── README.md                        # Main entry, install for MCP clients

crossmint-cpi-skill/                 # Secondary repo (Artifact 2)
├── SKILL.md                         # Decision tree for lobster.cash
├── README.md                        # Developer onboarding
└── package.json                     # Publishable via `npx skills add`
```

Two repos, one narrative. The skill repo references the MCP repo but is independently installable via `npx skills add`.

---

## Time budget

Hard budget: 48h - 12h sleep - 4h meals/breaks - 2h meta = **30h effective**.

| Phase | Hours | Running total |
|---|---|---|
| **H+5 → H+7** Scaffold MCP server repo (package.json, tsconfig, core/, mcp/ skeleton) | 2h | 7h |
| **H+7 → H+11** Build `pay_x402_endpoint` (the critical path — parse 402, sign via Crossmint, submit, return response) | 4h | 11h |
| **H+11 → H+12** Build `create_wallet` | 1h | 12h |
| **H+12 → H+13** Build `get_balance` | 1h | 13h |
| **H+13 → H+15** Build `transfer_token` | 2h | 15h |
| **H+15 → H+17** MCP server binding (wrap core/ in @modelcontextprotocol/sdk tool definitions) | 2h | 17h |
| **H+17 → H+19** Claude Desktop smoke test (mainnet USDC) | 2h | 19h |
| **H+19 → H+21** Lobster.cash skill: SKILL.md + README + test install path | 2h | 21h |
| **H+21 → H+22** Skill smoke test (shells out to @crossmint/lobster-cli) | 1h | 22h |
| **H+22 → H+25** Blog post draft | 3h | 25h |
| **H+25 → H+26** Campaign 1-pager | 1h | 26h |
| **H+26 → H+28** Final polish, README rewrite, video demo | 2h | 28h |
| **H+28 → H+32** Buffer (for unplanned problems, Saul voice pass on content, Devil's Advocate review) | 4h | 32h |
| **H+32 → H+36** Push + email submission | 4h | 36h |

**Ship at H+36.** Submission at ~2026-04-10 18:17 EDT (during EU business hours April 11, when Adolfo and the team in Madrid will see it).

**Hard cutoff:** H+40. After that, we ship whatever is ready.

---

## Cuts if we hit a blocker

In order of what to drop first:

1. **`crossmint_transfer_token`** — if x402 payment works but transfer has bugs, drop transfer. The headline feature is paying x402, not generic transfers.
2. **Lobster.cash skill testing** — ship the SKILL.md + README without a full smoke test. Ask Fede to test it during certification.
3. **Video demo** — ship a text-only README walkthrough with screenshots. No video.
4. **Mainnet support** — devnet only. Safer and still demoable.
5. **Campaign 1-pager** — keep it to half a page if time pressured.
6. **Entire Artifact 2 (lobster.cash skill)** — if the MCP server blows through the budget, drop the skill entirely. Ship only the MCP server. Mention the skill as "v0.2 on the roadmap."

---

## The critical path

**`pay_x402_endpoint` MUST work by H+11.** Everything else depends on it being technically feasible.

The risk: `@crossmint/wallets-sdk` may not expose a raw signing method for arbitrary x402 payloads from a smart wallet. Mitigations:

- **Plan A:** Use `@crossmint/wallets-sdk` `sign` method if it exists (need to verify)
- **Plan B:** Wrap `@crossmint/lobster-cli` via library exports (the CLI has published library exports per its README) — the CLI already solves this problem, we just need to call it
- **Plan C:** Use the Crossmint REST API directly with `POST /wallets/{id}/transactions/sign` and construct the x402 payload manually
- **Plan D (nuclear):** Fall back to using the Crossmint wallet's `transfer()` method with the x402 payTo address, which works for direct-transfer x402 schemes but not all variants. Degraded but shippable.

**Decision rule:** if none of A/B/C work by H+10, we pivot to Plan D and ship with an explicit caveat in the README that only "direct transfer" x402 schemes are supported. Still shippable, still honest, still useful.

---

## Non-negotiables (forwarded from concept-lock v1 + DA kickoff)

1. Facilitator-agnostic — Faremeter as default, Ultravioleta facilitator not mentioned in the MCP server code or skill
2. No "Ultravioleta" branding in public-facing artifacts
3. Saul voice pass on the blog post before ship
4. Final Devil's Advocate review at H+28
5. Ship during EU business hours (H+36 target, H+40 hard cutoff)
6. README first, then code (documentation-driven development)
7. The subject is the MCP server + the skill — Saul's facilitator is infrastructure, not showcase

---

## Next action

Scaffold the MCP server repo at `z:\crossmint-wallets-mcp\` (separate from `z:\crossmint-devrel-challenge\`). Then build Tool 4 first.

**`crossmint-devrel-challenge/`** stays as the coordination workspace (plans, research, brainstorm, decisions). **`crossmint-wallets-mcp/`** is the actual submission artifact (code, README, blog post, campaign). The skill repo `crossmint-cpi-skill/` lives separately.

Final submission points reviewer to both artifact repos via the top-level `crossmint-devrel-challenge/README.md`.
