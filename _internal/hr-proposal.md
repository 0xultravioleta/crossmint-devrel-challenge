# HR Proposal — Team Composition for Crossmint DevRel Challenge

> **Author:** HR Recruiter (written directly by Claude in main conversation due to API 529 overload on subagent spawns)
> **Date:** 2026-04-09 H+2
> **For review by:** Project Manager (ruthless)

---

## Executive Summary

This submission lives or dies on ONE thing: a clear, runnable, 5-minute-quickstart experience that fills the biggest gap in Crossmint's docs. Based on the research findings, that gap is the missing x402 tutorial for Crossmint smart wallets on Solana. I am proposing a lean 5-specialist team (plus the 3 coordinators already in place). Larger teams would produce coordination overhead that we cannot afford inside a 48-hour window.

---

## Proposed Roster (5 specialists)

### 1. Concept Architect
- **Mandate:** Lock the concept (one sentence, one opinion) and write the demo spec.
- **Justification (brief clause):** *"The best submissions have a clear opinion."* Someone must own the opinion statement and defend it under Devil's Advocate fire.
- **Outputs:**
  - `_internal/phase-notes/concept-lock.md` (the final concept + clear opinion)
  - `demo/SPEC.md` (what the demo does, what it does not do, how a developer runs it in 5 minutes)
- **Defensibility vs cuts:** This role cannot be merged with Demo Engineer because the Concept Architect must resist scope creep WHILE Demo Engineer builds. Two people, two responsibilities. The Architect also feeds the Technical Writer with the opinion statement.

### 2. Demo Engineer
- **Mandate:** Build a standalone, runnable repo that sends an x402 payment from a Crossmint smart wallet on Solana in under 5 minutes of developer time.
- **Justification (brief clause):** *"Build something a developer can look at, run, or learn from... It should be small enough to complete in a day."*
- **Outputs:**
  - `demo/` source code (TypeScript or minimal Node.js)
  - `demo/README.md` with copy-paste setup commands
  - `demo/.env.example`
  - A recorded or scripted "this runs in 5 minutes" smoke test
- **Defensibility vs cuts:** This role is the highest-leverage technical deliverable. Cannot be cut.

### 3. Technical Writer
- **Mandate:** Draft the developer-facing content piece (format: technical blog post + possibly a Twitter thread) explaining the concept, the CPI inner instruction nuance, and walking the reader through the demo.
- **Justification (brief clause):** *"One piece of developer-facing content... Pick the format you think fits best."*
- **Outputs:**
  - `content/blog-post.md` — primary artifact
  - `content/twitter-thread.md` — secondary distribution artifact
  - Saul does final voice pass after the draft
- **Defensibility vs cuts:** The brief requires a content piece. Technical Writer cannot be merged with Demo Engineer because the Engineer is a builder, not an explainer. Separate cognitive modes.

### 4. DevRel Campaign Strategist
- **Mandate:** Write the 1-pager campaign proposal with real channels, real people, and real metrics.
- **Justification (brief clause):** *"A campaign proposal (1 pager)... Make the case for why... Your coworkers are busy."* Plus: *"A communication plan that names real channels, communities, and people beats a generic one every time."*
- **Outputs:**
  - `campaign/proposal.md` (exactly 1 page)
- **Defensibility vs cuts:** Cannot be merged with Technical Writer because the Strategist thinks in distribution, metrics, and stakeholders — the Writer thinks in narrative and clarity. Different muscles.

### 5. Integration Lead (doubles as final QA)
- **Mandate:** Assemble the submission: rewrite the top-level README.md, verify the demo runs from scratch, verify all internal links work, cut anything that is half-finished.
- **Justification (brief clause):** *"A single GitHub repo... linking everything together."* Plus: Phase 5 in the masterplan requires explicit integration work.
- **Outputs:**
  - Top-level `README.md` (submission entry point)
  - QA log at `_internal/phase-notes/qa-log.md`
  - Cleanup of `_internal/` directory (move or archive anything that should not ship)
- **Defensibility vs cuts:** Someone must own the final polish. If we do not staff this role, the other specialists will ship incomplete work because they are all heads-down on their primary deliverables.

---

## Alternative Rosters Considered and Rejected

### Alternative A: 8 specialists (1 per major role)

**Composition:** Concept Architect, Demo Engineer, Technical Writer, DevRel Strategist, Integration Lead, + Crossmint Docs Auditor, Blog Style Reviewer, Distribution Specialist.

**Rejected because:** The Docs Auditor role is redundant — the research is already complete (`_internal/research/FINDINGS.md` has the gap analysis). Blog Style Reviewer is coordinator work that Saul should do himself (voice pass). Distribution Specialist overlaps entirely with DevRel Strategist. In a 48-hour window, every additional role adds coordination overhead that eats execution time.

### Alternative B: 3 specialists (lean maximum)

**Composition:** Full-stack Builder (concept + demo + content), DevRel Strategist, Integration Lead.

**Rejected because:** Full-stack Builder is a cognitive overload — concept decision, code implementation, AND content writing are three different modes and cramming them into one agent's context will produce mediocre output on at least two of the three. Saul's time is too limited to review three-in-one output.

---

## Dependencies and Sequencing

```
Phase 1 (H+2 → H+8): Concept lock
  Concept Architect → writes concept-lock.md
  Devil's Advocate → shreds concept-lock.md
  PM → picks winner, updates decisions.md
  Concept Architect → writes demo/SPEC.md

Phase 2 (H+8 → H+24): Demo + parallel content start
  Demo Engineer → builds demo/ (runs in parallel with Phase 3.1)
  Technical Writer → drafts content from SPEC (starts H+10, runs in parallel with Phase 2.2)

Phase 3 (H+16 → H+36): Content + parallel campaign
  Technical Writer → final draft
  DevRel Strategist → drafts campaign/proposal.md (starts H+24, runs in parallel)
  Devil's Advocate → critiques both

Phase 5 (H+36 → H+44): Integration
  Integration Lead → assembles everything
  Devil's Advocate → final review
  PM → signoff
```

Parallel opportunities: Demo Engineer + Technical Writer (H+10 onward), Technical Writer + DevRel Strategist (H+24 onward).

---

## Risk of Team Failure and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Concept Architect picks a concept nobody on the team can actually execute in the timebox | Medium | Critical | Concept Architect must include a "can this run in 48h?" section with explicit dependency list. PM veto on any concept without this. |
| Demo does not run on reviewer's Crossmint API key (works only with Saul's test key) | Medium | Critical | Integration Lead must smoke-test from a clean environment with a reviewer-facing API key creation step in README. |
| Content reads like generic AI output | Medium | High | Mandatory Saul voice pass in Phase 3.5. Devil's Advocate flags generic sentences. |
| Campaign proposal lists generic channels (Twitter, Discord) instead of specific handles | Low | Medium | DevRel Strategist must cite specific handles (e.g., @alfongj, Fede's Twitter, x402hackathon.com Discord channel name, Faremeter team). |
| Integration Lead cuts something critical in polish phase | Low | High | PM has final veto. Integration Lead submits cut list to PM before executing. |
| API overload prevents specialist spawning (already happened with coordinators) | High | Variable | Claude does the work directly in main conversation as fallback, like with HR/PM/DA themselves. |

---

## Final Hire List for PM Approval

Spawn order (sequential dependencies noted):

1. **Concept Architect** — spawn first (blocks everything else until concept locked)
2. **Demo Engineer** — spawn after concept locked (H+8)
3. **Technical Writer** — spawn after demo/SPEC.md exists (H+10, parallel with Demo Engineer)
4. **DevRel Campaign Strategist** — spawn once concept is locked and demo started (H+12, parallel)
5. **Integration Lead** — spawn at H+36 for Phase 5

**Note to PM:** Given API 529 overload is hitting spawns, recommend Claude does Concept Architect and Integration Lead work directly in the main conversation (they are high-judgment, low-volume tasks). Demo Engineer, Technical Writer, and DevRel Strategist can be attempted as subagents first, with direct Claude fallback if they fail.

**Note to Claude (orchestrator):** If subagent spawning keeps failing, run all 5 roles as sequential work in main conversation. The meta coordination overhead of 5 parallel agents is marginal at this scale; direct sequential execution may be faster than chasing overloaded API capacity.
