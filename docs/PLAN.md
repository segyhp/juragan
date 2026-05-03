# Juragan — 8-month plan (May 2026 → Jan 2027)

**Goal:** Land senior backend role (JP seishain primary; SG/AU, global remote secondary) by ~2027-01.

**Why:** Convert dispatch role at Rakuten Japan to permanent senior role with V-shape (deep backend + AI eng + DevOps).

**Time budget:** ~12 hours/week sustained × ~32 weeks ≈ **400 effective hours**, allocated:
- **60% (~240h)** — Juragan project build
- **25% (~100h)** — DSA / LeetCode (NeetCode 150)
- **15% (~60h)** — System design (DDIA, Hello Interview / ByteByteGo, Juragan sys-design write-up)

All three tracks run **in parallel from month 1.** Do not defer DSA or system-design study until "after the project."

## Capability matrix (locked)

| # | Capability | Senior signal |
|---|---|---|
| 1 | Multi-tenant POS + inventory backend (Go) | Schema, transactions, idempotency, money handling |
| 2 | Offline-first sync | Distributed systems, event sourcing, conflict resolution |
| 3 | Receipt OCR pipeline (Claude vision) | AI eng, prompt design, evals, cost control |
| 4 | k8s + observability + SLOs | DevOps, SRE, on-call story |
| 5 | Vue/Nuxt frontend | Full-stack credibility (minimal) |
| 6 | Public write-up + blog posts | Communication, narrative |

Dropped: demand forecasting (OCR/LLM gives stronger AI-eng narrative).

## 8-month phasing

### Month 1 — May 2026 — Foundations
- Architecture write-up + first ADRs
- Postgres schema (multi-tenant, event store, outbox)
- Bare Go service skeleton (cmd, internal layout, config, healthz)
- k3s cluster up on Hetzner
- DSA: NeetCode 150 warm-up (arrays, hashmaps, two-pointer)
- Sys-design: DDIA chapters 1–4

### Month 2 — Jun 2026 — POS + inventory MVP
- POS + inventory backend MVP, end-to-end happy path
- Outbox table + reliable event publishing
- CI/CD pipeline (GitHub Actions → Hetzner)
- Observability stack online (Prometheus, Grafana, Loki)
- DSA: trees, graphs warm-up
- Sys-design: DDIA chapters 5–7 (replication, partitioning, transactions)

### Month 3 — Jul 2026 — Offline-first sync
- Event store, sync log, server-side reconciler
- Per-device idempotency keys, replay semantics
- **Blog post #1: "Designing offline-first sync for Indonesian warungs — why I rejected CRDTs"**
- DSA: graphs, BFS/DFS, union-find
- Sys-design: DDIA chapters 8–9 (consistency, consensus)

### Month 4 — Aug 2026 — OCR pipeline + AI evals
- Extract OCR pipeline as separate service over Kafka/NATS
- Claude Haiku 4.5 integration with prompt caching on system prompt + SKU catalog
- Agentic verify/reconcile loop (extract → fuzzy-match via `pg_trgm` → propose-or-ask)
- Eval harness: 100 hand-labelled receipts, golden set, LLM-judge for fuzzy fields, CI gate on regression
- Cost telemetry via OpenTelemetry spans on every LLM call
- **Blog post #2: "Real evals for a Claude-powered receipt OCR pipeline"**
- DSA: dynamic programming
- Sys-design: DDIA chapters 10–12 (batch, streaming)

### Month 5 — Sep 2026 — SLOs and ops
- SLOs defined (POS write p99, sync convergence p95, OCR success rate, OCR p95 latency)
- Error budgets, alert routing, runbooks
- Grafana dashboards: golden signals + cost per receipt + cache hit rate
- On-call drill: deliberately break something, recover, write the post-mortem
- **Blog post #3: "Operating k3s on €20/month — observability and SLOs for a one-person team"**
- Begin mock interviews — system design only
- DSA: heaps, intervals, sliding window

### Month 6 — Oct 2026 — Frontend + live demo
- Vue 3 + Nuxt 3 frontend — POS view, inventory view, OCR upload view
- Seed data, public live demo deployed
- **Blog post #4: "Why I started Juragan as a monolith and extracted exactly one service"**
- Resume rewrite — quantify outcomes from project
- DSA: 5 mediums per week, sustained
- Sys-design: write own Juragan sys-design doc as interview cheat sheet (first draft)

### Month 7 — Nov 2026 — Polish + apply
- Hardening: edge cases, fraud signals, multi-tenancy isolation tests
- Cost telemetry dashboard finalized
- **Blog post #5: "Per-receipt cost telemetry — FinOps for an LLM feature"**
- Begin applications — JP first, then SG. LinkedIn cross-post all blog posts.
- DSA: hards, behavioural prep starts (STAR-format stories from Mekari + Juragan)
- Sys-design: refine cheat sheet, do 2–3 mock interviews per week

### Month 8 — Dec 2026 → Jan 2027 — Active interviewing
- Project frozen. No new features. Bugfixes only if asked.
- DSA hards sustained, behavioural drills daily
- Active interview loops; offer negotiation
- **Blog post #6 (optional):** retrospective — what worked, what didn't

## Non-negotiables

- Blog posts in **English**, cross-posted to LinkedIn. Recruiters in JP/SG/global find you here.
- Every architectural decision → ADR in `docs/adr/`.
- Project never sprawls beyond the 6-capability matrix.
- DSA + system design tracks run from week 1 — non-deferrable.
- **Senior signal beats feature count.** One feature with evals + observability + cost telemetry > five half-finished modules.

## Showcase outputs at month 8

- Public GitHub repo with clean commits, ADRs, README
- Live demo (juragan.\<your-domain>) with seed data
- 5–6 English blog posts
- One system-design write-up doc — your interview cheat sheet
- Quantified outcomes for every capability (latency, cost, eval scores, SLO attainment)
