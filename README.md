# juragan

Backend platform for Indonesian micro-retail (warung) — POS, inventory, offline-first sync, and AI-assisted receipt OCR.

Built as a senior-level engineering portfolio project: deep backend (Go), AI engineering (Claude vision + evals), and DevOps (self-operated k3s). Targeting interview-readiness by January 2027.

## Status

Pre-architecture. See [`docs/PLAN.md`](docs/PLAN.md) for the 8-month build plan and [`docs/adr/`](docs/adr/) for architecture decisions as they're recorded.

## Stack (locked)

- **Language:** Go (primary). Rust reserved for ancillary subsystems if a real need arises.
- **Architecture:** Modular monolith with one deliberate extraction (OCR pipeline → separate service over Kafka or NATS).
- **Data:** Postgres (JSONB, logical replication, `LISTEN/NOTIFY`, `pg_trgm`).
- **Sync:** Event-sourcing + per-aggregate last-writer-wins + outbox pattern. CRDTs explicitly rejected — see ADR.
- **AI:** Claude Haiku 4.5 vision (primary), Sonnet 4.6 fallback. Prompt caching. Agentic verify/reconcile pipeline. Real evals on a hand-labelled golden set. Cost telemetry via OpenTelemetry.
- **Deploy:** k3s on Hetzner. Prometheus + Grafana + Loki. SLOs and on-call runbook.
- **Frontend:** Vue 3 + Nuxt 3. Minimal — not the showcase.

## Capabilities

1. Multi-tenant POS + inventory backend (Go) — the spine.
2. Offline-first sync — the signature feature.
3. Receipt OCR pipeline (LLM/VLM) — AI engineering showcase.
4. k8s + observability + SLOs — DevOps showcase.
5. Vue/Nuxt frontend — minimal but real.
6. Public write-up + technical blog posts (English).

## Non-goals

- Demand forecasting (dropped — OCR/LLM track gives the AI-eng narrative the market pays for).
- Microservices-from-day-one. Modular monolith first; extraction is justified by data, not architecture astronautics.
- A polished SaaS product. This is a portfolio vehicle, not a startup.
