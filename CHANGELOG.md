# Changelog

All notable changes to TENIR-Gov are documented in this file.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [5.0.0] — 2026-06-29 (IRON OMEGA R5) — Current Release

### Summary
Full production release of TENIR-Gov as an integrated governance middleware.
Merges R4 (UM6P Shadow v4) and R5 (IRON OMEGA) into a single installable package with a shared canonical contract.

### Added — Governance Package (`tenir_governance`)
- **Sprint 0** — `nomenclature.py`: single canonical term registry for R4+R5 vocabularies, three-register language system (Canonical / Operator / Public)
- **Sprint 1** — `policy_engine.py`: frozen `PolicyEngine` dataclass with three factory profiles (default, UM6P Shadow v4, OCP Sovereign Pilot); `evaluate_membrane()` as the sole membrane decision function
- **Sprint 2** — `__init__.py`: clean public API boundary; installable via `pip install -e .`
- **Sprint 3** — `validator.py`: `TENIRValidator` with 9 named invariant checks (POL-001 → NOM-002); CLI (`tenir-validate`) and pytest fixture
- **Sprint 4** — `regression_corpus.py`: 281 golden `GoldenCase` fixtures across 16 scenario groups
- **Sprint 5** — `sdk.py`: `TENIRGovernanceClient` public API for both R4 (`adjudicate()`) and R5 (`adjudicate_from_nsl()`) paths
- **Sprint 6** — Plain-language hardening: `SCHIZOPHRENIA → SIGNAL_CONFLICT`, `WHALE_RESONANCE → DEEP_PATTERN_SIGNAL` with backward-compat aliases
- **Sprint 7** — CES classifier extended with `pressure` and `velocity` parameters; 14 corpus fixtures updated to reflect activity-based classification
- **Sprint 8** — Expanded posture family: `ROAM`, `LEARN`, `TRANSMIT`, `REST`, `SLEEP`, `STOP`; `AdmissibilityClass` added
- **Sprint 9** — `copy_lint.py`: CI-gated public-safe lexicon enforcement for `.md`, `.html`, `.txt`
- **Sprint 10** — `polymorphic_surface.py`: V5 Persona/Workflow surface contract (4 states, Two-Glass bridge, `requires_ceremony()`)
- **Sprint 11** — `ledger_migrate.py`: forensic JSONL ledger migration with migration-genesis hash and chain continuity

### Added — R5 Runtime (`r5_hardened/`, `r5_wired/`)
- LALR(1) NSL grammar parser with LLM fine-tuning pipeline (QLoRA / Axolotl)
- Neo4j 5.x graph ontology for CP-Net policy representation
- Merkle epoch audit ledger with inclusion proofs and peer broadcast
- `KeyCeremony` protocol for operator oath epoch-binding
- FastAPI server (`r5_server.py`) with `/adjudicate`, `/oath/sign`, `/transition`, `/ledger/verify`, `/ledger/proof/{id}` endpoints
- WebSocket hub (`ws_hub.py`) + Three.js VPS live engine

### Added — Infrastructure
- `Dockerfile` reproducibility container (Python 3.11-slim)
- `docker-compose.yml` with profile-gated Neo4j (`--profile graph`)
- 6-gate blocking GitHub Actions CI pipeline (`tenir-ci.yml`)
- `CITATION.cff`, `.zenodo.json` for Zenodo and GitHub citation metadata

### Validation
- 445 tests passing (100%): 313 governance package · 61 R4 · 56 R5 hardened · 15 R5 integration
- 70% statement coverage on `tenir_governance`
- 9/9 policy invariant checks across default, UM6P, and OCP profiles
- Policy fingerprint (default): `d083e0b82a16c04d`

### Known Limitations
- Multi-tenant / multi-organisation isolation not benchmarked
- Distributed ledger replication out of scope
- Administrative override records disabled (fail-closed posture)
- NSL grammars require domain-specific schema definitions

---

## [4.0.0] — 2026-01 (UM6P Shadow v4)

### Summary
R4 release: internal UAT package for UM6P Shadow-mode deployment.
Introduced `TenirMonitor`, `HashChainedLedger`, `OperatorRegistry`, `PolicyBundle`, CAVE adjudication framework, and burn-cost estimation.
R4 runtime known issues (G1–G6) documented in `HARDENING_REPORT_v1.0.0-rc2.md`; these are addressed by construction in R5 through the shared `PolicyEngine.evaluate_membrane()` contract.
