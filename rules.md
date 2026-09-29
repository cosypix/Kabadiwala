# rules.md — ScrapSetu
> Guardrails for every developer and every AI coding assistant working in this repo.
> Read this file, `memory.md`, and the current phase in `phases.md` before starting any task.

---

## 0. Working Protocol (for AI assistants)
1. Read `memory.md` first. Work only on the **current phase** and **current file(s)** listed there.
2. Before coding, state in 2–3 lines what you will change and which requirement IDs (FR/BR/AC) it serves.
3. Make small, reviewable changes. One feature per commit.
4. **Do not** invent APIs, libraries, or fields. If something is missing from `architecture.md`, propose the change first, then update the doc.
5. **Ask before** adding any dependency, changing the DB schema, or changing folder structure.
6. Never rewrite unrelated files. Never delete tests to make them pass.
7. After finishing, update `memory.md` (done / in progress / next / decisions).
8. If a requirement conflicts with another, stop and flag it. Don't silently pick one.

---

## 1. What to USE

### Collector app (`apps/collector`)
- React 18 + TypeScript (**strict**) + Vite. Function components + hooks.
- State: React state + **Zustand** (small) for global; server/cached data through repository functions on **Dexie**.
- Routing: `react-router` (lazy-loaded routes).
- Forms/validation: **zod** schemas from `packages/shared`. No heavy form libraries.
- i18n: **i18next**. Every user-visible string is a key.
- Styling: plain CSS variables + CSS Modules (or a very small utility layer). Design tokens from `design.md`.
- Icons: inline SVG sprite / small icon set. Prefer custom pictograms for materials.
- PWA: Workbox, `vite-plugin-pwa`.
- ML: TF.js (lazy, dynamic import) or ONNX Runtime Web; model files loaded on demand.
- Image compression: `browser-image-compression` or canvas-based own utility.
- Audio: native `HTMLAudioElement` + clip manifest.

### Backend (`backend/`)
- Django + DRF: `ModelViewSet`/`GenericAPIView`, serializers for **all** validation, permission classes per role.
- ORM only. Migrations for every schema change. UUID primary keys for synced entities.
- `django-filter`, `drf-spectacular` (OpenAPI), `djangorestframework-simplejwt`.
- Geo: PostGIS if available, else Haversine util in `common/geo.py`.
- Celery (or simple management commands + cron for MVP).
- Type hints everywhere. `ruff` + `black`.

### ML
- Python: pandas, scikit-learn, (LightGBM optional), TensorFlow/Keras or PyTorch **only** for classifier training. Export to TFJS/ONNX int8.
- Every model has `MODEL_CARD.md` (data, metrics, limits, version).

### Tooling
- ESLint + Prettier, Vitest, Testing Library, Playwright, pytest.
- Conventional commits (`feat:`, `fix:`, `docs:`, `test:`, `chore:`).
- Config through `.env` (never commit secrets).

---

## 2. What to AVOID

| Avoid | Reason / Use instead |
|---|---|
| `localStorage` for app data | Not structured/limited → IndexedDB (Dexie). Allowed only for tiny flags (language). |
| Heavy UI kits (MUI, Ant, Bootstrap full), moment, full lodash, chart libs > 30 KB | Bundle budget. Use small custom components; date-fns subset; tiny SVG sparkline. |
| Text-only UI or icon-only without label/audio | Low literacy. Use icon + short label + audio. |
| Hard-coded strings (Marathi/Hindi/English) in components | Use i18n keys. |
| Requiring login with email/password for collectors | Use device-bound ID + PIN. |
| Collecting name, Aadhaar, exact home address, contacts | Data minimization (BR-11/19). |
| Making network calls without an offline path | Every feature needs offline behaviour defined. |
| Blocking UI on network or ML | Async with visible progress; fallbacks. |
| Mandatory digital payment paths | Cash-first (FR-13). |
| Deducting fees from collectors | BR-14. |
| Storing raw photos with full EXIF | Strip EXIF. |
| Dumping DB state through API without permission checks | Role-based permissions on every endpoint. |
| Using AI classification as the final decision | Always human-confirmed; show confidence; manual fallback. |
| Silent failures / `except: pass` / empty `catch` | See §3. |
| Global mutable singletons, business logic inside React components or DRF views | Put rules in controllers/services (BE) and feature hooks/services (FE). |
| Adding deps "just in case" | Justify size and need. |
| Claiming legal facts about authorization in UI copy | Keep legal text reviewed and sourced. |

---

## 3. Error Handling Boundaries

**Principle:** errors are handled at boundaries, translated into user-friendly pictorial + audio messages, and logged with context.

### Frontend
- Wrap route trees in an **ErrorBoundary** → friendly screen: icon + short text + audio + "Try again".
- Data layer functions return `Result<T, AppError>` (no thrown strings). `AppError { code, messageKey, retryable }`.
- Network errors → **never** shown as failures if offline mode covers the action. Show "Saved, will send later" (icon: cloud with clock).
- Validation errors: inline, one field at a time, spoken.
- ML errors/timeouts (> 3 s) → fall back to manual category picker silently.
- Sync failures: exponential backoff (max 5 attempts, then park with visible "needs attention" marker). Never drop ops.
- Never display stack traces or raw server messages.

### Backend
- All errors return `{ "code": "LOT_STATE_INVALID", "message_key": "errors.lot.state_invalid", "details": {...} }`.
- Use custom exception handler; map `ValidationError` → 400, permission → 403, not found → 404, conflict → 409.
- Business rule violations raise domain exceptions from services (`BusinessRuleError(BR-05)`), not generic exceptions.
- Every write endpoint is idempotent by `op_id`.
- Wrap DB writes in `transaction.atomic()`.
- Log with request ID; no PII in logs.

### AI/ML boundaries
- Model outputs are **suggestions**, wrapped in `{label, confidence, model_version}`.
- Confidence < threshold → show "not sure", ask user to pick.
- Anomaly detectors **flag**, never auto-reject (BR-13).
- Never let ML output override authorization checks, payment status, or state transitions.

---

## 4. Coding Conventions
- TS: `strict: true`, no `any` without comment; prefer discriminated unions for state.
- Naming: components `PascalCase`, hooks `useX`, DB tables `snake_case`, API JSON `snake_case`.
- Files ≤ ~300 lines; split by feature.
- Each feature folder: `components/`, `hooks/`, `service.ts`, `types.ts`, `__tests__/`.
- Backend layering: `views.py` (thin) → `services.py` (business rules) → `models.py`. Selectors for reads.
- Comments explain **why**, referencing rule IDs (`# BR-05`).
- Business rules in code must have a unit test named after the rule ID (`test_br05_weight_tolerance`).

## 5. Performance Budgets (fail CI if exceeded)
- Initial JS ≤ 300 KB gzip; each lazy route ≤ 80 KB gzip.
- Model ≤ 5 MB; audio pack per language ≤ 4 MB (downloadable, cacheable).
- Lighthouse PWA ≥ 90, Performance ≥ 80 on mid-tier throttled profile.
- Photo upload ≤ 150 KB each after compression.

## 6. Accessibility / Low-literacy Rules
- Min touch target 56 × 56 px; base font 18 px; contrast ≥ 4.5:1 (7:1 for key actions).
- Every screen: one primary action, ≤ 4 choices visible, icon + label + 🔊 audio button.
- No timeouts that erase user input.
- Confirmations use pictorial ✔/✖ with audio.

## 7. Testing Rules
- Every FR has ≥ 1 automated test; every BR has a unit test; every AC has a test or a documented manual test script.
- Offline tests: simulate offline with Playwright/service worker mocks.
- Test data must be labelled synthetic; no real personal data in the repo.

## 8. Git & Docs
- Branch per phase/feature: `phase-3/lot-capture`.
- PR checklist: linked FR/BR/AC, tests passed, docs updated, `memory.md` updated.
- Docs are code: change to schema/API/flows ⇒ update `architecture.md` in the same PR.

## 9. Definition of Done
Feature works offline (if applicable) · i18n keys in mr/hi/en · audio hooked or ticketed · tests pass · budgets OK · no console errors · `memory.md` updated.
