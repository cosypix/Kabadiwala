# PRD — ScrapSetu (working name)
> A vernacular, low-literacy, offline-first bridge between informal e-scrap collectors and authorized recyclers.
> Covers workflow steps 1–7: Domain → Requirements → Use Cases → Business Rules → Workflows → Acceptance Criteria → Traceability.

---

## 1. Product Summary

**Problem.** Most Indian e-waste is collected informally. Collectors lack fair-price information, don't know which recyclers are authorized, and have no simple way to do a documented handover. So material goes to backyard processing (cable burning, acid leaching, manual desoldering): unsafe for workers, and critical materials (Li, Co, Nd, Ta, Ga, In) are lost.

**Solution.** A mobile-first platform where a collector can: photograph and log a lot → see an instant value estimate → see the fair price board (with audio) → get matched with nearby authorized recyclers → complete a verifiable handover → track cash/digital payment in a simple ledger. The formal route must be **more profitable and more convenient**, not a compliance burden.

**Product principles**
1. Formal route must pay better or equal, and be easier. No extra paperwork for collectors.
2. Picture + voice first, text second.
3. Works with no network. Sync later.
4. Cash is first-class; digital payment is optional.
5. Collect the minimum personal data.
6. Every lot is traceable from collection to recycler.

---

## 2. Target Users (Actors)

| Actor | Description | Device / Context | Key Need |
|---|---|---|---|
| **Collector** (primary) | Waste-picker / kabadiwala / door-to-door buyer. Limited literacy, Marathi/Hindi speaker. | Entry-level Android (≤2 GB RAM), patchy data | Best price, easy handover, proof of transaction |
| **Aggregator** | Small dealer who buys from several collectors and sells to recyclers. Can act as collector or as "buyer" to collectors. | Android + occasional laptop | Volume matching, price visibility |
| **Recycler Operator** | Staff of an authorized recycler / aggregator facility. | Desktop/tablet browser at the facility | Steady supply, verified lots, easy intake and weighing |
| **Platform Admin** | ScrapSetu ops team | Web | Verify recyclers, moderate prices, manage datasets, review anomalies |
| **Auditor / Regulator** (read-only, stretch) | SPCB / PRO / producer | Web | Traceability records |

---

## 3. Domain Understanding

### 3.1 Key Concepts (Glossary)
- **Lot** — a bundle of collected material a collector wants to sell. Contains one or more **Lot Items**.
- **Material Category / Sub-category** — CRT, LCD panel, PCB, cables, batteries, motors/magnet assemblies, mixed plastics, others (see `architecture.md` §Seed taxonomy).
- **Price Observation** — a recorded buying price (recycler-posted, collector-reported, or admin-verified).
- **Price Board** — the current fair price range per category and location.
- **Authorized Recycler / Aggregator** — facility with valid authorization/registration under E-Waste (Management) Rules, 2022. Verified by admin against the authorization number and expiry.
- **Quote** — a recycler's offer for a specific lot (rate × weight × condition factor).
- **Handover** — physical transfer of a lot from collector to recycler with a digital record.
- **Handover Record** — photos, weight, timestamp, GPS, unique reference, recycler confirmation.
- **Ledger** — collector's list of sales, payments received and pending dues.
- **Traceability chain** — Lot → Handover → Recycler confirmation → downstream processing status.

### 3.2 Domain Rules (external)
- E-Waste (Management) Rules, 2022 require EPR through authorized recyclers/PROs. Informal collectors are not themselves in that chain. ScrapSetu does **not** claim to make collectors "authorized"; it routes material to authorized recyclers.
- Hazardous items (lead-bearing CRTs, Li-ion batteries) need special handling.
- *(Verify with current CPCB/MoEFCC notifications before submission. Do not hard-code legal claims in the app.)*

### 3.3 Assumptions
- A1: Collectors own a basic Android phone; may share it.
- A2: Recyclers can post rates at least weekly (assumption to validate in field research).
- A3: Recycler authorization data can be entered/verified manually by admin (no dependency on scraping government sites).
- A4: Sufficient real image data will not exist on day 1, so we start with a small collected dataset + human-confirmed classification.

---

## 4. Scope

**In scope (MVP)**: Collector PWA/app, Recycler console, Admin console (light), backend, datasets, price board + audio, matching, handover, ledger, safety, Marathi + Hindi, offline sync, basic ML (classification, anomaly flags, price trend).

**Stretch**: Aggregator-as-buyer mode, UPI payment link, downstream processing status updates, auditor view, additional languages, voice input.

**Out of scope**: Payment gateway integration as mandatory, KYC of collectors, logistics/fleet routing, EPR certificate issuance.

---

## 5. Functional Requirements

| ID | Requirement | Problem-statement source |
|---|---|---|
| FR-01 | Collector creates a lot: photo(s), category (pictorial picker), sub-category, approx weight, condition, source type; works offline | Bullet 1 |
| FR-02 | Instant value estimate from category, weight, condition, latest local price range | Bullet 1 |
| FR-03 | Price board per category and location: median, range, last updated, 7/30-day trend, spoken price (audio) | Bullets 2, 6 |
| FR-04 | Price dataset: capture recycler-posted, collector-reported and verified prices with date, unit, location | Bullet 2 |
| FR-05 | Material + transaction datasets with the fields listed in the problem statement | Bullet 3 |
| FR-06 | Recycler registry with authorization details/status, materials accepted, rates, pickup, service area | Bullet 4 |
| FR-07 | Match and rank authorized recyclers for a lot (location, category, rate, pickup, authorization) | Bullets 4, 7 |
| FR-08 | Verifiable handover record: photos, weight, timestamp, GPS, unique reference, recycler confirmation | Bullet 8 |
| FR-09 | Earnings ledger: transactions, payments, pending dues | Bullet 9 |
| FR-10 | Pictorial/audio safety guidance (burning, opening, batteries, CRTs) | Bullet 10 |
| FR-11 | Marathi + Hindi UI; low-literacy: icons, audio on every screen, minimal text | Bullet 11 |
| FR-12 | Offline-first: create lot, view cached prices/recyclers, record handover offline; auto sync | Bullet 12 |
| FR-13 | Cash payments supported; digital payment optional | Bullet 13 |
| FR-14 | ML: material classification, valuation, recycler matching support, anomaly detection | Bullet 5 |
| FR-15 | Recycler console: profile, rates, incoming lots, confirm/reweigh, record payment, export | Expected outcome |
| FR-16 | Admin console: verify recyclers, moderate prices, review anomalies, export datasets | Dataset reqs |
| FR-17 | Minimal collector profile (random ID, language, area). No mandatory name/Aadhaar | Dataset reqs |
| FR-18 | Dataset lifecycle: generate, validate, clean, anonymize, version, feed models | Dataset reqs |
| FR-19 | Impact/unit-economics dashboard (collector earnings vs baseline, platform revenue) | Expected outcome |

## 6. Quality Requirements (NFR)

| ID | Requirement | Target |
|---|---|---|
| NFR-01 | Device support | Android 8+, 2 GB RAM, low-end CPU |
| NFR-02 | Size | Initial load ≤ 300 KB gzipped JS; total install ≤ 10 MB excl. optional ML/audio packs; ML model ≤ 5 MB int8 |
| NFR-03 | Offline | All core collector flows work offline; sync on reconnect with no data loss |
| NFR-04 | Task speed | New lot created in ≤ 90 sec, ≤ 6 taps beyond photo/weight |
| NFR-05 | Usability | ≥ 80% of test users with low literacy complete "create lot → see matches" unaided |
| NFR-06 | Performance | Screen response ≤ 300 ms offline; API p95 ≤ 800 ms on 3G |
| NFR-07 | Security | HTTPS, token auth, role-based access, tamper-evident handover hash |
| NFR-08 | Privacy | Data minimization, no precise home address, coarse location for public data, anonymized dataset exports |
| NFR-09 | Data quality | Schema validation at capture + server; outlier and duplicate checks |
| NFR-10 | Reliability | Idempotent sync; no duplicated lots/payments |
| NFR-11 | Maintainability | Typed code, tests for business rules, migrations, docs in sync |
| NFR-12 | Battery/data | Image compression ≤ 150 KB; sync batches; Wi-Fi/low-data mode |

---

## 7. Use Cases

| ID | Use Case | Actor | Summary |
|---|---|---|---|
| UC-01 | Onboard | Collector | Choose language, area (village/ward picker, GPS optional). Auto-generated collector ID. |
| UC-02 | Create lot | Collector | Photo → category → weight → condition → estimate → save (offline OK). |
| UC-03 | View price board | Collector | See per-category prices, hear them, view trend. |
| UC-04 | Find recycler | Collector | Ranked list of authorized recyclers with quote, distance, pickup. |
| UC-05 | Initiate handover | Collector | Choose recycler → show handover code/QR → go or request pickup. |
| UC-06 | Confirm handover | Recycler Op | Scan/enter code → reweigh → photo → confirm final price. |
| UC-07 | Record payment | Recycler + Collector | Recycler marks paid (cash/UPI); collector confirms received. |
| UC-08 | View ledger | Collector | Sales, received, pending dues. |
| UC-09 | Safety guidance | Collector | Pictorial + audio lessons; shown contextually for batteries/CRTs. |
| UC-10 | Sync | System | Push outbox, pull updates, resolve conflicts. |
| UC-11 | Manage rates & profile | Recycler Op | Post rates per category, accepted materials, service area, pickup. |
| UC-12 | Verify recycler | Admin | Validate authorization number/expiry; set status. |
| UC-13 | Review anomalies | Admin | Inspect flagged prices, weights, duplicate photos. |
| UC-14 | Raise dispute | Collector/Recycler | Flag mismatch; lot moves to DISPUTED. |
| UC-15 | Update downstream status | Recycler Op | Mark lot as processed/dispatched (traceability). |
| UC-16 | Report price | Collector | "What did the local dealer pay you?" → adds a price observation. |

---

## 8. Business Rules

| ID | Rule |
|---|---|
| BR-01 | Only recyclers with `authorization_status = ACTIVE` and unexpired authorization are matchable. |
| BR-02 | A recycler must accept the lot's category and cover the collector's location (service area or within radius). |
| BR-03 | Ranking score = weighted net value (rate × weight × condition), distance, pickup availability, freshness of rate. Weights are configurable. Ranking must be explainable ("best price", "nearest"). |
| BR-04 | A quote is valid for 48 h (configurable); expired quotes are recomputed at handover. |
| BR-05 | On handover, the recycler's scale weight is authoritative. If |recycler − collector| ≤ 10%, the price recomputes automatically. If more, the collector must accept or dispute. |
| BR-06 | Final value = confirmed weight × agreed rate × condition factor. The collector must see it before payment is recorded. |
| BR-07 | Handover is complete only when the recycler confirms and the collector acknowledges (or 24 h auto-ack, with dispute right for 7 days). |
| BR-08 | Once a handover is confirmed, lot data is immutable; corrections are appended as adjustments. |
| BR-09 | Cash payment requires two-sided confirmation (recycler "paid", collector "received"). Otherwise it remains `PENDING`/`DISPUTED`. |
| BR-10 | Batteries and CRT lots require the safety card to be viewed/acknowledged before handover scheduling. |
| BR-11 | Price board shows a range and median with a "last updated" stamp. Data older than 14 days is marked stale. |
| BR-12 | Price source weights: admin-verified > recycler-posted > collector-reported. Collector reports count only after outlier check. |
| BR-13 | Anomalies are flagged, not blocked. Admin reviews. |
| BR-14 | Platform fees are charged to recyclers or partners, never deducted from collectors. |
| BR-15 | Lot IDs are generated on-device (collision-safe), unique globally. |
| BR-16 | Min 1 photo per lot; images compressed; EXIF stripped except timestamp/coarse GPS. |
| BR-17 | If GPS is unavailable, fall back to village/ward picker and flag `location_accuracy = COARSE`. |
| BR-18 | Collector may sell to any recycler, or not at all. The platform never forces a match. |
| BR-19 | Public/exported datasets never include collector phone, name, or precise location. |
| BR-20 | Sync conflicts: server-side state machine wins for status and recycler-owned fields; collector-owned draft fields are last-write-wins by client timestamp. |

---

## 9. Key Workflows

**W1 — Sell a lot (happy path)**
1. Collector opens app (offline OK) → taps big **＋** button.
2. Takes photo → app suggests category (top-3, human-confirms) → picks/edit category.
3. Enters approx weight via big stepper/slider (kg) → chooses condition (icons).
4. App shows instant value estimate (audio + text).
5. Saves lot (`LISTED`). Queued for sync.
6. When online (or cached): match list shows ranked authorized recyclers.
7. Collector picks recycler → sees handover code/QR + directions or requests pickup.
8. At facility: recycler scans/enters code → reweighs → photo → confirms price.
9. Collector taps ✔ to accept the price.
10. Recycler records cash payment → collector taps "received".
11. Ledger updates; lot `COMPLETED`.

**W2 — Offline handover.** Both sides may be offline; handover is recorded locally with a signed payload/hash and code; syncs when either side connects (see `architecture.md`).

**W3 — Price freshness loop.** Recyclers post rates → admin moderates → price board updates → collectors report actual prices → outlier check → dataset grows → trend model retrains weekly.

**W4 — Recycler onboarding.** Register → upload authorization number/proof → admin verifies → status `ACTIVE` → sets rates/service area → visible to matching.

**W5 — Anomaly review.** System flags (price outlier / weight mismatch / duplicate photo) → admin queue → resolve/annotate → feedback into training data.

---

## 10. Acceptance Criteria (Given / When / Then)

| ID | Criteria | Covers |
|---|---|---|
| AC-01 | Given the phone is offline, when the collector saves a lot with photo, category, weight, then it persists locally with status `LISTED`, sync state `PENDING`, and a unique lot ID. | FR-01, FR-12, BR-15 |
| AC-02 | Given a lot is saved, when weight and category are set, then an estimate appears within 1 s using the latest cached price range, showing low–high value. | FR-02 |
| AC-03 | Given the price board, when the collector taps the speaker icon on a category, then the price (median and range) is spoken in the selected language, offline. | FR-03, FR-11 |
| AC-04 | Given ≥ 7 days of observations, when opening a category, then a 7-day trend arrow (up/flat/down) appears with a last-updated date. | FR-03 |
| AC-05 | Given recyclers with mixed statuses, when matching runs, then only `ACTIVE` unexpired recyclers that accept the category and cover the location are returned, ranked per BR-03, each with a reason label. | FR-07, BR-01/02/03 |
| AC-06 | Given a recycler with expired authorization, when a match is requested, then it never appears. | BR-01 |
| AC-07 | Given a handover, when the recycler enters the code, then a record is created with photos, weight, timestamp, GPS (or coarse flag), and reference number, visible to both. | FR-08 |
| AC-08 | Given recycler weight differs > 10%, then the collector sees an accept/dispute prompt (pictorial + audio). | BR-05 |
| AC-09 | Given cash payment, when only one side confirms, then status stays `PENDING` and appears under pending dues. | BR-09, FR-09 |
| AC-10 | Given a lot with batteries, when the collector selects a recycler, then the safety card must be viewed before proceeding. | BR-10, FR-10 |
| AC-11 | Given intermittent connectivity, when sync runs twice with the same batch, then no duplicate lots/handovers/payments exist. | NFR-10 |
| AC-12 | Given a price 3× above the local median, then it's flagged `ANOMALY` and appears in the admin queue, and is excluded from the price board median until reviewed. | FR-14, BR-13 |
| AC-13 | Given the app language is set to Marathi, then all UI text, audio and safety content are in Marathi with no untranslated keys. | FR-11 |
| AC-14 | Given a mid-range test device (2 GB RAM, Android 8), the app cold-starts in ≤ 4 s and stays under 150 MB RAM. | NFR-01/02 |
| AC-15 | Given exported datasets, then they contain no name/phone/precise coordinates. | BR-19, FR-18 |
| AC-16 | Given 2 field collectors test the app, ≥ 80% complete W1 without help. | NFR-05 |

---

## 11. Traceability Matrix

| Problem Statement Item | FR | UC | BR | AC | Phase (`phases.md`) |
|---|---|---|---|---|---|
| Photograph/categorize/lots + instant estimate | FR-01, 02 | UC-02 | BR-15, 16 | AC-01, 02 | 3, 4 |
| Price discovery + historical dataset | FR-03, 04 | UC-03, 16 | BR-11, 12 | AC-03, 04, 12 | 2, 4 |
| Material + transaction dataset | FR-05, 18 | UC-02, 05–07 | BR-08, 19 | AC-15 | 1, 6 |
| Authorized recycler dataset + ranking | FR-06, 07 | UC-04, 11, 12 | BR-01–04 | AC-05, 06 | 2, 5 |
| AI/ML features | FR-14 | UC-02, 04, 13 | BR-13 | AC-12 | 7 |
| Price board with spoken info + trends | FR-03, 11 | UC-03 | BR-11 | AC-03, 04 | 4 |
| Matching | FR-07 | UC-04 | BR-01–04, 18 | AC-05 | 5 |
| Verifiable handover record | FR-08 | UC-05, 06 | BR-05–08 | AC-07, 08 | 6 |
| Earnings ledger | FR-09 | UC-07, 08 | BR-09 | AC-09 | 6 |
| Safety guidance | FR-10 | UC-09 | BR-10 | AC-10 | 8 |
| Marathi + Hindi + low literacy | FR-11 | all | – | AC-13, 16 | 3, 8 |
| Offline-first | FR-12 | UC-10 | BR-20 | AC-01, 11 | 3 |
| Low-end Android | NFR-01, 02 | – | – | AC-14 | 8 |
| Cash-first | FR-13 | UC-07 | BR-09 | AC-09 | 6 |
| Recycler-side interface | FR-15 | UC-06, 11, 15 | BR-05 | AC-07 | 5 |
| Field research + demo + unit economics | FR-19 | – | BR-14 | AC-16 | 0, 9 |

---

## 12. Field Research Plan (≥ 2 collectors/aggregators)
- Interview 3–5 collectors/aggregators (Marathi/Hindi). Capture: items handled, current buying/selling prices per kg, who they sell to, pain points, phone/data usage, literacy, trust barriers.
- Observe one full collection-to-sale cycle. Photograph items (with consent) for the image dataset.
- Run a paper/clickable prototype test before building. Re-test with the working app.
- Log everything in `data/field_research/` (consent noted, no names).

## 13. Unit Economics Assessment (framework)

**Collector view (per month)**
`Baseline = Σ (kg_i × informal_price_i)`
`With ScrapSetu = Σ (kg_i × formal_price_i × grade_uplift) − travel/extra cost`
`Uplift % = (With − Baseline) / Baseline`

**Illustrative example — hypothetical numbers, replace with field data:**
| | Informal (baseline) | ScrapSetu |
|---|---|---|
| Volume | 100 kg | 100 kg |
| Blended price | ₹20/kg | ₹26/kg (proper grading + direct recycler rate) |
| Monthly revenue | ₹2,000 | ₹2,600 |
| Extra travel | – | ₹100 |
| Net | ₹2,000 | ₹2,500 (+25%) |

**Platform sustainability (revenue is never taken from collectors, BR-14)**
1. Per-kg or per-lot success fee paid by recyclers (they gain compliant, pre-graded, traceable supply).
2. Partnerships with PROs/producers who need verified collection data for EPR reporting.
3. Anonymized price/market insight subscriptions.
4. CSR / grants for onboarding and safety training.

**Break-even sketch:** `Monthly cost ≈ hosting + support + field ops`; `Break-even kg = Monthly cost / fee per kg`. Model 3 scenarios (conservative / base / optimistic) in `data/unit_economics.xlsx`.

## 14. Success Metrics
- % of lots that end at an authorized recycler
- Avg collector price uplift vs baseline
- Time to create lot; % lots created offline and synced OK
- Price board freshness (% categories updated ≤ 7 days)
- Handover confirmation rate; dispute rate
- Classification top-3 accuracy on field photos
- Usability test completion rate

## 15. Risks
| Risk | Mitigation |
|---|---|
| Recyclers don't post rates regularly | Admin seeds initial rates from calls; make rate posting a 2-tap action; SMS/WhatsApp-style reminders (stretch) |
| Collectors distrust the app | Cash-first, no fees, field co-design, voice explanations |
| Little training data for ML | Human-in-the-loop, rule-based fallbacks, honest reporting of dataset size/limits |
| Recyclers offer low rates | Show price range transparently; onboard multiple recyclers per area |
| Fake authorization details | Admin verification with expiry; audit log |
| Weak GPS/offline | Coarse location fallback; queued sync |
