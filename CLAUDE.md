# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the app

This is a zero-dependency static single-file app. Open `index.html` directly in a browser — no build step, no server, no npm.

To serve locally (useful for consistent behaviour across browsers):
```
python -m http.server 8080
# or
npx serve .
```

There are no tests, no linter, and no CI configuration.

## Architecture

Everything lives in `index.html`:
- **CSS** — custom properties (design tokens) in `:root`, then component styles; no framework
- **HTML** — seven `<section class="view">` placeholders that are rendered/swapped by JS
- **JavaScript** — vanilla ES6+, all inline in a single `<script>` block

### Data model

All data is hardcoded as JS arrays/objects at the top of the script:

| Variable | What it holds |
|---|---|
| `kpis` | Six top-level KPIs (GRP, inflation, budget, investment, export, unemployment) |
| `kpiQuarterValues` | Quarter-specific overrides for KPI display values |
| `sectors` | Six sector cards linking to a KPI, with tasks and metrics |
| `districts` | 16 districts of Andijan region with task counts |
| `districtKpis` | KPI breakdowns for specific districts (partial — only 4 districts populated) |
| `monitoring` | Monitoring registry rows (target vs actual, source, evidence status) |
| `tasks` | Task list sourced from the guarantee letter and Excel tables |
| `evidence` | Static evidence entries; user-submitted entries stored in `localStorage` under key `hm_v3_evidence` |
| `sources` | Source file registry mapping Excel filenames to sectors and processing notes |

### State and rendering

A single `state` object tracks the active view, selected quarter, filters, and selected district/sector. Every navigation action mutates `state` and calls `render()`, which re-renders only the active view section plus the nav and KPI bar.

Views: `command`, `sectors`, `districts`, `monitoring`, `tasks`, `evidence`, `sources`.

### Status colour system

Statuses use Uzbek labels that map to CSS classes via `cls()`:
- `Яшил` → `green` (on target)
- `Сариқ` → `amber` (watch)
- `Қизил` → `red` (behind)
- `Кулранг` → `grey` (no data)

Note: inflation, unemployment, and poverty are **lower-is-better** KPIs — their status logic is inverted compared to other indicators (see the Sources view "Import rules" section).

### Data files (not yet wired up)

`data/` contains Excel `.xlsx` files for all 14 regions of Uzbekistan, organised by region number and named by table type (`1.1-1.5-жадваллар (макро).xlsx`, `3-жадвал (бюджет).xlsx`, etc.). Currently the JS hardcodes only Andijan region data; the Excel files exist as the intended source of truth but are not dynamically imported.

### Localisation

All UI text is in Uzbek Cyrillic (`uz-Cyrl`). District and place names have spelling variants (e.g. `Марҳамат`/`Мархамат`, `Шаҳрихон`/`Шахрихон`) — normalisation rules are listed in the Sources view and must be respected when adding new data.
