# CARF DevOps Edition — Decision Intelligence for Software Development & Operations

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![License: BSL 1.1](https://img.shields.io/badge/License-BSL%201.1-red.svg)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-1138%20passing-brightgreen.svg)](#test-results)
[![Grade](https://img.shields.io/badge/benchmarks-A%2B%20(39%2F39)-gold.svg)](#benchmark-results)

> **Decision intelligence for DevOps teams** — move from dashboards and gut-feel decisions to causal reasoning, formal policy enforcement, and auditable AI-assisted operations.

This is the **DevOps branch** of [Project CARF (CYNEPIC Architecture)](https://github.com/eljaplacido/projectcarfcynepic), focused on applying complexity-aware causal-Bayesian reasoning to software development and operations workflows.

---

## The Problem

DevOps teams face a daily decision gap:

| What tools give you | What you actually need |
|---------------------|----------------------|
| Metrics dashboards (Grafana, Datadog) | **Why** the metric changed |
| Alert fatigue (PagerDuty) | **Which** alert is the root cause vs. symptom |
| Rollback by gut feel | **What would happen if** you rollback only part of the deploy |
| Tribal knowledge in Slack threads | **Institutional memory** that persists across teams |
| Manual change review checklists | **Formal policy enforcement** with self-repair |
| "It's probably correlated" | **Causal inference** that separates signal from noise |

CARF DevOps bridges this gap by applying **decision intelligence** — the same causal, Bayesian, and governance methods used in clinical trials and economic policy — to software engineering decisions.

---

## How It Works

```
DevOps Query → Cynefin Router → [Right reasoning method for the problem type]

  "Why did latency spike?"        → Complicated → Causal Inference (DoWhy)
  "Will this migration succeed?"  → Complex     → Bayesian Exploration (PyMC)
  "Production is down!"           → Chaotic     → Circuit Breaker + Escalation
  "What's the deploy status?"     → Clear       → Deterministic Lookup
  "Something feels off..."        → Disorder    → Human Escalation

All paths → Guardian Policy Check → Audit Trail → Experience Buffer (learns)
```

The system doesn't just classify — it **selects the mathematically appropriate reasoning engine** for each problem type, enforces organizational policies, and builds institutional memory.

---

## UIX Flow: Incident Investigation

Here's how a DevOps engineer uses CARF to investigate a real production issue, step by step.

### Scenario: "Checkout conversion dropped 12% after last week's deployment"

**Step 1 — Query the Cockpit**

The engineer types: *"Why did checkout conversion drop 12% after the March 8 deployment?"*

The Cynefin Router classifies this as **Complicated** (88% confidence) — it's a causal question with a knowable answer, not a simple lookup and not pure uncertainty. The dashboard explains why:

> **Domain: Complicated** — Causal analysis required. The query asks "why" something happened, implies treatment (deployment) and outcome (conversion drop), references specific data.
> **Method: Causal Inference Engine** (DoWhy/EconML)

*Instead of guessing which dashboard to check, the system routes to the right analytical engine automatically.*

**Step 2 — Causal Analysis**

The Causal DAG panel renders the causal graph:

```
[March 8 Deploy] → [New Payment SDK] → [Checkout Latency +400ms]
       │                                        │
       │                                        ↓
       └→ [Feature Flag: New UI] → [Conversion Drop -12%]
                                        ↑
              [Mobile Traffic Spike] ───┘  (confounder)
```

The response panel shows the structured output:

> **Why this?** The new payment SDK introduced 400ms latency. Research shows each 100ms of latency reduces conversion by ~1.5%. This explains 8 of the 12 percentage points.
>
> **How confident?** 76% — refutation tests passed 2/3.
>
> **Based on what?** Deployment manifest (2 PRs merged), latency metrics (p95: 230ms → 630ms), conversion funnel (N=142K sessions).

*No more finger-pointing between teams — you see which change caused how much of the drop.*

**Step 3 — Policy Check (Guardian Layer)**

Before presenting the recommendation, Guardian runs CSL-Core formal policies:

```
budget_limits.csl    ✅ PASS — recommendation cost within bounds
action_gates.csl     ✅ PASS — rollback is a reversible action
data_access.csl      ✅ PASS — user has access to deployment data
```

The Transparency Panel shows the full audit trail — every policy checked, every data source accessed, every reasoning step. EU AI Act compliant.

**Step 4 — What-If Simulation**

The engineer asks: *"What if we roll back only the payment SDK but keep the new UI?"*

The Counterfactual Engine (Pearl's 3-step) runs the scenario:

```
Roll back Payment SDK only:
  Predicted recovery:    +8.2%
  Residual (new UI):     -3.8%
  Net conversion:        -3.8%
  Confidence:            72%

vs Full rollback:
  Predicted recovery:    +11.4%
  Risk:                  lose new UI features
```

The Sensitivity Plot shows the recommendation holds even if latency impact is 30% weaker than estimated.

*Simulate interventions before deploying them.*

**Step 5 — Escalation (if needed)**

If the rollback affects >10K users, Guardian triggers human-in-the-loop escalation:

```
Action:    Rollback Payment SDK v2.3.1
Impact:    ~142K daily sessions
Policy:    action_gates.csl rule 7
Context:   Sent to #platform-oncall (Slack)
Awaiting:  SRE Lead approval
```

The approver sees the full causal analysis and counterfactual prediction — not just "approve rollback?"

**Step 6 — Learning Loop**

After the rollback, conversion recovers to -3.5% (predicted -3.8%). The system:
- Records the causal estimate was accurate (calibration score updates)
- Stores this incident in the Experience Buffer — next time someone asks about deployment-related conversion drops, CARF retrieves this case
- Logs total cost: 3 LLM calls ($0.04), 1 causal model ($0.00), 4.2s total

---

## CARF vs Traditional DevOps — Simulation Results

A 17-scenario comparative simulation demonstrates the robustness advantage:

```bash
pytest tests/simulation/test_carf_vs_traditional.py -v -s
```

| Metric | Value |
|--------|-------|
| CARF Wins | **14 / 16** scenarios |
| Traditional Wins | 0 |
| Ties | 2 |
| CARF Avg Score | **73.6%** |
| Traditional Avg Score | 19.1% |
| **CARF Advantage** | **+54.5%** |

### Per-Dimension Breakdown

| Dimension | What CARF Does | What Traditional Does |
|-----------|---------------|----------------------|
| **Epistemic Routing** | Classifies problem complexity, routes to appropriate engine | One-size-fits-all approach |
| **Causal Reasoning** | DoWhy causal inference with refutation tests | Correlation-based decisions |
| **Uncertainty Handling** | Bayesian quantification with epistemic/aleatoric decomposition | Point estimates, no confidence |
| **Policy Enforcement** | CSL-Core formal verification + self-repair | Manual checklists |
| **Knowledge Retention** | Semantic memory (sentence-transformers) | Tribal knowledge in Slack |
| **Audit & Explainability** | Full reasoning trace, EU AI Act compliant | Opaque logs |
| **Resilience & Recovery** | Graceful degradation, circuit breakers | Crash and retry |

---

## Test Results

```
Unit + Integration:  1,170+ passed, 9 skipped
Simulation:          17 passed (CARF vs Traditional)
Coverage:            72%
Benchmarks:          43/43 PASS (Grade A+)
Phase 18 Monitoring: H40-H43 PASS (drift, bias, plateau, fast-path)
DeepEval:            8 test files (require API keys)
```

---

## Quick Start

```bash
# Clone
git clone https://github.com/eljaplacido/carfdevops.git
cd carfdevops

# Setup
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -e ".[dev]"

# Configure
cp .env.example .env
# Edit .env with your API keys (DEEPSEEK_API_KEY minimum)

# Run backend
python -m src.main

# Run frontend (new terminal)
cd carf-cockpit && npm install && npm run dev
```

### Test Mode (No API Keys)

```bash
export CARF_TEST_MODE=1  # Windows: $env:CARF_TEST_MODE="1"
python -m src.main
```

### Docker Compose (Full Stack)

```bash
docker compose up --build
# API: http://localhost:8000 | Dashboard: http://localhost:5175
```

---

## LLM Quality Evaluation (DeepEval)

CARF evaluates LLM output quality using [DeepEval](https://github.com/confident-ai/deepeval) with **two evaluator backends**:

| Evaluator | Set in `.env` | API Key |
|-----------|---------------|---------|
| **DeepSeek** (default) | `DEEPEVAL_EVALUATOR=deepseek` | `DEEPSEEK_API_KEY` |
| **Google Gemini** | `DEEPEVAL_EVALUATOR=gemini` | `GEMINI_API_KEY` or `GOOGLE_API_KEY` |

The system auto-falls back to whichever key is available.

```bash
pip install -e ".[dev,evaluation]"
pytest tests/deepeval/ -v
```

**Metrics evaluated:** Relevancy, Hallucination Risk, Reasoning Depth, UIX Compliance (Why? How confident? Based on what?)

---

## Project Structure

```
carfdevops/
├── src/
│   ├── core/              # LLM config, state, deployment profiles, database
│   ├── services/          # 20+ services: Causal, Bayesian, Guardian, Governance,
│   │                      # World Model, Counterfactual, Neurosymbolic, Evaluation
│   ├── workflows/         # LangGraph orchestration, Guardian, Router
│   ├── api/               # FastAPI routers (17 modules, 100+ endpoints)
│   └── main.py            # Entry point
├── carf-cockpit/          # React dashboard (56 components, 5 hooks)
├── tests/
│   ├── unit/              # 55+ test files
│   ├── integration/       # API flow tests
│   ├── simulation/        # CARF vs Traditional DevOps (17 scenarios)
│   ├── deepeval/          # LLM quality evaluation (DeepSeek/Gemini)
│   └── e2e/               # End-to-end tests
├── config/                # Policies (YAML, CSL-Core, OPA), agents, prompts
├── benchmarks/            # 39 hypothesis tests, reports, baselines
├── demo/                  # 17 scenarios, 11 datasets, API payloads
├── docs/                  # 30+ architecture & operations docs
├── tla_specs/             # TLA+ formal specifications
└── docker-compose.yml     # Full stack (API, Dashboard, Neo4j, Kafka, OPA)
```

---

## Key Capabilities

### For DevOps Engineers
- **Incident root cause analysis** — causal inference, not just correlation
- **Deployment impact prediction** — counterfactual "what-if" before rollback
- **Policy-gated actions** — formal verification prevents unsafe changes
- **Institutional memory** — past incidents inform future decisions

### For Development Teams
- **Complexity-aware routing** — right tool for the right problem
- **Uncertainty quantification** — know your confidence level
- **Audit trail** — every decision is explainable and traceable
- **Multi-provider LLM** — DeepSeek, OpenAI, Anthropic, Gemini, Mistral, Ollama

### For Platform/SRE Leaders
- **Governance dashboard** — policy federation, cost intelligence, compliance
- **EU AI Act compliance** — built-in reporting and audit generation
- **Human-in-the-loop** — escalation to Slack/Email/Teams for high-impact decisions
- **Cost tracking** — per-query LLM spend with ROI analysis

---

## Upstream

This repo is a DevOps-focused branch of [Project CARF (CYNEPIC)](https://github.com/eljaplacido/projectcarfcynepic). The core causal-Bayesian reasoning engines, Guardian policy layer, and React cockpit are shared. This edition adds DevOps-specific simulation, evaluation configuration, and documentation.

## License

Business Source License 1.1 (BSL) — see [LICENSE](LICENSE).
