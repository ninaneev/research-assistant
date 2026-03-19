# Improvement Plan — Run 001

**Based on:** FAPESP PIPE Review (fapesp_review.md)
**Date:** 2026-03-18
**Overall score:** 3.4 / 5
**Verdict:** MAJOR REVISION

---

## Summary of Review Outcome

The run-001 report is structurally sound and intellectually honest — its limitations section, contradiction registry, and cross-cutting findings represent genuine research discipline. The artifact pipeline (question.md → web_evidence.jsonl → doc_evidence.jsonl → claims.json → report.md) is coherent and internally consistent. However, two categories of deficiency prevent acceptance under FAPESP PIPE standards:

- **Verifiability failure:** Evidence sources are referenced by internal IDs only. No external reviewer can locate, read, or verify any cited source. This makes the evidentiary chain opaque.
- **Execution/plan conflation:** The report presents the research plan as an execution record without distinguishing what has been done from what was intended. This overstates methodological completeness.

Secondary deficiencies include inconsistent treatment of vendor-sourced quantitative claims, an unstated innovation contribution, and the absence of a formal abstract and references section.

---

## Top 5 Improvements (ranked by impact)

### Improvement 1: Add Full Bibliographic Metadata to All Evidence Sources

- **Problem:** Every claim in the report is attributed to an internal ID (web-001, doc-003, etc.) but no source can be externally verified. Author, URL, publication date, and retrieval date are absent from both the report and claims.json. This is the most severe deficiency: a reviewer cannot audit a single factual claim.
- **Action:** (a) Extend the web_evidence.jsonl schema to require: `url`, `title`, `author`, `publication_date`, `retrieval_date`. (b) Propagate these fields into claims.json under each `supporting_evidence` entry. (c) Add a formal References section to the report listing all web-xxx sources in IEEE format and all doc-xxx sources as "Internal design artifact, [filename], run-001." (d) Update the report-writer agent's rules to require a References section in every output.
- **Target section:** report.md — new References section; claims.json — schema extension; web_evidence.jsonl — schema extension; report-writer agent system prompt.
- **Effort:** High (requires schema change propagating through the full pipeline and retroactive enrichment of the current run's evidence files)

---

### Improvement 2: Separate Executed Methodology from Research Plan

- **Problem:** The report describes the research methodology as if it has been fully executed, but question.md is a protocol plan and some phases may not have been completed as described. There is no record of actual search queries, retrieved source counts, or confidence assignment rationale. This conflation overstates methodological rigor.
- **Action:** (a) Add a Methods section to the report (between Section 2 Background and Section 3 Key Findings) with two subsections: "Planned Protocol" (summary of question.md) and "Executed Steps" (what was actually done, with concrete details — number of sources retrieved per SQ, queries used, phases completed). (b) Add a `execution_log.md` artifact to the run directory that each agent populates on completion, recording actual actions taken. (c) If any phase was simulated or incomplete, state this explicitly in the Methods section.
- **Target section:** report.md — new Section 2.5 Methods; new artifact execution_log.md.
- **Effort:** Medium (requires agent prompt updates to generate execution logs; retrospective documentation for run-001)

---

### Improvement 3: Apply Uniform Epistemic Standards to All Vendor-Sourced Quantitative Claims

- **Problem:** The report flags the 200% token cost increase claim (web-002) as speculative, but applies no such qualifier to: the 13–57% blackboard performance advantage (web-005), the 10–35 second framework latency benchmarks (web-024), or the 75% enterprise auditability survey (web-012). These sources have equal or greater conflicts of interest and equally absent peer review. The inconsistent standard weakens the report's scientific credibility.
- **Action:** (a) Apply the phrase "should be treated as directional pending independent validation" to every quantitative claim derived from a vendor, consulting firm, or non-peer-reviewed blog. (b) Add a Source Quality Appendix that classifies each evidence ID by type: peer-reviewed / independent non-commercial / vendor / internal documentation. (c) Update the synthesizer agent's rules to require this classification for every evidence item tagged `source_type: web`.
- **Target section:** report.md — Sections 4.1, 4.2, 4.3, 4.5; claims.json — add `source_quality` field; synthesizer agent system prompt.
- **Effort:** Low-Medium (primarily an editorial pass on the report plus a schema addition)

---

### Improvement 4: Resolve Citation Format Inconsistency (X5) and Implement IEEE Citations

- **Problem:** The research plan (question.md) recommends IEEE citation format for technical systems research, but the report-writer agent was never instructed to use IEEE format, and no citations in the report follow any standard format. Contradiction X5 is documented in claims.json but has not been acted upon. The report produces evidence IDs as substitutes for citations — a practice that satisfies internal traceability but fails external academic standards.
- **Action:** (a) Update the report-writer agent's system prompt to specify: "All external source references must be formatted as IEEE citations in the References section. Use the format: [ID] Author(s), 'Title,' *Venue*, Year. [Online]. Available: URL. Accessed: date." (b) For internal documentation sources (doc-xxx), use: [ID] Internal design artifact, *[filename]*, run-001, [date]. (c) Retroactively apply this to the current report's References section once bibliographic metadata is available (see Improvement 1). (d) Mark X5 as resolved in claims.json.
- **Target section:** report-writer agent system prompt; report.md References section; claims.json contradictions_registry entry X5.
- **Effort:** Low (prompt update is immediate; retroactive citation formatting depends on Improvement 1 completion)

---

### Improvement 5: Articulate the Innovation Contribution Explicitly

- **Problem:** The report makes a genuine synthetic contribution — identifying cross-cutting interdependencies across five dimensions of multi-agent system design, resolving six architectural contradictions, and enumerating ten knowledge gaps — but never states this as a contribution. A FAPESP reviewer assessing novelty has no basis for judging what the work adds to the field. The innovation is implicit and therefore invisible.
- **Action:** (a) Add a "Contribution Statement" paragraph at the end of Section 2 (Background), structured as: "This work contributes: (1) [specific synthesis not found elsewhere]; (2) [gap identification]; (3) [design recommendation with stated rationale]." (b) Compare the proposed architecture explicitly to at least one existing published multi-agent system design to establish the novelty boundary. (c) Frame the knowledge gaps registry (G1–G10) in the report as a research agenda contribution — state explicitly that making latent gaps explicit is the primary forward contribution of this work.
- **Target section:** report.md — Section 2 addition; brief comparative paragraph in Section 4 or a new Section 3.1.
- **Effort:** Low-Medium (primarily a writing task, but requires identifying at least one comparison system from the evidence base or literature)

---

## Secondary Improvements

These improvements are valuable but do not block FAPESP compliance on their own. Address after completing the top 5.

**S1 — Add a formal abstract (150–200 words).** Standard for academic technical reports. Should summarize: research question, method, key findings, and principal recommendations. Place before Section 1.

**S2 — Add a Source Quality Appendix.** Classify all 25 web evidence sources by type (peer-reviewed / independent / vendor / internal). Report the percentage of recommendations that rely exclusively on vendor evidence. This makes the epistemic structure of the report transparent.

**S3 — Define exit criteria for the FAPESP review feedback loop.** Contradiction C8 and gap G7 identify this as an architectural reliability issue. Specify: a numeric quality threshold (e.g., overall score >= 4.0/5.0 to accept without revision), a maximum revision count (e.g., 2 cycles), and a human escalation protocol when neither condition is met.

**S4 — Add an immutability mechanism to the artifact pipeline.** As identified in contradiction X3, intermediate artifact files (web_evidence.jsonl, doc_evidence.jsonl, claims.json) can be modified post-creation without detection. Implement git-based checksumming (commit each artifact immediately after creation) or file-level hash logging. This closes the audit trail integrity gap.

**S5 — Specify the target LLM provider and model version.** Gap G4 identifies this as a prerequisite for any concrete context management or fault tolerance specification. Add a `target_llm` field to the run metadata block in question.md and propagate it through the pipeline.

**S6 — Add a scope limitation statement to Section 7.** The report currently does not state that its findings address a single system (run-001) and may not generalize to systems with different orchestration topologies, task types, or scale requirements. This is standard practice in technical-scientific reports.

**S7 — Evaluate runtime role enforcement mechanisms.** Gap G10 identifies that role separation is currently enforced only through system prompt instructions, which are not mechanically binding. Specify output schema validation or contract-based enforcement as a development target.

---

## What Should NOT Change

The following elements of the current report represent genuine strengths and should be preserved in revision:

- **The Limitations section (Section 7) in its current form.** It is the most intellectually honest section in the report. Its reflexive acknowledgment that the report's own provenance cannot be cryptographically verified is rare and should not be softened.
- **The Contradictions registry structure (Section 6).** Naming contradictions, identifying conflicting evidence IDs, and proposing resolutions with stated rationale is rigorous practice. The six registered contradictions (X1–X6) are accurately characterized and the proposed resolutions are defensible.
- **The claims.json internal cross-reference system.** The evidence ID tagging, confidence level assignment, and open question enumeration per claim is a structural innovation in AI-generated research artifacts. It should be extended (with bibliographic metadata) rather than replaced.
- **The cross-cutting findings section (Section 5).** The identification of architectural interdependencies spanning multiple sub-questions — particularly the convergence of blackboard architecture and shared memory with access control — represents the highest-value synthetic output of this research run. It should not be condensed or removed.
- **The open questions section (Section 8).** The cross-referencing of open questions to specific claim IDs (C-xxx) and gap IDs (G-xxx) is precise and actionable. This practice should be retained and extended to future reports.
- **The next steps section (Section 9) with urgency sequencing.** Organizing recommendations by urgency (immediate pre-production blockers / short-term / medium-term) and citing specific claim and gap IDs for each recommendation is a strong research practice that aids execution planning.
