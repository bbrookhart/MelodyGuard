# MelodyGuard

[![Coverage](https://img.shields.io/badge/coverage-80%25-brightgreen)](https://github.com/your-org/melodyguard/actions)
[![Security](https://img.shields.io/badge/OWASP%20LLM-10%2F10-brightgreen?logo=owasp)](docs/threat-model/THREAT_MODEL.md)
[![EW Controls](https://img.shields.io/badge/EW%20Controls-12%2F12-blue)](docs/threat-model/THREAT_MODEL.md)
[![Red Team](https://img.shields.io/badge/Red%20Team-54%20scenarios-orange)](backend/tests/red_team/pyrit_scenarios.py)
[![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.115-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Next.js](https://img.shields.io/badge/Next.js-15-black?logo=next.js)](https://nextjs.org/)
[![LangGraph](https://img.shields.io/badge/LangGraph-0.2-7C3AED?logo=langchain)](https://langchain-ai.github.io/langgraph/)
[![Privacy](https://img.shields.io/badge/Privacy-(ε%3D1.0%2C%20δ%3D1e--5)--DP-5C6BC0)](backend/memory/episodic_memory.py)

> **AI-powered music discovery built on three convictions:**
> Music is a sequence, not a bag of songs. Your listening history belongs to you, not a recommendation model. And the tools that learn your taste are novel attack surfaces that almost nobody is defending yet.

---

## What This Is

MelodyGuard is a full-stack AI music recommendation platform that combines:

- **Acoustic intelligence** — key detection, energy arc analysis, and Camelot harmonic mixing, running inside an E2B sandbox
- **Differentially private episodic memory** — your taste profile is stored with formal privacy guarantees under (ε=1.0, δ=1e-5)-DP
- **Playlist coherence scoring** — sequences are optimized for harmonic flow, BPM transitions, and mood arc before they reach you
- **Five-node LangGraph agent pipeline** — context fusion → safety → memory → discovery → acoustic → coherence → output
- **12 Electronic Warfare security enhancements** — PatternGuard MTD, Canary Sentinel, Manifold Witness, Honey Track Layer, Temporal Memory Integrity, Output Semantic Firewall, Indirect Injection Sandbox, Prompt Archaeology, Adversarial Jitter, Zero-Trust Tool Auth, Acoustic Fingerprint Integrity, and a GAN-driven Red Team Harness

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                           MelodyGuard Architecture                           │
└──────────────────────────────────────────────────────────────────────────────┘

  FRONTEND (Next.js 15)
  ┌─────────────────────────────────────────────────────────────────┐
  │  Landing → Spotify OAuth (PKCE) → Dashboard                     │
  │  FrequencyVisualizer  CamelotWheel  EnergyArcTracker            │
  │  GenerationFlow  PlaylistPanel  PlayerBar (Web Playback SDK)    │
  └───────────────────────────┬─────────────────────────────────────┘
                              │ HTTPS + Clerk JWT
                              ▼
  SECURITY PERIMETER (Phase 3 — built before anything else)
  ┌─────────────────────────────────────────────────────────────────┐
  │  AdversarialJitter (EW-09)  │  ClerkJWT  │  RequestSize        │
  │  PatternGuard MTD (EW-01)   │  AuditLog  │  CanarySentinel     │
  │  Output Semantic Firewall (EW-06)         │  HoneyTrackLayer   │
  └───────────────────────────┬─────────────────────────────────────┘
                              │
                              ▼
  AGENT PIPELINE (LangGraph — 9 nodes)
  ┌─────────────────────────────────────────────────────────────────┐
  │  1. context_fusion      ← temporal + weather + behavioral       │
  │  2. safety_input        ← PatternGuard MTD, rate limit          │
  │  3. memory_recall       ← DP episodic memory (EW-05 HMAC)      │
  │  4. discovery           ← multi-vector RRF + EW-04 honey check │
  │  5. acoustic_analysis   ← Camelot + energy arc (EW-11 hash)    │
  │  6. lyric_analyst       ← LLM + PII scrub                      │
  │  7. coherence_scorer    ← Camelot graph TSP optimizer           │
  │  8. poisoning_detector  ← Manifold Witness dual-consensus       │
  │  9. safety_output       ← EW-06 firewall + canary check        │
  └───────────────────────────┬─────────────────────────────────────┘
                              │
              ┌───────────────┼──────────────────┐
              ▼               ▼                  ▼
       PostgreSQL          Redis           E2B Sandbox
       + pgvector        (sessions,       (acoustic analysis
       (embeddings,       episodes,        isolated execution)
        audit log)        rate limits)
```

---

## The Eight Pillars

<details>
<summary><strong>Pillar 1 — Acoustic Intelligence</strong></summary>

Key detection using the Krumhansl-Schmuckler algorithm. BPM extraction and beat stability analysis. Energy arc construction across 10 normalized sections. Camelot Wheel mapping for harmonic compatibility scoring. Russell Circumplex mood quadrant (arousal/valence). All analysis runs inside an E2B code sandbox — the audio bytes never touch the main process.

Files: `backend/ml/acoustic_intelligence.py`, `backend/workers/acoustic_worker.py`

</details>

<details>
<summary><strong>Pillar 2 — Differentially Private Episodic Memory</strong></summary>

User listening episodes are compressed to 32-D preference vectors. Before storage, Gaussian mechanism noise is applied: σ = sensitivity × √(2ln(1.25/δ)) / ε. Satisfaction scores are clipped to [0, 1]. Episodes expire after `EPISODE_TTL_DAYS`. EW-05 adds an HMAC chain — any out-of-order memory injection is detected.

Files: `backend/memory/episodic_memory.py`

</details>

<details>
<summary><strong>Pillar 3 — Playlist Coherence Scorer</strong></summary>

Evaluates four axes: harmonic flow (pairwise Camelot compatibility), energy arc intentionality, BPM transition smoothness (penalty for jumps >15 BPM), and mood quadrant consistency. Weighted overall score. If score < 0.60, greedy TSP reorder is applied. Flags individual transitions scoring < 0.4 for user visibility.

Files: `backend/ml/playlist_coherence.py`

</details>

<details>
<summary><strong>Pillar 4 — Context Fusion Agent</strong></summary>

Enriches user prompts with temporal context (time-of-day enum, season, day-type), weather conditions from Open-Meteo (GDPR-safe, no GPS stored), and behavioral context (listening velocity, avg BPM preference from recent history). Weather codes are enum-validated before use — raw API JSON never reaches the LLM.

Files: `backend/agents/context_fusion.py`

</details>

<details>
<summary><strong>Pillar 5 — Multi-Vector Retrieval</strong></summary>

Three embedding spaces fused via Reciprocal Rank Fusion (k=60): semantic (1536-D text embeddings in pgvector), acoustic (22-D fingerprint features in FAISS), cultural (genre ontology in FAISS). Weights computed dynamically from prompt content — tempo keywords → acoustic weight ↑, mood keywords → semantic weight ↑.

Files: `backend/ml/embedding_strategy.py`, `backend/tools/multi_vector_search.py`

</details>

<details>
<summary><strong>Pillar 6 — Embedding Poisoning Detector (EW-03 enhanced)</strong></summary>

Dual-witness Manifold Witness: two independent IsolationForest + Mahalanobis detectors trained on non-overlapping 70% subsets of the legitimate corpus. Both witnesses must approve an insertion. Shapley attribution on borderline cases. Rolling 30-day retraining invalidates attacks calibrated to stale manifold geometry.

Files: `backend/security/embedding_poisoning.py`

</details>

<details>
<summary><strong>Pillar 7 — Agent Observability</strong></summary>

Prometheus metrics for all 12 EW controls plus existing security + quality metrics. Grafana dashboard with 16 panels across 4 rows: security posture, EW enhancements, music quality, agent health. Prompt Archaeology (EW-08) detects multi-turn context drift. Tamper-evident JSONL audit log with SHA-256 chain.

Files: `backend/observability/agent_metrics.py`, `backend/observability/prompt_archaeology.py`

</details>

<details>
<summary><strong>Pillar 8 — MelodyGuard SDK</strong></summary>

Installable Python package (`pip install melodyguard-sdk`). Includes `MelodyGuardClient` with all API endpoints, `AgentSecurityContext` security baseline, and `secure_agent_step` decorator for applying the full OWASP LLM Top 10 + EW controls to any external LangGraph pipeline.

Files: `sdk/melodyguard_sdk/`

</details>

---

## Security: 10 OWASP + 12 EW Controls

| Control | Mechanism | File |
|---------|-----------|------|
| LLM01 Prompt Injection | PatternGuard MTD — 200+ patterns, 60s rotation (EW-01) | `security/agent_security.py` |
| LLM02 Insecure Output | Output validator + Semantic Firewall (EW-06) | `security/agent_security.py` |
| LLM03 Training Data Poisoning | Prompt version hash in every audit event | `agents/graph.py` |
| LLM04 DoS / Resource | Token budget 4096, rate limit 20r/min, 30s timeout | `security/agent_security.py` |
| LLM05 Supply Chain | `safety check` in CI, pinned requirements | `ci.yml` |
| LLM06 Sensitive Info | PII scrub (6 patterns), GDPR erasure endpoint | `security/agent_security.py` |
| LLM07 Insecure Plugin | Tool allow-list + Zero-Trust Tool Auth (EW-10) | `agents/graph.py` |
| LLM08 Excessive Agency | Privileged tools require explicit user consent | `security/agent_security.py` |
| LLM09 Overreliance | Confidence gate < 0.70 flags output | `security/agent_security.py` |
| LLM10 Model Theft | Audit log, rate limiting, no raw prompt logging | `security/audit_log.py` |
| EW-01 PatternGuard MTD | Dynamic 200+ pattern rotation, perplexity scoring | `security/agent_security.py` |
| EW-02 Canary Sentinel | 30 canary tokens across 3 attack surfaces | `security/canary_sentinel.py` |
| EW-03 Manifold Witness | Dual-witness embedding consensus, Shapley attribution | `security/embedding_poisoning.py` |
| EW-04 Honey Track Layer | 5 synthetic canary tracks, adversarial attractor | `security/honey_tracks.py` |
| EW-05 Temporal Memory | HMAC chain on episode sequence, drift monitoring | `memory/episodic_memory.py` |
| EW-06 Semantic Firewall | Haiku second-pass semantic alignment judge | `security/agent_security.py` |
| EW-07 Injection Sandbox | XML delimiter + classifier for all external content | `tools/spotify.py` |
| EW-08 Prompt Archaeology | Multi-turn cosine drift detection | `observability/prompt_archaeology.py` |
| EW-09 Adversarial Jitter | 5-50ms non-stationary timing noise | `security/adversarial_jitter.py` |
| EW-10 Zero-Trust Tool Auth | Per-call JWT re-validation + purpose declaration | `agents/graph.py` |
| EW-11 Acoustic Integrity | Content-hash fingerprint binding | `ml/acoustic_intelligence.py` |
| EW-12 Red Team Harness | 50+ scenarios including GAN-generated variants | `tests/red_team/` |

---

## Repository Structure

```
melodyguard/
├── .env.example
├── .gitignore
├── docker-compose.yml
├── README.md
│
├── backend/
│   ├── main.py                         # FastAPI application factory
│   ├── requirements.txt
│   ├── db.py                           # DB + Redis connection pools
│   ├── models.py                       # Shared Pydantic models
│   ├── agents/
│   │   ├── context_fusion.py           # Pillar 4 — context enrichment
│   │   └── graph.py                    # LangGraph 9-node pipeline + EW-10
│   ├── memory/
│   │   └── episodic_memory.py          # Pillar 2 — DP memory + EW-05
│   ├── ml/
│   │   ├── acoustic_intelligence.py    # Pillar 1 — Camelot + EW-11
│   │   ├── playlist_coherence.py       # Pillar 3 — coherence scoring
│   │   └── embedding_strategy.py       # Pillar 5 — multi-vector RRF
│   ├── observability/
│   │   ├── agent_metrics.py            # Pillar 7 — Prometheus metrics
│   │   └── prompt_archaeology.py       # EW-08 — multi-turn drift
│   ├── routers/
│   │   └── agents.py                   # REST endpoints
│   ├── security/
│   │   ├── agent_security.py           # OWASP LLM Top 10 + EW-01 + EW-06
│   │   ├── audit_log.py                # Tamper-evident JSONL log
│   │   ├── canary_sentinel.py          # EW-02 — tripwire detection
│   │   ├── honey_tracks.py             # EW-04 — deceptive attractors
│   │   ├── embedding_poisoning.py      # Pillar 6 — EW-03 dual-witness
│   │   ├── middleware.py               # FastAPI middleware stack
│   │   └── adversarial_jitter.py       # EW-09 — timing noise
│   └── tools/
│       ├── spotify.py                  # Spotify API + EW-07 sandbox
│       ├── vector_search.py            # pgvector semantic search
│       └── multi_vector_search.py      # Three-space RRF retrieval
│
├── sdk/
│   ├── pyproject.toml
│   └── melodyguard_sdk/
│       ├── __init__.py
│       └── client.py                   # Pillar 8 — installable SDK
│
├── frontend/                           # Next.js 15 + React 19
│   └── src/
│       ├── app/                        # Pages: landing, dashboard, callback
│       ├── components/                 # Player, visualizers, generation flow
│       ├── hooks/                      # Spotify Auth, Player, Audio Analyser
│       └── lib/api.ts                  # Typed API client
│
├── infra/docker/
│   ├── Dockerfile.backend
│   ├── Dockerfile.frontend
│   ├── init.sql                        # Database schema + RLS
│   ├── nginx/nginx.conf
│   ├── prometheus/prometheus.yml
│   └── grafana/
│       ├── provisioning/
│       └── dashboards/melodyguard.json
│
├── docs/
│   └── threat-model/THREAT_MODEL.md
│
└── .github/
    └── workflows/ci.yml                # 8-job pipeline + EW coverage gate
```

---

## Quick Start

### Prerequisites

- Docker 24+ and Docker Compose v2
- Spotify Developer account (for Client ID + Secret)
- Anthropic API key
- Clerk account (for authentication)
- E2B account (for acoustic analysis sandbox)

### 1. Clone and configure

```bash
git clone https://github.com/your-org/melodyguard.git
cd melodyguard
cp .env.example .env
# Fill in all required values in .env
```

Generate the required cryptographic keys:
```bash
# Fernet key (Spotify token encryption)
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"

# Canary signing key (EW-02)
python -c "import secrets; print(secrets.token_hex(32))"

# Memory HMAC key (EW-05)
python -c "import secrets; print(secrets.token_hex(32))"

# Pattern seed salt (EW-01)
python -c "import secrets; print(secrets.token_hex(32))"
```

### 2. Start the stack

```bash
docker compose up --build
```

### 3. Seed canaries and honey tracks (first deploy only)

```bash
docker compose --profile init up canary-seeder
```

This plants all 30 EW-02 canary tokens and 5 EW-04 honey tracks. Idempotent — safe to re-run.

### 4. Verify

```bash
curl http://localhost:8000/health
# → {"status":"ok","version":"2.0.0","db":"ok","redis":"ok"}

curl http://localhost:3000
# → MelodyGuard landing page
```

### 5. Access services

| Service | URL |
|---------|-----|
| Frontend | http://localhost:3000 |
| Backend API | http://localhost:8000 |
| API Docs | http://localhost:8000/docs |
| Prometheus | http://localhost:9090 |
| Grafana | http://localhost:3001 |

---

## Running Tests

```bash
cd backend

# Security controls (includes all EW tests)
pytest tests/security/ -v

# ML pipelines
pytest tests/ml/ -v

# Integration (requires running postgres + redis)
docker compose up postgres redis -d
pytest tests/integration/ -v

# Full suite with coverage
pytest tests/ -v --cov=. --cov-fail-under=80

# Red team (50+ adversarial scenarios)
python tests/red_team/pyrit_scenarios.py
```

---

## Design Decisions

**Why LangGraph over a simple chain?** The pipeline has conditional edges — a blocked input never reaches the discovery node. LangGraph's `StateGraph` makes this explicit and auditable in the trace. Each node's inputs and outputs are typed via `AgentState`.

**Why differential privacy and not just hashing?** Hashing is brittle against auxiliary information attacks. Differential privacy provides formal guarantees: no matter what an adversary knows about other users, they cannot distinguish your specific preferences from the noised aggregate with more than exp(ε) probability. Hashing gives you none of that.

**Why build the security layer before the agents?** Agents must pass through security controls. Security is not bolted on at the end — it is the envelope that the agent pipeline runs inside. Phase 3 passes its verification tests before Phase 6 is built.

**Why EW doctrine and not just OWASP?** OWASP provides a compliance checklist. EW doctrine provides a battle doctrine. The difference is orientation: compliance asks "have we covered the known attacks?" — doctrine asks "how do we win against an adaptive adversary who is actively studying our defenses?" Static defenses eventually lose. Moving targets, canaries, and honey tokens change the cost structure of attacking the system.

**Why is the Output Semantic Firewall a different model?** If the primary pipeline has been manipulated by an adversarial attack, using the same model to evaluate the output provides no independent signal. Claude-haiku-4-5 was not subject to the same manipulation as the primary pipeline — its semantic judgment is independent. This is the same principle as using a separate auditor rather than self-certification.

---

## The Audit Log Format

Every security event is appended to `audit.jsonl` with a tamper-evident chain:

```json
{
  "event_id": "550e8400-e29b-41d4-a716-446655440000",
  "timestamp": "2026-03-12T19:30:00.000Z",
  "user_id": "user_abc123",
  "session_id": "sess_xyz789",
  "agent_name": "safety_moderator_input",
  "action": "PROMPT_INJECTION_BLOCKED",
  "threat_level": "HIGH",
  "blocked": true,
  "input_hash": "a3f2c1d4",
  "duration_ms": 1.2,
  "ew_controls_triggered": ["EW-01"],
  "chain_hash": "sha256:a1b2c3d4e5f6..."
}
```

`chain_hash` = SHA-256(previous_event_json + current_event_json). Any modification to any past event breaks the chain. The genesis event uses `chain_hash = SHA-256("GENESIS")`.

---

## The Values This Is Built On

The tools we use to discover music should be honest. They should be yours.

Your listening history is one of the most intimate data sets that exists about you — it encodes moods, moments, relationships, and grief. Storing it with formal privacy guarantees is not a technical nicety. It is a minimum standard.

And when a system learns your taste well enough to predict what you want to hear next, it becomes powerful enough to subtly manipulate what you want to hear next. Building the defenses against that manipulation into the architecture — not as an afterthought — is what makes the difference between a tool that serves the user and one that eventually serves whoever can compromise it.
