# TENIR-Gov — Governance Middleware for AI-Enabled Operational Systems

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue)](https://www.python.org/downloads/)
[![CI](https://github.com/skiredj-prog/tenir-gov/actions/workflows/tenir-ci.yml/badge.svg)](https://github.com/skiredj-prog/tenir-gov/actions/workflows/tenir-ci.yml)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.10823456.svg)](https://doi.org/10.5281/zenodo.10823456)

**TENIR-Gov** is an open-source governance middleware that sits between decision-producing AI agents and downstream execution services. It enforces explicit policies, validates operational intents through a neuro-symbolic grammar layer, and persists every governance decision in a cryptographically verifiable Merkle-based audit ledger — independently of any underlying AI model.

> *"The interface is not the system; it is a lawful projection of a higher-dimensional invariant under contextual constraint."*
> — TENIR Master Doctrine v5

---

## Overview

Modern deployments of autonomous AI agents lack a formal, model-independent governance layer. TENIR-Gov addresses this by implementing governance as **infrastructure** rather than as a property of any single model.

```
Incoming Intent
      │
      ▼
┌─────────────────────────────────────────┐
│            Governance Spine             │
│  Polymorphic Surface → Nomenclature →   │
│  Policy Engine → Membrane Verdict       │
│   (PASS / FLAG / BLOCK)                 │
└─────────┬──────────────┬────────────────┘
          │              │
          ▼              ▼
    Audit Ledger    Execution Gateway
   (Merkle chain)  (downstream APIs)
```

### Key capabilities

| Capability | Implementation |
|:---|:---|
| **Neuro-Symbolic Validation** | LALR(1) grammar parser + optional LLM fine-tuning via QLoRA |
| **Deterministic Policy Engine** | Frozen dataclass contract; single source of truth for all thresholds |
| **Graph-Based Policy Store** | Neo4j 5.x with full ontology (CP-Net structure) |
| **Cryptographic Audit Ledger** | Merkle epoch trees + hash chain; inclusion proofs at O(log n) |
| **Administrative Governance Plane** | Policy lifecycle, approval workflows, asymmetric-key change-control |
| **Polymorphic Surface Contract** | Four UI states (AMBIENT / ANTICIPATION / ADJUDICATION / FORENSIC) |
| **Legacy Migration Tooling** | `ledger_migrate.py` rewrites JSONL ledgers preserving forensic continuity |

---

## Architecture

### Release R5.0.0 (IRON OMEGA R5)

```
tenir-gov/
├── tenir_governance/          ← core governance package (public API)
│   ├── nomenclature.py        Sprint 0 — canonical term registry (R4+R5 unified)
│   ├── policy_engine.py       Sprint 1 — policy contract + membrane decision
│   ├── validator.py           Sprint 3 — 9-check CI gate (CLI + pytest fixture)
│   ├── regression_corpus.py   Sprint 4 — 281 golden test cases
│   ├── sdk.py                 Sprint 5 — TENIRGovernanceClient public API
│   ├── polymorphic_surface.py Sprint 10 — V5 surface state contract
│   ├── copy_lint.py           Sprint 9 — public-safe lexicon enforcement
│   └── ledger_migrate.py      Sprint 11 — ledger label migration
├── r4/                        ← R4 partner_a Shadow v4 monitor runtime
│   ├── tenir_v4_test/         adjudication, control-auth, ledger, models
│   └── tests/                 61 R4 monitor tests
├── r5_hardened/               ← R5 IRON OMEGA — hardened runtime
│   └── IRON_OMEGA_R5/         NSL grammar, graph ontology, Merkle crypto, WebSocket hub
├── r5_wired/                  ← R5 wired to govern package (integration layer)
├── interface/                 ← operational dashboard (HTML/JS)
├── tests/                     ← governance package test suite (313 tests)
├── Dockerfile                 ← reproducibility container
├── docker-compose.yml         ← full stack (middleware + Neo4j)
└── .github/workflows/         ← 6-gate blocking CI pipeline
```

### CES State Machine

The Cognitive Engagement System (CES) models the governance agent's internal state:

| State | Condition |
|:---|:---|
| `REST` | S ≥ s_alert_floor (nominal) |
| `METABOLIZING` | S ≥ s_alert_floor AND activity ≥ 1.00 |
| `TENSION` | s_block_floor ≤ S < s_alert_floor |
| `SIGNAL_CONFLICT` | S < s_alert_floor AND option_space ≥ 0.55 |
| `COLLAPSE` | S < tau_floor OR (S ≤ s_block_floor AND option_space ≤ 0.30) |

### Membrane Verdicts

Every governance decision resolves to one of: **PASS · FLAG · BLOCK**

---

## Requirements

| Component | Version |
|:---|:---|
| Python | 3.10+ |
| Neo4j | 5.x (optional — middleware runs without graph features) |
| Docker Engine | 27.x (for containerised deployment) |
| OS | Linux · macOS · Windows |

---

## Quickstart

### Option A — Docker (recommended, full stack)

```bash
git clone https://github.com/skiredj-prog/tenir-gov.git
cd tenir-gov

# Core middleware only (no Neo4j required)
docker compose up -d --build

# With Neo4j graph features
docker compose --profile graph up -d --build

# Run the full validation suite inside the container
docker compose exec tenir-middleware pytest -v --cov=tenir_governance
```

### Option B — Local install (Python only)

```bash
git clone https://github.com/skiredj-prog/tenir-gov.git
cd tenir-gov

pip install -e .
pip install pytest pytest-cov

# Run governance package tests
pytest tests/ -v --cov=tenir_governance
```

### Option C — SDK usage

```python
from tenir_governance import TENIRGovernanceClient, GovernanceEvent

# Use the partner_a Shadow v4 policy profile
client = TENIRGovernanceClient.for_um6p_shadow()
result = client.adjudicate(GovernanceEvent(
    pressure=0.8,
    velocity=0.7,
    capacity=1.0,
    option_space=0.5
))
print(result.to_business_payload())
# {'membrane': 'FLAG', 'ces_state': 'TENSION', ...}
```

---

## Running the Full Test Suite

```bash
# Governance package (313 tests)
pytest tests/ -v --cov=tenir_governance --cov-report=term-missing

# R4 monitor suite (61 tests)
pytest r4/tests/ -v

# R5 hardened suite (56 tests)
pytest r5_hardened/IRON_OMEGA_R5/test_r5_all.py \
       r5_hardened/IRON_OMEGA_R5/test_institutional_hardening.py -v

# R5 governance integration (15 tests)
pytest r5_wired/test_r5_governance_integration.py -v

# Complete suite (all 445 tests)
pytest tests/ r4/tests/ \
       r5_hardened/IRON_OMEGA_R5/test_r5_all.py \
       r5_hardened/IRON_OMEGA_R5/test_institutional_hardening.py \
       r5_wired/test_r5_governance_integration.py \
       --cov=tenir_governance --cov-report=term-missing
```

**Expected result:** 445 tests passing, 70% statement coverage on `tenir_governance`.

### CI gate

```bash
tenir-validate --policy default           # 9/9 invariant checks
tenir-validate --policy partner_a --json       # partner_a Shadow v4
tenir-validate --policy partner_b               # partner_b Sovereign Pilot
```

---

## Policy Profiles

Three governance policy profiles are shipped:

| Profile | Factory | Use case |
|:---|:---|:---|
| `default` | `PolicyEngine.default()` | Canonical baseline |
| `partner_a` | `PolicyEngine.um6p_shadow_v4()` | partner_a Shadow v4 equivalence (`s_alert=0.90`, `event_window=8`) |
| `partner_b` | `PolicyEngine.ocp_sovereign_pilot()` | Tight industrial lock-in (`tau_floor=0.50`, `reaction_budget=3`) |

Policy fingerprint (default): `d083e0b82a16c04d`

---

## API Reference (R5 FastAPI server)

Start the server:

```bash
docker compose up -d --build
# or: uvicorn r5_hardened.IRON_OMEGA_R5.r5_server:app --host 0.0.0.0 --port 8000
```

| Endpoint | Method | Description |
|:---|:---|:---|
| `/api/v1/adjudicate` | POST | Submit intent for governance evaluation |
| `/api/v1/oath/sign` | POST | Operator oath signature for mode transition |
| `/api/v1/transition` | POST | Transition operating mode (requires oath) |
| `/api/v1/ledger/verify` | GET | Chain integrity verification |
| `/api/v1/ledger/proof/{entry_id}` | GET | Merkle inclusion proof for an entry |
| `/health` | GET | Service health check |
| `ws://…/ws/vps` | WS | Live VPS Three.js engine feed |

---

## Tooling

### Copy-lint (public-safe lexicon enforcement)

```bash
python -m tenir_governance.copy_lint docs/
python -m tenir_governance.copy_lint --exposure public homepage.html
```

### Ledger migration (legacy label rename)

```bash
python -m tenir_governance.ledger_migrate --dry-run audit/ledger.jsonl
python -m tenir_governance.ledger_migrate audit/ledger.jsonl
python -m tenir_governance.ledger_migrate --verify audit/ledger.jsonl
```

---

## Terminology Notes

| Legacy term | Canonical term (R5.0.0) | Notes |
|:---|:---|:---|
| `SCHIZOPHRENIA` | `SIGNAL_CONFLICT` | State validation conflict |
| `SCHIZOPHRENIA_ALERT` | `SIGNAL_CONFLICT_ALERT` | — |
| `WHALE_RESONANCE` | `DEEP_PATTERN_SIGNAL` | — |

Backward-compatible aliases are preserved in `CESStateNames.LEGACY_ALIASES`; existing ledgers re-read cleanly.

---

## Performance (R5.0.0 Benchmark)

Measured on AMD Ryzen 9 7950X · 64 GB DDR5 · Ubuntu 22.04 · 10,000 sequential requests:

| Metric | Value |
|:---|:---|
| Mean policy latency | 12.4 ms |
| Median latency | 10.1 ms |
| P95 latency | 24.7 ms |
| Ledger write overhead | 4.2 ms |
| NSL parsing success rate | 99.98% |
| Throughput | 1,250 decisions/sec |

---

## Known Limitations (R5.0.0)

- Multi-tenant / multi-organisation isolation has not been benchmarked.
- Distributed ledger replication and global graph synchronisation are out of scope.
- NSL grammars require domain-specific schema definitions (not plug-and-play).
- Merkle ledger storage grows linearly with transaction volume.
- Administrative override records are structurally declared but disabled in this release (strict fail-closed posture).

---

## Citation

If you use TENIR-Gov in your research, please cite:

```
Skiredj, A. (2026). TENIR-Gov: Governance Middleware for AI-Enabled Operational Systems.
SoftwareX. https://doi.org/10.5281/zenodo.10823456
```

Or use the `CITATION.cff` file in this repository.

---

## License

Apache License 2.0 — see [LICENSE](LICENSE).

Copyright 2026 Abdelaziz Skiredj / TENIR Labs
