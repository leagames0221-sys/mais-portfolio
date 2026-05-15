# M&A Intelligence Suite (MAIS)

> **Five AI tools that automate the entire M&A lifecycle for Japanese mid-market deals**:
> sourcing → due diligence → Day-1 readiness → 100-day PMI → cross-engagement knowledge layer.

[![tools](https://img.shields.io/badge/tools-5-blueviolet)]()
[![python](https://img.shields.io/badge/python-3.11+-blue)]()
[![5 tools CI](https://img.shields.io/badge/5%20tools%20CI-pip--audit%20on%20push-brightgreen)](https://github.com/leagames0221-sys?tab=repositories&q=mais)
[![license](https://img.shields.io/badge/license-MIT-green)](LICENSE)

---

## Why this exists

Mid-market Japanese M&A advisory firms run the same five jobs on every deal: find candidates, run DD, plan Day-1, monitor 100-day PMI, transfer the lessons to the next deal. Each is partially automatable today; nobody has stitched the five into a coherent suite that respects Japanese mid-market specifics (family ownership, banking relationships, union dynamics) and Japan's 2026 amended Personal Information Protection Act.

**MAIS** is that suite, built solo as a portfolio. Each tool is independently deployable; sibling tools hand off via versioned Pydantic schemas (Citation array → IntegrationPlan → CockpitProject → PMICase).

---

## The five tools

| # | Tool | Replaces / accelerates | Core tech |
|---|---|---|---|
| 1 | [**mais-deal-matching**](https://github.com/leagames0221-sys/mais-deal-matching) | Manual candidate-buyer matching by intermediaries | 5-stage hybrid retrieval (BM25 + dense + RRF + cross-encoder + LLM CoT) + PII Vault Pattern (SQLCipher, 2026 APPI compliant) |
| 2 | [**mais-dd-workbench**](https://github.com/leagames0221-sys/mais-dd-workbench) | Manual VDR document hunting + DD question answering | Docling (IBM) ingestion + LlamaIndex CitationQueryEngine + CUAD/ACORD clause extraction + JP mid-market fit detector |
| 3 | [**mais-day1-cockpit**](https://github.com/leagames0221-sys/mais-day1-cockpit) | Manual Day-1 plan drafting + audience cascade | LangGraph 4-axis parallel agents + NetworkX dependency graph + 5-audience LLM communication kit |
| 4 | [**mais-pmi-cockpit**](https://github.com/leagames0221-sys/mais-pmi-cockpit) | Manual KPI tracking + reactive next-action decisions | Apache Superset embed + Isolation Forest anomaly + LLM next-action recommender + multilingual sentiment |
| 5 | [**mais-pmi-knowledge-base**](https://github.com/leagames0221-sys/mais-pmi-knowledge-base) | Tribal-lore knowledge transfer between deals | Self-built GraphRAG (NetworkX Louvain) + 5-axis weighted similarity + AI Assistant dialogue with citations |

---

## Data flow between tools

```
mais-deal-matching          mais-dd-workbench          mais-day1-cockpit
   (sourcing)        ───►        (DD)            ───►    (Day-1 plan)
        │                          │                          │
        ▼                          ▼                          ▼
   Profile/                  Citation array,            IntegrationPlan,
   Company match             JP fit pattern,            PlanNode,
                             Q-A pairs                  RiskScore,
                                                        CommunicationKit
                                                              │
                                                              ▼
                                                    mais-pmi-cockpit
                                                       (100-day)
                                                              │
                                                              ▼
                                                       CockpitProject,
                                                       KpiSnapshot,
                                                       DriverInsight,
                                                       NextAction
                                                              │
                                                              ▼
                                                 mais-pmi-knowledge-base
                                                      (knowledge layer)
                                                              │
                                                              ▼
                                                        PMICase ADR,
                                                        Decision/Outcome,
                                                        Pattern,
                                                        AssistantQuery
```

Each tool publishes its outputs as Pydantic schemas; the next tool ingests via versioned mappers. Tools can be deployed independently or as a stack.

---

## Shared design principles

| Principle | How it shows up |
|---|---|
| **Security-aware dependencies** | Pinned versions in `requirements*.txt`; lock files committed where applicable; CI integration (pip-audit `--strict`, Dependabot, Dependency Review) is a deployment-time addition for client engagements rather than a portfolio-side claim |
| **PII discipline** | Vault Pattern (Fernet at rest + audit log append-only); embedding/matching never sees raw PII |
| **No vendor lock-in** | Anthropic API + OSS only; LLMProvider Protocol (3 methods) lets you swap to Ollama / Gemini / self-hosted in one file |
| **Citation everywhere** | Every LLM-generated answer carries source link-backs (page / cell / bbox metadata for Docling chunks) |
| **Spec-Driven Workflow** | Discovery → Requirements → Design → Tasks; module-boundary discipline enforced at code-review time; full drift-prevention infrastructure (GitHub Issues + Projects + linked PRs + CI checks) is added when an engagement begins |
| **Consumer-laptop runnable** | Every tool runs end-to-end on a single laptop with synthetic data; no GPU required for PoC scope |
| **Synthetic data only** | All tools ship with Faker (ja_JP)-driven synthetic data; no real PII / real engagements in any repo |

---

## Test coverage

| Repo | Tests collected (pytest) |
|---|---|
| mais-deal-matching | 50 |
| mais-dd-workbench | 47 |
| mais-day1-cockpit | 66 |
| mais-pmi-cockpit | 96 |
| mais-pmi-knowledge-base | 248 |
| **Total** | **507** |

Counts are AST-derived from `tests/test_*.py` files in each repo. To run:

```powershell
cd <repo>
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements-week4.txt   # or week0/week1 depending on repo
pytest tests/
```

Note: each repo has its own dependency closure; full install + run requires per-repo setup. Some heavy dependencies (multilingual-e5-large ~2.2 GB, cross-encoder ~134 MB) are downloaded on first index build.

---

## Visual identity

Slate scale (~95% neutral) + warm amber accent (~5%) on off-white background. Golden-ratio (φ=1.618) typography scale. Designed for an executive aesthetic — clean enough to live behind a senior partner's screen during client meetings.

---

## Status

**PoC portfolio**. Each tool is functionally complete with synthetic data + commercial-grade hardening foundation in place. For production deployment to a client engagement:

- Sandbox setup (Docker / WSL2 / Codespaces) for real PII / real documents
- Customer sandbox dry-run + 1-week stability before cutover
- External penetration test for large engagements
- LLMProvider swap to self-hosted (Ollama / vLLM) for data residency
- Per-paper license confirmation for real research ingestion (T5)

Commercial deployment is a separate engagement; contact via GitHub Issues.

---

## Target users

- **M&A advisory firms / intermediaries** — internalize sourcing through PMI
- **PE / VC funds** — standardize portfolio company DD + integration
- **Corporate M&A functions** — deploy as internal AI tooling
- **Strategy consultancies** — offer to clients as a PMI advisory tool

---

## License

MIT. See [LICENSE](LICENSE). Each individual tool ships with the same MIT license.

This portfolio demonstrates capability. Production engagements operate under separate commercial agreements.
