# Run-002 Research Plan
## Paper Rewrite: "From Signals to Decisions: A Framework for Executive Signal Synthesis in AI Systems"

**Date:** 2026-03-19
**Run type:** Paper rewrite (full, from scratch)
**Canonical framing:** ESS as a methodological research framework — not a product, not an automation tool.

---

## Canonical Research Question

**How can AI systems aggregate heterogeneous information signals into structured, traceable, executive-level decisions — and what methodological framework is required to make that process rigorous, auditable, and organizationally useful?**

---

## Sub-Questions

### SQ1 — Problem Validity
**Why are current AI-based decision support systems insufficient for executive-level decision-making?**

- Paper sections addressed: §1 (Problem), §2 (Why Current Systems Fail)
- Evidence type: Web research + light doc grounding
- Web-researcher must gather:
  - Literature on limitations of existing Decision Support Systems (DSS) for executive contexts
  - Studies on AI opacity, non-traceability, and lack of auditability in high-stakes decisions
  - Evidence that current systems handle individual signals (single-modality) rather than synthesis across signal types
- Document-analyst must look for:
  - MULTI_AGENT_PLAN.md: the stated motivation for building this system — what problem it was designed to solve
  - runs/run-001/question.md: the original research question framing
- Risk: The problem statement must not read as a product pitch. Keep it anchored to literature-based gaps, not system capabilities.

---

### SQ2 — Signal Taxonomy
**What constitutes a "signal" in the context of executive decision-making, and how should signals be classified for synthesis purposes?**

- Paper sections addressed: §3 (Definition of Signals)
- Evidence type: Web research + doc analysis
- Web-researcher must gather:
  - Weak signals theory (Ansoff, Hiltunen) in organizational and strategic management literature
  - Environmental scanning frameworks — what categories of organizational intelligence exist
  - Information types in Executive Information Systems (EIS): operational, financial, reputational, relational, trend-based
- Document-analyst must look for:
  - runs/run-001/web_evidence.jsonl and doc_evidence.jsonl: what signal types were actually ingested and labeled in a live run
  - .claude/agents/web-researcher.md and document-analyst.md: how each source type is operationally defined
- Risk: "Signal" is used loosely in AI literature. The paper must define it precisely and consistently, distinguishing raw data from processed evidence from synthesized claims.

---

### SQ3 — Synthesis Requirements
**What does "synthesis" require methodologically — and how does evidence-based synthesis differ from simple aggregation or summarization?**

- Paper sections addressed: §4 (Definition of Synthesis)
- Evidence type: Web research primary
- Web-researcher must gather:
  - Systematic review methodology — how evidence is graded, weighted, and integrated
  - Evidence synthesis literature (Campbell Collaboration, Cochrane methods adapted to non-clinical domains)
  - XAI traceability requirements — what conditions make a synthesized output auditable
  - Distinction between aggregation, summarization, and synthesis in information science
- Document-analyst must look for:
  - runs/run-001/claims.json: structure of synthesized claims — confidence scores, source attribution, claim type
  - .claude/agents/synthesizer.md: the operational definition of synthesis used in this system
- Risk: Do not conflate synthesis with LLM summarization. The distinction between traced, attributed synthesis vs. generative paraphrase is critical for academic credibility.

---

### SQ4 — ESS Framework Definition
**What are the core components, principles, and logical structure of the Executive Signal Synthesis (ESS) framework?**

- Paper sections addressed: §5 (The ESS Framework)
- Evidence type: Doc analysis primary, web research for conceptual grounding
- Document-analyst must look for:
  - MULTI_AGENT_PLAN.md: full system design — roles, routing logic, artifact schema
  - All six agent definition files in .claude/agents/: these define the functional layers of ESS (ingestion, analysis, synthesis, output, review)
  - runs/run-001/claims.json: the artifact schema that represents synthesized output
- Web-researcher must gather:
  - Existing multi-layer decision frameworks in organizational theory (Mintzberg, Simon bounded rationality)
  - Information processing theory as applied to executive cognition
  - Pipeline models in systematic review: search → screen → extract → synthesize → report
- Risk: The ESS framework must be presented as a generalizable methodology, not as a description of one specific implementation. Abstract the agent roles into functional layers (signal ingestion, evidence analysis, synthesis, structured output, quality review).

---

### SQ5 — Prototype Implementation Mapping
**How does a working prototype instantiate the ESS framework — and what does the mapping between framework and implementation reveal about design requirements?**

- Paper sections addressed: §6 (Implementation Mapping), §7 (Example Flow)
- Evidence type: Doc analysis primary
- Document-analyst must look for:
  - runs/run-001/ full artifact set: question.md → web_evidence.jsonl → doc_evidence.jsonl → claims.json → report.md → fapesp_review.md → improvement_plan.md (this is the complete ESS flow)
  - .claude/agents/: agent role definitions as implementation of framework layers
  - MULTI_AGENT_PLAN.md: routing logic, artifact handoff schema
- Web-researcher must gather:
  - Prototype-theory validation literature — how prototypes support framework claims
  - Traceability standards in software and systems engineering (ISO/IEC 15288 or similar)
- Risk: The implementation section must not name proprietary tooling, specific LLM vendors, or internal system names. Describe the prototype in functional and architectural terms only. The concrete example flow (§7) should use run-001 as the walkthrough case — framed as "a research synthesis task" not as a product demo.

---

### SQ6 — Organizational Implications
**What are the implications of ESS adoption for organizational decision-making, research practice, and systems design?**

- Paper sections addressed: §8 (Implications)
- Evidence type: Web research primary
- Web-researcher must gather:
  - Organizational decision-making research: how executive teams currently handle signal overload
  - Literature on trust in AI-assisted decisions — conditions for adoption in high-stakes environments
  - Research on AI governance and auditability requirements in enterprise/regulatory contexts
  - Implications of XAI for institutional accountability
- Document-analyst must look for:
  - runs/run-001/fapesp_review.md: the quality review cycle — what a structured peer-review of AI-synthesized output looks like in practice
  - runs/run-001/improvement_plan.md: how iteration and auditability work in the ESS loop
- Risk: Implications must be grounded in literature, not asserted from prototype performance. Avoid speculative claims about adoption rates or ROI.

---

### SQ7 — Limitations and Future Work
**What are the known limitations of the ESS framework and prototype, and what open questions constitute the research agenda?**

- Paper sections addressed: §9 (Limitations), §10 (Future Work)
- Evidence type: Synthesis of prior sub-questions + doc analysis
- Document-analyst must look for:
  - runs/run-001/fapesp_review.md: the reviewer's identified weaknesses and scores — these are real, documented limitations
  - runs/run-001/improvement_plan.md: the open items and unresolved gaps from round 3 review
  - .claude/agents/fapesp-reviewer.md: the review criteria — what the framework was evaluated against and where it fell short
- Web-researcher must gather:
  - Open problems in evidence synthesis automation — scalability, bias, domain adaptation
  - Research gaps in executive AI systems — what the field has not yet addressed
  - Known failure modes of decision support systems in practice
- Risk: Limitations must be honest and specific, not boilerplate. The FAPESP review scores and identified gaps from run-001 are primary evidence here — use them directly.

---

## Execution Order

```
Phase 1 (parallel): SQ1 + SQ2 + SQ3
  → web-researcher handles SQ1, SQ2, SQ3 literature simultaneously
  → document-analyst reads MULTI_AGENT_PLAN.md + all agent definitions + run-001 artifacts

Phase 2 (sequential, depends on Phase 1): SQ4
  → synthesizer integrates doc findings + web findings into ESS framework definition

Phase 3 (parallel, depends on SQ4): SQ5 + SQ6
  → document-analyst maps run-001 flow to ESS layers (SQ5)
  → web-researcher gathers organizational/XAI implications literature (SQ6)

Phase 4 (sequential, depends on all prior): SQ7
  → synthesizer reviews fapesp_review.md + improvement_plan.md + prior synthesis
  → produces limitations and future work content

Phase 5: report-writer composes full paper
  → fapesp-reviewer runs quality review
```

---

## Key Risks and Gaps

| Risk | Category | Mitigation |
|------|----------|------------|
| ESS sounds like a product pitch | Framing | Report-writer must use framework/methodology language throughout; never "our system" or "our tool" |
| Signal definition is under-specified | Conceptual gap | SQ2 must produce a clear taxonomy before SQ4 can define the framework |
| Synthesis vs. summarization distinction is lost | Conceptual risk | SQ3 evidence must be gathered before the framework section is written |
| Run-001 artifacts used as proof rather than illustration | Evidence risk | Synthesizer must frame run-001 as prototype validation evidence, not as sufficient proof of effectiveness |
| FAPESP limitations are too specific to cite | Framing risk | Generalize the limitation types; cite the structural category of limitation, not internal scores |
| No external validation data exists | Gap | Limitations section must acknowledge this explicitly; future work must include evaluation against external benchmarks |
| Claude/AI tooling mentioned in agent files | Framing risk | Document-analyst must extract functional roles only; report-writer must not carry through any tool names |

---

## Document-Analyst Checklist

Files to read and extract from:

- [ ] `MULTI_AGENT_PLAN.md` — system design intent, routing logic, artifact schema
- [ ] `.claude/agents/research-architect.md` — planning layer definition
- [ ] `.claude/agents/web-researcher.md` — signal ingestion (web) layer
- [ ] `.claude/agents/document-analyst.md` — signal ingestion (document) layer
- [ ] `.claude/agents/synthesizer.md` — synthesis layer definition
- [ ] `.claude/agents/report-writer.md` — structured output layer
- [ ] `.claude/agents/fapesp-reviewer.md` — quality review layer + evaluation criteria
- [ ] `runs/run-001/question.md` — the research question that drove the prototype run
- [ ] `runs/run-001/web_evidence.jsonl` — structure and content of web-sourced signals
- [ ] `runs/run-001/doc_evidence.jsonl` — structure and content of document-sourced signals
- [ ] `runs/run-001/claims.json` — synthesized claims schema: confidence, attribution, claim type
- [ ] `runs/run-001/report.md` — structured output artifact
- [ ] `runs/run-001/fapesp_review.md` — quality review: scores, identified weaknesses
- [ ] `runs/run-001/improvement_plan.md` — open gaps and unresolved items post-review

For each file, extract:
1. Functional role in the ESS pipeline
2. Input/output schema (what goes in, what comes out)
3. Quality or traceability mechanisms
4. Any explicitly stated limitations or constraints

---

## Web-Researcher Tasklist

Gather literature on the following topics. For each, retrieve at least 2–3 primary or secondary academic sources. IEEE-citable sources preferred.

1. **Decision Support Systems (DSS)** — classical frameworks (Gorry & Scott Morton 1971, Keen & Scott Morton 1978) and modern AI-based DSS limitations
2. **Explainable AI (XAI)** — DARPA XAI program, Gunning & Aha (2019), Arrieta et al. (2020) on transparency and trust in AI decisions
3. **Weak signals theory** — Ansoff (1975) strategic issue management; Hiltunen (2008) on weak signals in foresight
4. **Environmental scanning / organizational intelligence** — Aguilar (1967), Daft & Weick (1984) on information processing in organizations
5. **Executive Information Systems (EIS)** — Rockart & Treacy (1982), Watson et al. on EIS evolution and limitations
6. **Evidence synthesis methodology** — Higgins & Green (Cochrane Handbook), Petticrew & Roberts (2006) on systematic reviews in social sciences
7. **Information processing theory** — Simon (1955, 1972) bounded rationality; March & Simon organizational decision-making
8. **AI governance and auditability** — EU AI Act traceability requirements, IEEE 7001 on transparency

---

## Report-Writer Framing Guidance

**Do:**
- Use "the ESS framework" throughout — never "our system," "our tool," or "this platform"
- Refer to the prototype as "a prototype implementation" or "a working instantiation of the framework"
- Use passive or third-person constructions for the implementation description
- Cite all claims to the specific source: literature citation, evidence artifact, or claims.json entry
- Use academic hedging language where empirical validation is absent: "suggests," "indicates," "warrants further investigation"
- Present run-001 as an illustrative case, not as a controlled experiment
- Frame the agent pipeline functionally: "signal ingestion layer," "evidence analysis layer," "synthesis layer," "structured output layer," "quality review layer"

**Do not:**
- Mention Claude, LLMs by name, multi-agent orchestration as a product category, or any vendor
- Claim the framework has been validated beyond prototype level
- Write promotional language ("powerful," "revolutionary," "state-of-the-art")
- Name internal agents (research-architect, fapesp-reviewer) in the paper body
- Present Flowity AI as the paper's subject — it may appear only as a downstream application context in §8 (Implications) if needed

**Tone:** Methodological, precise, hedged. Closest academic register: IEEE Transactions on Systems, Man, and Cybernetics or Information Systems Research.

---

## References Baseline (to be expanded by web-researcher)

- Ansoff, H.I. (1975). Managing strategic surprise by response to weak signals. California Management Review.
- Gorry, G.A. & Scott Morton, M.S. (1971). A framework for management information systems. Sloan Management Review.
- Simon, H.A. (1972). Theories of bounded rationality. Decision and Organization.
- Gunning, D. & Aha, D. (2019). DARPA's explainable artificial intelligence program. AI Magazine.
- Arrieta, A.B. et al. (2020). Explainable Artificial Intelligence (XAI): Concepts, taxonomies, opportunities and challenges toward responsible AI. Information Fusion.
- Rockart, J.F. & Treacy, M.E. (1982). The CEO goes on-line. Harvard Business Review.
- Daft, R.L. & Weick, K.E. (1984). Toward a model of organizations as interpretation systems. Academy of Management Review.
- Petticrew, M. & Roberts, H. (2006). Systematic Reviews in the Social Sciences. Blackwell.

---

*Plan authored by: research-architect agent | Run: 002 | Date: 2026-03-19*
