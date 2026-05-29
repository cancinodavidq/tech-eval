# AI Architect Technical Evaluation — Submission

**Candidate submission for MAS Global Consulting / Sprocket International Group**

This repository contains my responses to the three-part AI Architect technical evaluation: an architecture design (Part A, `q1/`), a coding challenge (Part B, `q2/`), and a presales case (Part C, `q3/`).

Before the structure, a short note on how to read this: the evaluation explicitly states it is assessing *thinking, prioritization, and technical judgment* — not a perfect solution. I took that at face value. The throughline across all three parts is the same engineering posture, and the sections below make both the structure and the reasoning explicit.

---

## Repository Structure

```
ai-architect-tech-eval/
├── README.md                            ← you are here
├── LICENSE
├── .gitignore
├── Legal_Department_RAG_Research.docx   ← background research that informed Part A
├── Part_C_Presales_Case.docx            ← top-level copy of the Part C deliverable
│
├── q1/   —  Part A · Architecture Design
│   ├── Part_A_Questions_and_Answers.docx ← Q1–Q4: written rationale
│   └── rag_vanilla_final.svg             ← Q1: end-to-end architecture diagram
│
├── q2/   —  Part B · RAG Evaluation Pipeline
│   ├── README.md                         ← run instructions, metrics, trade-offs
│   ├── rag_evaluation.ipynb              ← the evaluation pipeline
│   ├── build_notebook.py                 ← reproducible notebook builder
│   ├── synthetic_dataset.json            ← evaluation input samples
│   ├── evaluation_per_sample.csv         ← per-sample scores (output)
│   ├── evaluation_summary.json           ← aggregate report (output)
│   ├── requirements.txt
│   └── .env.example                      ← API key template
│
└── q3/   —  Part C · Presales Case
    └── Part_C_Presales_Case.docx
```

The folders are named by part order — `q1` → Part A, `q2` → Part B, `q3` → Part C — so the repo navigation and the evaluation rubric line up directly.

---

## The Unifying Thesis

The three parts look like different exercises — a system design, a Python pipeline, a sales conversation — but they are built on one idea:

**Earn the right to add complexity. Measure before you commit. Be honest about what is known versus assumed.**

- In **Part A** (`q1/`), this shows up as a deliberate vanilla-RAG baseline with a full evaluation layer from day one — complexity gets added in response to measured gaps, not in anticipation of them.
- In **Part B** (`q2/`), this is the literal instrument that makes Part A's philosophy executable: an evaluation pipeline is what turns "we should measure before we change things" from a slogan into a workflow.
- In **Part C** (`q3/`), the same posture is applied to a human problem: don't promise, scope a small proof of concept, and let the client's own technicians be the measurement.

Part A says *instrument it and let the data drive*. Part B builds the instrument. Part C sells the same discipline to a skeptical buyer. That coherence is intentional.

---

## Part A — Architecture Design  (`q1/`)

**Deliverables.** `rag_vanilla_final.svg` is the end-to-end architecture diagram answering Q1; `Part_A_Questions_and_Answers.docx` is the written rationale covering Q1–Q4.

**The reasoning.** The stated requirements (500 concurrent users, p95 < 3s, 200,000 docs updated weekly) were not taken as fixed inputs to engineer against blindly. I challenged them first: internal legal and compliance tools rarely sustain uniform concurrency, so sizing for peak theoretical load from day one is likely overprovisioning. That framing changes the entire design.

From there the logic is:

1. **Build the simplest system that meets every functional requirement** — hybrid retrieval (semantic 0.7 / BM25 0.3), metadata filtering, page-level citation, source grounding, online evaluation, Azure-native deployment.
2. **Instrument it properly** — RAGAS for retrieval quality and ALCE for citation-level verification, running continuously over real traffic. This is the engine of iteration, not optional decoration.
3. **Let observed performance decide what gets added next** — reranker, query router, embedding fine-tuning, context-aware chunking. Each is justified by a measured failure pattern, with the precision/latency trade-off made explicit.

The Q4 roadmap follows the same discipline: a robust upstream data pipeline (highest impact, least visible), retrieval refinement conditioned on observed failure scenarios, and structured adoption work — because digital transformation efforts fail at adoption more often than at architecture.

The `Legal_Department_RAG_Research.docx` at the repository root is the background research that fed this section — the source material I worked from when grounding claims like the documented gains from domain-tuned legal embedding models.

---

## Part B — RAG Evaluation Pipeline  (`q2/`)

**Deliverables.** `rag_evaluation.ipynb` is the pipeline; `build_notebook.py` regenerates it reproducibly; `synthetic_dataset.json` provides the input samples; `evaluation_per_sample.csv` and `evaluation_summary.json` are example outputs. See `q2/README.md` for run instructions and the full trade-off discussion.

**The reasoning.** The metric design is the point, not the code. I designed the pipeline around the failure modes that actually matter in a grounded-generation system: whether retrieved context relates to the question (context relevance), whether the answer is supported by what was retrieved (faithfulness), and how close the answer is to ground truth (correctness). At least one metric uses an LLM-as-a-Judge, because some of these judgments are not reducible to string overlap.

The design decisions — which metrics, how they are scored, the judge prompting strategy, the structure of the output report, and the production concerns (typing, documentation, graceful error handling) — were specified by me.

This is deliberately the same evaluation layer described in Part A, made concrete. The analytics flow is mine end to end.

---

## Part C — Presales Case  (`q3/`)

**Deliverable.** `Part_C_Presales_Case.docx` — the discovery & qualification call (Q5), including the follow-up that handles the client's disclosure of a prior failed AI engagement.

**The reasoning.** The bottleneck in this conversation is not technical capability — it is trust, and the buyer has already told me her trust was burned. So the strategy is built around that:

1. **Validate the skepticism instead of defending against it.** Her doubt is rational evidence, not an objection to overcome.
2. **Run real discovery before solutioning** — separate downtime from daily friction, anchor the economic stakes by order of magnitude, and diagnose whether the prior failure was technical or a scoping problem.
3. **Neutralize the privacy concern in one sentence** — confirm it is solvable with a standard private-infrastructure design, then defer the detail. Don't architect in minute three of a discovery call.
4. **Change the dynamic with a low-commitment scoped pilot** — 50–100 real documents, her technicians as the judges, no retainer, no long timeline. Every element is designed to remove the specific risks that burned her before.
5. **Define success in her terms before building anything** — if she can't articulate what "actually works" looks like, the pilot fails regardless of technical quality.

The conversation, its sequencing, and the reasoning behind each move are entirely my own simulation of how I would actually run that call.

---

## How AI Was Used in Producing This Submission

I am disclosing this directly, because for an AI Architect role the relevant skill is not pretending AI wasn't involved — it is using it well, with judgment, while owning every decision. That is itself part of what this evaluation should be testing.

**What was 100% mine — design and judgment:**

- The **entire architectural proposal** in Part A — the vanilla-RAG-first posture, the decision to challenge the stated requirements, every RAG design choice and its justification, the latency/scale trade-off analysis, the architecture diagram, and the 6-month roadmap. No architectural decision was made by an AI.
- The **analytics and evaluation flow** in Part B — the choice of metrics, the scoring logic, the LLM-as-a-Judge strategy, and the report structure.
- The **discovery-call strategy** in Part C — the conversation design, the sequencing, the objection handling, and the reasoning behind every move.

**Where I used AI, and how:**

- **As a thinking partner.** To pressure-test my reasoning, surface counterarguments, and check whether my justifications held up under scrutiny. The ideas were mine; the AI was a sparring partner, not an author.
- **As a data researcher.** To locate and verify supporting figures and current facts — the work captured in `Legal_Department_RAG_Research.docx` — rather than relying on memory. The synthesis and the architectural conclusions drawn from that research are mine.
- **As a coder.** To accelerate the implementation of the Part B notebook: boilerplate, type annotations, and error-handling scaffolding. The metric design and evaluation logic were specified by me first; the AI sped up execution against my spec.
- **As an editor.** To tighten prose, structure the documents, and improve clarity. The substance and the arguments are mine.

The short version: **AI helped me articulate and execute faster. It did not design the architecture, the analytics, or the call. Those are mine.**

---

## A Note on Scope

The evaluation allotted roughly three hours and asked for thinking over polish. Where I made simplifying assumptions — particularly around the financial firm's actual usage shape in Part A and the manufacturing client's pain profile in Part C — I stated them explicitly rather than hiding them, because surfacing assumptions is part of the judgment being assessed. I'm glad to walk through any of these decisions, and the trade-offs I'd revisit, in the technical interview.
