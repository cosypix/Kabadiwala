# memory.md — ScrapSetu Project Memory
> Living log. **Update after every work session or task.** AI assistants: read this first, update it last.

---

## Current Status
- **Date last updated:** 2026-09-28
- **CURRENT PHASE:** Phase 0 — Discovery & Documentation
- **File(s) currently being worked on:** `PRD.md` (review), field research plan
- **Blocked by:** nothing yet

---

## Completed
- [x] Problem statement analysed (SIH e-waste informal collector platform)
- [x] Engineering workflow defined (13 steps)
- [x] `PRD.md` drafted (domain, FR/NFR, use cases, BRs, workflows, ACs, traceability, unit-economics framework)
- [x] `architecture.md` drafted (stack, domain model, BCE, sequences, state models, datasets, sync, ML, API, folders)
- [x] `rules.md` drafted
- [x] `phases.md` drafted
- [x] `design.md` drafted
- [x] `memory.md` created
- [x] `AGENTS.md` created in root for AI context and token optimization

## In Progress
- [ ] Review docs with team; freeze v1 of PRD and architecture
- [ ] Book field visits (≥ 2 collectors/aggregators + 1–2 recyclers)

## Next Up (in order)
1. Field research: interviews, price logs, photos, vocabulary check (Marathi/Hindi terms)
2. Record audio clips script and pictogram sketches
3. Phase 1: monorepo, Docker Compose, Django models, seed data
4. Phase 2: auth, materials, recyclers, prices APIs

---

## Phase Checklist
| Phase | Status | Notes |
|---|---|---|
| 0 Discovery & docs | 🟡 In progress | Docs drafted; field research pending |
| 1 Setup & data | ⚪ Not started | |
| 2 Backend core | ⚪ Not started | |
| 3 Collector shell + offline lots | ⚪ Not started | |
| 4 Price board + audio | ⚪ Not started | |
| 5 Matching + recycler console | ⚪ Not started | |
| 6 Handover + payment + ledger | ⚪ Not started | |
| 7 ML & analytics | ⚪ Not started | |
| 8 Safety + language + optimisation | ⚪ Not started | |
| 9 Testing + pilot + demo | ⚪ Not started | |

Legend: ⚪ not started · 🟡 in progress · 🟢 done · 🔴 blocked

---

## Decisions Log
| Date | Decision | Reason | Impact |
|---|---|---|---|
| 2026-09-28 | Collector app = React PWA (optional Capacitor wrap) | Small size, offline, matches React choice | architecture.md §1 |
| 2026-09-28 | Backend = Django + DRF + PostgreSQL | ORM, admin, speed | architecture.md §1 |
| 2026-09-28 | Pre-recorded audio clips instead of device TTS | Works offline, quality in mr/hi | design.md §6 |
| 2026-09-28 | ML is suggestion-only; human confirms | Limited data, trust | rules.md §3 |
| 2026-09-28 | Collectors identified by random ID + PIN (no name/Aadhaar) | Privacy/minimization | BR-11, BR-19 |
| 2026-09-28 | Fees charged to recyclers/partners only | Collector trust | BR-14 |
| 2026-09-28 | Place all 6 core docs + AGENTS.md in root directory | Easy discovery and low token overhead | AGENTS.md, root structure |

## Open Questions
1. Which city/district is the pilot area? (decides area codes and recycler list)
2. Which authorized recyclers/aggregators can we contact for real rates? (need ≥ 2)
3. Do recyclers offer pickup, and at what minimum weight?
4. Real price ranges per category (₹/kg) from the field — needed to replace synthetic seed data.
5. Marathi/Hindi terminology validation with collectors (e.g., words for "lot", "rate", "handover").
6. Is UPI needed for the demo, or is cash-only acceptable? (stretch)
7. Public e-waste image datasets: which are usable and under what licences? Field photos are the priority.

## Known Issues / Risks
- None in code yet. Risks are tracked in `PRD.md` §15.

## Datasets Status
| Dataset | Source | Size now | Target | Quality notes |
|---|---|---|---|---|
| Material images | Field photos | 0 | ≥ 150–300 / class | – |
| Prices | Field + recycler calls (+ labelled synthetic) | 0 | ≥ 200 observations | – |
| Recyclers | Manual research | 0 | ≥ 5 | authorization verified? |
| Transactions | Pilot/demo | 0 | ≥ 50 (pilot) | – |

## Session Log
| Date | Who | What was done | Files touched | Next |
|---|---|---|---|---|
| 2026-09-28 | Team + Claude | Generated 6 project docs from problem statement | docs/* | Review + field research |
| 2026-09-28 | Team + Antigravity | Placed 6 docs + AGENTS.md in root directory with token router | ./*.md | Review + field research |

---

### How to update this file
1. Move finished items from **In Progress** to **Completed** (with date).
2. Set **CURRENT PHASE** and **file currently being worked on**.
3. Add any decision to the **Decisions Log**, and any new doubts to **Open Questions**.
4. Add one row to **Session Log** at the end of each session.
