# Crossmint DevRel Challenge — Master Plan

> **Status:** ACTIVE
> **Received:** 2026-04-09 06:17 AM EDT (email from Adolfo Fernandez)
> **Deadline:** 2026-04-11 06:17 AM EDT (48 hours from receipt)
> **Candidate:** Saul Jaramillo (0x Ultravioleta)
> **Application:** [APP-011 Crossmint Senior DevRel Engineer](../consultoria-x/docs/aplicaciones/APP-011-crossmint.md)
> **Challenge brief:** `_internal/research/challenge-brief.md`

---

## The Challenge (TL;DR)

Pick ONE concept/product from Crossmint docs that is not explained well enough. Deliver three artifacts in 48 hours:

1. **Demo / code artifact** — runnable, small, clear
2. **Developer-facing content** — blog post / tutorial / video script / thread
3. **Campaign proposal (1 pager)** — why, what, outcomes

Submission: single GitHub repo or Notion/Google doc linking everything.

Evaluation axes: technical judgment, communication, audience instinct, AI fluency, taste.

---

## Strategic Principles (non-negotiable)

1. **One concept, explained clearly.** The challenge punishes breadth. Go narrow.
2. **Leverage existing assets.** Saul already has production code on Crossmint smart wallets (CPI scanning), 19-mainnet x402 facilitator, Execution Market, MeshRelay, and 4 years of streaming. Do NOT start from zero.
3. **Clear opinion beats balanced overview.** The brief literally says: *"the best submissions have a clear opinion."* Be specific, be critical.
4. **Name real channels and people.** The brief says generic distribution plans lose.
5. **Taste is a scoring axis.** If we would not personally share it, we do not ship it.
6. **AI fluency means judgment, not prompt dumping.** Every AI-assisted artifact gets a human voice pass.

---

## Team Architecture

> Full roster in `TEAM.md`. Spawning workflow in `_internal/hr-proposal.md` and `_internal/pm-journal.md`.

**Coordinators (meta, no execution):**
- **HR Recruiter** — runs once at start, proposes team composition
- **Project Manager** — ruthless coordinator, holds accountability, no execution
- **Devil's Advocate** — challenges every decision, shreds weak ideas

**Specialists (execution):** 4–5 agents, determined by HR+PM after concept selection.

---

## Phases & Tasks

### Phase 0 — Setup & Team Selection `[H+0 → H+2]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 0.1 | Create repo skeleton at `z:\crossmint-devrel-challenge\` | Claude (meta) | Directory structure |
| 0.2 | Write MASTERPLAN (this doc) | Claude (meta) | `MASTERPLAN.md` |
| 0.3 | Copy challenge brief to `_internal/research/challenge-brief.md` | Claude (meta) | Brief archived |
| 0.4 | Spawn HR Recruiter agent | Claude (meta) | `_internal/hr-proposal.md` |
| 0.5 | Spawn PM + Devil's Advocate in parallel | Claude (meta) | `_internal/pm-journal.md`, `_internal/devils-advocate/kickoff.md` |
| 0.6 | PM reviews HR proposal (ruthless pass, can veto) | PM | Hiring decision |
| 0.7 | Spawn approved specialist agents | Claude (meta) | Specialists running |

### Phase 1 — Concept Selection `[H+2 → H+8]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 1.1 | Audit Crossmint docs for gaps / under-explained concepts | Docs Auditor | `_internal/research/docs-gaps.md` |
| 1.2 | Audit Crossmint blog for recent announcements to piggyback | Docs Auditor | `_internal/research/blog-signals.md` |
| 1.3 | Map Saul's assets to potential concepts (x402 facilitator, CPI scanning, Execution Market, MeshRelay) | Technical Architect | `_internal/research/asset-mapping.md` |
| 1.4 | Propose 3 concept candidates with pros/cons | Technical Architect | `_internal/phase-notes/concept-candidates.md` |
| 1.5 | Devil's Advocate shreds each candidate | Devil's Advocate | `_internal/devils-advocate/concept-critique.md` |
| 1.6 | PM picks winning concept (data-driven, not consensus) | PM | `_internal/decisions.md` |
| 1.7 | Lock scope: one concept, clearly stated opinion | PM | Decision log entry |

### Phase 2 — Demo Development `[H+8 → H+24]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 2.1 | Write demo spec (what, why, stack, runnability) | Technical Architect | `demo/SPEC.md` |
| 2.2 | Implement minimal runnable demo | Demo Engineer | `demo/` code |
| 2.3 | Write demo README with 5-minute quickstart | Demo Engineer | `demo/README.md` |
| 2.4 | Add cleanup, error handling, minimal polish | Demo Engineer | Polish commits |
| 2.5 | Devil's Advocate tries to break the demo | Devil's Advocate | `_internal/devils-advocate/demo-critique.md` |
| 2.6 | PM signs off on demo | PM | Decision log entry |

### Phase 3 — Content Creation `[H+16 → H+36, overlaps Phase 2]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 3.1 | Pick content format (blog / thread / tutorial / script) based on target audience | DevRel Strategist | Format decision |
| 3.2 | Draft content with clear opinion and runnable examples | Technical Writer | `content/draft.md` |
| 3.3 | Integrate demo code snippets with line-level explanations | Technical Writer | Draft v2 |
| 3.4 | Devil's Advocate reviews for clarity and weak claims | Devil's Advocate | `_internal/devils-advocate/content-critique.md` |
| 3.5 | Saul does final voice pass (personal voice, no generic AI tone) | Saul (user) | `content/final.md` |

### Phase 4 — Campaign Proposal `[H+24 → H+36]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 4.1 | Draft 1-pager: Why / What / Outcomes | DevRel Strategist | `campaign/draft.md` |
| 4.2 | Name real channels (Crossmint blog, x402 hackathon Discord, Twitter, specific devs) | DevRel Strategist | Distribution section |
| 4.3 | Define concrete metrics (not vanity): signups, time-to-first-API-call, GitHub stars | DevRel Strategist | Metrics section |
| 4.4 | Devil's Advocate challenges assumptions | Devil's Advocate | `_internal/devils-advocate/campaign-critique.md` |
| 4.5 | PM approves final 1-pager | PM | `campaign/proposal.md` |

### Phase 5 — Integration & Polish `[H+36 → H+44]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 5.1 | Write top-level `README.md` as submission entry point | Technical Writer | `README.md` |
| 5.2 | Verify demo runs cleanly from scratch | Demo Engineer | Test log |
| 5.3 | Verify all internal links work | PM | Link audit |
| 5.4 | Final Devil's Advocate pass (must approve to ship) | Devil's Advocate | `_internal/devils-advocate/final-review.md` |
| 5.5 | PM final signoff | PM | Go / No-go decision |

### Phase 6 — Submission `[H+44 → H+48]`

| # | Task | Owner | Output |
|---|------|-------|--------|
| 6.1 | Clean repo: remove `_internal/` or leave as "how this was built" | PM | Cleaned repo |
| 6.2 | Push to GitHub (public repo under 0xultravioleta) | Saul | Repo URL |
| 6.3 | Test public access (incognito clone + run) | Saul | Smoke test pass |
| 6.4 | Draft submission email to Adolfo | Claude (meta) | Email draft |
| 6.5 | Saul reviews and sends email | Saul | Submission sent |

---

## Deliverables Checklist

- [ ] Phase 0: Setup complete
- [ ] Phase 1: Concept locked
- [ ] Phase 2: Demo runnable
- [ ] Phase 3: Content polished
- [ ] Phase 4: Campaign 1-pager approved
- [ ] Phase 5: Integration + final review
- [ ] Phase 6: Submitted

---

## Timebox Math

- Total available: 48 hours
- Human sleep (Saul): ~14 hours (two 7-hour nights)
- Effective working time: ~34 hours
- Phase 0: 2h (meta setup)
- Phases 1–5: ~28h (parallelized across agents)
- Phase 6: 4h (submission + buffer)

**Buffer:** Aim to finish Phase 5 at H+40, leaving 8 hours for the unexpected.

---

## Risk Register

| Risk | Mitigation |
|---|---|
| Concept pivot after demo started | Devil's Advocate reviews concept before Phase 2 starts |
| Demo does not run on reviewer's machine | Dockerize or use Replit / CodeSandbox fallback |
| Content reads like generic AI output | Mandatory Saul voice pass in Phase 3.5 |
| Campaign proposal names generic channels | DevRel Strategist must cite specific handles/repos/people |
| Scope creep into multi-concept | PM has veto authority on scope expansion |
| Submission late | Hard cutoff at H+44 for development, H+46 for push, H+48 for email |

---

## Decision Log

See `_internal/decisions.md` for the running log.

---

*Created: 2026-04-09 | Owner: Saul (0x Ultravioleta) + Claude agent team*
