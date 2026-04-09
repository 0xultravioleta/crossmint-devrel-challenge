# BUILD-PLAN — Crossmint DevRel Challenge Submission

> **Status:** DRAFT — pending Saul approval before execution
> **Concept locked:** `concept-lock-v2.md` (dual artifact)
> **Current time:** ~H+5 (2026-04-09 ~11:30 AM EDT)
> **Ship target:** H+36 (2026-04-10 ~18:30 EDT) — during EU business hours Friday April 11
> **Hard cutoff:** H+40 (2026-04-10 ~22:30 EDT)
> **Author:** Claude (main conversation)

---

## 0. Guiding principles (refuse to compromise on these)

1. **Research before code.** Don't write a line of TypeScript until we know exactly what the Crossmint SDK exposes, what lobster-cli's library surface is, and how the MCP SDK wants tools defined. Assumptions are the biggest time sink in a 48h sprint.
2. **Critical path first.** `pay_x402_endpoint` has 4 fallback plans. If none work, we pivot within 4 hours, not after we've sunk 20 hours.
3. **Documentation-driven development.** README comes before code. Blog post outline comes before blog post. Tool descriptions come before tool implementations.
4. **Real chains, real clients.** Solana mainnet + Devnet, Base mainnet + Sepolia. Claude Desktop as the primary smoke test client. No toy demos.
5. **Cut scope, never quality.** If behind schedule, we drop a tool or drop chain support, but we never ship something that doesn't actually run on the reviewer's machine.
6. **Framing discipline.** The MCP server is the MCP-native companion to lobster.cash. The skill is a net-new certified lobster.cash skill. Both position Saul as an ecosystem builder, not a competitor.
7. **No Ultravioleta branding.** In the code, README, blog post, campaign, or any public-facing artifact. Saul's facilitator is infrastructure, not subject.
8. **Ship during EU business hours.** H+36 target gives the Madrid team a business day to evaluate.

---

## 1. Repo layout (final)

```
z:\crossmint-devrel-challenge\            # Coordination workspace (private)
├── MASTERPLAN.md
├── TEAM.md
├── README.md                             # Final submission entry point
├── _internal/                            # Working notes, brainstorm, decisions
│   ├── hr-proposal.md
│   ├── pm-journal.md
│   ├── decisions.md
│   ├── devils-advocate/
│   ├── brainstorm/
│   ├── phase-notes/
│   │   ├── concept-lock.md               # v1 (Hello x402, killed)
│   │   ├── concept-lock-v2.md            # v2 (dual artifact, active)
│   │   └── BUILD-PLAN.md                 # THIS FILE
│   └── research/
│       ├── FINDINGS.md
│       ├── crossmint-docs/
│       ├── crossmint-github/
│       ├── crossmint-sdk-api.md          # NEW, Phase 2A
│       ├── lobster-cli-library.md        # NEW, Phase 2A
│       ├── mcp-sdk-patterns.md           # NEW, Phase 2A
│       └── x402-payload-format.md        # NEW, Phase 2A
├── content/                              # Blog post + twitter thread (Phase 3)
├── campaign/                             # 1-pager (Phase 4)
└── demo/                                 # DEPRECATED - killed v1 Hello x402 files

z:\crossmint-wallets-mcp\                 # ARTIFACT 1 — public repo
├── README.md                             # Install + quickstart
├── LICENSE (MIT)
├── package.json                          # npm publish
├── tsconfig.json
├── .gitignore
├── .env.example
├── src/
│   ├── index.ts                          # Library exports
│   ├── core/
│   │   ├── client.ts                     # CrossmintWallets client init
│   │   ├── create-wallet.ts              # Tool 1 logic
│   │   ├── get-balance.ts                # Tool 2 logic
│   │   ├── transfer-token.ts             # Tool 3 logic
│   │   ├── pay-x402-endpoint.ts          # Tool 4 logic (CRITICAL)
│   │   └── types.ts                      # Shared types
│   └── mcp/
│       ├── server.ts                     # MCP server entry (bin)
│       ├── tools.ts                      # Tool definitions + Zod schemas
│       └── errors.ts                     # Standardized error responses
├── demo/
│   ├── smoke-test.ts                     # Runs all 4 tools end to end
│   └── claude-desktop-config.json        # Copy-paste config example
└── dist/                                 # Compiled output (published to npm)

z:\crossmint-cpi-skill\                   # ARTIFACT 2 — public repo
├── README.md                             # Developer onboarding
├── LICENSE (MIT)
├── package.json                          # For `npx skills add` discoverability
└── SKILL.md                              # Lobster.cash skill format
```

**Decision:** The two artifact repos are SIBLING to `crossmint-devrel-challenge`, not nested inside. This lets us push each to GitHub independently with clean git histories.

**Decision:** Both artifact repos are MIT licensed. This makes it trivial for Crossmint to fork or adopt officially.

**Decision:** Both repos will be created public under Saul's personal GitHub (`0xultravioleta/crossmint-wallets-mcp` and `0xultravioleta/crossmint-cpi-skill`) so Crossmint can fork them into their org if they want.

---

## 2. Phase 2A — Research (H+5 → H+7, 2 hours)

**Goal:** Answer the 4 questions that determine whether each Plan A/B/C/D of the critical path works. Write findings to files.

### Research tasks (parallelizable)

| # | Question | File output | Primary source | Fallback |
|---|---|---|---|---|
| R1 | What methods does `@crossmint/wallets-sdk` Wallet class expose? Does it have a raw `sign()` method? | `_internal/research/crossmint-sdk-api.md` | npmjs.com/package/@crossmint/wallets-sdk | Playwright → GitHub source → ask user |
| R2 | What does `@crossmint/lobster-cli` export as a library? Can we import its functions into an MCP server? | `_internal/research/lobster-cli-library.md` | npmjs.com/package/@crossmint/lobster-cli | Playwright → GitHub/Crossmint/lobstercash-cli-skills → ask user |
| R3 | How does `@modelcontextprotocol/sdk` define tools with stdio transport? What's the minimal server example? | `_internal/research/mcp-sdk-patterns.md` | github.com/modelcontextprotocol/typescript-sdk | Playwright → ask user (Saul already has AutoJob MCP experience) |
| R4 | What does an x402 402 response body look like on Solana? What payment payload does Faremeter expect? | `_internal/research/x402-payload-format.md` | x402 spec + Faremeter docs | Playwright → Saul's own x402-rs source → ask user |

### Research gate (H+7)

After 2 hours, the 4 files must exist with enough detail to answer:

- **R1 verdict:** Can we call `wallet.sign(payload)` directly for arbitrary x402 messages? YES / NO / NEEDS WRAPPER
- **R2 verdict:** Does `@crossmint/lobster-cli` have importable library exports? YES / NO / ONLY VIA SHELL
- **R3 verdict:** What's the minimal MCP server skeleton we need? (just the code shape)
- **R4 verdict:** What fields does a Solana x402 payment payload need?

**If R1 is YES → Plan A for Tool 4 (direct SDK signing)**
**If R1 is NO but R2 is YES → Plan B for Tool 4 (wrap lobster-cli library)**
**If R1 and R2 are NO but R4 is clear → Plan C for Tool 4 (manual payload + REST API)**
**If R1, R2 are NO and R4 is unclear → Plan D (transfer fallback, degraded mode)**

### Gate rule

**No code is written until the research gate passes.** The gate output is a one-line decision in `_internal/decisions.md`: "Tool 4 will use Plan [A/B/C/D] because [reason]."

---

## 3. Phase 2B — Scaffolding (H+7 → H+8, 1 hour)

**Goal:** Create the repo structure and skeleton files for both artifacts. No business logic yet.

### Tasks

- `z:\crossmint-wallets-mcp\`:
  - [x] `package.json` (already exists — review after R3 to confirm SDK versions)
  - [x] `tsconfig.json` (already exists)
  - [x] `.gitignore` (already exists)
  - [x] `LICENSE` (already exists)
  - [ ] `.env.example` with required env vars
  - [ ] `README.md` skeleton with the sections we'll fill in later
  - [ ] `src/index.ts` empty export stubs
  - [ ] `src/core/client.ts` — stub
  - [ ] `src/core/types.ts` — interface definitions
  - [ ] `src/core/create-wallet.ts` — stub function signature
  - [ ] `src/core/get-balance.ts` — stub
  - [ ] `src/core/transfer-token.ts` — stub
  - [ ] `src/core/pay-x402-endpoint.ts` — stub with TODO comments referencing Plan decision
  - [ ] `src/mcp/server.ts` — minimal MCP server that does nothing yet
  - [ ] `src/mcp/tools.ts` — tool definition stubs
  - [ ] `src/mcp/errors.ts` — stub
  - [ ] `demo/smoke-test.ts` — stub that imports all 4 core functions
  - [ ] `demo/claude-desktop-config.json` — config example

- `z:\crossmint-cpi-skill\`:
  - [ ] `package.json` (minimal, for npm / skills discoverability)
  - [ ] `LICENSE` (MIT)
  - [ ] `.gitignore`
  - [ ] `README.md` skeleton
  - [ ] `SKILL.md` skeleton (follows lobster.cash compatibility guide format)

### Gate (H+8)

`tsc --noEmit` on the MCP server repo compiles cleanly (even though all functions are stubs). The repo is pushable to GitHub (but not pushed yet).

---

## 4. Phase 2C — Critical Path: `pay_x402_endpoint` (H+8 → H+12, 4 hours)

**Goal:** Prove the killer tool works. This is the single highest-risk task in the whole submission.

### Plan A: Direct Crossmint SDK signing

If R1 confirmed the SDK has a raw sign method:

1. Create a Crossmint smart wallet on Solana Devnet
2. Make a GET request to a Solana x402 endpoint (need to identify one — the `crossmint-agentic-finance/weather` subfolder or Faremeter test endpoint)
3. Parse the 402 response
4. Construct the x402 payment payload according to spec (from R4)
5. Call `wallet.sign(payload)` to get a signed authorization
6. Add the signed payload to `X-PAYMENT` header
7. Retry the request
8. Return the response body

### Plan B: Wrap @crossmint/lobster-cli library

If R2 confirmed library exports:

1. Import the relevant functions from `@crossmint/lobster-cli` (likely something like `x402.fetch(url, options)`)
2. Initialize with the Crossmint API key
3. Call the library function
4. Return the response
5. Document in README: "This tool is a thin wrapper around @crossmint/lobster-cli's `crypto x402 fetch` command"

### Plan C: Manual x402 payload + Crossmint REST API

If Plans A and B fail:

1. Create wallet via Crossmint REST API
2. Fetch the x402 endpoint, parse 402
3. Construct the EIP-3009 or Solana equivalent payment payload
4. Submit the payload for signing via `POST /wallets/{id}/transactions/sign`
5. Get back the signed bytes
6. Add to `X-PAYMENT` header, retry

### Plan D: Degraded — `transfer()` fallback

Only if A/B/C all fail:

1. Treat the x402 `payTo` address as a regular transfer destination
2. Call `wallet.transfer(payTo, amount, token)` (the standard Crossmint SDK send method)
3. Wait for confirmation
4. Then retry the x402 endpoint with a manually-constructed `X-PAYMENT` header indicating the tx signature
5. Document in README as "direct-transfer x402 only, other variants not supported in v0.1"

### Implementation order for Tool 4

1. **Setup** (30 min): Create Devnet wallet, fund with USDC via Circle faucet, identify target x402 endpoint
2. **Plan attempt** (2h): Try Plans A, B, C, D in order until one works
3. **Smoke test** (30 min): Run the function end-to-end, confirm it returns a valid response
4. **Error handling** (30 min): Add proper error messages for the common failure modes (insufficient balance, invalid payload, facilitator rejection)
5. **Commit** (30 min): One clean commit with the working implementation + comments explaining the plan chosen

### Gate (H+12)

**`pay_x402_endpoint` returns a valid response body from a Solana Devnet x402 endpoint.** If this gate fails by H+12, we pivot to Plan B concept (lobster.cash skill only, no MCP server) and save the MCP server for v2.

---

## 5. Phase 2D — Remaining Core Tools (H+12 → H+15, 3 hours)

**Goal:** Build the 3 simpler tools. Each should be < 1h with solid implementation.

### Tool 1: `crossmint_create_wallet` (1 hour)

- Accepts: `{ email, chain }` where chain ∈ {"solana", "solana-devnet", "base", "base-sepolia"}
- Calls `CrossmintWallets.createWallet({ chain, recovery: { type: "server", secret } })`
- Returns: `{ address, chain, walletId, email }`
- Handles: duplicate wallet (return existing), invalid chain, missing API key

### Tool 2: `crossmint_get_balance` (1 hour)

- Accepts: `{ email, tokens? }` (defaults to `["usdc"]`)
- Calls `wallet.balances(tokens)` on the fetched wallet
- Returns: `{ address, chain, balances: [{ symbol, amount, decimals }] }`
- Handles: wallet not found, empty balance

### Tool 3: `crossmint_transfer_token` (1 hour)

- Accepts: `{ email, to, token, amount }`
- Calls `wallet.send(to, token, amount)`
- Returns: `{ txHash, from, to, token, amount }`
- Handles: insufficient balance, invalid address

### Gate (H+15)

All 4 tools implemented and smoke-tested individually. `demo/smoke-test.ts` runs them in sequence and prints output.

---

## 6. Phase 2E — MCP Server Wiring (H+15 → H+17, 2 hours)

**Goal:** Expose the 4 core functions as MCP tools via stdio transport.

### Tasks

1. Implement `src/mcp/server.ts` with `@modelcontextprotocol/sdk` (use patterns from R3)
2. Register the 4 tools in `src/mcp/tools.ts` with Zod schemas
3. Wire tool invocations to call `src/core/` functions
4. Wire stdio transport
5. Add stderr logging for debugging (MCP servers must NOT log to stdout — stdio transport uses stdout for JSON-RPC)
6. Build with `tsc`
7. Create `dist/mcp/server.js` as the binary entry point
8. Test locally: `node dist/mcp/server.js < test-input.json > test-output.json`

### Claude Desktop config example

```json
{
  "mcpServers": {
    "crossmint-wallets": {
      "command": "npx",
      "args": ["-y", "crossmint-wallets-mcp"],
      "env": {
        "CROSSMINT_API_KEY": "sk_...",
        "CROSSMINT_RECOVERY_SECRET": "your_server_secret"
      }
    }
  }
}
```

### Gate (H+17)

- `dist/mcp/server.js` exists and runs
- Valid MCP server (responds to `initialize`, `tools/list`, `tools/call`)
- Each tool call returns a well-formed response

---

## 7. Phase 2F — Claude Desktop Smoke Test (H+17 → H+19, 2 hours)

**Goal:** End-to-end verify the MCP server works inside Claude Desktop. Record output for the video demo.

### Tasks

1. Publish the package to npm (first publish, `crossmint-wallets-mcp@0.1.0`)
2. Add the config block to local Claude Desktop's `claude_desktop_config.json`
3. Restart Claude Desktop
4. Open a chat, type: *"Create a Crossmint smart wallet on Solana Devnet for test@example.com"*
5. Verify the tool is called and returns the wallet address
6. Type: *"What's the USDC balance of that wallet?"*
7. Type: *"Send 0.01 USDC from that wallet to <some test address>"*
8. Type: *"Pay the x402 endpoint at <url> from that wallet, max $0.10"*
9. Take screenshots at each step for the blog post
10. Record a screen capture of the full flow for the video demo

### Gate (H+19)

All 4 tools work in Claude Desktop. Screenshots captured. Video recorded.

---

## 8. Phase 2G — Lobster.cash Skill (H+19 → H+22, 3 hours)

**Goal:** Ship the second artifact: a certified lobster.cash skill that teaches agents the Solana CPI scanning nuance.

### SKILL.md structure (per lobster.cash compatibility guide)

```markdown
---
name: crossmint-cpi-solana
description: Teaches AI agents how to pay x402 endpoints from Solana Crossmint smart wallets, with awareness of the CPI inner instruction nuance.
compatibility:
  lobster: ">= 0.1.0"
---

# Decision tree: paying x402 from a Solana Crossmint smart wallet

## When to use this skill

Use this skill when all of the following are true:
1. The user or agent needs to pay an x402-paywalled HTTP endpoint
2. The payment will come from a Crossmint smart wallet on Solana
3. The agent has access to `@crossmint/lobster-cli`

## Step 1: Wallet precheck
...

## Step 2: Payment action description
...

## Step 3: Call lobster-cli x402 fetch
...

## Step 4: Handle CPI inner instruction nuance
...

## Step 5: Return response
...

## Edge cases
- Insufficient USDC balance → ...
- Wallet not configured → ...
- Payment timeout → ...
- CPI scanning failure → ...
```

### README.md structure

- What this skill is
- Why the CPI nuance matters
- Install path: `npx skills add https://github.com/<user>/crossmint-cpi-skill --global --yes`
- How to certify it in the lobster.cash directory (link to Fede)
- Example agent conversation

### Gate (H+22)

- `SKILL.md` passes the 3 required behaviors check (wallet precheck, payment action description, payment status + error handling)
- `README.md` covers install + usage
- Repo is pushable to GitHub

---

## 9. Phase 3 — Content (H+22 → H+27, 5 hours)

### 9.1 Blog post (3 hours)

**Title options (pick one at H+22):**

1. "The two things lobster.cash doesn't ship: an MCP server and a Solana CPI skill"
2. "Claude Desktop can't install lobster.cash. Here's the MCP server that fixes that."
3. "Crossmint ships the wallet. lobster.cash ships the CLI. I shipped the MCP bridge."

**Outline:**

1. **Hook** (100 words) — "Fede told me x402 está en abandono un poco. Then he told me lobster.cash was their play — but 'no es lo que dices' when I described what I was building. So I built it."
2. **The two gaps** (300 words) — lobster.cash serves Claude Code, Cursor, OpenClaw via skills. But Claude Desktop, Continue.dev, Cline, and Codex CLI speak MCP, not skills. And nobody has shipped a Solana CPI scanning skill for agents paying x402 from smart wallets. Two gaps, same product area.
3. **Artifact 1: the MCP server** (400 words) — 4 tools, install in 30 seconds, Claude Desktop demo, architecture notes
4. **Artifact 2: the lobster.cash skill** (300 words) — what a certified skill does, how the CPI nuance is taught, cert path via Fede
5. **The CPI inner instruction nuance** (400 words) — the unique technical content. Why Solana smart wallet payments look invisible to naive facilitators. The "two-path verification" pattern (top-level first, simulation + inner instruction scan second). Plain English, no Rust code, no assembly.
6. **Why this matters for agents in 2026** (200 words) — x402 is the quiet payment rail for agent commerce. MCP is the agent tool discovery protocol. Smart wallets are the agent custody primitive. The three compose. Here's how.
7. **What's next** (100 words) — if Crossmint wants to adopt either artifact, it's MIT licensed, PR-ready, and the cert path is a Telegram DM away.

**Voice pass by Saul mandatory** (Phase 5 gate).

### 9.2 Twitter thread (1 hour)

8-10 tweets. Each tweet stands alone. Thread ends with a link to the repo.

### 9.3 Video demo script (1 hour)

60-90 second screen recording script:
- 0:00-0:10 Title card ("Crossmint Wallets MCP Server")
- 0:10-0:20 Install the config block in Claude Desktop
- 0:20-0:40 Claude creates a Solana wallet, checks balance
- 0:40-0:60 Claude pays an x402 endpoint, shows the response
- 0:60-0:90 Final frame: "Install: npx crossmint-wallets-mcp"

### Gate (H+27)

- `content/blog-post.md` draft complete
- `content/twitter-thread.md` complete
- `content/video-script.md` complete (video recording is optional — script is the commitment)

---

## 10. Phase 4 — Campaign Proposal (H+27 → H+29, 2 hours)

**Goal:** One-page proposal for Crossmint team to adopt both artifacts.

### Structure

**Page 1 (max 500 words):**

- **Why this campaign** (100 words)
  - Fede on Telegram: "x402 está en abandono un poco" + "crossmint está muy metido en agentic stuff"
  - Solana Foundation's x402 guide says Crossmint is "currently in progress"
  - Existing Crossmint MCP (`mcp-crossmint-checkout`) is stale from May 2025
  - Two real gaps, two shippable fixes, MIT licensed and ready

- **What needs to happen** (200 words)
  - Fork both repos into the Crossmint org (or accept the PR)
  - MCP server → publish to npm as `@crossmint/wallets-mcp` official
  - MCP server → submit to MCP Registry (modelcontextprotocol.io)
  - MCP server → add to Claude Desktop community listings
  - Lobster.cash skill → certify via Fede, list in lobster.cash/skills directory
  - One blog post on blog.crossmint.com using the draft provided
  - One Twitter thread from the Crossmint account (draft provided)
  - Total effort for Crossmint team: ~4 hours of review + 2 hours of distribution

- **Expected outcomes** (150 words)
  - 30-day metrics: MCP registry installs, GitHub stars on both repos, skill directory installs, x402hackathon mentions referencing the artifacts
  - 60-day strategic: Claude Desktop users can pay x402 endpoints from Crossmint wallets → first-party integration with the biggest MCP-native client
  - Strategic positioning: Crossmint becomes the first blockchain platform with BOTH an official MCP server AND an official lobster.cash skill ecosystem
  - Ecosystem: positions Crossmint as the reference implementation for agentic commerce across multiple transport protocols

- **Real distribution channels** (50 words)
  - MCP Registry: modelcontextprotocol.io/servers (submit via GitHub PR)
  - Claude Desktop community listings
  - lobster.cash/skills (via Fede certification)
  - x402hackathon Discord (announce in #projects)
  - Twitter: @crossmint_io, @alfongj, @cryptofede, @0xultravioleta
  - Crossmint blog: blog.crossmint.com
  - Solana Developer Relations: via the "in progress" guide update

### Gate (H+29)

`campaign/proposal.md` is exactly 1 page. Reads in 60 seconds. Names real handles.

---

## 11. Phase 5 — Integration & Polish (H+29 → H+33, 4 hours)

**Goal:** Everything readable, runnable, cross-referenced. Final QA.

### Tasks

1. **Top-level README rewrite** (1 hour)
   - `z:\crossmint-devrel-challenge\README.md` becomes the submission entry point
   - Links to both artifact repos
   - Links to blog post, twitter thread, campaign proposal
   - 60-second overview at the top
2. **Cross-references** (30 min)
   - MCP server README links to lobster.cash skill
   - Lobster.cash skill README links to MCP server
   - Both link back to the submission entry point (optional)
3. **Clean commit history** (30 min)
   - Review all commits in both artifact repos
   - Squash any messy WIP commits
   - Ensure each commit has a conventional message
4. **Final smoke test from clean state** (1 hour)
   - Fresh clone of the MCP server repo
   - Fresh `node_modules`
   - Fresh Claude Desktop config
   - Full end-to-end: create wallet, balance, transfer, pay x402
5. **Saul voice pass on content** (30 min — Saul's task)
   - Saul reads blog post, edits for voice
   - Saul reads twitter thread, edits
6. **Devil's Advocate final review** (30 min)
   - DA reads everything end-to-end
   - Tries to break the submission
   - Flags any red flags before ship

### Gate (H+33)

- All three deliverables run on a clean machine
- Saul has signed off on content voice
- Devil's Advocate has cleared the submission
- Top-level README is complete and accurate

---

## 12. Phase 6 — Submission (H+33 → H+36, 3 hours)

**Goal:** Public push, email draft, send.

### Tasks

1. **Push both artifact repos to public GitHub** (30 min)
   - `0xultravioleta/crossmint-wallets-mcp` (public)
   - `0xultravioleta/crossmint-cpi-skill` (public)
   - Verify both are accessible from an incognito window
2. **Publish MCP server to npm** (30 min)
   - `npm publish` as `crossmint-wallets-mcp@0.1.0`
   - Verify: `npx crossmint-wallets-mcp --version` works from a fresh shell
3. **Verify blog post and campaign are accessible** (15 min)
   - Host at a permanent URL or link directly from the top-level submission README
4. **Draft submission email to Adolfo** (30 min)
   - Subject: "Crossmint DevRel challenge submission: Crossmint Wallets MCP + lobster.cash CPI skill"
   - Body: 3 paragraphs, links to the submission README (which has everything)
   - Mention the 48h window was respected, shipping early during EU business hours
   - Thank Adolfo + Filippos + Fede
5. **Saul final review + send** (30 min)
   - Saul reads the email
   - Saul verifies all links work
   - Saul sends
6. **Confirm delivery** (15 min)
   - Check that the email is in Adolfo's inbox
   - Optional: Telegram DM to Fede letting him know the submission is in

### Gate (H+36)

**SUBMISSION SENT.** Done.

---

## 13. Parallelism strategy

### What can run in parallel

| Phase | Can overlap with | Why |
|---|---|---|
| Phase 2A (Research) | — | Foundation, must complete first |
| Phase 2B (Scaffold) | — | Needs research outputs |
| Phase 2C (Tool 4) | — | Critical path, serial |
| Phase 2D (Tools 1-3) | Phase 3.1 (blog outline) | Content outline doesn't need working code |
| Phase 2E (MCP wiring) | Phase 3.1 (blog draft start) | Blog introduction doesn't need MCP working |
| Phase 2F (Claude Desktop smoke) | Phase 2G (lobster.cash skill) if spawning subagent | Skill is independent work |
| Phase 3 (Content) | Phase 4 (Campaign) | Both are writing tasks |
| Phase 5 (Polish) | — | Integration, serial |
| Phase 6 (Submission) | — | Submission, serial |

### Subagent spawning plan

**DO spawn (if API recovers):**

- A "Content Writer" agent to draft the blog post + twitter thread in parallel with Phase 2E wiring (saves ~2h of wall clock)
- A "Campaign Strategist" agent to draft the 1-pager in parallel with Phase 3 content (saves ~1h)

**DO NOT spawn:**

- Research agents for Phase 2A — research findings are high-judgment and must feed the critical path decision
- Code builder agents — too risky for 48h, direct Claude execution is more reliable
- PM / DA coordinator agents — already running as meta-analysis in main conversation

---

## 14. Risk register

| # | Risk | Severity | Mitigation | Trigger for mitigation |
|---|---|---|---|---|
| R01 | `@crossmint/wallets-sdk` doesn't expose raw signing → Plan A fails | HIGH | Plan B (lobster-cli wrap), Plan C (REST API), Plan D (transfer fallback) | Research gate at H+7 |
| R02 | `@crossmint/lobster-cli` has no library exports, only shell-callable → Plan B fails | MEDIUM | Shell out from Node via `child_process.execSync` (ugly but works) | Phase 2C hour 2 |
| R03 | Crossmint REST API signing endpoint is undocumented or behind auth gate → Plan C fails | MEDIUM | Plan D (transfer fallback) with explicit README caveat | Phase 2C hour 3 |
| R04 | Solana Devnet / Circle faucet is down during development | MEDIUM | Fund wallet manually from a personal mainnet wallet, or use a cached faucet allocation | At wallet funding step |
| R05 | Claude Desktop config path differs on Windows vs macOS, config fails on reviewer's OS | MEDIUM | Document both paths in README, provide working config examples for each | Phase 2F |
| R06 | npm publish blocked (name conflict, rate limit, account issue) | LOW | Change package scope or name, use GitHub npm registry as fallback | Phase 6.2 |
| R07 | Blog post reads like generic AI output | MEDIUM | Mandatory Saul voice pass in Phase 5 | Phase 5.5 |
| R08 | Campaign proposal names generic channels | LOW | Real handles list already drafted in Phase 4 spec above | Phase 4 |
| R09 | API 529 overload blocks subagent spawning for content / campaign | MEDIUM | Fall back to direct Claude execution in main conversation | Any spawn attempt |
| R10 | Time budget blown by H+30 | HIGH | Scope cut hierarchy: drop tool (any beyond Tool 4), drop lobster.cash skill entirely, drop video demo, drop mainnet support, drop campaign polish | Gate at H+30 |
| R11 | Reviewer's Claude Desktop can't reach the Crossmint API (API key issue) | MEDIUM | Document how to create an API key with required scopes, include screenshots | Phase 2F |
| R12 | Faremeter facilitator is down during demo | LOW | Have a local facilitator fallback documented | Phase 2F |
| R13 | Fede says "we ARE shipping a wallets MCP server next week" after all | HIGH | Pivot to the lobster.cash skill only (Plan B concept). The skill is not duplicative because it's a new skill Fede can certify, not a competing server. | Only if Fede responds before H+8 with this |
| R14 | The lobster.cash skill SDK has undocumented constraints that block certification | MEDIUM | Ship the skill as a "pending certification" draft; blog post says "submitted for certification review" | Phase 2G |
| R15 | EU business hours ship misses the window if we're delayed | LOW | Hard cutoff H+40 catches this; even H+40 is still within the 48h spec | Phase 6 |

---

## 15. Decision gates (hard stops)

| Gate | Time | Criterion | Action if failed |
|---|---|---|---|
| G1: Research complete | H+7 | All 4 research files exist, Plan decision made | Extend 1h, force decision at H+8 |
| G2: Tool 4 working | H+12 | `pay_x402_endpoint` returns valid response end-to-end | Pivot to Plan B concept (skill only) |
| G3: All 4 tools working | H+15 | `demo/smoke-test.ts` runs all tools successfully | Drop 1 tool, ship with 3 |
| G4: MCP server working in Claude Desktop | H+19 | Video recording of working tool calls | Ship without video, text walkthrough only |
| G5: Content draft complete | H+27 | Blog + thread + script all drafted | Cut thread, ship blog only |
| G6: Campaign proposal complete | H+29 | 1-pager, 1 page, real channels | Cut to half page, ship |
| G7: Clean smoke test | H+33 | Fresh clone + fresh install + end-to-end runs | Debug, lose buffer time |
| G8: Saul voice pass | H+33 | Saul has edited blog + thread | Ship with Claude voice, flag in cover email |
| G9: DA final review | H+33 | DA clears submission | Fix flagged items, lose buffer |
| G10: Ship | H+36 | Email sent | Ship at H+40 absolute max |

---

## 16. Conventional commits for this project

All commits in both artifact repos follow:

- `feat:` new feature
- `fix:` bug fix
- `docs:` documentation
- `chore:` tooling, dependencies
- `refactor:` code refactor
- `test:` tests

Scoped when helpful:
- `feat(mcp): add pay_x402_endpoint tool`
- `docs(readme): add Claude Desktop config`
- `chore(deps): bump @crossmint/wallets-sdk to 0.8.0`

No co-author line on these repos (they are Saul's personal GitHub, not a Crossmint repo).

---

## 17. What I need from Saul to start execution

1. **Approval of this plan.** Read and say go / revise / kill.
2. **Confirm the GitHub handle and repo names:**
   - MCP server repo: `0xultravioleta/crossmint-wallets-mcp` ?
   - Skill repo: `0xultravioleta/crossmint-cpi-skill` ?
   - Or different handles/names?
3. **Confirm the npm publish authority:** does Saul have an npm account that can publish under the chosen package name? If not, we ship as `@0xultravioleta/crossmint-wallets-mcp` or similar scoped name.
4. **Confirm the Crossmint API key access:** Saul needs a valid server API key with the 5 required scopes. If it's not ready, that's a Phase 2A blocker.
5. **Windows vs WSL:** confirm the build will happen on Windows (per the CLAUDE.md rules) and the Claude Desktop smoke test will happen on Windows. If Saul is on Mac for this part, note it.
6. **Fede's Telegram response:** did Fede say anything further about "x402 en abandono" or the MCP angle? Any intel he shared privately should be incorporated now.
7. **Subagent preference:** do you want me to spawn Content Writer + Campaign Strategist agents in parallel during Phase 2E to save ~3h wall clock? Or keep everything in main conversation for reliability?

---

## 18. After approval, the first concrete action is

**Phase 2A research, Task R1: open Playwright and navigate to `https://www.npmjs.com/package/@crossmint/wallets-sdk` to read the full SDK API surface.**

Nothing else starts until research is done.

---

## Appendix A — Unknowns that could kill the plan

These are risks I cannot fully size without doing the research first:

1. **Does `@crossmint/wallets-sdk` support Solana smart wallets for arbitrary signing, or only for transfers?** If the SDK is transfer-only for Solana, Tool 4 is impossible without bypassing the SDK entirely.
2. **Does `@crossmint/lobster-cli` actually have library exports, or is that a misreading of the CLI README?** The README says "Library exports for programmatic use" but I haven't seen the actual module interface.
3. **Is the MCP stdio transport stable across npm-published packages?** There have been breaking changes in `@modelcontextprotocol/sdk` in the last few months. Need to pin a known-good version.
4. **Does `npx crossmint-wallets-mcp` actually work as a published binary?** Need to test from a fresh directory before submitting.
5. **Is the lobster.cash skill format actually verifiable without internal Crossmint review?** The compatibility guide is behavioral, not technical — Fede is the gate.

Every one of these has a research task in Phase 2A. The plan is adaptive to whatever we find.

---

## Appendix B — What "done" looks like at H+36

- Two public GitHub repos: `crossmint-wallets-mcp` and `crossmint-cpi-skill`, both MIT
- One npm package published: `crossmint-wallets-mcp@0.1.0`
- One blog post draft (markdown) at `crossmint-devrel-challenge/content/blog-post.md`
- One Twitter thread at `content/twitter-thread.md`
- One campaign 1-pager at `campaign/proposal.md`
- One top-level `README.md` pointing reviewers to all of the above
- One submission email sent to Adolfo, copied to Fede, referencing Filippos
- A video recording OR a screenshot walkthrough proving the MCP server works in Claude Desktop
- All committed, all pushed, all public

Anything beyond this is out of scope. Anything missing from this is a PM veto.
