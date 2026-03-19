# Architecting Multi-Agent Research Systems for Reliability, Traceability, and Academic-Grade Outputs

**Run ID:** run-001
**Date:** 2026-03-18
**Status:** Final Report

---

## 1. Research Question

How should a multi-agent research system be architected to guarantee reliability, full traceability, and academic-grade outputs — covering agent role separation, orchestration patterns, MCP (Model Context Protocol) integration, and context window management?

This question was decomposed into five operative sub-questions (SQ1 through SQ5) addressing orchestration architecture, fault tolerance, auditability, MCP integration, and context window management respectively. Evidence was retrieved and synthesized across these dimensions before this report was composed.

---

## 2. Context and Background

Multi-agent systems (MAS) built on large language models (LLMs) have matured rapidly from research prototypes to production deployments. Unlike single-agent pipelines, MAS introduces coordination complexity: multiple agents must divide labor, share information, and hand off intermediate artifacts without introducing errors, losing provenance, or exhausting computational context. For research-oriented MAS specifically — where outputs must meet academic-grade standards of citation, reasoning transparency, and reproducibility — the architectural stakes are particularly high.

The system under study (run-001) implements a four-phase sequential pipeline with distinct specialist agents: a web-researcher for evidence retrieval, a document-analyst for extraction and annotation, a synthesizer for cross-question integration, and a report-writer for structured output composition. These roles are coordinated by a central research-architect orchestrator. The architectural decisions examined in this report are drawn from both the system's own design artifacts and the broader literature on multi-agent systems, reliability engineering, AI governance, and context management.

The analysis proceeds across five dimensions, followed by cross-cutting observations, identified contradictions, and open design questions.

---

## 3. Key Findings

- The orchestrator-worker hierarchical pattern is the dominant, production-validated primitive for multi-agent research systems, but evidence suggests a hybrid incorporating blackboard-style shared artifact spaces may outperform pure hierarchies for research tasks (C1, C2; X1).
- Multi-agent LLM systems exhibit at least 14 distinct failure modes, with system correctness as low as 25% on challenging tasks; the system under study currently lacks any implemented fault-tolerance layer — a critical gap before production deployment (C5, C6; X2).
- A minimum viable audit log must capture six fields per agent action; the current artifact pipeline provides structural traceability but lacks immutability guarantees, making it vulnerable to undetected post-creation modification (C9, C10; X3).
- MCP and A2A are complementary protocols operating at different architectural levels; on-demand tool loading via MCP reduces context overhead, making context-efficient tool registration a design requirement rather than an optimization (C12, C13).
- Virtual context management combining in-context and archival storage is viable for long research runs, but no single strategy is sufficient — production deployments require a composited approach (C15, C16).
- Traceability standards for multi-agent AI systems are not yet standardized; the current JSONL schema is an ad hoc design substituting for an established convention (C11; G2).
- Strict agent role separation and phase sequencing create natural traceability checkpoints, but without runtime schema enforcement, boundary violations remain possible and undetectable (C3; G10).

---

## 4. Detailed Analysis

### 4.1 Orchestration and Architectural Patterns (SQ1)

The orchestrator-worker hierarchical pattern is identified as the dominant production architecture for multi-agent research systems (C1). Evidence from formal orchestration frameworks [web-001] and Google's Agent Development Kit [web-004] confirms its adoption as a production-grade primitive. The system under study implements this pattern through the research-architect agent, which scopes questions, assigns sub-questions to specialist agents, sequences phases, and manages the run directory [doc-001, doc-002].

Multiple alternative patterns exist, each with distinct cost and reliability trade-offs (C2). Evidence [web-002] identifies six core patterns — Orchestrator-Worker, Swarm, Mesh, Hierarchical, Pipeline, and Blackboard — and notes that poor pattern selection can inflate token costs substantially, though this specific cost claim lacks peer-reviewed validation and should be treated as speculative (X6). Blackboard architectures, in which agents share a common information space, are shown to outperform master-slave and pipeline patterns by 13–57% on multi-agent data discovery tasks [web-005, web-006], raising the question of whether the current orchestrator-worker design is optimal for research workloads (X1).

The resolution to this tension is a hybrid approach: orchestrator-worker for phase-level coordination, with blackboard-style shared artifact files enabling within-phase agent communication. This preserves the auditability and role isolation benefits of the pipeline while capturing some of the collaborative advantages of blackboard sharing.

Role separation is enforced through a formal evidence-type matrix documenting primary versus secondary agent responsibilities per sub-question (C3) [doc-003, doc-004, doc-005]. The synthesizer is explicitly prohibited from fetching new data [doc-018], establishing a phase boundary that prevents context contamination. This design is functionally sound, but no runtime enforcement mechanism (e.g., output schema validation) is documented to detect boundary violations — a gap that could allow silent role drift (G10).

Framework selection among LangGraph, CrewAI, and AutoGen is identified as a consequential architectural decision (C4). Latency benchmarks from a vendor source [web-024] favor LangGraph for complex stateful workflows, but these benchmarks derive from a single scenario by a commercially interested party and should not be treated as definitive.

### 4.2 Reliability and Failure Mitigation (SQ2)

The failure landscape for multi-agent LLM systems is formally characterized in the MAST taxonomy (C5), which identifies at least 14 distinct failure modes across three categories — system design issues, inter-agent misalignment, and task verification failures — derived from 1,600+ annotated traces across seven frameworks, with expert-validated inter-annotator agreement of kappa=0.88 [web-007]. State-of-the-art system correctness is as low as 25% on challenging tasks, establishing a sobering baseline for production expectations. The MAST dataset covers open-source frameworks only; enterprise system failure distributions may differ.

Production-grade reliability requires a layered fault-tolerance strategy (C6). Retry policies address transient errors; fallback routing handles provider outages; circuit breakers prevent retry storms that can amplify system load by an order of magnitude [web-008, web-010]. These patterns are complementary, not interchangeable — applying only one addresses only one failure class. The current system design contains none of these mechanisms, representing a critical gap between the recommended architecture and what is actually implemented (X2) [doc-006]. This must be addressed before any production deployment.

Error classification — distinguishing transient, LLM-recoverable, and human-required error types — is identified as the highest-impact single reliability intervention (C7) [web-009]. For LLM agents specifically, silent failures (plausible-looking but incorrect outputs that trigger no error signal) are a particular concern, and no runtime mechanism for detecting them is documented in the current system (G5). The use of judge agents — additional agents that evaluate outputs against quality thresholds — is proposed in the literature [web-009] but has not been implemented or specified.

The system includes a FAPESP review feedback loop indicating intent for iterative quality improvement, but this loop lacks defined exit criteria, revision thresholds, or maximum iteration counts (C8) [doc-024, doc-021]. This creates both an unbounded execution risk and an audit trail ambiguity: without a defined terminal state, it is unclear which version of a report constitutes the final auditable output (G7).

### 4.3 Traceability and Audit Trail Design (SQ3)

A minimum viable audit log for agentic AI must capture six fields per action: initiating actor, timestamp, full prompt, knowledge sources with document references, response generated, and all tool calls executed (C9) [web-011]. Enterprise leaders broadly recognize this requirement — 75% rank auditability as crucial for agent deployment [web-012], though this survey figure lacks a disclosed sample size and should be read as directional rather than precise.

The system's artifact pipeline — from question.md through web_evidence.jsonl, doc_evidence.jsonl, claims.json, report.md, fapesp_review.md, to improvement_plan.md — constitutes a de-facto structural audit trail preserving intermediate outputs at each stage [doc-010]. This satisfies the sequential provenance requirement. However, no hash, checksum, or immutability mechanism is specified for these files (C10, X3). Files can be modified post-creation without detection, which is incompatible with the completeness and retrievability requirements that authoritative governance frameworks specify [web-013]. Git-based immutability (committing each artifact immediately after creation) or file checksumming would close this gap without requiring additional infrastructure.

Multi-agent governance requires validation at three layers: unit-level agent behavior, integration-level handoff correctness, and system-level decision provenance (C10) [web-013]. The current system addresses the system-level layer through its artifact pipeline but lacks documented unit-level or integration-level test harnesses.

Traceability standards for multi-agent AI are not yet standardized (C11) [doc-009]. The JSONL evidence schema used in this system is an ad hoc design — functional but without interoperability guarantees, no formal conflict-resolution protocol, and no alignment with emerging standards such as W3C PROV or OpenLineage (G2). Conflict resolution during synthesis is currently left to the synthesizer's discretion [doc-023], substituting judgment for protocol.

### 4.4 MCP Integration Patterns (SQ4)

The Model Context Protocol (MCP) is an open, standardized protocol replacing fragmented bespoke tool integrations with a universal client-server architecture (C12). A single MCP server works with any MCP-compatible AI, and a single AI can connect to multiple servers simultaneously [web-015, web-016]. MCP uses JSON-RPC 2.0 as its protocol layer; on-demand tool loading filters irrelevant tool schemas from the model's active context, making context efficiency a direct benefit of correct MCP integration [web-017].

MCP governs agent-to-tool communication, while Google's Agent-to-Agent (A2A) protocol governs peer-to-peer agent task delegation; the two protocols are complementary and can be used simultaneously within the same system (C13) [web-018, web-019]. A2A launched in April 2025 with over 50 technology partners; MCP was donated to the Linux Foundation in December 2025, providing governance stability that may reduce the protocol drift risk — though this remains unconfirmed.

MCP's rapid evolution represents a concrete integration risk (C14). Documentation as of early 2026 may already be outdated relative to implementation [doc-013], and the current system contains no MCP configuration artifacts, no SDK version pins, and no protocol monitoring strategy [doc-014] (G3). Adopting version pinning and changelog monitoring as standard engineering practice is the minimum required mitigation.

The relationship between A2A and the current file-based inter-agent handoff has not been evaluated, leaving open whether A2A adoption would improve or complicate the existing orchestration design (G8).

### 4.5 Context Window and Memory Management (SQ5)

Virtual context management — dynamically moving information between in-context memory and external archival storage via agent-initiated tool calls — is a viable architecture for maintaining coherent long-running research tasks beyond single context window limits (C15). The MemGPT two-tier architecture [web-020] and the canonical agent memory taxonomy [web-023] both support this pattern. The system partially implements it: each agent has a dedicated file-based persistent memory directory that survives context resets [doc-017], providing an external memory mechanism. However, no cross-agent memory access or shared state is specified beyond the artifact files, limiting coordination effectiveness.

No single context management strategy is sufficient for long research runs (C16). Production deployments require a combination of selective context injection, compression and summarization, RAG-based retrieval, and sliding window pruning [web-021, web-022]. The research plan targets all four strategies [doc-015], but no implementation details or eviction policies are specified. The point at which context window exhaustion becomes a bottleneck in this system's pipeline is unknown (G9).

Context management strategies vary significantly by LLM provider and model (C17) [doc-016, web-023]. The system does not specify which provider and model version are in production scope, making it impossible to determine which strategies are natively supported versus requiring custom implementation (G4). Defining the target LLM scope is a prerequisite for any concrete context management specification.

Shared memory architectures for multi-agent systems require dynamic access control to maintain coordination while preserving traceability (C18) [web-025, web-022]. Uncontrolled shared memory degrades both coherence and accountability. The current per-agent isolated memory design avoids this problem but at the cost of limiting inter-agent knowledge sharing. The design decision between extended shared memory with access control versus maintained isolation should be made explicit before scaling the system.

---

## 5. Cross-Cutting Findings

Several findings span multiple sub-questions and represent architectural interdependencies that should be treated as unified design concerns rather than independent decisions.

**Reliability and traceability are architecturally intertwined (SQ2, SQ3).** The same audit logging that enables post-hoc traceability also enables real-time fault detection and recovery routing [web-007, web-011, web-013, doc-010, doc-011]. Systems designed without integrated logging sacrifice both properties simultaneously. Designing the logging system once, for both purposes, avoids redundant infrastructure and ensures consistency.

**MCP integration and context window management are mutually reinforcing (SQ4, SQ5).** MCP's on-demand tool loading filters irrelevant tool schemas from the context window, making context-efficient tool registration a design requirement rather than an optimization [web-017, web-021, doc-012, doc-015]. MCP and context management strategies should be co-designed.

**Role separation and traceability are co-dependent (SQ1, SQ3).** Strict agent role boundaries create natural traceability checkpoints at handoff stages [doc-003, doc-010, web-013]. Conversely, unenforced boundaries make it impossible to attribute errors or outputs to specific agents in an audit. Runtime enforcement of role boundaries is therefore a traceability requirement, not merely an architectural preference.

**The blackboard pattern and shared memory with access control converge on the same design (SQ1, SQ5).** Both propose a common information space with controlled read/write semantics [web-005, web-006, web-025, web-022]. Rather than treating these as separate decisions, they should be evaluated together as a single architectural option: a shared, access-controlled knowledge space serving both coordination and memory functions.

**Agent-specific file-based persistent memory simultaneously addresses SQ5 (external memory) and SQ3 (per-agent audit records)** [doc-017, web-025, doc-008]. Well-chosen implementation primitives can satisfy multiple architectural requirements simultaneously. However, the absence of cross-agent memory access limits coordination; extending this mechanism to a shared store with access control would compound its benefits.

**Orchestration pattern choice, fault tolerance design, and framework selection are tightly coupled (SQ1, SQ2, SQ5).** The choice of LangGraph, CrewAI, or AutoGen determines which fault tolerance primitives are available, which context management features are built in, and which orchestration topologies are feasible [web-024, web-008, web-010, doc-021]. These three decisions should be made jointly rather than sequentially.

---

## 6. Contradictions and Ongoing Debates

**X1 — Blackboard vs. orchestrator-worker performance.** Blackboard architecture is shown to outperform master-slave and pipeline patterns by 13–57% on multi-agent data discovery tasks [web-005], yet the system implements orchestrator-worker [doc-001]. The resolution is domain-specific: blackboard advantages were demonstrated on data science discovery, not research pipeline tasks, and orchestrator-worker provides stronger auditability. A hybrid approach is recommended.

**X2 — Fault tolerance specification vs. implementation gap.** Web evidence characterizes layered fault tolerance (retry, fallback, circuit breaker) as essential for production reliability [web-008], but the system's design documents contain no implementation of any such mechanism [doc-006]. The gap is real and unresolved; the research plan treats these patterns as investigative targets rather than implemented features.

**X3 — Structural vs. cryptographic audit trail.** The artifact pipeline is described as a de-facto audit trail [doc-010], but governance frameworks require audit trails to be complete and retrievable without modification risk [web-013]. The current mutable artifact files satisfy the structural requirement but not the cryptographic one.

**X4 — Single-agent vs. multi-agent memory taxonomy.** The canonical four-type memory taxonomy (sensory, short-term, long-term, episodic) [web-023] was developed for single-agent systems and does not account for inter-agent access control, a requirement that emerges in multi-agent settings [web-022]. The taxonomy should be extended with a shared/collaborative memory type rather than replaced wholesale.

**X5 — Citation format inconsistency.** The research plan recommends IEEE citation format for technical systems research [doc-010], but the report-writer agent's operational rules do not explicitly specify this format [doc-019]. Until resolved, outputs may default to a different citation standard, undermining academic-grade consistency.

**X6 — Token cost impact of pattern selection.** A developer blog claims wrong pattern selection inflates token costs by 200% [web-002], while independent benchmarks show framework performance differences of 10–35 seconds for comparable workflows [web-024]. The magnitude of cost impact from pattern selection is unresolved; the 200% figure should be treated as speculative pending peer-reviewed validation.

---

## 7. Limitations of This Research

Several limitations bound the conclusions that can be drawn from this analysis.

**Vendor-sourced evidence predominates.** A substantial portion of the web evidence originates from vendors with commercial interests in specific tools or frameworks (Portkey [web-008], Maxim [web-009], Langfuse [web-024]). Recommendations derived from these sources should be treated as directional rather than independently validated, particularly where quantitative claims are made without cited benchmarks.

**No peer-reviewed benchmarks for research-specific MAS workloads exist.** Available benchmarks for multi-agent system performance cover coding, mathematics, and conversational tasks. No benchmark was identified that evaluates orchestration patterns, fault tolerance compositions, or context management strategies under academic research workflows specifically (G1, G9). The generalizability of available findings to research-oriented MAS is uncertain.

**The target LLM provider and model version are unspecified.** Context management strategies, MCP integration details, and fault tolerance implementations all vary materially by LLM provider. Without specifying the target model, concrete implementation recommendations cannot be made with confidence (G4).

**The MAST taxonomy covers open-source frameworks only** [web-007]. Failure mode distributions for proprietary or enterprise-grade MAS deployments may differ substantially, limiting the applicability of the 14-failure-mode taxonomy to the system under study.

**MCP documentation may already be outdated.** MCP is a rapidly evolving protocol. Evidence gathered in early 2026 may not reflect the current specification, and no mechanism exists within this research run to detect post-retrieval protocol changes (C14).

**The artifact pipeline lacks immutability.** The claims in this report are derived strictly from the claims.json file as specified, but the integrity of that file relative to the original retrieved evidence cannot be cryptographically verified within the current system design. This is a limitation of the system's own audit trail that applies to this report as well.

---

## 8. Open Questions

The following questions remain unresolved by the available evidence and represent the most consequential decision points for the system's continued development.

1. **Which LLM provider and model version are in production scope?** This is a prerequisite for specifying context management strategies, MCP integration details, and fault tolerance parameters. (G4; C17)

2. **Should the system adopt a hybrid blackboard/orchestrator-worker architecture?** Evidence suggests this could improve research task performance without sacrificing auditability, but no empirical evaluation exists for research-specific workflows. (C2; X1)

3. **What exit criteria should terminate the FAPESP review feedback loop?** Without defined revision thresholds and maximum iteration counts, the loop is functionally unbounded and undermines audit trail completeness. (C8; G7)

4. **Should intermediate artifact files be cryptographically signed or checksummed?** Git-based immutability is the lowest-cost path to closing the audit trail integrity gap. (C10; X3)

5. **Should A2A be adopted for inter-agent communication, or should file-based handoff be retained?** The relationship between these two coordination mechanisms has not been evaluated. (C13; G8)

6. **How should silent agent failures be detected?** No runtime mechanism for detecting plausible-but-incorrect LLM outputs is documented. Judge agents are proposed in the literature but remain unspecified in this system. (C7; G5)

7. **Should the per-agent isolated memory be extended to a shared access-controlled store?** This decision has implications for both coordination effectiveness (SQ5) and traceability (SQ3). (C18; cross-cutting finding 5)

8. **Which provenance standard — W3C PROV, OpenLineage, or a domain-specific schema — should be evaluated for adoption?** The current ad hoc JSONL schema is a viable interim solution but not a durable standard. (C11; G2)

---

## 9. Suggested Next Steps

The following actions are sequenced by urgency and dependency, based on the gaps and contradictions identified in this report.

**Immediate (pre-production blockers):**

1. **Define the target LLM provider and model version.** This unblocks all context management, fault tolerance, and MCP integration specifications. It is the highest-leverage single decision in the system's current state. (G4)

2. **Implement a fault tolerance layer.** Add retry policies, fallback routing, and circuit breakers to the orchestration layer. Begin with exponential backoff retries for transient LLM errors and circuit breakers protecting against retry storms. Select patterns jointly with the framework decision. (C6; X2)

3. **Define exit criteria for the FAPESP review loop.** Specify a numeric quality threshold, a maximum revision count, and a protocol for escalating to human review when neither is met. (C8; G7)

**Short-term (architecture quality):**

4. **Evaluate and select an orchestration framework explicitly.** Make this choice jointly with the orchestration topology and fault tolerance pattern decisions, as they are tightly coupled. LangGraph is the current evidence-preferred option for stateful research workflows, subject to independent validation. (C4; cross-cutting finding 6)

5. **Add artifact immutability to the pipeline.** Implement git-based checksumming or file signing so that intermediate artifacts cannot be modified post-creation without detection. This closes the audit trail integrity gap identified in X3. (C10; G2)

6. **Specify a judge agent design for research workloads.** Define when judge agents activate, what quality thresholds they enforce, and how their evaluations are logged as part of the audit trail. (C7; G5)

**Medium-term (architectural enhancement):**

7. **Evaluate the hybrid blackboard/orchestrator-worker architecture.** Prototype shared artifact files as a blackboard space for within-phase agent communication while retaining the orchestrator-worker pattern for phase coordination. Measure the impact on task success rate and token efficiency against a pure orchestrator-worker baseline. (C2; X1)

8. **Evaluate A2A adoption.** Assess whether replacing file-based inter-agent handoff with A2A protocol would improve coordination latency and observability, or introduce complexity that outweighs the benefits. (C13; G8)

9. **Evaluate a provenance standard for the evidence schema.** Assess W3C PROV or OpenLineage against the current JSONL schema, and determine whether interoperability benefits justify migration cost. (C11; G2)

10. **Resolve the citation format inconsistency.** Update the report-writer agent's operational rules to specify IEEE citation format explicitly, consistent with the research plan's recommendation. (X5)

---

*This report was produced by the report-writer agent (run-001, 2026-03-18) based strictly on the synthesized claims in claims.json and the research question in question.md. All claim references (C1–C18), evidence identifiers (web-xxx, doc-xxx), contradiction identifiers (X1–X6), and gap identifiers (G1–G10) are traceable to those source files. No citations, paper titles, or author names were invented; all evidence references correspond to identifiers present in the synthesized claims.*
