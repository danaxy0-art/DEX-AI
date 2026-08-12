# DEX — Enterprise AI Consulting & Decision Support

**DEX** is a multi-agent enterprise AI consultant built as a capstone project for the SDAIA Academy training programme. Instead of a single AI model giving a generic answer, DEX acts like a full consulting team: a company describes a project or problem, and a set of specialist agents analyze it from their own domain before a supervisor agent synthesizes a unified, risk-aware feasibility report.

## Team

- Dana Alsaidan
- Waad Alsaif
- Deema Alaowairdhi
- Reema Alsahli
- Majdoleen Alhamdan

## The Idea

A company submits an "Enterprise Case" — either as structured fields (company, project, problem, requirements, budget, expected users) or as a natural-language description. DEX extracts the relevant information and routes it to the specialists needed to answer it, rather than always invoking every agent.

**Example input:**

> "Our company wants to build an AI-powered customer service platform for 100,000 users. It should support Arabic and English, connect to our CRM, use internal documents for answers, and escalate complex cases to human employees. Our budget is 500,000 SAR. Analyze whether this project is technically and financially feasible."

**Example output:** an Enterprise AI Feasibility Report covering executive summary, technical feasibility, architecture, infrastructure, business integration, risk assessment, cost estimate, and a scored decision (Recommended / Recommended with Conditions / Not Recommended).

## Architecture

```
                    USER
                      │
                      ▼
              ┌───────────────┐
              │ Intake Agent  │
              └───────┬───────┘
                      │
                      ▼
              ┌───────────────┐
              │ Router Agent  │
              └───────┬───────┘
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
     Specialists   Specialists   Specialists
          │           │           │
          └───────────┼───────────┘
                      ▼
              ┌───────────────┐
              │  Supervisor   │
              └───────┬───────┘
                      │
                      ▼
               Risk / Quality
                      │
                      ▼
                Human Review
                      │
                      ▼
                Final Report
```

## Specialist Agents

| Agent | Real Role |
|---|---|
| **AI Agent** | Model choice, RAG needs, fine-tuning, hallucination handling, guardrails, evaluation |
| **Software Engineering Agent** | Architecture, APIs, testing, maintainability, scalability, SDLC |
| **Computer Science Agent** | Algorithms, retrieval, ranking, optimization, model evaluation |
| **IT Agent** | Cloud, networking, deployment, monitoring, compute, disaster recovery |
| **Information Systems Agent** | Business processes, CRM/ERP integration, data flow, organizational impact |
| **Actuarial / Risk Agent** | Financial and operational risk, expected losses, probability-based risk scenarios, cost-benefit |

A **Router Agent** decides which specialists a given question actually needs (e.g. an infrastructure question only goes to the IT agent, while a full feasibility question goes to all six). A **Supervisor Agent** dispatches tasks, collects results, detects conflicts, requests re-analysis when needed, and compiles the final report.

## Key Capabilities

- **Multi-agent routing** — questions are routed only to the relevant specialists (Track C: Router Across Sources)
- **RAG per specialist** — each agent retrieves from its own domain knowledge base (AI, software engineering, IT, information systems, actuarial) using `HuggingFaceEmbeddings`, not fake embeddings
- **Long-term memory** — the system remembers company, project, budget, users, and requirements across turns so follow-up questions don't need to repeat context
- **Human-in-the-loop** — before a final report is issued, it passes through risk validation and requires human approval (`interrupt()` / `resume()`)
- **Retry handling** — failed or incomplete agent/retrieval steps are detected and retried automatically
- **LangSmith tracing** — full traces of the consultation (intake → routing → specialist calls → RAG lookups → supervisor → risk validation → human approval → final report) for debugging and evidence of the above patterns actually occurring

## Interface

DEX includes a Gradio UI with the following tabs:

- **Consultation** — submit a new enterprise case
- **Assessment Results** — view the specialist analyses and final decision
- **Knowledge Base** — browse the domain sources each agent draws on
- **Trace & Evidence** — inspect the LangSmith trace for a given run
- **Follow-up Consultation** — ask follow-up questions using stored memory of the case

## Tech Stack

- LangGraph for multi-agent orchestration (routing, supervisor, HITL, retries)
- HuggingFace embeddings for RAG
- LangSmith for tracing and observability
- Gradio for the UI

## Why Six Agents?

Evaluating an enterprise AI system isn't a single-discipline problem. Each specialist agent analyzes a distinct dimension of feasibility, risk, and integration — technical (AI/CS/SE), operational (IT), organizational (IS), and financial (Actuarial) — so the final recommendation reflects the full picture rather than one narrow perspective.
