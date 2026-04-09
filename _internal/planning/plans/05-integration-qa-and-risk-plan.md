# 05 — Integration, QA & Risk Plan

> **Role:** Integration, QA & Risk Planner
> **Owner:** Claude (direct execution) + Saul (final ship decision)
> **Mandate:** Plan Phase 5 (polish + smoke test) + Phase 6 (submission) + full 15-risk playbook + 10 decision gates
> **Scope:** Phases 5 and 6 (H+29 → H+36) + all risks across the build + all gates across all phases
> **Depends on:** `03-mcp-build-and-skill-plan.md`, `04-content-and-campaign-plan.md`, `02-critical-path-plan.md`
> **Line target:** 600-900

---

## Framing discipline

- **Submission is Crossmint-first.** The top-level `README.md` of the submission entry point references Crossmint, lobster.cash, Faremeter. It does NOT reference Ultravioleta, Execution Market, or MeshRelay.
- **Ship during EU business hours** — target H+36 (2026-04-10 ~18:30 EDT = 2026-04-11 ~00:30 CEST), hard cutoff H+40
- **No hardcoded secrets anywhere** in public commits — pre-push review required
- **Fresh-machine reproduction is a PHASE, not a checkbox** (per PM tightening)
- **Email to Adolfo is Saul's final sign-off** — Claude drafts, Saul sends

---

## What this plan is NOT

- NOT the plan to write the blog post / thread / campaign (that's `04-content-and-campaign-plan.md`)
- NOT the plan to build the MCP server or the skill (that's `03-mcp-build-and-skill-plan.md`)
- NOT the plan to build Tool 4 (that's `02-critical-path-plan.md`)
- NOT a plan to modify concept-lock-v2.md (locked)
- NOT a plan for post-submission follow-ups (those belong in a post-mortem file after ship)

---

## Dependencies on other plans

- **Depends on:** All 4 previous plans (research, critical path, MCP build, content/campaign)
- **Provides to:** Nothing downstream — this is the last plan. Output is the submission itself.

---

## Phase 1 — Top-level README rewrite (Phase 5.1 in BUILD-PLAN) — H+29 → H+30 (60 min)

### Task 1.1 — Rewrite `z:\crossmint-devrel-challenge\README.md` as the submission entry point

- **Goal:** A reviewer lands on this README and understands the whole submission in 60 seconds
- **Structure:**
  ```markdown
  # Crossmint DevRel Challenge Submission

  > Saul Jaramillo (0x Ultravioleta) — April 2026
  > Challenge brief: [take-home from Adolfo Fernández, 2026-04-09]

  ## TL;DR

  Two artifacts filling two gaps in Crossmint's agentic commerce stack:

  1. **Crossmint Wallets MCP Server** — `github.com/0xultravioleta/crossmint-wallets-mcp` — 4-tool MCP server installable in Claude Desktop, Continue.dev, Cline, Codex CLI, and any MCP-native client. The MCP-native companion to lobster.cash.

  2. **Crossmint CPI Skill for lobster.cash** — `github.com/0xultravioleta/crossmint-cpi-skill` — A certified-ready lobster.cash skill teaching AI agents the Solana CPI inner instruction nuance when paying x402 endpoints from Crossmint smart wallets.

  Both are MIT licensed. Both are ready for Crossmint to fork into the official org.

  ## Deliverables

  ### 1. Demo / Code artifact
  → [crossmint-wallets-mcp repo](https://github.com/0xultravioleta/crossmint-wallets-mcp)
  → Install: `npx crossmint-wallets-mcp`
  → Claude Desktop config example in the repo README
  → 4 tools: `crossmint_create_wallet`, `crossmint_get_balance`, `crossmint_transfer_token`, `crossmint_pay_x402_endpoint`

  ### 2. Developer-facing content
  → [Blog post](content/blog-post.md) — 1300-1500 words, ready to publish at blog.crossmint.com
  → [Twitter thread](content/twitter-thread.md) — 8-10 tweets, ready to post from @crossmint_io or @0xultravioleta
  → [Video script](content/video-script.md) — 60-90 second screen recording shot list

  ### 3. Campaign proposal (1 pager)
  → [campaign/proposal.md](campaign/proposal.md) — one page, real channels, concrete metrics

  ## Bonus: how this was built

  This submission was built by a multi-agent AI-native workflow documented in `_internal/`:
  - Planning: HR recruiter + PM coordinator + Devil's Advocate + 5 specialist planners
  - Research: 4 research files verifying the Crossmint SDK, lobster-cli, MCP SDK, and x402 format
  - Brainstorm: 4 parallel agents (Wild Innovator, Feasibility Scorer, Competitive Scanner, Saul Asset Analysis)
  - Build: following the 5 numbered plans at `_internal/planning/plans/`

  The brief says "AI is expected — we're evaluating your judgment." This repo is an artifact of that judgment: when to spawn agents, when to go direct, when to pivot, when to ship.

  ## Start here

  1. Read the [blog post](content/blog-post.md) for the story
  2. Clone [crossmint-wallets-mcp](https://github.com/0xultravioleta/crossmint-wallets-mcp) to run the demo
  3. Read the [campaign proposal](campaign/proposal.md) for the why-ship-this pitch
  ```
- **Time budget:** 40 minutes
- **Fallback:** If Saul wants a different tone, iterate — but keep the 60-second readability promise

### Task 1.2 — Verify every link in the top-level README actually works

- **Action:** Click-test every link (use `markdown-link-check` or manual)
- **Command:** `find . -name "*.md" -exec grep -l "\[" {} \;` then spot-check
- **Time budget:** 15 minutes
- **Fallback:** Remove broken links before publication

### Task 1.3 — Add a brief "what happens if you review this" section

- **Content:**
  ```markdown
  ## For the reviewer

  The 4 tools in the MCP server have been end-to-end tested on Solana Devnet with a real Crossmint API key. Screenshots are in `content/screenshots/`. The video is at `content/video-demo.mp4` (if recorded).

  If any setup step fails for you, open an issue at the MCP server repo or DM @0xultravioleta on Twitter. I'll fix it in <1 hour.
  ```
- **Time budget:** 5 minutes

### Phase 1 gate (H+30)

- **Criteria:** Top-level README renders cleanly, all links work, 60-second readable
- **Pass:** Proceed to Phase 2

---

## Phase 2 — Cross-references between repos (Phase 5.2 in BUILD-PLAN) — H+30 → H+30:30 (30 min)

### Task 2.1 — Add a link to the skill repo from the MCP server README

- **Action:** In `crossmint-wallets-mcp/README.md`, add a section:
  ```markdown
  ## Companion skill

  If you're a lobster.cash user (agents on Claude Code, Cursor, or OpenClaw), install the companion skill: [crossmint-cpi-skill](https://github.com/0xultravioleta/crossmint-cpi-skill). It teaches the Solana CPI inner instruction nuance that affects x402 payments from Crossmint smart wallets.
  ```
- **Time budget:** 10 minutes

### Task 2.2 — Add a link to the MCP server from the skill README

- **Action:** In `crossmint-cpi-skill/README.md`, add a section:
  ```markdown
  ## Companion MCP server

  If you use an MCP-native client (Claude Desktop, Continue.dev, Cline, Codex CLI), install the companion MCP server: [crossmint-wallets-mcp](https://github.com/0xultravioleta/crossmint-wallets-mcp). It exposes Crossmint wallet primitives as MCP tools including a `pay_x402_endpoint` function.
  ```
- **Time budget:** 10 minutes

### Task 2.3 — Both repos link back to the submission entry point

- **Optional:** Add a "See also" pointing at the DevRel challenge submission repo. ONLY include if the submission repo is made public.
- **Default:** Keep submission repo private, do NOT cross-link publicly
- **Time budget:** 5 minutes

### Phase 2 gate (H+30:30)

- **Criteria:** Both repos cross-link correctly, no broken references
- **Pass:** Proceed to Phase 3

---

## Phase 3 — Clean commit history audit (Phase 5.3 in BUILD-PLAN) — H+30:30 → H+31 (30 min)

### Task 3.1 — Review commit history in both repos

- **Commands:**
  ```bash
  cd z:/crossmint-wallets-mcp && git log --oneline
  cd z:/crossmint-cpi-skill && git log --oneline
  ```
- **Time budget:** 10 minutes

### Task 3.2 — Squash WIP commits if any

- **Action:** If there are messy WIP commits, interactive rebase to squash into clean conventional commits
- **Command pattern:**
  ```bash
  git rebase -i HEAD~N  # where N = number of recent commits
  # squash messy ones, keep clean ones
  ```
- **Time budget:** 15 minutes
- **Fallback:** If rebase is risky, leave commits as-is — messy history is better than broken history

### Task 3.3 — Verify no co-author lines

- **Command:**
  ```bash
  cd z:/crossmint-wallets-mcp && git log --format="%B" | grep -i "co-authored" || echo "CLEAN"
  cd z:/crossmint-cpi-skill && git log --format="%B" | grep -i "co-authored" || echo "CLEAN"
  ```
- **Expected:** "CLEAN" output
- **Time budget:** 5 minutes
- **Fallback:** If co-author lines exist, rewrite commits (per CLAUDE.md global rule: no Claude co-author)

### Phase 3 gate (H+31)

- **Criteria:** Both repos have clean conventional commit histories, no co-author lines
- **Pass:** Proceed to Phase 4

---

## Phase 4 — Fresh-machine reproduction test (Phase 5.4 in BUILD-PLAN, FIRST-CLASS PHASE per DA+PM) — H+31 → H+33 (120 min)

**This is the most important phase in the integration plan.** DA kickoff section 3 explicitly said "no explicit reviewer-reproduction gate until Phase 5 — that's the single most likely failure mode." PM upgraded this from a checkbox to a dedicated phase.

### Task 4.1 — Simulate a reviewer's environment

- **Action:** Create a clean environment that mimics what Adolfo or a Crossmint reviewer would have:
  - Fresh Node.js 20+ install (or verify system Node is 20+)
  - No prior `node_modules` in the project directory
  - No `.env` file yet
  - No local Claude Desktop config for this server yet
- **Command:**
  ```bash
  # On the build machine, simulate a clean env:
  rm -rf /tmp/mcp-repro && mkdir -p /tmp/mcp-repro
  cd /tmp/mcp-repro
  git clone https://github.com/0xultravioleta/crossmint-wallets-mcp.git
  cd crossmint-wallets-mcp
  ```
- **Time budget:** 10 minutes

### Task 4.2 — Run the install from scratch

- **Commands:**
  ```bash
  pnpm install    # fresh deps
  cp .env.example .env  # new config
  # edit .env: add API key and recovery secret
  pnpm build
  pnpm demo  # smoke test
  ```
- **Expected:** Clean install, no errors, smoke test runs end-to-end
- **Time budget:** 20 minutes
- **Fallback:** If dependencies fail to install (version conflict, missing peer dep), fix and repeat

### Task 4.3 — Test the MCP server in a fresh Claude Desktop profile

- **Action:** Add the server to Claude Desktop config, restart, test each tool
- **Fallback for Claude Desktop state isolation:**
  - On Windows: rename `%APPDATA%\Claude` to `Claude.bak` temporarily (to start fresh)
  - On macOS: rename `~/Library/Application Support/Claude` to `Claude.bak`
  - Restore after the test
- **Time budget:** 30 minutes
- **Fallback:** If fresh profile is too invasive, use a fresh user account or a second machine

### Task 4.4 — Test each tool via Claude Desktop chat

- **Prompts:**
  1. "Create a Crossmint smart wallet on Solana Devnet for reviewer@example.com"
  2. "What's the USDC balance of that wallet?"
  3. "Send 0.001 USDC from that wallet to <devnet burn address>"
  4. "Pay the x402 endpoint at <URL> from that wallet, max 0.01 USDC"
- **Expected:** All 4 tools return valid responses within 30 seconds each
- **Time budget:** 30 minutes (including wallet funding + debugging)
- **Fallback:** If Tool 3 or Tool 4 fails, note the failure in the repro log, don't block submission

### Task 4.5 — Verify `npx crossmint-wallets-mcp` from a fresh shell

- **Command:**
  ```bash
  # In a brand new terminal, not using the cloned repo:
  cd /tmp
  npx crossmint-wallets-mcp --version  # or equivalent
  ```
- **Expected:** Downloads package from npm, runs the binary
- **Time budget:** 10 minutes
- **Fallback:** If npm publish hasn't happened yet (Phase 5), defer this test until after publish

### Task 4.6 — Document the reproduction result

- **File:** `_internal/phase-notes/repro-test-log.md`
- **Content:**
  ```markdown
  # Fresh-machine reproduction test log

  Date: 2026-04-10 H+33
  Tester: Claude (direct execution) + Saul (visual confirmation)

  ## Results

  - [x] `git clone` succeeded
  - [x] `pnpm install` succeeded without errors
  - [x] `pnpm build` produced dist/mcp/server.js
  - [x] `pnpm demo` smoke test ran end-to-end
  - [x] `crossmint_create_wallet` works in Claude Desktop
  - [x] `crossmint_get_balance` works in Claude Desktop
  - [x] `crossmint_transfer_token` works in Claude Desktop
  - [x] `crossmint_pay_x402_endpoint` works in Claude Desktop
  - [x] `npx crossmint-wallets-mcp` works from a fresh shell (after npm publish)

  ## Issues found + fixed

  (list any bugs found and fixed during repro)

  ## Open issues

  (list any bugs NOT fixed — these become README caveats or v0.2 roadmap)
  ```
- **Time budget:** 10 minutes

### Phase 4 gate (H+33)

- **Criteria:** Fresh reproduction works end-to-end (or has a documented degraded mode)
- **Pass:** Proceed to Phase 5 (final voice pass)
- **Fail action:** Lose buffer time debugging, but do NOT ship broken code. If unrecoverable, fall back to skill-only submission.

---

## Phase 5 — Saul voice + DA final review (Phase 5.5-5.6 in BUILD-PLAN) — H+33 → H+34 (60 min)

### Task 5.1 — Saul final voice pass on content

- **Files:** `content/blog-post.md`, `content/twitter-thread.md`, `campaign/proposal.md`
- **Action:** Saul re-reads everything one more time now that he's seen the real demo screenshots
- **Time budget:** 30 minutes
- **Fallback:** If Saul is too tired, skip this pass (the Phase 6 pass in `04-content-and-campaign-plan.md` was the main one)

### Task 5.2 — Devil's Advocate final review

- **Action:** Claude (acting as DA) reads the entire submission end-to-end with adversarial eyes
- **File:** `_internal/devils-advocate/final-review.md`
- **Check list:**
  - [ ] Does the submission name a "clear opinion" sentence (per brief's `X→Y` template)?
  - [ ] Are there any Ultravioleta / Execution Market / MeshRelay references that slipped through?
  - [ ] Does the demo actually run from a clean clone?
  - [ ] Is the blog post Saul's voice or generic AI tone?
  - [ ] Are the real handles in the campaign proposal actually correct (verify spelling)?
  - [ ] Is the CPI inner instruction nuance explained in plain English (no Rust code)?
  - [ ] Does the submission respect the 48h deadline (H+36 ship, H+40 hard cutoff)?
- **Time budget:** 20 minutes
- **Fallback:** If DA finds a critical issue, fix it before ship

### Task 5.3 — Fix any DA flags

- **Action:** Address each critical flag
- **Time budget:** 10 minutes
- **Fallback:** If a flag requires more time to fix, document it as a known limitation in the README and ship anyway

### Phase 5 gate (H+34)

- **Criteria:** Saul has voice-passed, DA has cleared (or flags are acknowledged)
- **Pass:** Proceed to Phase 6 (public push)
- **Fail action:** Do NOT ship until Saul has read and approved content

---

## Phase 6 — Public GitHub push (Phase 6.1 in BUILD-PLAN) — H+34 → H+34:30 (30 min)

### Task 6.1 — Push `crossmint-wallets-mcp` to public GitHub

- **Commands:**
  ```bash
  cd z:/crossmint-wallets-mcp
  # Create the public repo on GitHub first (via web or gh CLI):
  gh repo create 0xultravioleta/crossmint-wallets-mcp --public --source . --push
  # Or if using existing remote:
  git remote add origin https://github.com/0xultravioleta/crossmint-wallets-mcp.git
  git push -u origin main
  ```
- **Time budget:** 15 minutes
- **Fallback:** If `gh` CLI not available, create repo via GitHub web UI + manual push

### Task 6.2 — Push `crossmint-cpi-skill` to public GitHub

- **Commands:**
  ```bash
  cd z:/crossmint-cpi-skill
  gh repo create 0xultravioleta/crossmint-cpi-skill --public --source . --push
  ```
- **Time budget:** 10 minutes
- **Fallback:** Same as Task 6.1

### Task 6.3 — Verify public access from incognito browser

- **Action:** Open incognito window, navigate to both repo URLs, confirm they render
- **URLs:**
  - `https://github.com/0xultravioleta/crossmint-wallets-mcp`
  - `https://github.com/0xultravioleta/crossmint-cpi-skill`
- **Time budget:** 5 minutes
- **Fallback:** If repos show 404, check repo visibility settings

### Phase 6 gate (H+34:30)

- **Criteria:** Both repos are public, render correctly from incognito
- **Pass:** Proceed to Phase 7

---

## Phase 7 — npm publish (Phase 6.2 in BUILD-PLAN) — H+34:30 → H+35 (30 min)

### Task 7.1 — Check npm authentication

- **Command:** `pnpm whoami`
- **Expected:** returns the username (e.g., `0xultravioleta`)
- **Time budget:** 2 minutes
- **Fallback:** `pnpm login` if not authenticated

### Task 7.2 — Verify package name availability

- **Command:** `pnpm view crossmint-wallets-mcp` (or `@0xultravioleta/crossmint-wallets-mcp` if scoped)
- **Expected:** "package not found" (confirming we can claim it)
- **Time budget:** 2 minutes
- **Fallback:** If name is taken, rename to `@0xultravioleta/crossmint-wallets-mcp` or similar

### Task 7.3 — Publish

- **Command:**
  ```bash
  cd z:/crossmint-wallets-mcp
  pnpm build  # ensure dist/ is fresh
  pnpm publish --access public
  ```
- **Expected:** Successful publish, returns package URL
- **Time budget:** 5 minutes
- **Fallback:** If publish fails due to README issues (npm validates), fix README and retry

### Task 7.4 — Verify `npx crossmint-wallets-mcp` works from a fresh shell on a different machine (if possible)

- **Command:** On a second machine or clean terminal: `npx crossmint-wallets-mcp --help`
- **Expected:** Binary runs, prints help or handshake
- **Time budget:** 10 minutes
- **Fallback:** If only one machine available, use a fresh shell

### Task 7.5 — Rollback plan (in case of broken publish)

- **Action:** Document how to unpublish or publish a `0.1.1` patch if anything is broken
- **Command:** `pnpm unpublish crossmint-wallets-mcp@0.1.0 --force` (within 72 hours of publish)
- **Time budget:** 5 minutes (prep, not execution)
- **Fallback:** Publish a `0.1.1` patch with the fix

### Phase 7 gate (H+35)

- **Criteria:** `npx crossmint-wallets-mcp` works from a fresh environment
- **Pass:** Proceed to Phase 8 (email)

---

## Phase 8 — Email draft + send (Phases 6.3-6.5 in BUILD-PLAN) — H+35 → H+36 (60 min)

### Task 8.1 — Draft the submission email

- **File:** `_internal/submission/email-to-adolfo.md`
- **Subject:** `Re: Crossmint | DevRel Challenge submission`
- **Body template:**
  ```
  Hola Adolfo,

  Aqui va el submission del DevRel Challenge. Lo entrego un poco antes del deadline para que el equipo tenga un dia completo de business hours para revisarlo.

  Resumen en 60 segundos:

  Construi dos artifacts que llenan dos gaps en el stack de agentic commerce de Crossmint:

  1. Crossmint Wallets MCP Server
     https://github.com/0xultravioleta/crossmint-wallets-mcp
     4 tools MCP-native (create_wallet, get_balance, transfer_token, pay_x402_endpoint)
     Instalable en Claude Desktop, Continue.dev, Cline, Codex CLI — los clientes MCP-native que lobster.cash no alcanza hoy via el skills architecture.
     Instalable via: npx crossmint-wallets-mcp

  2. Crossmint CPI Skill para lobster.cash
     https://github.com/0xultravioleta/crossmint-cpi-skill
     Skill compatible con el lobster.cash Skill Compatibility Guide. Enseña a los agentes el matiz del Solana CPI inner instruction cuando pagan x402 endpoints desde Crossmint smart wallets — el unico tema que ninguno de los 13 skills certificados cubre hoy.
     Listo para certificacion via Fede.

  Ambos son MIT licensed y estan listos para que Crossmint los forkee en la org oficial.

  Los 3 deliverables del challenge estan en el submission repo:
  - Demo/code artifact: los dos repos arriba
  - Developer-facing content: blog post + Twitter thread + video script en content/
  - Campaign proposal (1 pager): campaign/proposal.md

  Todo esta documentado en el top-level README del submission repo:
  [link al submission repo o a un doc publico]

  Si quieres correr el demo en Claude Desktop, el MCP server esta publicado en npm y tiene el config block listo para copy-paste en el README del repo.

  Cualquier duda me avisas. Feliz de hacer un walkthrough en vivo en un call si quieren.

  Un abrazo,
  Saul
  ```
- **Time budget:** 25 minutes
- **Fallback:** If time is short, cut the email to 3 paragraphs — the submission README has all the details

### Task 8.2 — Saul reviews the email

- **Action:** Saul reads the draft, edits for voice, confirms
- **Time budget:** 15 minutes
- **Fallback:** Saul makes lightweight edits only; Claude has done the structural work

### Task 8.3 — Saul sends the email

- **Action:** Saul opens his email client, pastes the draft, sends to `adolfo@crossmint.com`
- **Cc:** Consider Cc'ing `filippos@paella.dev` since Filippos was the interview lead
- **Do NOT Cc:** `fede@crossmint.com` without his explicit permission (private relationship, shouldn't be tagged publicly in a formal submission)
- **Time budget:** 5 minutes

### Task 8.4 — Optional: DM Fede on Telegram letting him know submission is in

- **Message:** "hey fede, submission entregado a adolfo hace un momento. incluye el wallets mcp server y una skill para el directorio de lobster.cash (que te puedo mandar para certificacion cuando tengas tiempo). gracias por el feedback del brief y el apoyo con el proceso."
- **Time budget:** 5 minutes
- **Fallback:** Skip if Fede is offline — the email to Adolfo is the primary signal

### Task 8.5 — Confirm delivery

- **Action:** Check that the email is in Adolfo's inbox (via sent folder, or ask for read receipt)
- **Time budget:** 10 minutes
- **Fallback:** If email doesn't send (spam filter, wrong address), use Telegram fallback

### Phase 8 gate (H+36)

- **Criteria:** Email sent, delivery confirmed, Saul has closed the loop
- **Pass:** **SUBMISSION COMPLETE.** Celebrate. Rest.
- **Fail action:** If email bounce, use alternative channels (Telegram, LinkedIn InMail)

---

## Phase 9 — 15-Risk playbook

| # | Risk | Severity | Trigger detection | Mitigation command / action | Mitigation time | Fallback if mitigation fails |
|---|---|---|---|---|---|---|
| R01 | `@crossmint/wallets-sdk` doesn't expose the method we need for Tool 4 | HIGH | Phase 2C Task 2.2 fails with "method not found" | Pivot to Plan B (wrap lobster-cli) per `02-critical-path-plan.md` Phase 3 | 1h | Plan C, then Plan D degraded mode |
| R02 | `@crossmint/lobster-cli` library imports don't work cleanly | MED | Phase 2C Task 3.2 fails or throws import errors | Shell out to `lobstercash crypto x402 fetch` via child_process | 30min | Plan C (REST API direct) |
| R03 | Crossmint REST API signing endpoint is undocumented or gated | MED | Phase 2C Task 4.2 returns 403/404 | Plan D degraded mode (transfer fallback with README caveat) | 20min | Ship with "v0.1 limitations" note |
| R04 | Solana Devnet down OR Circle USDC faucet rate limited during build | MED | Phase 2C Task 1.4 can't fund the wallet | Use alternative faucet (`faucet.solana.com`, `solana airdrop`) or manual mainnet transfer | 15min | Run local Solana test validator, fake USDC mint |
| R05 | Claude Desktop config path differs on reviewer's OS | MED | Phase 5 Task 4.3 can't find config location | Document both Windows + macOS paths in README explicitly | 5min | Provide a `claude-desktop-config-install.sh` script that auto-detects |
| R06 | npm publish blocked (name taken, rate limit, account issue) | LOW | Phase 7 Task 7.3 returns error | Change scope to `@0xultravioleta/crossmint-wallets-mcp`, re-publish | 10min | Ship via `git clone` install only, document in README |
| R07 | Blog post reads like generic AI output | MED | Saul voice pass flags it in Phase 5.1 | Saul rewrites in his own voice — mandatory | 30min | Cut blog post from 1500 to 800 words, tighter prose |
| R08 | Campaign proposal names generic channels | LOW | Phase 5 DA review flags it | DevRel Strategist rewrote with real handles in Phase 4 Task 5.1 | 10min | Cut to half page |
| R09 | API 529 overload blocks subagent spawning | MED | Multiple failed spawn attempts in <5 min | Fall back to direct Claude execution in main conversation | 0min (instant) | All work happens in main conversation |
| R10 | Time budget blown at H+30 | HIGH | Phase 5 not started by H+30 | Scope cut hierarchy: drop tools, drop skill, drop video | 0min | Ship minimum viable (MCP server + blog + 1-pager only) |
| R11 | Reviewer's Claude Desktop can't reach Crossmint API | MED | Phase 4.4 reproduction test fails on reviewer side | Document "create API key in 60 seconds" with screenshots in README | 10min | Provide a pre-generated test API key in encrypted form (ask Saul first) |
| R12 | Faremeter facilitator down during demo | LOW | Phase 2C Task 2.4 fails with facilitator unreachable error | Document local facilitator fallback, include local Express paywall server in demo/ | 20min | Ship with "facilitator required" caveat |
| R13 | Fede responds that Crossmint IS shipping a wallets MCP server | HIGH | Telegram response from Fede before H+12 | Pivot to skill-only submission, drop MCP server | 60min | Ship the CPI skill as the sole artifact + rewrite blog post to focus on the skill |
| R14 | lobster.cash skill format has undocumented constraints | MED | Phase 2G Task 8.2 hits an edge case | Mark skill as "pending certification", ship as draft, ask Fede for cert review post-submission | 0min (flag only) | Ship MCP server only as Artifact 1, mention skill as roadmap |
| R15 | EU business hours ship misses the window | LOW | H+36 gate passes H+37 | H+40 is the hard cutoff — still within 48h spec | 0min | Ship at H+40 with reduced polish |

### Risk monitoring

- **At each phase gate, Claude re-checks the risk register** for relevance to the current phase
- **New risks found during build → add to this table** with trigger, mitigation, fallback
- **High-severity risks (HIGH) trigger immediate PM notification** in `pm-journal.md`

---

## Phase 10 — 10 Decision gates consolidation (G1-G10 from BUILD-PLAN §15)

| Gate | Time | Criterion | Action if failed |
|---|---|---|---|
| G1 | H+7 | Research complete (R1/R2/R3 DONE, R4 deferred), Plan A decision written | Extend 1h, force decision at H+8 |
| G2 | H+12 | Tool 4 working end-to-end on Devnet | Pivot to skill-only concept |
| G3 | H+15 | All 4 MCP tools passing `demo/smoke-test.ts` | Drop 1 tool, ship with 3 |
| G4 | H+19 | MCP server working in Claude Desktop (video-recordable) | Ship without video, text walkthrough only |
| G5 | H+27 | Content drafts complete (blog + thread + video script) | Cut thread or video script, ship blog only |
| G6 | H+29 | Campaign 1-pager complete | Cut to half page |
| G7 | H+33 | Clean-state reproduction test passes | Debug, lose buffer time |
| G8 | H+34 | Saul voice pass done on all content | Ship with Claude voice, flag in cover email |
| G9 | H+34 | DA final review cleared (or flags fixed) | Fix flagged items, lose buffer |
| G10 | H+36 | Email sent to Adolfo | Ship at H+40 absolute max |

### Gate orchestration

- **Claude checks each gate at the specified time** during Phase 2/3/4/5/6 execution
- **If a gate fails, the "Action if failed" is triggered immediately** — no deliberation
- **PM (Claude or Saul) has veto on any gate action** — can override if gut says otherwise

---

## Phase 11 — Post-submission cleanup (H+36+, optional)

### Task 11.1 — Archive internal planning files

- **Action:** Move `_internal/` sub-directories to a separate archive branch so they don't clutter the submission repo's main branch
- **Command:** `git branch archive-internal && git push origin archive-internal`
- **Time budget:** 10 minutes
- **Fallback:** Leave `_internal/` in place — it's documented as "how this was built" in the top-level README

### Task 11.2 — Send follow-up thread from @0xultravioleta Twitter

- **Action:** Post the Twitter thread from `content/twitter-thread.md` with the two repo links
- **Time budget:** 10 minutes
- **Fallback:** Post Monday after the team has seen the email

### Task 11.3 — Write a post-mortem

- **File:** `_internal/post-mortem.md`
- **Content:** What worked, what didn't, what would I do differently for the next 48h sprint
- **Time budget:** 30 minutes
- **Fallback:** Skip if exhausted — do later

---

## Open questions for PM / Research

1. **Is the submission repo public or private at ship time?** Default: both artifact repos public, coordination workspace (`crossmint-devrel-challenge`) stays private. If Crossmint wants to see the whole story (including brainstorm and planning), we can make it public after submission confirmation.
2. **Does Saul have npm publish permissions?** Verify in Phase 7 Task 7.1 — blocker if not.
3. **Does Saul have GitHub write access to create public repos under `0xultravioleta`?** Should, but verify.
4. **Email Cc to Filippos — yes or no?** Recommendation: yes, since Filippos was the interview lead.

---

## Total time budget for Phase 5+6 (Integration Planner's scope)

| Phase | Hours |
|---|---|
| Phase 1: Top-level README | 1h (H+29 → H+30) |
| Phase 2: Cross-references | 0.5h (H+30 → H+30:30) |
| Phase 3: Commit history audit | 0.5h (H+30:30 → H+31) |
| Phase 4: Fresh-machine reproduction | 2h (H+31 → H+33) |
| Phase 5: Saul voice + DA final | 1h (H+33 → H+34) |
| Phase 6: Public GitHub push | 0.5h (H+34 → H+34:30) |
| Phase 7: npm publish | 0.5h (H+34:30 → H+35) |
| Phase 8: Email draft + send | 1h (H+35 → H+36) |
| **Total** | **7h (H+29 → H+36)** |

**Buffer:** 4 hours between H+36 ship target and H+40 hard cutoff. Used only if a gate fails.

---

## Integration, QA & Risk Planner sign-off

This plan covers ~7 hours of polish, QA, and submission activity. 10 gates, 15 risks, full cut hierarchy. Fresh-machine reproduction is a first-class phase (Phase 4). Email to Adolfo is Saul's final action. Ship at H+36 target, H+40 hard cutoff, hard 48h spec deadline at 2026-04-11 06:17 EDT.

Submission complete when email is sent.
