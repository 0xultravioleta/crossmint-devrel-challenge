# PM Journal — Crossmint DevRel Challenge

> **Author:** Project Manager — ruthless (written directly by Claude after subagent 529 errors)
> **Started:** 2026-04-09 H+2

---

## Phase 0: Kickoff

- **Status at start:** Repo skeleton exists. MASTERPLAN.md and TEAM.md written. Research complete (FINDINGS.md has the gap analysis and candidate concepts ranked).
- **Blockers:** API 529 overload on all subagent spawns. HR, PM, and Devil's Advocate all failed twice. Two additional specialist spawns (docs-scraper, github-explorer) also failed.
- **Fallback in effect:** Claude is running HR, PM, and Devil's Advocate roles as direct meta-analysis in the main conversation. This is WORKING because these roles are judgment-heavy, not tool-heavy.
- **Clock:** Received H+0 at 2026-04-09 06:17 EDT. Current pace is on track. Phase 0 target is H+2.

---

## HR Proposal Review

### Summary of HR Proposal

HR proposed 5 specialists: Concept Architect, Demo Engineer, Technical Writer, DevRel Campaign Strategist, Integration Lead. HR considered an 8-person roster (rejected for coordination overhead) and a 3-person roster (rejected for cognitive overload in the full-stack role).

### What I Accept

- **Concept Architect (1).** Accepted. The opinion statement is the load-bearing beam of this submission. Someone must own it and defend it.
- **Demo Engineer (2).** Accepted. No question. This is the highest-leverage technical deliverable.
- **Technical Writer (3).** Accepted. The brief explicitly requires a content piece, and the cognitive mode is different from Demo Engineer. Do not merge.
- **DevRel Campaign Strategist (4).** Accepted. The brief requires a 1-pager with real channels and metrics. This is a specialized skill that a Technical Writer would do poorly because Writers optimize for narrative, Strategists optimize for stakeholder buy-in.
- **Integration Lead (5).** Accepted. Someone must own the final polish. Without this role, every specialist ships the last 10% incomplete.

### What I Cut or Change

- **Nothing cut.** The 5-specialist roster survives ruthless review. HR correctly rejected the 8-person and 3-person alternatives in their own proposal, so there is nothing further to trim.
- **Change 1: Integration Lead role clarified.** HR said "Integration Lead doubles as final QA." I am upgrading this: **Integration Lead also owns the submission email to Adolfo.** That is the last mile and it must not be forgotten.
- **Change 2: Concept Architect has a hard deadline.** HR said "Phase 1 (H+2 → H+8)". I am tightening this: **concept lock at H+6, not H+8.** Two-hour buffer is needed because the demo cannot start until the concept is locked, and the demo is the largest single block of work.
- **Change 3: Saul's role clarified.** HR correctly notes that Saul does the final voice pass on content. I am adding: **Saul also owns the final ship decision in Phase 6.** Claude orchestrates, specialists execute, PM approves, but Saul is the human pressing "send".

### Defending the Cuts Against HR's Alternatives

- I rejected 8-person roster myself even though HR had already rejected it. Rationale repeated: 8 agents = 8 context loads = too much coordination in 48h.
- I rejected the 3-person roster for the same reason HR gave: cognitive mode conflict.

### Roles That Did NOT Make the Cut (Explicitly)

- **Crossmint Docs Auditor** — rejected because FINDINGS.md already has the gap analysis. Adding this role would mean re-doing research that is done.
- **Blog Style Reviewer** — rejected. This is Saul's job on the voice pass. No need for a dedicated agent.
- **Distribution Specialist** — rejected, fully overlaps with DevRel Campaign Strategist.
- **Taste Reviewer** — rejected. This is Devil's Advocate's job in Phase 5.4 final review.

---

## Final Team Roster (APPROVED)

Coordinators (already in place):
- HR Recruiter (done)
- Project Manager (me, ruthless)
- Devil's Advocate

Specialists (5, approved for spawn):

| # | Role | Spawn at | Blocks | Parallel with |
|---|------|---------|--------|--------------|
| 1 | **Concept Architect** | H+2 (immediately) | Blocks #2, #3, #4 | — |
| 2 | **Demo Engineer** | H+6 (after concept lock) | Blocks #5 | #3 after H+10 |
| 3 | **Technical Writer** | H+10 (needs demo SPEC) | — | #2, #4 |
| 4 | **DevRel Campaign Strategist** | H+12 | — | #2, #3 |
| 5 | **Integration Lead** | H+36 | — | — |

---

## Non-Negotiables for the Team

### Scope Rules

1. **ONE concept.** No hedging, no "we also cover X." If a specialist tries to expand scope, I kill it.
2. **The opinion must be clear.** The content piece must have a sentence of the form *"This is confusing because X, so I approached it as Y."*
3. **The demo must run in 5 minutes.** Setup + execution. If it takes longer, it fails the "clarity and intentionality" test.
4. **Real channels, real names.** The campaign proposal must name specific Twitter handles, Discord channel names, and GitHub repo names.

### Quality Gates

1. **Gate 1 (H+6): Concept lock.** No demo code until PM approves the concept in `decisions.md`.
2. **Gate 2 (H+24): Demo runs from clean state.** Integration Lead runs the demo on a fresh clone. If it fails, back to Demo Engineer.
3. **Gate 3 (H+36): Content voice pass by Saul.** PM does NOT approve content until Saul has read and edited.
4. **Gate 4 (H+44): Final Devil's Advocate review.** If Devil's Advocate can identify a developer who would be frustrated by this submission, we fix it.
5. **Gate 5 (H+46): PM final signoff.** Go / no-go.

### Deadline Discipline

- Hard cutoff for development: H+44 (2026-04-10 ~02:17 EDT)
- Hard cutoff for push: H+46 (2026-04-10 ~04:17 EDT)
- Hard cutoff for email send: H+48 (2026-04-11 06:17 EDT)
- If any phase runs over, we cut scope. We do NOT cut the email.

---

## Next Actions (Next 2 Hours)

1. **Devil's Advocate kickoff critique** — DONE. The first subagent spawn actually succeeded at writing kickoff.md before failing on a subsequent call. Output is excellent, kept as-is.
2. **Adopt DA recommendations that survive my review** — see "Response to Devil's Advocate" below.
3. **Concept Architect: running directly in main conversation** — will lock concept by H+4 per DA's aggressive timeline.
4. **Update decisions log** — team cut to 4 specialists, Phase 0 collapsed, README-first.

---

## Response to Devil's Advocate (addendum H+2.5)

The DA kickoff lands hard. Most points are accepted. Some I am pushing back on.

### Accepted (I am revising the plan)

1. **Cut the team to 4 specialists, not 5.** The DA is right that Demo Engineer + Technical Writer is one cognitive task (documentation-driven development). Merging them.
2. **Collapse Phase 0 to 30 minutes (in effect).** This already happened because HR/PM/DA are running directly in main conversation. No ceremony overhead.
3. **Concept lock at H+4, not H+6.** I was already tightening from H+8 to H+6, DA pushes to H+4. Accepted — the concept is the single load-bearing decision.
4. **Reproducibility gate at H+30, not H+44.** Accepted. Integration Lead runs the smoke test at H+30, not at the end. Any failure has 14 hours of buffer to fix.
5. **Write the README BEFORE the demo code.** Accepted. This is documentation-driven development and it is the right move for a 48h deliverable where the submission is judged as "the top of its README."
6. **The asset-reuse question IS the concept-selection question.** Accepted. I am re-evaluating the provisional concept with this filter.
7. **Filippos compliment inversely correlates with asset value for this submission.** Accepted as a mental model. Executive Market and MeshRelay are OFF the table (they were already off scope). The x402 facilitator is the harder call — see concept re-evaluation below.
8. **The 5 red flags are good. Committing to watching them.**

### Rejected (pushing back)

1. **DA says "combine PM and Devil's Advocate — overlap is 70%".** Rejected. They serve different functions. PM decides and approves; DA attacks. One of them has commitment bias (the PM, because they own the plan), the other is free to attack. Merging removes the tension that catches bad decisions.
2. **DA says "DevRel Strategist and Technical Writer are split on the wrong seam".** Partially rejected. Writer-Strategist merge is too much cognitive surface for one role. But I am now merging Demo Engineer + Technical Writer instead (since writing the README is part of building the demo in the new DDD model). So Writer is gone as a separate role, but not because the DA's specific merge was right.
3. **DA's contrarian suggestion: "the content piece is the primary artifact, the demo exists to validate it".** Partially accepted. I agree the README comes first, but the demo still has to actually run. The content is the frame, but the demo is the proof. Cannot collapse one into the other entirely.

### Concept re-evaluation (the hardest question)

DA raised the critical question: if the x402 facilitator is something Filippos already knows about via Fede, does showing it in the submission read as "I already know this" to the reviewer?

**My answer: it depends on the framing.**

- **Framing A (BAD):** "Here is my x402 facilitator and the CPI scanning I did." — reads as self-promotion. Filippos already saw it. REJECTED.
- **Framing B (GOOD):** "Crossmint's docs have zero x402 quickstart. Here is the quickstart the docs should have, ready to publish, with code examples using any facilitator (mine, Faremeter, or Corbits)." — reads as filling a Crossmint docs gap, not showing off an asset.

The difference: Framing A makes the submission about Saul. Framing B makes the submission about Crossmint's developers. The brief says the job is to fix a gap in Crossmint's docs. Framing B does that directly.

**But — the DA is still right that a fresher artifact would score higher.** Two options:

- **Option 1:** Stick with the x402 quickstart, but use Framing B aggressively. Write the quickstart so it is agnostic to the facilitator (the reader can use any of 3). The facilitator becomes infrastructure, not subject.
- **Option 2:** Pivot to a different concept entirely. DA suggested "delegated signers" or "agent wallets primitives" — both are real gaps in the docs.

My decision: **Option 1, with one concession to the DA.** The quickstart will NOT center Saul's facilitator. It will show how to pay from a Crossmint smart wallet via x402 using Faremeter (Crossmint's own preferred facilitator per Filippos's own words in the interview). Saul's facilitator gets a one-line footnote as an alternative. This keeps the concept fresh (because the framing and the code are net-new) but does not require building a brand new domain demo from scratch.

I am committing this decision in decisions.md.

### New non-negotiables after DA review

1. **Write the README first.** Before any code. The README is the contract.
2. **The demo MUST be facilitator-agnostic.** It works with Faremeter (primary) and Ultravioleta (footnote). Saul's facilitator is not the subject.
3. **No "Ultravioleta" or "0xultravioleta" branding in the demo or README.** The submission is for Crossmint. The repo can be under Saul's personal GitHub but the content is Crossmint-first.
4. **Reproducibility gate at H+30.** Non-negotiable. Integration Lead spins up a clean node_modules and runs the demo from scratch.
5. **Opinion sentence rubric.** The content's opinion sentence must be under 280 characters (Twitter-length). If it is longer, it is unfocused.
6. **Ship at H+36, not H+48.** (From DA's timezone analysis — Adolfo is in Spain, team reviews probably happen during EU business hours on April 11. Ship 12h early as a trust signal.)

---

## What I Am Watching For

- **API 529 patterns.** If spawns keep failing, I shift all specialist work to direct Claude execution. This is not a failure mode, it is an adaptation.
- **Scope creep into multi-concept.** Already seeing HR correctly identify risk. Watching for it in Concept Architect.
- **Generic AI output.** Technical Writer must produce something that sounds like Saul, not like ChatGPT.
- **Demo runtime.** If the demo has hidden dependencies that take >5 minutes to install, that fails the "5-minute quickstart" promise.
