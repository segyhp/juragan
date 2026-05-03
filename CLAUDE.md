# Juragan — Claude context

## What this is

Senior-level portfolio project: Indonesian warung (micro-retail) backend platform. Built as an interview vehicle for senior backend roles in JP (primary), SG/AU, and global remote markets. Hard deadline ~2027-01.

Owner: Segy Hendro Pratama. 6+ years backend (PHP, Go, MySQL/Postgres, Docker, k8s). Currently dispatch at Rakuten Japan, targeting permanent senior role.

See [`docs/PLAN.md`](docs/PLAN.md) for full 8-month phasing and [`docs/adr/`](docs/adr/) for architecture decisions.

## Locked architecture

- Go modular monolith → one deliberate extraction (OCR service) over Kafka/NATS
- Postgres (JSONB, logical replication, `LISTEN/NOTIFY`, `pg_trgm`)
- Event-sourcing + per-aggregate LWW + outbox for offline-first sync. CRDTs rejected (over-engineered for POS).
- Claude Haiku 4.5 vision primary, Sonnet 4.6 fallback. Prompt caching on system prompt + SKU catalog. Agentic extract → match → reconcile loop. Eval harness with hand-labelled golden set. Cost telemetry via OpenTelemetry.
- k3s on Hetzner, Prometheus + Grafana + Loki, real SLOs and on-call runbook.
- Vue 3 + Nuxt 3. Minimal. Not the showcase.

## How to collaborate on this repo

- Treat decisions as **already made** unless explicitly reopening one. Don't re-litigate scope.
- Every architectural decision gets an ADR in `docs/adr/NNNN-title.md` (MADR format: Context / Decision / Consequences).
- Every significant feature gets a blog-post draft in `docs/blog/`, in English. These are the interview narrative — they're not optional.
- Resist scope creep beyond the capability list in `README.md`. If a new capability is tempting, write down *what it would cost* before adding it.
- Senior signal beats feature count. One well-engineered feature with evals + observability + cost telemetry > five half-finished CRUD modules.
- Default to depth: tests, benchmarks, traces, ADRs, runbooks. The portfolio value comes from the surrounding rigor, not the LOC.

## Parallel tracks

The owner is also running two parallel tracks alongside this repo (not in this repo, but relevant context):

- **DSA / LeetCode** — NeetCode 150, focus on graphs / DP / intervals / heaps. ~25% of weekly time.
- **System design** — DDIA cover-to-cover, Hello Interview / ByteByteGo. Output: a single system-design write-up of Juragan that doubles as interview cheat sheet.

Don't let the project consume the time budget for these — they're independently load-bearing for interview success.
