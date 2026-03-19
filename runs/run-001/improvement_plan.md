# Improvement Plan — Run 001 (Round 3 — Final)

**Based on:** FAPESP Review — Round 3 (fapesp_review.md)
**Date:** 2026-03-19
**Overall Score:** 4.2 / 5.0
**Verdict:** SOLID
**Previous Score:** 3.8 / 5.0 (Minor Revision)

---

## Summary of Review Outcome

The run-001 report has reached Solid status. All five blocking issues identified across rounds 1 and 2 have been addressed:

- **Round 1 blocker (citations):** Resolved in round 2 — 25-entry References section and IEEE inline citations added.
- **Round 2 blocker (plan/execution conflation):** Resolved in this submission — explicit status disclaimer added at §2 close.
- **Round 2 blocker (implicit contributions):** Resolved in this submission — three-point contribution statement added to §2 with explicit synthesis framing.

The score gains in this round were driven by Methodological Clarity (+1, from 3 to 4) and Innovation and Contribution Relevance (+1, from 4 to 5). All remaining issues are minor editorial items that do not affect the intellectual integrity or research credibility of the artifact.

**No blocking issues remain for publication.**

---

## Status: No Blocking Issues for Publication

The following three items must be resolved before Zenodo archival. They require no new research, no structural changes, and no new evidence. Total estimated effort: 15–20 minutes.

---

### Fix 1 — Resolve the Orphan Reference [3]

**Status: Required before archival**

**Action:** Reference [3] (Falconer, Sean; Sellers, Andrew. "Four Design Patterns for Event-Driven, Multi-Agent Systems." Confluent, 2025.) appears in the References section but is not cited anywhere in the report body.

Choose one of the following resolutions:

- **Option A (preferred):** Add an inline citation to [3] in §4.1, at the sentence discussing event-driven asynchronous architectures as blackboard-adjacent patterns that improve reliability versus synchronous orchestration. The sentence currently reads: "A separate study demonstrates that agents sharing full reasoning context on a blackboard reduce redundant computation and reach consensus-driven outputs [6]." Consider adding [3] here or at the prior sentence on event-driven architectures, consistent with how web-003 supports Claim C2.
- **Option B:** Remove [3] from the References section entirely if the finding is already adequately covered by [5] and [6].

**Rationale:** An archival artifact must not contain bibliography entries that are never cited in the body. This is the single issue preventing a clean Zenodo deposit.

**Effort:** Low — one inline citation added or one reference entry removed.

**Criterion affected:** Citation and Attribution Quality.

---

### Fix 2 — Add Retrieval Dates to Three References

**Status: Required before archival**

**Action:** References [2], [10], and [13] carry no publication date. Per IEEE web citation practice, append "[accessed 2026-03-19]" to each entry in the References section (§10).

- [2] Jose GuruSup. "Agent Orchestration Patterns..." DEV Community. — add "[accessed 2026-03-19]"
- [10] Gunndu, Klement. "4 Fault Tolerance Patterns..." DEV Community. — add "[accessed 2026-03-19]"
- [13] PricewaterhouseCoopers. "Validating Multi-Agent AI Systems..." PwC. — add "[accessed 2026-03-19]"

**Rationale:** IEEE citation format for web sources requires either a publication date or a retrieval date. Three of 25 references are currently missing both.

**Effort:** Low — three line edits in §10.

**Criterion affected:** Citation and Attribution Quality.

---

### Fix 3 — Map SQ6 and SQ7 to Report Sections

**Status: Recommended before archival**

**Action:** Add one sentence in §1 or §2 explicitly mapping sub-questions SQ6 (Academic-Grade Output Standards) and SQ7 (End-to-End System Design Synthesis) — both defined in question.md — to their coverage locations in the report body.

Example sentence for §1: "Sub-questions SQ6 (academic-grade output standards) and SQ7 (end-to-end synthesis) inform the contribution framing in §2, the cross-cutting analysis in §5, and the recommendations in §9, rather than appearing as standalone evidence sections."

**Rationale:** A reader with access to question.md can verify that all seven sub-questions were addressed; a reader without that access cannot. One sentence closes this gap without requiring any structural change.

**Effort:** Low — one sentence added.

**Criterion affected:** Methodological Clarity.

---

## Optional Polish Items

The following items are not blocking for publication or archival. Address them if time permits, or carry them forward to the next research run as standing quality standards.

**Optional — O1: Tighten the blackboard performance interval framing (§4.1)**
Add "— a range wide enough to signal high task-type sensitivity" after "13–57% improvement." Current framing presents the wide interval without comment. Effort: Low.

**Optional — O2: Add an explicit evidence-gap sentence to §4.2**
In §4.2 (Reliability and Fault Tolerance), add: "No peer-reviewed benchmark for fault tolerance pattern composition in LLM research pipelines was identified in the retrieved evidence set; this represents a genuine knowledge gap in the field rather than a retrievable source quality issue." This is more precise than the current §7 framing. Effort: Low.

**Optional — O3: Split §2 into labeled subsections**
Split §2 into "2.1 Background" and "2.2 Novel Contributions of This Synthesis" to allow a cold reader to encounter system context before contribution claims. Content unchanged; labeling only. Effort: Low.

**Optional — O4: Add section cross-references to the §2 contribution list**
For each of the three contribution points in §2, add a parenthetical reference to the section where it is elaborated (e.g., "hybridization of orchestrator-worker and blackboard architectures — see §4.1, §5, and §6 (X1)"). This aids navigation for portfolio readers. Effort: Low.

**Optional — O5: Add an opening explanatory sentence to §5**
After "Six cross-cutting findings emerge from integration across the five sub-questions," add: "These findings identify architectural interdependencies that sub-question literatures treat as separate concerns; integrated analysis reveals design decisions that must be made jointly." This articulates the section's purpose for a reader unfamiliar with multi-question synthesis. Effort: Low.

---

## What Must Not Change

The following elements are strengths and must be preserved unchanged in any revision:

- **Limitations section (§7) in its current form.** The reflexive acknowledgment that the report's own provenance cannot be cryptographically verified, and the specific reference-by-number disclosure of generalizability constraints, are uncommon and rigorous. Do not soften or abbreviate.
- **Contradictions registry (§6, X1–X6).** Each contradiction names both conflicting evidence positions, proposes a resolution with stated rationale, and cites by reference number. This is the report's highest-value analytical feature.
- **Cross-cutting findings section (§5).** The blackboard/shared-memory convergence framing and the reliability-traceability co-design principle are the core synthesis contributions. Do not condense.
- **Open questions cross-referencing (§8).** Each question is linked to specific claim IDs and gap IDs from claims.json. This precision must be retained and extended in future runs.
- **Footer attribution disclaimer.** The explicit statement that no citations, paper titles, or author names were invented, with a traceable provenance chain, is a transparency commitment that should be carried into all future report outputs.
- **Next steps urgency sequencing (§9).** The organization as immediate blockers / short-term / medium-term with cited evidence IDs is a strong research practice. Retain and apply to future runs.

---

## Round 3 Score Summary

| Criterion | Round 2 | Round 3 | Delta | Status |
|-----------|---------|---------|-------|--------|
| Scientific Rigor | 4/5 | 4/5 | 0 | Stable |
| Methodological Clarity | 3/5 | 4/5 | +1 | Resolved |
| Citation & Attribution | 4/5 | 4/5 | 0 | Minor fixes remain |
| Structural Presentation | 4/5 | 4/5 | 0 | Stable |
| Innovation & Contribution | 4/5 | 5/5 | +1 | Resolved |
| **Overall** | **3.8/5** | **4.2/5** | **+0.4** | **SOLID** |

The projected round 3 score range from the round 2 improvement plan was 4.0–4.2/5 with verdict Solid. The actual round 3 score is 4.2/5 with verdict Solid. The projection was accurate.

No further full review cycle is required. Resolve Fixes 1–3 and deposit.
