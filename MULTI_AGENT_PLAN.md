# Multi-Agent Research Plan

## System Purpose

This repository contains a reusable multi-agent research assistant designed to help plan, gather evidence, synthesize findings, draft research reports, and evaluate outputs against a FAPESP PIPE-aligned research standard.

It is intended to support multiple research projects, including but not limited to Zenodo-oriented research, GitHub research repos, literature reviews, technical memos, and structured evidence-based writeups.

---

## Active Research Target

- Project Name: Multi-Agent Research System Design
- Target Path: runs/run-001/
- Research Question: How should a multi-agent research system be architected to guarantee reliability, full traceability, and academic-grade outputs — covering agent role separation, orchestration patterns, MCP integration, and context window management?
- Final Output Goal: Structured markdown report with inline citations (IEEE style), covering architecture, reliability, traceability, MCP integration, context management, and output quality standards
- Status: Planned

---

## Current Run

- Run ID: run-001
- Started At: 2026-03-18
- Coordinator: research-architect
- Current Phase: Phase 1 — Parallel Evidence Retrieval (SQ1–SQ5)
- Current Status: Ready to execute

### Planned Phases

- [x] Scope research question → `runs/run-001/question.md`
- [ ] Phase 1 — Parallel evidence retrieval (SQ1–SQ5): web-researcher + document-analyst
- [ ] Phase 2 — Standards analysis (SQ6): document-analyst
- [ ] Phase 3 — Cross-cutting synthesis (SQ7): synthesizer
- [ ] Phase 4 — Report writing: report-writer
- [ ] Run FAPESP review
- [ ] Revise if needed
- [ ] Finalize outputs

---

## Specialist Agents

- research-architect — plans and structures the research workflow
- web-researcher — gathers external evidence and source-based support
- document-analyst — extracts evidence from local project materials
- synthesizer — transforms evidence into claims, conflicts, and gaps
- report-writer — drafts structured research outputs
- fapesp-reviewer — evaluates rigor, clarity, novelty, and research credibility

---

## Evidence Strategy

### Web Evidence Needed

- Academic literature and technical reports on multi-agent system (MAS) architectures (SQ1)
- Engineering blogs and white papers on multi-agent reliability and fault tolerance (SQ2)
- Research on provenance tracking and AI pipeline auditability (SQ3)
- Official MCP documentation, spec, and published integration patterns (SQ4)
- Long-context LLM strategies: RAG, summarization, sliding windows, vector stores (SQ5)

### Document Evidence Needed

- Local project files and prior notes in this repository (all SQ)
- Academic writing standards: APA, Chicago, IEEE citation norms (SQ6)
- Peer-reviewed work on AI in academic research workflows (SQ6)

### Expected Risks / Gaps

- MCP is rapidly evolving; documentation as of early 2026 may already be outdated
- "Academic-grade" is under-defined for AI-assisted research — synthesizer must flag this
- Context management strategies vary by model; findings may not generalize across LLM providers
- No single source covers all 7 sub-questions; synthesis requires combining heterogeneous evidence
- Traceability standards for multi-agent AI systems are not yet standardized

---

## Decisions

- Citation standard: IEEE (technical systems research)
- Orchestration assumption: LLM-based agents, not symbolic AI
- Tool integration: MCP as primary protocol (not function calling alone)
- Output consumers: human review (not automated downstream consumption)
- SQ1/SQ2 boundary overlap: document-analyst must cross-reference both

---

## Open Questions

- What does "academic-grade" mean specifically in the STEM context this system targets?
- Should SQ6 results be used to constrain the report-writer's output format before Phase 4?
- Should the fapesp-reviewer run before or after the first full report draft?

---

## Next Actions

1. Run web-researcher agent on SQ1–SQ5 in parallel → save to `runs/run-001/web_evidence.jsonl`
2. Run document-analyst on local project files for SQ1–SQ6 → save to `runs/run-001/doc_evidence.jsonl`
3. Run synthesizer on combined evidence → save to `runs/run-001/claims.json`

---

## Run Outputs

Expected artifacts per research run:

- question.md
- web_evidence.jsonl
- doc_evidence.jsonl
- claims.json
- report.md
- fapesp_review.md
- improvement_plan.md

---

## Notes

- Keep planning separate from evidence gathering and report writing.
- Do not treat draft notes as confirmed facts.
- Do not allow major claims without evidence.
- Use the FAPESP reviewer to strengthen research rigor and credibility.
