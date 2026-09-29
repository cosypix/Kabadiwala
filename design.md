# design.md — ScrapSetu
> Visual and interaction design for a low-literacy, low-end-device audience. Applies to the collector app first; recycler/admin consoles reuse tokens but can be denser.

---

## 1. Design Principles
1. **See it, hear it, then read it.** Icon + 🔊 audio + short label on every actionable element.
2. **One job per screen.** One primary action, max 4 choices.
3. **Big, forgiving, high contrast.** Outdoor sun, cracked screens, dirty fingers.
4. **Money is always shown as ₹ + number** (Western numerals for clarity), with audio.
5. **No dead ends.** Every state has a way back and a "call for help" affordance.
6. **Calm, trustworthy tone.** Not gamified, never scary except for safety warnings.

---

## 2. Colour & Theme

| Token | Hex | Use |
|---|---|---|
| `--color-primary` | `#1B7F4B` | Primary actions, "go", recycling/green identity |
| `--color-primary-dark` | `#125C36` | Pressed state, headers |
| `--color-accent` | `#F2A900` | Money/price highlights, upward emphasis |
| `--color-danger` | `#C62828` | Hazard, stop, dispute |
| `--color-warning` | `#EF6C00` | Stale price, pending |
| `--color-info` | `#1565C0` | Sync, help |
| `--color-bg` | `#FAF8F2` | App background (warm off-white) |
| `--color-surface` | `#FFFFFF` | Cards |
| `--color-text` | `#1A1A1A` | Primary text |
| `--color-text-muted` | `#5A5A5A` | Secondary text (≥ 4.5:1 on bg) |
| `--color-border` | `#D9D4C7` | Dividers |

- **Dark mode**: optional, later. High-contrast light theme is default (better in sunlight).
- **Never use colour alone** to convey status: pair with icon + shape (✔ green circle, ⏳ amber, ✖ red).
- Material categories each get a colour **and** a pictogram (keep saturation moderate, colour-blind-safe pairs).

| Category | Colour hint |
|---|---|
| PCB | Green `#2E7D32` |
| Cables | Copper `#B26A00` |
| Batteries | Red-orange `#D84315` (hazard border) |
| CRT | Grey-blue `#546E7A` (hazard border) |
| LCD | Blue `#1E88E5` |
| Motors/magnets | Purple `#6A1B9A` |
| Plastics | Teal `#00897B` |
| Others | Brown-grey `#795548` |

---

## 3. Typography

| Role | Font | Notes |
|---|---|---|
| Devanagari (Marathi/Hindi) | **Noto Sans Devanagari** (subset, self-hosted, WOFF2, weights 400/700 only) | Consistent rendering on old Androids |
| Latin / numerals | **Noto Sans** subset (or system-ui) | Numerals tabular |
| Fallback stack | `"Noto Sans Devanagari", "Noto Sans", system-ui, sans-serif` | |

| Style | Size / line height | Weight |
|---|---|---|
| Display (price) | 40 / 48 px | 700 |
| Title | 28 / 36 px | 700 |
| Body | 18 / 28 px | 400 |
| Label | 16 / 24 px | 700 |
| Caption | 14 / 20 px | 400 (use sparingly) |

- Never below 16 px for anything actionable. Sentence case. Short words. Avoid English jargon; use everyday Marathi/Hindi terms trader-friendly ("भाव" for price, "कबाड़/भंगार" as locally natural, validate in field research).

---

## 4. Layout & Components

- **Grid**: single column, 16 px side padding, 8 px spacing scale (8, 16, 24, 32).
- **Touch targets**: ≥ 56 × 56 px; spacing between ≥ 12 px.
- **Bottom navigation (max 4)**: 🏠 Home (घर) · ➕ New Lot (नया माल) · 💰 Prices (भाव) · 📒 Ledger (खाता). Safety and language via Home/Settings.
- **Primary button**: full-width, 64 px height, icon left, label, 🔊 at right. Green fill.
- **IconTile**: 2-column grid of large tiles for categories (pictogram 64 px + label + colour bar).
- **WeightStepper**: big − / + buttons, tap-and-hold to change fast, quick chips (5, 10, 25, 50 kg), plus a slider; numeric keypad optional.
- **Condition picker**: 3–4 icon choices (working / broken / dismantled).
- **Price card**: category icon, median ₹/kg (Display size), range in small text, trend arrow (↑ ↔ ↓), "updated 2 days ago" chip, 🔊.
- **Recycler card**: name, verified badge (shield ✔), distance, quote ₹, pickup icon, reason chip ("Best price", "Nearest"), call button.
- **Status chips**: shapes + icon: ✔ Paid, ⏳ Pending, ⚠ Dispute, ☁↑ Waiting to sync.
- **Sync indicator**: small cloud icon in header; never blocks.
- **Handover screen**: big 6-digit code + QR, one-line instruction, audio auto-plays on request.
- **Confirmation dialogs**: two big buttons ✔ (green) / ✖ (red) with audio.

## 5. Iconography & Imagery
- Consistent line + fill pictograms, 2 px stroke, rounded corners; drawn for each material (real photo thumbnails inside the tile are a bonus once dataset exists).
- Safety cards use **illustrations with red ✖ / green ✔ overlays**: no text needed to understand (e.g., burning cable ✖, cutting wire outdoors with gloves ✔).
- Avoid culturally specific gestures/symbols that may mislead; test with collectors.

## 6. Audio & Voice UX
- 🔊 button on every screen and card. Tap = speak; long-press = repeat slower (optional).
- Voice style: friendly, calm, local dialect neutral; recorded by a native speaker.
- Number reading: composed from clips (e.g., "बावीस रुपये किलो").
- Auto-play only for: safety warnings, handover code, critical confirmations (respect a global mute).
- Provide captions (text) synchronized in large font.

## 7. Motion & Feedback
- Minimal motion; no infinite animations (battery/CPU).
- Immediate tactile feedback: press state, short vibration on success (if supported), tone for save success.
- Skeletons instead of spinners for lists; progress bars for uploads.

## 8. Language & Localization
- Language chosen on first launch by tapping flags/labels (मराठी · हिंदी · English) with audio greeting in each.
- All strings via i18n keys; length-tolerant layouts (Devanagari is taller).
- Numbers: Western numerals by default; optional Devanagari numerals toggle (test with users).
- Units: kg default; toggle for piece where relevant.

## 9. Accessibility Checklist
- Contrast ≥ 4.5:1 (key actions ≥ 7:1) · focus states · `aria-label` from i18n · works with 200% font scaling · no reliance on hover · no time-limited input · screen reader labels present even though primary audience uses audio.

## 10. Recycler & Admin Console Style
- Same tokens; desktop layouts with tables, filters, keyboard shortcuts.
- Font size 14–16 px allowed; density higher.
- Key screens: Incoming lots, Intake/Weigh, Rates, Payments, Traceability, Export.

## 11. Screen Inventory (collector)
1. Language select → 2. Area select (GPS optional) → 3. Home (big "New Lot", today's prices strip, pending dues) → 4. Photo capture → 5. Category confirm → 6. Weight & condition → 7. Estimate + Save → 8. Matches list → 9. Recycler detail → 10. Handover code → 11. Price accept → 12. Payment confirm → 13. Ledger → 14. Price board (category detail + trend) → 15. Safety library → 16. Settings/help.
