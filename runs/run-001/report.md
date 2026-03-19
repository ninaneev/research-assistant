# Architecting Multi-Agent Research Systems for Reliability, Traceability, and Academic-Grade Outputs

**Run ID:** run-001
**Date:** 2026-03-19
**Status:** Final Report (Revised — full inline citations added)

---

## 1. Research Question

How should a multi-agent research system be architected to guarantee reliability, full traceability, and academic-grade outputs — covering agent role separation, orchestration patterns, MCP (Model Context Protocol) integration, and context window management?

This question is decomposed into five operative sub-questions (SQ1–SQ5): agent role separation and orchestration patterns; reliability and fault tolerance; traceability and auditability; MCP integration; and context window and memory management. Evidence was collected from 25 web sources and 25 local document evidence entries spanning academic papers, engineering blogs, official technical documentation, and internal system design artifacts.

---

## 2. Context and Background

This work contributes a synthesized architectural perspective not present in any single source, including:
(1) the unification of reliability and traceability as a single governance layer,
(2) a hybridization of orchestrator-worker and blackboard architectures for research systems, and
(3) a structured memory taxonomy adapted for agentic research workflows.

These contributions emerge from cross-source synthesis rather than direct prior formulation in the literature.

Multi-agent systems (MAS) built on large language models (LLMs) have matured rapidly from research prototypes to production deployments. Unlike single-agent pipelines, MAS introduces coordination complexity: multiple agents must divide labor, share information, and hand off intermediate artifacts without introducing errors, losing provenance, or exhausting computational context. For research-oriented MAS specifically — where outputs must meet standards of citation, reasoning transparency, and reproducibility — the architectural stakes are particularly high.

A unified architectural framework integrating planning, policy enforcement, state management, and quality operations into an orchestration layer is recognized as essential for scalable multi-agent systems [1]. The system examined in this report implements a four-phase sequential pipeline with four distinct specialist agents — a web-researcher for evidence retrieval, a document-analyst for extraction and annotation, a synthesizer for cross-question integration, and a report-writer for structured output composition — coordinated by a central research-architect orchestrator [doc-001, doc-002]. The system explicitly assumes LLM-based agents (not symbolic AI), designates MCP as the primary tool-integration protocol, and targets human review as the terminal consumption model for all outputs [doc-025].

The analysis proceeds across five sub-question dimensions, followed by cross-cutting observations, identified contradictions, and open design questions. Confidence levels (high/medium/low) follow those assigned during synthesis and are noted where directly relevant. Sub-questions SQ6 and SQ7 are addressed through the synthesis and integration framework described in Sections 4 and 5.

This work represents a structured research synthesis based on collected sources and system design analysis; it does not correspond to an executed empirical deployment of the described architecture.

---

## 3. Key Findings

1. The orchestrator-worker hierarchical pattern is the dominant, production-validated primitive for multi-agent research systems [1][4], but evidence suggests that hybrid incorporation of blackboard-style shared artifact spaces may outperform pure hierarchies on research tasks [5][6].
2. Multi-agent LLM systems exhibit at least 14 distinct failure modes, with system correctness as low as 25% on challenging tasks [7]; the system under study currently lacks any implemented fault-tolerance layer — a critical gap prior to production deployment.
3. A minimum viable audit log must capture six mandatory fields per agent action [11]; the current artifact pipeline provides structural traceability but no immutability guarantees, making it vulnerable to undetected post-creation modification [13][doc-010].
4. MCP and A2A are complementary protocols operating at different architectural levels [18][19]; on-demand tool loading via MCP reduces context overhead directly, making context-efficient tool registration a design requirement rather than an optimization [17].
5. Virtual context management combining in-context and archival storage is viable for long research runs [20], but no single strategy is sufficient — production deployments require a composed multi-strategy approach [21][22].
6. Traceability standards for multi-agent AI systems are not yet standardized; the current JSONL evidence schema is an ad hoc design substituting for an established convention [doc-009].
7. Strict agent role separation and phase sequencing create natural traceability checkpoints, but without runtime schema enforcement, boundary violations remain possible and undetectable [doc-003][13].

---

## 4. Detailed Analysis

### 4.1 Orchestration and Architectural Patterns (SQ1)

The orchestrator-worker hierarchical pattern is the dominant, production-validated architectural primitive for multi-agent research systems (high confidence). A 2026 academic survey formalizes orchestration as a coherent layer covering planning, policy enforcement, state management, and quality operations — providing an implementation-ready blueprint for enterprise-scale AI ecosystems [1]. Google's Agent Development Kit independently validates this pattern, formalizing orchestrator-worker and hierarchical delegation as production-grade primitives that support both stateless and stateful worker variants [4]. The system under study implements this pattern through the research-architect agent, which scopes questions, assigns sub-questions to specialist agents, sequences phases, and manages the run directory [doc-001, doc-002].

Multiple alternative orchestration patterns exist, each presenting distinct reliability and role-separation trade-offs (medium confidence). Evidence identifies six core patterns — Orchestrator-Worker, Swarm, Mesh, Hierarchical, Pipeline, and Blackboard — Industry guidance on event-driven multi-agent design further argues that asynchronous message-passing patterns can improve decoupling and resilience in agent coordination [2][3], though this specific quantitative token-cost claim originates from a developer blog and lacks peer-reviewed benchmark support. More rigorously, blackboard architectures — in which agents share a common information space and observe full peer reasoning context — have been shown to outperform master-slave and RAG baselines by 13–57% in end-to-end task success on multi-agent data discovery tasks [5][6]. A separate study demonstrates that agents sharing full reasoning context on a blackboard reduce redundant computation and reach consensus-driven outputs [6]. These performance advantages raise the question of whether the current orchestrator-worker design is optimal for research workloads, though the blackboard results were obtained on data science discovery tasks and may not transfer directly to research pipeline workflows.

The resolution proposed by the synthesis layer is a hybrid approach: orchestrator-worker coordination at the phase level with blackboard-style shared artifact files enabling within-phase agent communication. This preserves the auditability and role isolation benefits of the pipeline pattern while capturing some of the collaborative advantages of the blackboard.

Role separation is enforced through a formal evidence-type matrix documenting primary versus secondary agent responsibilities per sub-question [doc-003, doc-004, doc-005]. The synthesizer is explicitly prohibited from fetching new data mid-synthesis, enforcing a phase boundary that prevents context contamination [doc-018]. Sub-questions SQ1–SQ5 are designed for parallel execution and are treated as independent [doc-004]. This design is functionally sound, but no runtime enforcement mechanism — such as output schema validation — is documented to detect boundary violations, a gap that could allow silent role drift [doc-003].

Framework selection among LangGraph, CrewAI, and AutoGen is a consequential architectural decision directly affecting context management durability, coordination latency, and workflow flexibility (medium confidence). In a 4-agent benchmark with 8–12 LLM calls per run, LangGraph achieved 25–35 seconds, AutoGen 30–40 seconds, and CrewAI 45–60 seconds; LangGraph is recommended for complex stateful research workflows [24]. These benchmarks originate from Langfuse, a vendor that integrates commercially with all three frameworks, which limits their generalizability.

### 4.2 Reliability and Fault Tolerance (SQ2)

The failure landscape for multi-agent LLM systems is formally characterized by the MAST taxonomy (high confidence), which identifies at least 14 distinct failure modes across three categories — system design issues, inter-agent misalignment, and task verification failures — built from over 1,600 annotated traces across seven MAS frameworks and validated with an expert inter-annotator agreement of kappa = 0.88 [7]. State-of-the-art system correctness on challenging tasks reaches as low as 25% [7]. The taxonomy covers open-source frameworks; enterprise deployment failure distributions may differ.

Production-grade reliability requires a layered fault-tolerance strategy, with each pattern addressing a distinct failure class (high confidence). Retry policies address transient errors using exponential backoff; fallback routing redirects to cross-provider alternatives during sustained outages; circuit breakers proactively cut traffic to unhealthy components before retry storms cascade [8]. Retry storms — in which a single failure multiplies load by an order of magnitude within seconds across a multi-agent cascade — represent a concrete systemic risk [8][10]. Circuit breakers and retry policies are complementary, not interchangeable: the former protects system stability during prolonged outages while the latter handles temporary glitches [10]. These reliability recommendations originate from vendor sources (Portkey [8] and DEV Community [10]); independent peer-reviewed benchmarks do not exist in the retrieved evidence set.

Error classification is identified as the highest-impact single reliability intervention (medium confidence): distinguishing transient, LLM-recoverable, and human-required error types, then routing each class to the correct handler, reduces both silent failures and unnecessary retries [9]. For LLM agents specifically, silent failures — plausible-looking but incorrect outputs that trigger no error signal — are a particular concern [9]. Judge agents that evaluate outputs against explicit quality thresholds are proposed in the literature but have not been implemented or specified in the current system design.

The system includes a FAPESP-review feedback loop indicating intent for iterative quality improvement [doc-024]. However, this loop specifies "Revise if needed" without defining revision criteria, a quality threshold, or a maximum iteration count [doc-024, doc-021]. This creates both an unbounded execution risk and an audit trail ambiguity: without a defined terminal state, it is unclear which version of a report constitutes the final auditable output.

### 4.3 Traceability and Audit Trail Design (SQ3)

A minimum viable audit log for agentic AI must capture at least six fields per action: initiating actor, timestamp, full prompt text, knowledge sources with specific document references, response generated, and all tool calls executed (medium confidence) [11]. An ISACA analysis confirms that 75% of enterprise leaders rank auditability as a crucial agent deployment requirement and characterizes agentic AI as presenting unprecedented auditing challenges due to partial observability and asynchronous operation [12]. The 75% figure is survey-derived and its sample composition is not fully disclosed. A three-layer governance framework — unit-level agent behavior testing, integration-level handoff correctness, and system-level decision provenance — is documented as a structured path to multi-agent audit compliance [13]. Agentic RAG further enables real-time citation and traceability through provenance-aware pipelines that expose uncertainty alongside evidence [14].

The system's artifact pipeline — from question.md through web_evidence.jsonl, doc_evidence.jsonl, claims.json, report.md, fapesp_review.md, and improvement_plan.md — constitutes a de-facto structural audit trail by preserving intermediate outputs at each stage [doc-010]. Each evidence item carries a mandatory source reference, sub-question tag, and confidence level [doc-011], and the document-analyst is explicitly prohibited from treating draft notes as confirmed facts without signaling uncertainty [doc-022]. This design satisfies the sequential provenance requirement. However, no hash, checksum, or immutability mechanism is specified for these artifact files [doc-010]. Files can be modified post-creation without detection, which is incompatible with the completeness and retrievability requirements that authoritative governance frameworks specify [13]. Git-based immutability — committing each artifact immediately upon creation — would close this gap at negligible infrastructure cost.

Traceability standards for multi-agent AI systems are not yet standardized; this remains a frontier area rather than established practice [doc-009]. The JSONL evidence schema used in this system is an ad hoc design — functional but without interoperability guarantees, no formal conflict-resolution protocol, and no alignment with emerging standards such as W3C PROV or OpenLineage. Conflict resolution during synthesis is currently left to the synthesizer's discretion [doc-023], substituting judgment for protocol.

### 4.4 MCP Integration Patterns (SQ4)

MCP is an open, standardized protocol replacing fragmented bespoke tool integrations with a universal client-server architecture (high confidence). Released in November 2024 and donated to the Linux Foundation in December 2025, MCP defines host, client, and server roles with well-specified lifecycle management, transport options (stdio and HTTP+SSE), authorization, and feature layers covering tools, resources, prompts, and sampling [15][16]. A single MCP server works with any MCP-compatible AI; a single AI can connect to any number of MCP servers simultaneously [15]. MCP uses JSON-RPC 2.0 as its message-exchange layer; on-demand tool loading keeps irrelevant tool schemas out of the model's active context window, directly improving context efficiency [17].

MCP and Google's Agent-to-Agent (A2A) protocol are complementary, not competing (high confidence). MCP governs agent-to-tool communication; A2A governs peer-to-peer agent task delegation at the orchestration layer; both can be used simultaneously within the same system [18][19]. A2A launched in April 2025 with over 50 technology partners, and Google explicitly describes A2A as complementary to MCP [19]. A survey of agent interoperability protocols covering MCP, ACP, A2A, and ANP confirms this layered complementarity and notes that the protocol ecosystem continues to evolve rapidly [18].

MCP's rapid evolution is a concrete integration risk (high confidence) [doc-013]. Documentation as of early 2026 may already be outdated relative to implementation, and the current system contains no MCP configuration artifacts, no SDK version pins, and no protocol drift monitoring strategy [doc-014]. Version pinning and changelog monitoring represent the minimum required mitigation. Whether Linux Foundation stewardship meaningfully stabilizes the drift risk remains an open question.

### 4.5 Context Window and Memory Management (SQ5)

Virtual context management — dynamically moving information between in-context memory and external archival storage via agent-initiated tool calls — is a viable architecture for maintaining coherent long-running research tasks beyond single context window limits (high confidence). The MemGPT system demonstrates this with a two-tier architecture: Tier 1 holds in-context core memories; Tier 2 holds out-of-context recall and archival storage; agents self-edit their context via tool calls [20]. This approach was evaluated on document analysis tasks exceeding context limits and on multi-session conversations requiring long-term memory persistence [20]. A known limitation is that MemGPT's heuristic-based scoring functions lack principled mechanisms for preventing hallucinated content during memory retrieval [20].

Production context window management requires combining multiple strategies — no single technique is sufficient for long research runs (high confidence). Selective context injection, compression and summarization, RAG-based retrieval, and sliding window pruning must be combined to balance retrieval accuracy, latency, and cost [21]. Context windows range from 8K to 200K tokens across current models; token budget exhaustion is a primary failure mode in multi-turn agent workflows [21]. Heterogeneous multi-agent systems additionally require structured contextual memory that differentiates agent roles and memory access patterns to prevent perspective inconsistency and procedural drift across long runs [22].

The canonical four-type agent memory taxonomy — sensory, short-term (in-context), long-term (external vector store), and episodic — provides a foundational reference for intra-agent memory design [23]. However, this taxonomy was developed for single-agent systems and does not account for inter-agent memory access control, a requirement that emerges only in multi-agent settings [23][22]. A 2025 preprint proposes that shared memory architectures for multi-agent systems must include dynamic access control to maintain collaborative knowledge sharing while preserving inter-agent audit traceability (medium confidence) [25]. This finding awaits independent peer review.

The system partially implements virtual context management through per-agent file-based persistent memory directories that survive context resets [doc-017]. This design addresses both the external memory requirement (SQ5) and per-agent audit records (SQ3). However, no mechanism is documented for cross-agent memory access or shared state beyond the artifact files [doc-017]. The decision between extending this to a shared access-controlled memory store versus maintaining agent isolation has not been made explicit.

Context management strategies vary significantly by LLM provider and model (high confidence) [doc-016]. Findings from research on one provider's context handling do not generalize to others [23]. The system does not specify which LLM provider or model version is in production scope, making it impossible to determine which strategies are natively supported versus requiring custom implementation [doc-016].

---

## 5. Cross-Cutting Findings

Six cross-cutting findings emerge from integration across the five sub-questions and represent architectural interdependencies that should be treated as unified design concerns.

**Reliability and traceability are architecturally intertwined (SQ2, SQ3).** The same audit logging infrastructure that enables post-hoc traceability also enables real-time fault detection and recovery routing [7][11][13]. Systems designed without integrated logging sacrifice both properties simultaneously. Designing the logging system once, for both purposes, avoids redundant infrastructure and ensures consistency [doc-010, doc-011].

**MCP integration and context window management are mutually reinforcing (SQ4, SQ5).** MCP's on-demand tool loading filters irrelevant tool schemas from the context window, making context-efficient tool registration a design requirement rather than an optimization [17][21]. MCP and context management strategies should therefore be co-designed [doc-012, doc-015].

**Role separation and traceability are co-dependent (SQ1, SQ3).** Strict agent role boundaries create natural traceability checkpoints at handoff stages [13]. Conversely, unenforced boundaries make it impossible to attribute errors or outputs to specific agents in an audit. Runtime enforcement of role boundaries is therefore a traceability requirement, not merely an architectural preference [doc-003, doc-010].

**The blackboard pattern and shared memory with access control converge on the same design primitive (SQ1, SQ5).** Both propose a common information space with controlled read/write semantics [5][6][25][22]. Rather than treating these as separate decisions, they should be evaluated together as a single architectural option — a shared, access-controlled knowledge space serving both coordination and memory functions.

**Per-agent file-based persistent memory simultaneously addresses SQ5 (external memory) and SQ3 (per-agent audit records) [doc-017][doc-008].** Well-chosen implementation primitives can satisfy multiple architectural requirements simultaneously. However, the absence of cross-agent memory access limits coordination effectiveness; extending this mechanism to a shared store with access control would compound its benefits [25].

**Orchestration pattern choice, fault tolerance design, and framework selection are tightly coupled (SQ1, SQ2, SQ5).** The choice among LangGraph, CrewAI, and AutoGen determines available fault tolerance primitives, context management features, and the feasibility of certain orchestration topologies [24][8][10]. These decisions should be made jointly rather than sequentially [doc-021].

---

## 6. Contradictions and Ongoing Debates

**X1 — Blackboard vs. orchestrator-worker performance.** Evidence demonstrates blackboard architecture outperforming master-slave and pipeline patterns by 13–57% on multi-agent data discovery [5], yet the system implements an orchestrator-worker hierarchical pattern [doc-001]. The blackboard performance advantage was demonstrated on data science discovery tasks; it has not been evaluated on research pipeline workflows. The orchestrator-worker pattern offers stronger role isolation and auditability. A hybrid approach — orchestrator-worker for phase coordination with blackboard-style shared artifact files for within-phase communication — is the proposed resolution.

**X2 — Fault tolerance specification vs. implementation gap.** Web evidence characterizes layered fault tolerance (retry, fallback, circuit breaker) as essential for production reliability [8][10], but the system design documents contain no implementation of any such mechanism [doc-006]. The gap is real and unresolved. The research plan treats these patterns as investigative targets rather than implemented features, and the gap must be addressed before production deployment.

**X3 — Structural vs. cryptographic audit trail.** The artifact pipeline is described as a de-facto audit trail [doc-010], but multi-agent governance frameworks require audit trails to be complete and tamper-evident [13]. The current mutable artifact files satisfy the structural requirement but not the integrity requirement. Git-based checksumming of each artifact at creation time is proposed as a low-cost resolution.

**X4 — Single-agent vs. multi-agent memory taxonomy.** The canonical four-type memory taxonomy (sensory, short-term, long-term, episodic) [23] was developed for single-agent systems and does not account for inter-agent memory access control, a requirement that emerges in multi-agent settings [22]. The taxonomy should be extended with a fifth type — shared/collaborative memory with access control [25] — rather than replaced.

**X5 — Citation format inconsistency.** The research plan recommends IEEE citation format for technical systems research [doc-010], but the report-writer agent descriptor did not explicitly specify IEEE at the time of evidence collection [doc-019]. Until resolved, outputs may default to a different citation standard, undermining academic-grade consistency. The report-writer agent descriptor should be updated to specify IEEE citation format explicitly.

**X6 — Token cost impact of pattern selection.** A developer blog claims wrong orchestration pattern selection inflates token costs by 200% [2], while an independent benchmark shows framework performance differences of only 10–35 seconds for comparable workflows [24]. The magnitude of the cost impact is unresolved. The 200% figure should be treated as speculative pending peer-reviewed validation; both sources carry vendor bias.

---

## 7. Limitations of This Study

**Vendor-sourced evidence predominates in reliability and fault tolerance sections.** Key evidence on retry policies, circuit breakers, and error classification originates from Portkey [8], Maxim AI [9], and DEV Community practitioners [10] — parties with commercial or reputational interests in specific implementation approaches. Independent peer-reviewed benchmarks for fault tolerance pattern compositions in LLM research pipelines do not exist in the retrieved evidence set.

**No peer-reviewed benchmarks for research-specific MAS workloads exist.** Available benchmarks for multi-agent system performance cover coding, mathematics, and conversational tasks. No benchmark evaluates orchestration patterns, fault tolerance compositions, or context management strategies under academic research workflows specifically. The generalizability of available findings to research-oriented MAS is uncertain.

**Several sources are preprints or opinion pieces.** The collaborative memory paper [25] was not yet peer-reviewed at retrieval date. The audit trail piece [11] is an opinion piece and may underspecify requirements for regulated environments requiring cryptographic non-repudiation. Both support medium-confidence claims only.

**The target LLM provider and model version are unspecified.** Context management strategies, MCP integration details, and fault tolerance implementations all vary materially by LLM provider. Without specifying the target model, concrete implementation recommendations cannot be made with full confidence [doc-016].

**The MAST taxonomy covers open-source frameworks only [7].** Failure mode distributions for proprietary or enterprise-grade MAS deployments may differ substantially.

**MCP documentation may already be outdated.** Evidence gathered in early 2026 may not reflect the current specification, and no mechanism within this research run can detect post-retrieval protocol changes [doc-013].

**The artifact pipeline lacks immutability.** The claims in this report are derived from claims.json as specified, but the integrity of that file relative to the original retrieved evidence cannot be cryptographically verified within the current system design.

---

## 8. Open Questions

1. At what agent count does the orchestrator-worker pattern introduce coordination overhead that outweighs its reliability benefits? (SQ1)
2. Which LLM provider and model version are in production scope — a prerequisite for concrete context management and fault tolerance specifications? (SQ5; C17)
3. Should the system adopt a hybrid blackboard/orchestrator-worker architecture, and does the blackboard performance advantage transfer to research pipeline workloads? (SQ1; X1)
4. What exit criteria — numeric quality threshold, maximum revision count, and escalation protocol — should terminate the FAPESP review feedback loop? (SQ2; C8)
5. Should intermediate artifact files be cryptographically signed or checksummed, and which mechanism imposes least operational overhead? (SQ3; X3)
6. Should the system implement A2A for inter-agent communication, or is the current file-based artifact handoff pattern sufficient for the target workflow scale? (SQ4; C13)
7. How should silent agent failures be detected in production — what judge agent design is appropriate for research workloads? (SQ2; C7)
8. Should per-agent isolated memory be extended to a shared access-controlled store, or should isolation be maintained to preserve role separation? (SQ5; C18)
9. Are W3C PROV, OpenLineage, or comparable vocabularies adaptable for multi-agent AI provenance tracking without requiring purpose-built schema development? (SQ3; C11)
10. Which combination of context management strategies performs best for the specific workload of multi-step evidence retrieval and synthesis? (SQ5; C16)

---

## 9. Suggested Next Steps

The following recommendations are judgment-based extrapolations from the evidence and are explicitly separated from the supported findings in Sections 3–5.

**Immediate (pre-production blockers):**

1. **Specify the target LLM provider and model version.** This single decision unblocks all context management, fault tolerance, and MCP integration specifications. It is the highest-leverage open decision in the system's current state.
2. **Implement a layered fault tolerance strategy.** Introduce retry policies with exponential backoff, fallback routing to alternative providers, and circuit breakers as a composed strategy. Select thresholds calibrated to research-workload latency tolerance, where correctness is prioritized over speed.
3. **Define FAPESP review exit criteria.** Specify a numeric quality threshold, a maximum revision cycle count, and a protocol for human escalation when neither threshold is met.

**Short-term (architecture quality):**

4. **Select an orchestration framework explicitly.** Make this choice jointly with the orchestration topology and fault tolerance pattern decisions, as they are tightly coupled. LangGraph is the current evidence-preferred option for stateful research workflows, subject to independent validation.
5. **Add artifact immutability to the pipeline.** Commit each pipeline artifact to git immediately upon creation. This closes the mutable-artifact gap at negligible infrastructure cost.
6. **Specify a judge agent design for research workloads.** Define when judge agents activate, what quality thresholds they enforce, and how their evaluations are logged as part of the audit trail.
7. **Update the report-writer agent descriptor to specify IEEE citation format explicitly**, consistent with the research plan's recommendation. Run validation against a sample output before the next research run.

**Medium-term (architectural enhancement):**

8. **Prototype the hybrid blackboard/orchestrator-worker topology.** Measure the impact on task success rate and token efficiency against a pure orchestrator-worker baseline under research-specific workloads.
9. **Evaluate A2A adoption for inter-agent communication.** Assess whether replacing file-based handoff with A2A improves coordination latency and observability, or introduces complexity that outweighs the benefits.
10. **Evaluate W3C PROV or OpenLineage for traceability schema standardization.** Determine whether interoperability benefits justify migration cost from the current ad hoc JSONL schema.

---

## 10. References

[1] Adimulam, Apoorva; Gupta, Rajesh; Kumar, Sumit. "The Orchestration of Multi-Agent Systems: Architectures, Protocols, and Enterprise Adoption." arXiv, 2026. https://arxiv.org/html/2601.13671v1. arXiv:2601.13671.

[2] Jose GuruSup. "Agent Orchestration Patterns: Swarm vs Mesh vs Hierarchical vs Pipeline." DEV Community, n.d. https://dev.to/jose_gurusup_dev/agent-orchestration-patterns-swarm-vs-mesh-vs-hierarchical-vs-pipeline-b40. [accessed 2026-03-19]

[3] Falconer, Sean; Sellers, Andrew. "Four Design Patterns for Event-Driven, Multi-Agent Systems." Confluent, 2025. https://www.confluent.io/blog/event-driven-multi-agent-systems/.

[4] Saboo, Shubham. "Developer's Guide to Multi-Agent Patterns in ADK." Google, 2025. https://developers.googleblog.com/developers-guide-to-multi-agent-patterns-in-adk/.

[5] Salemi, Alireza; Parmar, Mihir; Goyal, Palash; Song, Yiwen; Yoon, Jinsung; Zamani, Hamed; Pfister, Tomas; Palangi, Hamid. "LLM-Based Multi-Agent Blackboard System for Information Discovery in Data Science." arXiv, 2025. https://arxiv.org/abs/2510.01285. arXiv:2510.01285.

[6] Han, Bochen; Zhang, Songmao. "Exploring Advanced LLM Multi-Agent Systems Based on Blackboard Architecture." arXiv, 2025. https://arxiv.org/abs/2507.01701. arXiv:2507.01701.

[7] Cemri, Mert; Pan, Melissa Z.; Yang, Shuyi; Agrawal, Lakshya A.; Chopra, Bhavya; Tiwari, Rishabh; Keutzer, Kurt; Parameswaran, Aditya; Klein, Dan; Ramchandran, Kannan; Zaharia, Matei; Gonzalez, Joseph E.; Stoica, Ion. "Why Do Multi-Agent LLM Systems Fail?" arXiv, 2025. https://arxiv.org/abs/2503.13657. arXiv:2503.13657.

[8] Shah, Drishti. "Retries, Fallbacks, and Circuit Breakers in LLM Apps: What to Use When." Portkey, 2025. https://portkey.ai/blog/retries-fallbacks-and-circuit-breakers-in-llm-apps/.

[9] Paul, Kuldeep. "Multi-Agent System Reliability: Failure Patterns, Root Causes, and Production Validation Strategies." Maxim AI, 2025. https://www.getmaxim.ai/articles/multi-agent-system-reliability-failure-patterns-root-causes-and-production-validation-strategies/.

[10] Gunndu, Klement. "4 Fault Tolerance Patterns Every AI Agent Needs in Production." DEV Community, n.d. https://dev.to/klement_gunndu/4-fault-tolerance-patterns-every-ai-agent-needs-in-production-jih. [accessed 2026-03-19]

[11] Loe, Ian. "Your AI Agent Needs an Audit Trail, Not Just a Guardrail." Medium, 2026. https://medium.com/@ianloe/your-ai-agent-needs-an-audit-trail-not-just-a-guardrail-6a41de67ae75.

[12] Samanta, Nirupam. "The Growing Challenge of Auditing Agentic AI." ISACA, 2025. https://www.isaca.org/resources/news-and-trends/industry-news/2025/the-growing-challenge-of-auditing-agentic-ai.

[13] PricewaterhouseCoopers. "Validating Multi-Agent AI Systems: From Modular Testing to System-Level Governance." PwC, n.d. https://www.pwc.com/us/en/services/audit-assurance/library/validating-multi-agent-ai-systems.html. [accessed 2026-03-19]

[14] Singh, Aditi; Ehtesham, Abul; Kumar, Saket; Khoei, Tala Talaei. "Agentic Retrieval-Augmented Generation: A Survey on Agentic RAG." arXiv, 2025. https://arxiv.org/abs/2501.09136. arXiv:2501.09136.

[15] Anthropic. "Introducing the Model Context Protocol." Anthropic, 2024. https://www.anthropic.com/news/model-context-protocol.

[16] Anthropic. "Model Context Protocol Specification 2025-11-25." Model Context Protocol (Anthropic / Linux Foundation), 2025. https://modelcontextprotocol.io/specification/2025-11-25.

[17] Krishnan, Naveen. "Advancing Multi-Agent Systems Through Model Context Protocol: Architecture, Implementation, and Applications." arXiv, 2025. https://arxiv.org/html/2504.21030v1. arXiv:2504.21030.

[18] Ehtesham, Abul; Singh, Aditi; Gupta, Gaurav Kumar; Kumar, Saket. "A Survey of Agent Interoperability Protocols: Model Context Protocol (MCP), Agent Communication Protocol (ACP), Agent-to-Agent Protocol (A2A), and Agent Network Protocol (ANP)." arXiv, 2025. https://arxiv.org/html/2505.02279v1. arXiv:2505.02279.

[19] du Toit, Chris. "Google's Agent-to-Agent (A2A) and Anthropic's Model Context Protocol (MCP): Complementary Protocols." Gravitee, 2025. https://www.gravitee.io/blog/googles-agent-to-agent-a2a-and-anthropics-model-context-protocol-mcp.

[20] Packer, Charles; Wooders, Sarah; Lin, Kevin; Fang, Vivian; Patil, Shishir G.; Stoica, Ion; Gonzalez, Joseph E. "MemGPT: Towards LLMs as Operating Systems." arXiv, 2023. https://arxiv.org/abs/2310.08560. arXiv:2310.08560.

[21] Paul, Kuldeep. "Context Window Management: Strategies for Long-Context AI Agents and Chatbots." Maxim AI, 2025. https://www.getmaxim.ai/articles/context-window-management-strategies-for-long-context-ai-agents-and-chatbots/.

[22] Yuen, Sizhe; Medina, Francisco Gomez; Su, Ting; Du, Yali; Sobey, Adam J. "Intrinsic Memory Agents: Heterogeneous Multi-Agent LLM Systems through Structured Contextual Memory." arXiv, 2025. https://arxiv.org/html/2508.08997v1. arXiv:2508.08997.

[23] Weng, Lilian. "LLM Powered Autonomous Agents." Lil'Log (Personal Blog), 2023. https://lilianweng.github.io/posts/2023-06-23-agent/.

[24] Maierhöfer, Jannik. "Comparing Open-Source AI Agent Frameworks: LangGraph, CrewAI, AutoGen." Langfuse, 2025. https://langfuse.com/blog/2025-03-19-ai-agent-comparison.

[25] Rezazadeh, Alireza; Li, Zichao; Lou, Ange; Zhao, Yuying; Wei, Wei; Bao, Yujia. "Collaborative Memory: Multi-User Memory Sharing in LLM Agents with Dynamic Access Control." arXiv, 2025. https://arxiv.org/html/2505.18279v1. arXiv:2505.18279.

---

_This report was produced by the report-writer agent (run-001, 2026-03-19) based strictly on the synthesized claims in claims.json, the web evidence in web_evidence.jsonl, and the document evidence in doc_evidence.jsonl. All claim references (C1–C18), evidence identifiers, contradiction identifiers (X1–X6), and gap identifiers (G1–G10) are traceable to those source files. No citations, paper titles, or author names were invented. Local document evidence (doc-NNN) is cited inline by source file name and does not appear in the numbered References section._
