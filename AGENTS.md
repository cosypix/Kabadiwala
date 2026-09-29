# AGENTS.md — ScrapSetu Agent Operating Guide

> **Primary Directive:** Maintain zero token waste. Do NOT load all project specifications into context at once. Follow the on-demand routing protocol below.
> **Repository Context:** Project workspace `Kabadiwala`, product name **ScrapSetu**.

---

## 1. Operating Protocol (Every Prompt)

1. **Check State First:** Read [`memory.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/memory.md) to inspect:
   - Current Phase & active task
   - File(s) currently being worked on
   - Blockers & recent decisions
2. **Selective Doc Loading:** Read *only* the specific spec file relevant to the immediate sub-task (see Router below).
3. **Pre-flight Statement:** Before writing code, state in 2–3 lines:
   - What you are modifying
   - Which requirement IDs (`FR-xx`, `BR-xx`, `AC-xx`) are being addressed
4. **Execution Guardrails:** Follow strict boundaries in [`rules.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/rules.md) (no unapproved dependencies, no schema surprises, offline-first, cash-first, data minimization).
5. **Session Wrap-up:** Update [`memory.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/memory.md) before completing the turn (move items to Completed, record decisions, update next steps).

---

## 2. On-Demand Doc Router

Load *only* the file needed for your active task:

| If working on... | Read this file | Purpose |
|---|---|---|
| **Status, Phase, Session Log** | [`memory.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/memory.md) | Current phase, open questions, session history |
| **Phase Scope & Roadmap** | [`phases.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/phases.md) | Phase exit criteria, MVP cut-line, workflow mapping |
| **Coding Standards & Guardrails** | [`rules.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/rules.md) | Allowed/forbidden tech, error boundaries, test requirements |
| **Data Models, Architecture, APIs** | [`architecture.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/architecture.md) | Domain models, BCE, sequence diagrams, state machines, API endpoints |
| **UI, Styling, Audio, Low-Literacy** | [`design.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/design.md) | Design tokens, Devanagari typography, pictograms, audio UX |
| **Requirements & Acceptance Tests** | [`PRD.md`](file:///c:/Users/abhin/OneDrive/Desktop/sih/Kabadiwala/PRD.md) | FRs, NFRs, Business Rules (`BR-01` to `BR-20`), ACs (`AC-01` to `AC-16`) |

---

## 3. Core Anti-Patterns (Immediate Failures)

- ❌ Inventing unapproved libraries or DB columns without prior approval.
- ❌ Storing PII (names, Aadhaar, exact locations) for collectors (violates BR-11/19).
- ❌ Building network-only features without offline fallback (violates FR-12 / NFR-03).
- ❌ Text-only UI or English jargon without vernacular audio and pictograms (violates FR-11).
- ❌ Modifying files outside the current phase defined in `memory.md`.
