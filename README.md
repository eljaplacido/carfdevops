# CARF DevOps Edition — Decision Intelligence for Software Development & Operations

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![License: BSL 1.1](https://img.shields.io/badge/License-BSL%201.1-red.svg)](LICENSE)
[![Tests](https://img.shields.io/badge/tests-1170%2B%20passing-brightgreen.svg)](#test-results)
[![Grade](https://img.shields.io/badge/benchmarks-A%2B%20(43%2F43)-gold.svg)](#benchmark-results)

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

## Use Cases — When, How, and Why to Apply

### Decision Matrix

| Situation | Cynefin Domain | Engine | What You Get | When to Use |
|-----------|---------------|--------|-------------|-------------|
| **Incident root cause** — latency spike, error rate jump, conversion drop | Complicated | Causal Inference (DoWhy/EconML) | Causal DAG + ATE + refutation tests | When you have deployment data and want to know *which change caused the problem* |
| **Deployment risk** — evaluating a risky rollout | Complex | Bayesian Active Inference (PyMC) | Calibrated posterior + uncertainty decomposition | When past data is sparse and you need to quantify confidence in predictions |
| **Production emergency** — site down, data corruption | Chaotic | Circuit Breaker + Escalation | Immediate escalation to on-call with situation context | When every second counts and automated actions are too risky |
| **Routine status checks** — "what's the deploy pipeline state?" | Clear | Deterministic Lookup | Fast, rule-based answer from known state | When the answer is a known fact with no ambiguity |
| **Ambiguous alerts** — "something feels off" | Disorder | Human Escalation | Structured context to help a human triage | When the problem type itself is unclear |

### Practical Workflow Examples

**1. Post-incident RCA (Root Cause Analysis)**
```bash
# Feed incident timeline to CARF
curl -X POST http://localhost:8000/query \
  -H "Content-Type: application/json" \
  -d '{
    "query": "Did the payment-sdk v2.3 deployment cause the p99 latency increase?",
    "causal_estimation": {
      "treatment": "deployment_v2_3",
      "outcome": "p99_latency_ms",
      "covariates": ["traffic_spike", "cache_hit_ratio"]
    }
  }'
```
Returns: Causal effect estimate, refutation test results, counterfactual scenarios.

**2. Pre-deployment What-If**
```bash
# Before promoting to production, simulate the impact
curl -X POST http://localhost:8000/simulations/compare \
  -H "Content-Type: application/json" \
  -d '{
    "scenario_id": "canary_rollout",
    "variables": {
      "canary_pct": [5, 10, 25],
      "rollback_threshold_ms": [500, 1000]
    }
  }'
```
Returns: Multi-scenario comparison with sensitivity analysis.

**3. Policy-Enforced CI/CD Gate**
```yaml
# .github/workflows/deploy.yml
- name: CARF Pre-Deploy Gate
  run: |
    result=$(curl -s -X POST http://carf-api:8000/query \
      -d '{"query": "Is deployment safe?", "context": {"commit_sha": "${{ github.sha }}"}}')
    verdict=$(echo "$result" | jq -r '.guardian_verdict')
    if [ "$verdict" != "APPROVED" ]; then
      echo "Deploy blocked: $verdict"
      exit 1
    fi
```

**4. Continuous Monitoring (Phase 18)**
```bash
# Check if routing patterns are drifting (model staleness)
curl http://localhost:8000/monitoring/drift

# Audit agent memory for bias
curl http://localhost:8000/monitoring/bias-audit

# Check if retraining has plateaued
curl http://localhost:8000/monitoring/convergence

# Get unified health
curl http://localhost:8000/monitoring/status
```

---

## Integration — Embed CARF into Your Stack

### Option 1: REST API (Any Language)

The FastAPI backend exposes 100+ endpoints. Call directly from any HTTP client:

```python
import requests

# Classify a DevOps query
resp = requests.post("http://localhost:8000/query", json={
    "query": "Why did the payment service start returning 503s?",
    "context": {"domain_hint": "complicated"}
})
print(resp.json()["cynefin_domain"])  # complicated
```

### Option 2: Python Library (Notebooks & Pipelines)

```python
from src.api.library import classify_query, run_causal, run_pipeline

# Reuse CARF inside Jupyter or data pipelines
result = await classify_query("Should we scale up the cache layer?")
print(result["domain"], result["confidence"])

# Run full causal pipeline
pipeline = await run_pipeline("Does canary rollout reduce incident count?")
```

### Option 3: MCP Server (AI Agent Tooling)

CARF exposes 18 cognitive tools via Model Context Protocol. Connect any MCP-compatible AI agent:

```json
// Agent calls CARF tool
{
  "method": "tools/call",
  "params": {
    "name": "classify_domain",
    "arguments": { "query": "P99 latency up 40% after deploy v4.2" }
  }
}
```

### Option 4: CI/CD Gate (GitHub Actions, GitLab CI, Jenkins)

```yaml
# Block deploys that violate policy
- name: CARF Guardian Gate
  uses: carfdevops/carf-gate@v1
  with:
    query: "Is deployment to production safe?"
    policy_profile: production
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

---

## API Endpoint Map

| Category | Key Endpoints | Purpose |
|----------|-------------|---------|
| **Query** | `POST /query`, `POST /query/transparent` | Main analytical pipeline |
| **Causal** | `POST /simulations/run`, `/simulations/compare` | What-if scenarios, counterfactuals |
| **Bayesian** | Inference via `/query` pipeline | Uncertainty quantification |
| **Guardian** | `GET /guardian/status`, `/guardian/policies` | Policy enforcement status |
| **Governance** | `/governance/*` (18 endpoints) | MAP-PRICE-RESOLVE framework |
| **Monitoring** ⭐ | `/monitoring/drift`, `/monitoring/bias-audit`, `/monitoring/convergence`, `/monitoring/status`, `/monitoring/posterior-cache` | Phase 18 operational intelligence |
| **Data** | `POST /data/load/csv`, `/data/detect-schema` | Multi-format data ingestion |
| **Memory** | `/experience/similar`, `/experience/patterns` | Semantic memory retrieval |
| **History** | `POST /history`, `GET /history` | Per-user analysis history |
| **Health** | `GET /health` | System health check |

---

## Metrics & KPIs — What to Track

### System Health

| Metric | Endpoint | What it means | Healthy range |
|--------|----------|---------------|---------------|
| **Router drift** | `/monitoring/drift` | KL-divergence between current and baseline routing | < 0.15 |
| **Memory bias** | `/monitoring/bias-audit` | Chi-squared p-value for domain fairness | p > 0.05 |
| **Retraining convergence** | `/monitoring/convergence` | Accuracy delta per epoch | > 0.5% improvement or plateau detected |
| **Posterior cache hit rate** | `/monitoring/posterior-cache` | Fraction of Bayesian queries served from cache | > 60% in production |

### Decision Quality

| Metric | Source | What it means | Target |
|--------|--------|---------------|--------|
| **Router F1 score** | `H0` benchmark | Classification accuracy across 5 Cynefin domains | ≥ 0.895 |
| **Causal ATE error** | `H1` benchmark | MSE ratio vs raw LLM on treatment effects | < 0.001 (1,138x better) |
| **Guardian violation detection** | `H3` benchmark | Policy violations caught | 100% |
| **Hallucination rate** | `H7` benchmark | Grounded query hallucination rate | 0% |
| **ChimeraOracle accuracy** | `H8` benchmark | Fast-path prediction accuracy | ≤ 3.4% accuracy loss |

### Operational

| Metric | Source | What it means | Target |
|--------|--------|---------------|--------|
| **P95 latency** | `H37` benchmark | Response time at 25 concurrent users | ≤ 42ms |
| **Memory growth** | `H39` benchmark | RSS growth after 1,000 queries | ≤ -1.5% |
| **Chaos containment** | `H38` benchmark | Fault cascade containment rate | ≥ 80% |
| **LLM cost per query** | Governance cost panel | Token spend per analytical query | Tracked via `PRICE` |

---

## Monitoring (Phase 18)

CARF DevOps now includes a full operational intelligence layer:

- **Drift Detection**: Tracks routing distribution shifts using KL-divergence. If your system starts classifying queries differently over time, you get an alert before decisions degrade.
- **Bias Auditing**: Chi-squared fairness tests across agent memory. Detects if certain domains get systematically worse-quality analyses.
- **Plateau Detection**: Monitors router retraining accuracy. Stops retraining when you're overfitting — saves compute and prevents accuracy regression.
- **Scalable Inference**: Three Bayesian modes — `full` (MCMC), `approximate` (analytical conjugates, <1µs), `cached` (hash-keyed posterior reuse). Controlled by `CARF_INFERENCE_MODE`.

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

## UIX Flow: Incident Investigation

Here's how a DevOps engineer uses CARF to investigate a real production issue, step by step.

### Scenario: "Checkout conversion dropped 12% after last week's deployment"

**Step 1 — Query the Cockpit**

The engineer types: *"Why did checkout conversion drop 12% after the March 8 deployment?"*

The Cynefin Router classifies this as **Complicated** (88% confidence). The dashboard shows:
> **Domain: Complicated** — Causal analysis required.
> **Method: Causal Inference Engine** (DoWhy/EconML)

**Step 2 — Causal Analysis**

The Causal DAG panel renders the causal graph:
```
[March 8 Deploy] → [New Payment SDK] → [Checkout Latency +400ms]
       │                                        │
       └→ [Feature Flag: New UI] → [Conversion Drop -12%]
                                        ↑
              [Mobile Traffic Spike] ───┘  (confounder)
```

Response:
> **Why this?** New payment SDK introduced 400ms latency. Each 100ms reduces conversion ~1.5%. Explains 8 of 12 percentage points.
> **How confident?** 76% — refutation tests passed 2/3.
> **Based on what?** Deployment manifest, latency metrics (p95: 230ms→630ms), conversion funnel (N=142K).

**Step 3 — Policy Check (Guardian Layer)**
```
budget_limits.csl    ✅ PASS
action_gates.csl     ✅ PASS  
data_access.csl      ✅ PASS
```

**Step 4 — What-If Simulation**
```
Roll back Payment SDK only:
  Predicted recovery:   +8.2%
  Net conversion:       -3.8%
  Confidence:           72%

vs Full rollback:
  Predicted recovery:   +11.4%
  Risk:                 lose new UI features
```

**Step 5 — Escalation (if needed)**

If rollback affects >10K users, Guardian triggers Slack/Email escalation with full causal context.

**Step 6 — Learning Loop**

After rollback, conversion recovers to -3.5% (predicted -3.8%). System records calibration accuracy, stores incident in Experience Buffer. Total cost: 3 LLM calls ($0.04), 4.2s.

---

## CARF vs Traditional DevOps — Simulation Results

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

---

## Quick Start

```bash
# Clone
git clone https://github.com/eljaplacido/carfdevops.git
cd carfdevops

# Setup
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"

# Configure
cp .env.example .env
# Set DEEPSEEK_API_KEY=sk-...

# Run backend
python -m src.main

# Run frontend (new terminal)
cd carf-cockpit && npm install && npm run dev
```

### Test Mode (No API Keys)

```bash
export CARF_TEST_MODE=1
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
│   ├── services/          # 25+ services: Causal, Bayesian, Guardian, Governance,
│   │                      # World Model, Counterfactual, Neurosymbolic, Evaluation,
│   │                      # Drift Detection, Bias Auditing, ChimeraOracle
│   ├── workflows/         # LangGraph orchestration, Guardian, Router, Chimera fast-path
│   ├── api/               # FastAPI routers (18 modules, 100+ endpoints)
│   └── main.py            # Entry point
├── carf-cockpit/          # React dashboard (57 components, monitoring panel)
├── tests/
│   ├── unit/              # 58+ test files
│   ├── integration/       # API flow tests
│   ├── simulation/        # CARF vs Traditional DevOps (17 scenarios)
│   ├── deepeval/          # LLM quality evaluation (DeepSeek/Gemini)
│   └── e2e/               # End-to-end tests
├── config/                # Policies (YAML, CSL-Core, OPA), agents, prompts
├── benchmarks/            # 43 hypothesis tests, reports, baselines
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
- **Operational monitoring** — drift detection, bias auditing, convergence tracking
- **Human-in-the-loop** — escalation to Slack/Email/Teams for high-impact decisions
- **Cost tracking** — per-query LLM spend with ROI analysis

---

## Upstream

This repo is a DevOps-focused branch of [Project CARF (CYNEPIC)](https://github.com/eljaplacido/projectcarfcynepic). The core causal-Bayesian reasoning engines, Guardian policy layer, and React cockpit are shared. This edition adds DevOps-specific simulation, evaluation configuration, operational monitoring (Phase 18), and documentation.

## License

Business Source License 1.1 (BSL) — see [LICENSE](LICENSE).
