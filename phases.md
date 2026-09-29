# phases.md — ScrapSetu
> Breaks the project into phases mapped to your workflow steps (implementation = steps 12–13).
> Only work on the phase marked **CURRENT** in `memory.md`. A phase is done only when its **exit criteria** pass.

---

## Phase overview

| # | Phase | Workflow step | Output |
|---|---|---|---|
| 0 | Discovery, field research, docs | 1–10 | 6 docs, interview notes, price logs, first field photos |
| 1 | Project setup + data foundation | 11 | Monorepo, DB models, seed data, CI |
| 2 | Backend core | 12 | Auth, materials, recyclers, prices APIs |
| 3 | Collector app shell + offline lots | 12 | PWA, Dexie, i18n, lot capture, outbox |
| 4 | Price board, valuation, audio | 12 | Price board, estimate, trend, spoken prices |
| 5 | Matching + recycler console | 12 | Ranking, recycler UI, admin verification |
| 6 | Handover, payment, ledger | 12 | Verifiable handover, cash flow, ledger |
| 7 | ML & analytics | 12 | Classifier, anomaly, trend model, dataset cards |
| 8 | Safety, language polish, low-end optimisation | 12 | Safety cards, mr/hi audio, budgets |
| 9 | Testing, field pilot, demo, unit economics | 13 | Test reports, usability results, deck |

---

## Phase 0 — Discovery & Documentation (runs in parallel with everything)
**Tasks**
- Finalise 6 docs (this set).
- Field research with ≥ 2 collectors/aggregators + 1–2 recyclers (see PRD §12).
- Collect price list per category (₹/kg) from at least 2 sources; photograph materials with consent.
- Record Marathi + Hindi audio: numbers 0–100, "hundred/thousand", "rupees", "per kilo", material names, key phrases.
- Draw pictograms for the material categories and safety cards (paper sketches OK first).

**Exit criteria**: interview notes stored; first price dataset (≥ 30 observations); ≥ 100 labelled photos; audio script ready.

---

## Phase 1 — Setup & Data Foundation
**Tasks**
- Monorepo (`pnpm` workspaces) + `backend/` Django + Postgres via Docker Compose.
- Django models for all entities in `architecture.md` §3 & §6, migrations.
- Seed fixtures: categories/sub-categories, sample areas, 5–8 sample recyclers, price history (real + labelled synthetic).
- `packages/shared`: TS types + zod schemas (generated from OpenAPI when possible).
- Lint/format/test CI.

**Files**: `backend/apps/*/models.py`, `data/seeds/*`, `packages/shared/*`, `infra/docker-compose.yml`.
**Exit**: `docker compose up` works; migrations + seeds load; model unit tests pass.

---

## Phase 2 — Backend Core
**Tasks**
- Auth (collector register with PIN, JWT; recycler/admin login).
- Endpoints: categories, areas, prices (board/history/report), recyclers (list/profile/rates).
- Price aggregation service (rolling median/range, staleness) — BR-11, BR-12.
- Admin: verify recycler authorization — BR-01.
- OpenAPI docs.

**Exit**: API tests pass; AC-06, AC-12 (price part) covered.

---

## Phase 3 — Collector App Shell & Offline Lots
**Tasks**
- PWA scaffold, service worker, manifest, install prompt.
- Dexie schema; repositories; outbox.
- i18n (`mr`, `hi`, `en`) + language picker on first launch.
- Design system components (BigButton, IconTile, AudioButton, WeightStepper).
- Lot capture flow: camera/photo → category picker → weight → condition → save offline.
- Sync worker: push/pull with idempotency.
- Backend: lots + sync endpoints.

**Exit**: AC-01, AC-11 pass; airplane-mode demo works.

---

## Phase 4 — Price Board, Valuation, Audio
**Tasks**
- Price board screen (per category, area), cached offline.
- Value estimate service (`ValuationService`), range display.
- 7/30-day trend + sparkline.
- Audio composer: number → clip sequence (mr/hi).
- "Report price" (UC-16).

**Exit**: AC-02, AC-03, AC-04 pass.

---

## Phase 5 — Matching & Recycler Console
**Tasks**
- `MatchingService` + `RankingPolicy` (filters BR-01/02, score BR-03) + explainable reason labels.
- Match list screen; recycler detail (distance, quote, pickup, call button).
- Recycler console: register, rates, service area, incoming lots, profile.
- Admin queue for recycler verification.

**Exit**: AC-05, AC-06 pass; recycler can update a rate and see it reflect on the collector price board after sync.

---

## Phase 6 — Handover, Payment, Ledger
**Tasks**
- Handover code/QR; recycler intake (scan/enter code, reweigh, photos, GPS).
- BR-05 weight tolerance, accept/dispute UI.
- Integrity hash + reference number; handover record view (shareable image/PDF).
- Cash payment two-sided confirmation; optional UPI link (stretch).
- Ledger screen (sales, received, pending).

**Exit**: AC-07, AC-08, AC-09 pass; full W1 demo end-to-end.

---

## Phase 7 — ML & Analytics
**Tasks**
- Build dataset from field photos; `DATASET_CARD.md`; train/export classifier; on-device integration with top-3 UX and fallback.
- Anomaly detection (rules → Isolation Forest), admin anomaly queue (UC-13).
- Trend model (moving avg → optional Prophet).
- Impact dashboard: collector uplift vs baseline, platform revenue model.
- `MODEL_CARD.md` for each model.

**Exit**: metrics reported on **real** field images; AC-12 fully passes; classifier ≤ 5 MB and works offline.

---

## Phase 8 — Safety, Language & Low-End Optimisation
**Tasks**
- Safety cards (pictorial + audio): open burning, acid leaching, battery handling, CRT handling, opening/dismantling.
- Contextual safety prompt for batteries/CRT (BR-10).
- Complete mr/hi strings and audio review with native speakers.
- Performance work: code-splitting, image budgets, memory profiling on a low-end device.

**Exit**: AC-10, AC-13, AC-14, AC-15 pass; budgets in `rules.md` §5 met.

---

## Phase 9 — Testing, Field Pilot & Submission
**Tasks**
- Full regression, offline tests, load test on sync.
- Usability sessions with ≥ 2 real collectors/aggregators (task success, time, confusion points).
- Live demo script + fallback recording.
- Unit-economics workbook filled with field numbers.
- Submission deck: problem → approach → demo → datasets → impact → sustainability.

**Exit**: AC-16 recorded; demo rehearsed twice; docs frozen.

---

## MVP cut-line (if time is short, e.g. 36-hour finale)
**Must show (demo-critical path):**
Offline lot creation → price board with audio → ranked authorized recyclers → handover code + recycler confirmation → ledger entry.
**Then add, in order:** anomaly flag → classifier suggestion → safety card → trend arrows → recycler rate update.
**Fake honestly if needed:** seed data can be synthetic if labelled; never claim ML accuracy you haven't measured.
