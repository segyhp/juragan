# 0001 — Modular monolith with one deliberate extraction (OCR)

- Status: accepted
- Date: 2026-05-03

## Context

Juragan is a one-person backend platform built on a ~240-hour budget over eight months. It must demonstrate senior-level architecture judgment, not microservice cargo-culting.

Forces in tension:

- **POS + inventory + sync** are tightly coupled OLTP concerns. They share a transactional boundary (a sale debits stock, appends to the event log, and writes to the outbox in one transaction). Splitting them costs distributed-transaction complexity for no real benefit at this scale.
- **Receipt OCR** has the opposite profile: variable latency (1–10s per receipt), variable cost (LLM API spend), independent scaling needs, and a failure mode (model timeout, rate limit, eval regression) that must not affect a cashier ringing up a sale.
- The platform must show a credible production-ops story (k3s, SLOs, runbooks). That story is more legible with two deploy units that communicate over a durable bus than with one giant binary.
- The owner has limited time. Premature service decomposition burns weeks on infrastructure (per-service CI, schema-per-service, cross-service tracing) that could go into evals, observability, and the offline-sync engine — the actual senior signals.

## Decision

Build POS, inventory, sync, and outbox as a single Go modular monolith. Extract exactly one service: the OCR pipeline, which communicates with the monolith over Kafka or NATS via the outbox.

Module boundaries inside the monolith are enforced by package layout under `internal/`, with no cross-module imports except through explicit ports. Each module owns its tables and exposes a Go interface to its callers; there are no shared mutable structs.

## Consequences

Easier:

- One deploy artifact for the core platform. Fast iteration in months 1–3.
- A sale, stock decrement, event-store append, and outbox write happen in one Postgres transaction — no two-phase commit, no saga.
- The OCR service can scale, restart, hit rate limits, or regress on an eval without touching POS p99.
- A clean future-extraction story: when (if) sync grows to need its own runtime, the module boundary is already drawn.
- The operational story has a real distributed-systems surface (outbox → bus → consumer) without fabricating one.

Harder:

- Module discipline must be enforced. A lint rule (`depguard` or equivalent) blocks cross-module imports outside the declared ports. Without this, the monolith rots into a ball of mud and the "modular" claim is theater.
- Two deploy units, two CI pipelines, two sets of dashboards from day one. Acceptable cost — the OCR service needs them anyway.
- Operating Kafka or NATS on a single-node k3s cluster adds an opinion to defend in interviews. NATS JetStream is the likely pick: lighter footprint, simpler ops, durable streams sufficient for outbox semantics.

Impossible (intentionally):

- Independent team ownership of POS vs. inventory. Not a goal — there is no team.
- Per-module database technology choice. Postgres for everything inside the monolith. (See ADR 0003 when written.)

## Alternatives considered

- **Microservices from day one (POS, inventory, sync, OCR each separate)** — rejected. Wrong scale for one engineer and one cluster. Distributed transactions or sagas across POS and inventory would dominate the build. Reads as junior-imitating-senior in interviews.
- **Pure monolith, OCR in-process** — rejected. LLM call latency and cost variance would pollute POS request budgets and complicate SLOs. A model outage or rate limit would degrade the cashier path. Also weakens the AI-engineering narrative (no clean place to show queue depth, retry policy, dead-letter handling, cost telemetry per request).
- **Serverless OCR (AWS Lambda / Cloud Run)** — rejected. Conflicts with the self-operated k3s/Hetzner ops narrative that is one of the project's senior signals. Cold-start variance hurts eval reproducibility. Vendor-managed hides exactly the operational surface the portfolio needs to show.
- **Two monoliths split by tenant or region** — rejected. Multi-tenancy is solved at the schema level (row-level tenant_id + RLS or equivalent), not by binary duplication. No real load justifies the split.
