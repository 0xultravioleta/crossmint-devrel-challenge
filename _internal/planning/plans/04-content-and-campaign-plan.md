# 04 — Content & Campaign Plan

> **Role:** Content & Campaign Planner (merged per DA critique)
> **Owner:** Claude (direct execution), with Saul voice pass mandatory
> **Mandate:** Plan the blog post, Twitter thread, video script, AND the 1-page campaign proposal — all as a single writing track with shared narrative
> **Scope:** Phase 3 (Content, H+22 → H+27) + Phase 4 (Campaign, H+27 → H+29)
> **Depends on:** `03-mcp-build-and-skill-plan.md` (needs the artifacts to exist before the blog post can reference real tool outputs)
> **Line target:** 400-700

---

## Framing discipline (ALL content outputs)

- **MCP server is positioned as the MCP-native COMPANION to lobster.cash** — never a replacement, never a competitor
- **Faremeter is the preferred facilitator** (per Filippos's exact words in the interview)
- **Ultravioleta facilitator is NEVER named** in any content artifact — not in the blog post, not in the Twitter thread, not in the video script, not in the campaign proposal
- **Execution Market, MeshRelay, and Saul's x402-rs facilitator are NEVER mentioned as subjects** (Devil's Advocate rule: "the stronger Filippos's compliment during the interview, the weaker the asset for this submission")
- **No Ultravioleta branding anywhere** in public-facing content
- **Saul's voice is the final voice** — mandatory voice pass at H+24 before ship
- **Fede's quote is usable** ONLY with explicit permission (he said "x402 está en abandono un poco" and "crossmint está muy metido en agentic stuff" on Telegram — but quoting a private DM publicly requires his OK)
- **Quote Filippos from the public interview signal** — Saul's interview with Filippos revealed "we've been focused on Faremeter" and "we need someone to focus on agents" — these can be used as context but NOT as direct quotes without permission
- **Safer framing:** reference the PUBLIC signals — Solana Foundation's "Crossmint is currently in progress" public guide, the `mcp-crossmint-checkout` stale public repo, the lobster.cash public landing page — not private Telegram or interview quotes

---

## What this plan is NOT

- NOT the plan for the MCP server build (that's `03-mcp-build-and-skill-plan.md`)
- NOT the plan for Tool 4 (that's `02-critical-path-plan.md`)
- NOT the plan for integration, smoke test, or submission (that's `05-integration-qa-and-risk-plan.md`)
- NOT the plan to write the actual blog post or thread — this plan defines OUTLINE and CONSTRAINTS only, execution writes the actual prose
- NOT a plan for Crossmint marketing assets (logos, brand guidelines) — keep the content voice neutral
- NOT a plan for a video production (only a script for a 60-90s screen recording)

---

## Dependencies on other plans

- **Depends on:** `03-mcp-build-and-skill-plan.md` — needs the MCP server functional and the lobster.cash skill drafted so the blog post can reference real outputs
- **Provides to:** `05-integration-qa-and-risk-plan.md` — the blog post, thread, video script, and campaign are inputs to the final submission README

---

## Phase 1 — Narrative + title lock (H+22 → H+22:30, 30 min)

### Task 1.1 — Lock the narrative frame

- **The one-sentence narrative:**
  > *"lobster.cash is Crossmint's payment engine for AI agents, shipped as a CLI that installs into Claude Code, Cursor, and OpenClaw via skills. But Claude Desktop, Continue.dev, Cline, and Codex CLI speak MCP, not skills. And the nuance of how Crossmint smart wallets sign Solana transactions via CPI is not explained anywhere. I built the MCP server that fills the first gap and the lobster.cash skill that fills the second."*
- **Why this narrative works:**
  - Acknowledges lobster.cash as the incumbent (non-competitive framing)
  - Names the specific MCP clients lobster.cash doesn't reach
  - Introduces the CPI nuance as the unique technical content
  - Implicitly positions Saul as a builder who extends existing products
- **Time budget:** 10 minutes
- **Fallback:** N/A — this is the locked narrative

### Task 1.2 — Pick the blog post title

- **Candidate titles:**
  1. *"The two things lobster.cash doesn't ship: an MCP server and a Solana CPI skill"* — sharp but slightly negative tone about lobster.cash
  2. *"Every AI agent in 2026 should have a Crossmint wallet. Here's the MCP server that makes that one config line."* — positive but doesn't mention the skill
  3. *"Give Claude Desktop a Crossmint wallet. Give lobster.cash the Solana CPI skill. Ship both."* — action-oriented
  4. *"Crossmint ships the wallets. lobster.cash ships the CLI. I shipped the MCP bridge — and the skill the CLI is missing."* — most accurate positioning
  5. *"Hello, Crossmint: a wallets MCP server for every AI agent, and the lobster.cash skill that explains Solana's CPI quirk"* — longer but self-contained
- **Locked pick:** **#4** — *"Crossmint ships the wallets. lobster.cash ships the CLI. I shipped the MCP bridge — and the skill the CLI is missing."*
- **Rationale:** Most accurate positioning. Treats both Crossmint and lobster.cash respectfully (they SHIPPED). Claims only the GAPS as Saul's contribution. Non-competitive.
- **Time budget:** 10 minutes
- **Fallback:** If the title feels too long, cut to: *"Crossmint + lobster.cash, meet MCP: a new wallets server and a Solana CPI skill"*

### Task 1.3 — Twitter thread hook

- **Tweet 1 (the hook):**
  > Crossmint ships world-class smart wallets.
  > lobster.cash ships the CLI that lets AI agents pay with them.
  > But Claude Desktop, Cursor, Cline, Codex — half the 2026 agent world — speak MCP, not CLI.
  > So I built the missing MCP server. And the skill lobster.cash is missing too. 🧵
- **Time budget:** 10 minutes
- **Voice pass required:** Saul's voice should be used here — the draft above is neutral; Saul might want to punch it up

---

## Phase 2 — Blog post (H+22:30 → H+25:30, 180 min)

### Task 2.1 — Hook section (100 words, 15 min)

- **Goal:** In 100 words, explain the problem this content solves
- **Structure:**
  - 1-2 sentences naming the 2026 agent landscape (Claude Code, Cursor, OpenClaw — all MCP-native now)
  - 1 sentence naming lobster.cash as Crossmint's existing play
  - 1 sentence naming the gap (MCP-native clients can't install lobster.cash)
  - 1 sentence naming the bonus gap (no skill teaches the CPI nuance)
  - 1 sentence framing the artifact (MCP server + skill, MIT, ready to fork)
- **Draft direction:** Conversational, first-person, Saul's voice — NOT a corporate blog post tone
- **Time budget:** 15 minutes

### Task 2.2 — "What lobster.cash actually is" section (200 words, 25 min)

- **Goal:** Establish that lobster.cash is REAL and SHIPPED, not a gap we're filling with attack. Non-competitive framing throughout.
- **Structure:**
  - 1 paragraph: lobster.cash IS Crossmint's payment layer — CLI + skill architecture, 13 certified skills, published to npm as `@crossmint/lobster-cli@3.0.8` one hour before I wrote this (as of research time)
  - 1 paragraph: how it works — install via `npx skills add`, works in Claude Code + Cursor + OpenClaw via the skills pattern
  - 1 paragraph: what it doesn't do — doesn't work in MCP-native clients (Claude Desktop, Continue.dev, Cline, Codex CLI), doesn't have a skill that explains the CPI quirk on Solana
- **Time budget:** 25 minutes

### Task 2.3 — "Gap 1: the MCP server" section (400 words, 40 min)

- **Goal:** Introduce the MCP server artifact, explain the 4 tools, show one install + call screenshot
- **Structure:**
  - 1 paragraph: what MCP is, why Claude Desktop/Cursor/Cline speak it
  - 1 paragraph: the 4 tools — `crossmint_create_wallet`, `crossmint_get_balance`, `crossmint_transfer_token`, `crossmint_pay_x402_endpoint`
  - 1 code block: the Claude Desktop config block (copy-paste)
  - 1 code block: a sample Claude chat showing the tool being called
  - 1 screenshot: Claude Desktop calling `crossmint_create_wallet` successfully
  - 1 paragraph: the killer tool `pay_x402_endpoint` — one function call, any URL, any MCP client
- **Time budget:** 40 minutes

### Task 2.4 — "Gap 2: the Solana CPI inner instruction skill" section (300 words, 30 min)

- **Goal:** Introduce the skill artifact, explain the CPI nuance in plain English, show the install
- **Structure:**
  - 1 paragraph: what the CPI inner instruction nuance is — analogy: "it's like the difference between writing a check yourself and your bank writing it on your behalf — the money moves, but different signatures show up on the receipt"
  - 1 paragraph: why this breaks naive x402 facilitators — they only scan top-level instructions, miss the USDC transfer nested inside the smart wallet's execution
  - 1 paragraph: how lobster.cash handles this correctly — via Faremeter, which scans CPI inner instructions
  - 1 code block: the `SKILL.md` decision tree excerpt showing the wallet precheck + error handling
  - 1 paragraph: why this skill fills a real gap — none of the 13 certified skills in the lobster.cash directory teach this, and it's the single most important technical insight for developers paying x402 from Crossmint wallets
- **Time budget:** 30 minutes

### Task 2.5 — "How to use both" section (200 words, 20 min)

- **Goal:** Show the reader how to install and use both artifacts
- **Structure:**
  - 1 code block: MCP server install (Claude Desktop config)
  - 1 code block: skill install (`npx skills add`)
  - 1 paragraph: which one to use when — MCP-native client → MCP server; skill-architecture client → skill
  - 1 paragraph: they are complementary, not competitors
- **Time budget:** 20 minutes

### Task 2.6 — "What's next" section (100 words, 15 min)

- **Goal:** Signal openness, invite Crossmint to adopt
- **Structure:**
  - 1 sentence: both artifacts are MIT, ready to fork into the Crossmint org
  - 1 sentence: the MCP server is drafted to be published at `@crossmint/wallets-mcp` if the team wants to adopt
  - 1 sentence: the lobster.cash skill is drafted for certification via Fede
  - 1 sentence: if there's a path forward, the next conversation is about shipping it as `@crossmint/*`
  - 1 sentence: signature — Saul, DevRel candidate, Miami, hackathon mentor at x402hackathon.com
- **Time budget:** 15 minutes

### Task 2.7 — Review for word count and voice (30 min)

- **Target total:** 1300-1500 words (HR plan said 1500-1800; tighten if we can)
- **Check list:**
  - [ ] No word "seamlessly" (DA veto)
  - [ ] No word "unlock" (DA veto)
  - [ ] No word "empower" (DA veto)
  - [ ] No word "at scale" (DA veto)
  - [ ] No "Ultravioleta" anywhere
  - [ ] No "Execution Market" or "MeshRelay" anywhere
  - [ ] First person, conversational
  - [ ] Every claim backed by a real file, real URL, or real command
- **Time budget:** 30 minutes

### Phase 2 gate (H+25:30)

- **Criteria:** `content/blog-post.md` exists, 1300-1500 words, passes the 8 voice checks
- **Saul voice pass:** Saul reads the draft at H+25:30, edits in 30 min, returns by H+26
- **Pass:** Proceed to Phase 3 (Twitter thread)

---

## Phase 3 — Twitter thread (H+26 → H+27, 60 min)

### Task 3.1 — Write the thread (8-10 tweets, 45 min)

- **Structure:**
  - **Tweet 1:** Hook (from Task 1.3)
  - **Tweet 2:** What lobster.cash is + what it doesn't reach (1 sentence + link)
  - **Tweet 3:** The MCP server — 4 tools, install in 30s, works in Claude Desktop (screenshot)
  - **Tweet 4:** Sample Claude chat showing a wallet being created (screenshot)
  - **Tweet 5:** The `pay_x402_endpoint` killer tool — one call, any URL (1-line example)
  - **Tweet 6:** The Solana CPI nuance — plain English analogy (1 sentence)
  - **Tweet 7:** The lobster.cash skill that teaches this — install via `npx skills add`
  - **Tweet 8:** Both MIT, repo links
  - **Tweet 9:** Credit where it's due — Crossmint shipped the wallets, lobster.cash shipped the CLI, Faremeter handles the CPI on the facilitator side, I built the MCP bridge and the skill that explains the quirk
  - **Tweet 10:** Call to action — if you're on the Crossmint team or lobster.cash team, DMs open
- **Time budget:** 45 minutes
- **Fallback:** If short on time, cut to 8 tweets (drop Tweets 4 and 9)

### Task 3.2 — Each tweet stands alone

- **Rule:** Every tweet must be understandable without the previous tweets (for people who only see one in their feed)
- **Check:** Re-read each tweet in isolation
- **Time budget:** 10 minutes

### Task 3.3 — Saul voice pass on the thread

- **Action:** Saul reads, rewrites in his Twitch-stream-voice, confirms
- **Time budget:** 5 minutes (lightweight)

### Phase 3 gate (H+27)

- **Criteria:** `content/twitter-thread.md` exists, 8-10 tweets, each standalone, Saul voice pass done
- **Pass:** Proceed to Phase 4 (video script)

---

## Phase 4 — Video demo script (H+27 → H+27:30, 30 min)

### Task 4.1 — Write the 60-90 second screen recording script

- **Structure:**
  ```
  [0:00-0:05] Title card
    TEXT: "Crossmint Wallets MCP Server — install in 30 seconds"

  [0:05-0:15] Show the Claude Desktop config being added
    SCREEN: Open claude_desktop_config.json, paste the config block, save

  [0:15-0:25] Restart Claude Desktop, show the MCP indicator
    SCREEN: Claude Desktop loading, MCP server icon appears in the chat input

  [0:25-0:45] Prompt: "Create a Crossmint smart wallet on Solana Devnet for demo@example.com"
    SCREEN: Claude calls crossmint_create_wallet, shows the tool invocation, prints the wallet address with Solana explorer link

  [0:45-1:00] Prompt: "What's the USDC balance of that wallet?"
    SCREEN: Claude calls crossmint_get_balance, shows the result

  [1:00-1:20] Prompt: "Pay the x402 endpoint at <URL> from that wallet, max 0.01 USDC"
    SCREEN: Claude calls crossmint_pay_x402_endpoint, shows the tool call, prints the transaction signature + response body, opens Solana explorer in a new tab

  [1:20-1:30] End card
    TEXT: "Install: npx crossmint-wallets-mcp"
    TEXT: "Repo: github.com/0xultravioleta/crossmint-wallets-mcp"
    TEXT: "MIT licensed"
  ```
- **File:** `content/video-script.md`
- **Time budget:** 25 minutes

### Task 4.2 — Decide: record or skip

- **Decision rule:** If Phase 7 of Plan #3 (Claude Desktop smoke test) completed with a clean run, record the video. If not, skip and ship with screenshots only.
- **Time budget:** 5 minutes (decision)

### Phase 4 gate (H+27:30)

- **Criteria:** `content/video-script.md` exists with a shot list
- **Pass:** Proceed to Phase 5 (campaign 1-pager)

---

## Phase 5 — Campaign proposal 1-pager (H+27:30 → H+28:30, 60 min)

### Task 5.1 — Write the 1-page proposal (one page, 500 words max)

- **File:** `campaign/proposal.md`
- **Structure:**

#### Page layout (target 500 words)

**Title:** *Crossmint Wallets MCP Server + lobster.cash CPI Skill: the two gaps that deserve a blog post from Crossmint this week*

**Why this campaign** (120 words)
- The 2026 agent landscape is MCP-native — Claude Desktop, Continue.dev, Cline, Codex CLI — and growing fast. lobster.cash ships to Claude Code and Cursor via skills, but doesn't reach MCP-native clients.
- Solana Foundation's public x402 guide at `solana.com/developers/guides/getstarted/intro-to-x402` literally says "Crossmint is currently in progress". This is a public invitation.
- The only existing Crossmint MCP server (`mcp-crossmint-checkout`) is a 1-star repo from May 2025, checkout-only, no Solana, no x402. Stale.
- The Solana CPI inner instruction nuance (why Crossmint smart wallet payments look invisible to naive facilitators) is not explained anywhere — not in Crossmint docs, not in lobster.cash skill directory, not in any public blog.

**What needs to happen** (200 words)

*For the Crossmint team:*
- Fork `github.com/0xultravioleta/crossmint-wallets-mcp` into the Crossmint org, rename to `@crossmint/wallets-mcp`, publish to npm
- Submit the MCP server to the MCP Registry at `modelcontextprotocol.io/registry`
- Add it to Claude Desktop community listings
- Publish the blog post draft at `blog.crossmint.com` using the content drafted in `content/blog-post.md`
- Tweet the thread from `@crossmint_io` using the draft in `content/twitter-thread.md`
- Total Crossmint team effort: ~4 hours of review + 2 hours of distribution

*For the lobster.cash team (Fede):*
- Review `github.com/0xultravioleta/crossmint-cpi-skill` for certification
- If certified, add to `lobster.cash/skills` directory
- Fede already knows this is coming — no cold outreach needed

*For DevRel distribution:*
- Announce in the x402hackathon Discord `#projects` channel
- Cross-post to the Solana Developer Relations channel (they're the ones publicly waiting for Crossmint's x402 guide)
- Cross-post to the Claude Code community + Cursor Discord
- Mention in the next Crossmint weekly developer update

**Expected outcomes** (150 words)

*30-day metrics (measurable):*
- MCP Registry installs
- GitHub stars on both repos
- lobster.cash/skills directory count (currently 13 → target 14+)
- x402hackathon submission mentions referencing either artifact
- Blog post page views on blog.crossmint.com
- Twitter thread reach and reply engagement

*60-day strategic outcomes:*
- Crossmint becomes the first wallet infrastructure with BOTH an official MCP server AND a lobster.cash skill ecosystem
- Solana Foundation's "Crossmint is currently in progress" note on their x402 guide gets updated to "shipped"
- Claude Desktop users can natively pay x402 endpoints with Crossmint wallets — first-party integration with the biggest MCP-native client

**Real channels and real people** (30 words)

- Crossmint team (Filippos, Fede, Alfonso, Rodri): `@crossmint_io`, `@alfongj`, `@cryptofede`
- MCP Registry: `modelcontextprotocol.io/registry`
- lobster.cash directory: `lobster.cash/skills` (Fede's certification)
- x402hackathon Discord: `#projects`
- Solana DevRel: via the "in progress" guide update

- **Word count target:** 500 words exactly (1 page when printed)
- **Time budget:** 45 minutes

### Task 5.2 — Verify the 1-page constraint

- **Action:** Render the markdown and count visible lines. Adjust if it's 1.5 pages instead of 1.
- **Time budget:** 10 minutes
- **Fallback:** Cut the "60-day strategic outcomes" section if over budget

### Task 5.3 — Saul voice pass on campaign

- **Action:** Saul reads, confirms it sounds like his voice, approves
- **Time budget:** 5 minutes

### Phase 5 gate (H+28:30)

- **Criteria:** `campaign/proposal.md` exists, 500 words, fits on one page, names real handles
- **Pass:** Proceed to Phase 6 (final review)

---

## Phase 6 — Saul voice pass consolidated review (H+28:30 → H+29, 30 min)

### Task 6.1 — Saul reads all 4 content outputs in one pass

- **Files:**
  - `content/blog-post.md`
  - `content/twitter-thread.md`
  - `content/video-script.md`
  - `campaign/proposal.md`
- **Focus:** voice consistency across the 4 files, fix any drift
- **Time budget:** 20 minutes

### Task 6.2 — Final word count + sentence variety check

- **Check:** Blog post ≤1500 words, thread ≤10 tweets, video script ≤90 seconds, campaign ≤500 words
- **Check:** No repetitive sentence starts, no AI-sounding boilerplate
- **Time budget:** 10 minutes

### Phase 6 gate (H+29)

- **Criteria:** All 4 content files are final, Saul has voice-passed them, no blockers
- **Pass:** Content track complete. Hand off to Integration Planner (Plan #5) for polish.

---

## Banned words/phrases (DA veto list)

- "seamlessly"
- "unlock"
- "empower"
- "at scale"
- "leverage" (as a verb)
- "ecosystem" (overused — use specific framework names instead)
- "revolutionize"
- "game-changer"
- "paradigm shift"
- "synergy"
- Any sentence starting with "In today's fast-paced..."
- Any sentence starting with "In the rapidly evolving..."

---

## Mandatory inclusions

- The MCP Registry URL: `modelcontextprotocol.io/registry`
- The lobster.cash directory URL: `lobster.cash/skills`
- The Solana Foundation x402 guide URL: `solana.com/developers/guides/getstarted/intro-to-x402`
- The `@crossmint/lobster-cli@3.0.8` version (fresh signal)
- The Claude Desktop config JSON block (copy-paste ready)
- The 4 tool names: `crossmint_create_wallet`, `crossmint_get_balance`, `crossmint_transfer_token`, `crossmint_pay_x402_endpoint`
- The CPI nuance analogy (plain English, no Rust code)

---

## Saul voice pass protocol

### What Saul sees

At H+25:30, Saul gets a message: "Blog post draft is ready at `content/blog-post.md`. Please read and edit. Target: 20 minutes. Return with your edits."

At H+26, Saul gets the Twitter thread for a quick 5-minute pass.

At H+28:30, Saul gets the campaign proposal for a 5-minute pass.

### What Saul does

- Read the blog post out loud in his head — does it sound like his stream voice?
- Fix any word that sounds like an AI wrote it
- Add a personal anecdote or rant if the section needs it
- Delete any sentence that sounds like "thought leadership"

### What Saul does NOT do

- Does NOT rewrite the structure (that's locked)
- Does NOT add new sections
- Does NOT change the narrative framing
- Does NOT add Ultravioleta branding

---

## Cut hierarchy if behind schedule

1. **Drop the video recording** (Phase 4 Task 4.2) — script exists, recording is optional
2. **Cut the Twitter thread from 10 to 6 tweets** — save 10 min
3. **Cut the blog post from 1500 to 1000 words** — same structure, tighter prose
4. **Drop the campaign proposal to 300 words** — half page instead of full page
5. **LAST RESORT: Ship with blog post + campaign only** — no thread, no video script

---

## Open questions for PM / Research

1. **Is there permission to quote Fede's Telegram message?** If yes, use the "x402 está en abandono un poco" quote in the blog hook. If no, use public signals only (Solana Foundation guide, stale checkout MCP).
2. **Can we publish the blog post on `blog.crossmint.com` directly?** Probably not without Crossmint team approval. Default: ship as a markdown file in the submission repo, propose that Crossmint publishes it as-is.
3. **Does Saul have a personal blog or Medium where he can publish the post immediately?** If yes, we can publish it there AS WELL as including the markdown in the submission repo — bonus distribution signal.
4. **Should the thread be posted from `@0xultravioleta` before or after submission?** Recommendation: hold until the submission is delivered, then post as a follow-up on Monday (day after delivery) for maximum reach.

---

## Time budget summary

| Phase | Hours |
|---|---|
| Phase 1: Narrative + title lock | 0.5h |
| Phase 2: Blog post | 3h |
| Phase 3: Twitter thread | 1h |
| Phase 4: Video script | 0.5h |
| Phase 5: Campaign 1-pager | 1h |
| Phase 6: Voice pass consolidated | 0.5h |
| **Total** | **6.5h (H+22:30 → H+29)** |

**Note:** Total is 6.5h but elapsed wall clock is ~6.5h. Saul voice pass is within that window (parallel with wait time).

---

## Content & Campaign Planner sign-off

This plan covers 4 content artifacts across ~6.5 hours of writing. Gate at H+25:30 (blog draft), H+27 (thread), H+27:30 (video script), H+28:30 (campaign), H+29 (final voice pass). All 4 outputs exist as files by H+29. Hand off to Integration Planner.
