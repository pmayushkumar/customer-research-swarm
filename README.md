# Customer Research Swarm

A multi-agent system that turns weeks of customer research into a critiqued, OKR-aligned strategy deck in 15 minutes — without sending a single proprietary transcript to a public LLM.

Built for a mid-market tech firm whose Head of Customer Research was spending three weeks per cycle reading Zendesk tickets, App Store reviews, and interview transcripts, then hand-assembling a deck that arrived stale and shaped by their own biases.

> **Result.** Synthesis time dropped from three weeks to 15 minutes. Strategic rigor went up, not down, because the AI stress-tests its own logic before a human sees the output.

---

## The problem

Customer feedback was disconnected from strategic reality. A UI bug got the same weight as an enterprise account threatening to churn, because the UI bug came up more often. The team needed a system that could read the data, format it, critique it against the needs of different departments, and tie it back to current OKRs — all while keeping proprietary customer data on-prem.

## The framework: four levels of AI maturity

We mapped the design space before committing.

**Level 1 — Naive summarization.** Shove transcripts into a context window, ask for "key insights." Result: generic platitudes, lost-in-the-middle hallucinations, no citations. No executive trusts it.

**Level 2 — Structured formatting.** Few-shot prompts and schemas matching the company's deck template. Looks pretty, but insights stay surface-level.

**Level 3 — Multi-agent critique swarm.** A Synthesizer drafts insights. Persona agents (PM, Eng, Sales) critique the draft, each prompted with established behavioral and trust dynamics. The AI stress-tests its own logic before a human ever sees it.

**Level 4 — Enterprise orchestration & governance.** Rigid orchestration, mandatory citation tracing back to transcript timestamps, strict PII sanitization, and forced alignment to dynamic Q3 OKRs. **This is what we shipped.**

<img width="2160" height="2700" alt="image" src="https://github.com/user-attachments/assets/df900487-051e-48c3-9886-677b709b77a4" />


## The stack

**Orchestration: n8n.** Open-source, self-hostable, built for developers. Handles the asynchronous batch processing a dozen debating agents require. Visual drag-and-drop tools become debugging nightmares in production — n8n stays auditable.

**Models: Opus 4.7 + Sonnet 4.6.** Scalpel, not a default. Opus 4.7 runs the Orchestrator/Judge for final OKR alignment. Sonnet 4.6 powers the iterative persona critiques — 40% cheaper, exceptional product logic, makes the token burn sustainable.

**Application UI: Dyad.** Local-first natural-language app builder. The Head of Research got a clean desktop interface without us burning weeks on front-end work or accepting cloud vendor lock-in.

**Integrations: Composio.** Out-of-the-box connections from n8n to Zendesk (input) and Slack (output). No wasted engineering cycles wiring APIs.

**Privacy & RAG: Microsoft Presidio + Chroma.** Presidio scrubs PII locally before any transcript enters the vector store. Chroma runs on-prem so proprietary customer sentiment never leaves company servers.

**LLMOps safety net:**
- **Observability (LangSmith / Helicone)** — per-agent token tracking, auto-terminate if a swarm gets stuck and burns past a $2 threshold.
- **Resiliency (LiteLLM + LangGraph checkpointers)** — automatic failover from Opus 4.7 to GPT-4o if Anthropic's API drops; failed 15-minute runs resume from the failed node, not from scratch.
- **Automated evals (Braintrust)** — LLM-as-Judge scoring against a golden dataset of 50 past reports before any prompt change ships.

**Memory engine: write-back loop + Mem0/Zep.** Final human-approved insights get vectorized back into Chroma, so next month's baseline includes last month's conclusions. When the Head of Research consistently rejects a critique pattern, Mem0 extracts the rule and injects it permanently into that agent's system prompt. The swarm learns from human pushback.

## Tradeoffs we negotiated

**Controllability vs. autonomy.** Three personas left to "debate" spiral into infinite loops about button colors. We capped critique at two rounds before the Orchestrator forces convergence.

**Cost & latency vs. performance.** A critiqued 15-slide deck takes 12 minutes. We trained the team this is an async batch job, not a chatbot. Trade the loading spinner for boardroom rigor.

**Agency vs. control.** The system cannot update Jira or email the CEO. It drafts, it critiques, then it Slack-pings the Head of Research: *"The Q3 Enterprise Churn Deck is ready for your review."*

**Precision vs. recall in RAG.** Tuned for high recall. We'd rather pull in a bit of noise than miss the one interview where an enterprise account threatened to leave. The critique layer filters noise later.

## What this is not

It isn't a machine that replaces the Head of Research. It's a machine that gives them leverage. The AI synthesizes the *what* and debates the *how*. The human stays irreplaceable on the *why* — boardroom politics, relationship capital, the final roadmap call.

---

*Built as a consulting engagement. The client remains anonymized.*
