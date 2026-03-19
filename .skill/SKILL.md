---
name: research-assistant
description: "Use this skill to run a complete multi-agent research workflow producing a publication-ready paper or structured research artifact. Trigger when the user says: 'run research', 'write a paper', 'rewrite the paper', '/research-assistant', 'generate a publication', 'run the research pipeline', or describes a structured research task with evidence collection, synthesis, and academic output. Coordinates planning → evidence → synthesis → writing → FAPESP-level review in strict order."
---

# Research Assistant Orchestrator

You are coordinating a multi-agent research workflow to produce a structured, evidence-based research artifact suitable for academic publication.

## Base Project Path

`C:\Users\Usuario\Documents\Flowity AI\multi-agent-research-orchestration`

All runs and output artifacts are saved relative to this base path, inside the relevant project subfolder.

## When Invoked

1. Identify the **project subfolder** (e.g., `executive-signal-synthesis-prototype`, `research-assistant`) from the user's message or context.
2. Confirm the **research goal** and **canonical framing** before proceeding.
3. Generate a **run ID** in the format `run-YYYYMMDD-HHMMSS` and create `runs/<run-id>/` inside the project folder.
4. Execute all agents **in strict order** — do not skip steps or jump to writing early.

## Agent Pipeline (in order)

### 1. research-architect
- Define the research question, subquestions, scope, and rewrite/writing plan.
- Output → `runs/<run-id>/question.md`
- **Do not proceed until this is complete.**

### 2. document-analyst
- Read all source files in the project (README, framework docs, source code, existing outputs).
- Extract repo-grounded evidence and implementation support.
- Output → `runs/<run-id>/doc_evidence.jsonl`

### 3. web-researcher
- Gather the minimum necessary external literature for: decision support systems, explainable AI, organizational signal analysis, information synthesis.
- Focus on traceable, citable sources only.
- Output → `runs/<run-id>/web_evidence.jsonl`

### 4. synthesizer
- Convert all evidence (doc + web) into structured claims.
- Assign confidence levels and note limitations per claim.
- Distinguish conceptual contributions from prototype-level validation.
- Output → `runs/<run-id>/claims.json`

### 5. report-writer
- Write the full paper in markdown using the validated claims.
- Follow the required structure exactly (see below).
- Output → `runs/<run-id>/report.md`
- **Also overwrite the canonical paper file** in the project's `paper/` directory.

### 6. fapesp-reviewer
- Critique the draft against FAPESP PIPE standards: rigor, novelty, limitations, references.
- Output → `runs/<run-id>/fapesp_review.md`

### 7. Revision pass
- Apply the critique to produce a final revised version.
- Output → `runs/<run-id>/improvement_plan.md`
- Overwrite the canonical paper file with the revised version.

## Required Paper Structure

Every paper produced by this skill must follow this structure:

1. Problem
2. Why Current Systems Fail
3. Definition of Signals
4. Definition of Synthesis
5. The Framework
6. Implementation Mapping
7. Example Flow
8. Implications
9. Limitations
10. Future Work
11. References

## Canonical Framing Rules

These apply to all papers produced by this workflow:

- Write in **research language**: structured methodology, evidence-based synthesis, systematic analysis.
- **Never** mention Claude, internal agent tooling, multi-agent orchestration, or automation workflows in the output paper.
- **Never** write as a product pitch or startup pitch.
- Flowity AI may appear only as downstream application context, not as the paper's subject.
- Present any framework as a **methodological research contribution**, not a commercial system.
- Keep **prototype limitations explicit** — clearly distinguish conceptual contribution from prototype validation.
- Produce a **references section with traceable citations** (DOI or URL where possible).

## Execution Rules

- Always collect evidence **before** writing. Do not draft sections during planning or evidence phases.
- Ensure each phase saves its artifact before the next phase begins.
- If any required source file is missing, stop and report which file is absent — do not fabricate content.
- Maintain academic tone throughout all outputs.
- Do not expose internal tooling, agent names, or orchestration details in any published artifact.

## Artifacts Saved Per Run

```
runs/<run-id>/
  question.md          ← research plan from architect
  doc_evidence.jsonl   ← repo-grounded evidence
  web_evidence.jsonl   ← external literature
  claims.json          ← synthesized claims with confidence
  report.md            ← full paper draft
  fapesp_review.md     ← academic critique
  improvement_plan.md  ← revision plan and final version notes
```

## Output Goal

Produce a research artifact that is:
- Academically credible and structured
- Evidence-based with traceable citations
- Suitable for Zenodo publication
- Aligned with FAPESP-level rigor expectations
- Consistent with the repo's README and framework documentation
