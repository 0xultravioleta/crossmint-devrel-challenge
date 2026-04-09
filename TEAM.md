# Team Roster — Crossmint DevRel Challenge

> **Architecture:** 3 coordinators (no execution) + 4–5 specialists (execution).
> **Full rationale:** See `_internal/hr-proposal.md` (HR) and `_internal/pm-journal.md` (PM).

---

## Coordinators (meta, no code)

### HR Recruiter
- **Role:** Analyze challenge + Saul's strengths, propose team composition with justification per role.
- **Runs:** Once, at kickoff.
- **Output:** `_internal/hr-proposal.md`

### Project Manager (ruthless)
- **Role:** Coordinate team, hold accountability, approve/reject deliverables, own scope. Does NOT execute technical work.
- **Runs:** Throughout all phases.
- **Veto power:** Can kill any phase output, can remove any specialist.
- **Output:** `_internal/pm-journal.md`, `_internal/decisions.md`

### Devil's Advocate
- **Role:** Challenge every decision, shred weak ideas, try to break the demo, flag generic content, question assumptions in the campaign proposal.
- **Runs:** Throughout all phases.
- **Output:** `_internal/devils-advocate/<phase>-critique.md`

---

## Specialists (execution, subject to HR proposal + PM approval)

The HR Recruiter will propose the final specialist roster. Expected candidates:

### Crossmint Docs Auditor
- **Role:** Read `docs.crossmint.com` end to end. Identify concepts that are under-explained, out-of-date, or misleading. Catalog blog posts for recent product signals.
- **Output:** `_internal/research/docs-gaps.md`, `_internal/research/blog-signals.md`

### Technical Architect
- **Role:** Map Saul's existing assets (x402 facilitator on 19 mainnets, CPI inner instruction scanning for Crossmint smart wallets, Execution Market, MeshRelay) to the docs gaps. Propose 3 concept candidates. After PM picks winner, write demo spec.
- **Output:** `_internal/research/asset-mapping.md`, `_internal/phase-notes/concept-candidates.md`, `demo/SPEC.md`

### Demo Engineer
- **Role:** Implement the minimal runnable demo per spec. Write a 5-minute-quickstart README. Must actually run from scratch.
- **Output:** `demo/` (code + README)

### Technical Writer
- **Role:** Draft the developer-facing content piece (format picked by DevRel Strategist based on target audience). Integrate demo code snippets with explanations.
- **Output:** `content/draft.md`, `content/final.md`

### DevRel Campaign Strategist
- **Role:** Pick the content format based on where the real target developers live. Draft the 1-pager campaign proposal: why, what, outcomes. Name real channels (Crossmint blog, x402 hackathon Discord, specific Twitter handles, specific developer communities).
- **Output:** `campaign/proposal.md`

---

## Chain of Command

```
                    Saul (human, final voice)
                           |
                    Claude (meta orchestrator)
                           |
         +-----------------+-----------------+
         |                 |                 |
         HR            PM (ruthless)    Devil's Advocate
         (runs once)       |              (parallel)
                           |
         +---------+-------+--------+---------+
         |         |                |         |
       Docs    Technical          Demo      Writer + Strategist
      Auditor  Architect        Engineer    (content + campaign)
```

---

## Operating Rules

1. **PM has veto.** Any specialist output can be rejected.
2. **Devil's Advocate runs in parallel.** They do not wait for phases to complete.
3. **No coordinator writes code or content.** HR, PM, DA are all meta.
4. **Saul is final voice on content.** The Technical Writer drafts, Saul does a voice pass in Phase 3.5.
5. **Output files are source of truth.** If it is not in a file, it did not happen.
