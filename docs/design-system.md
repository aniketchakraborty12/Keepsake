# Keepsake — Design System

**Status:** v0.1 (Phase 0)
**Related:** `product-rules.md`, `wireframes.md`

Single source of truth for the visual language. Hex values live only here; the
database stores color **token names** (`occasion_types.color`), and Tailwind
maps tokens to CSS variables in Phase 1 (light/dark switch via a root
`data-theme` attribute).

## 1. Principles

- Warm, understated, personal — never enterprise.
- Generous spacing, clear hierarchy, minimal clutter.
- Rounded surfaces and subtle borders; borders preferred over shadows.
- Color is an accent, never a large fill. Occasion types are distinguished by
  icon + label first, color second.
- Gentle motion; the OS reduced-motion preference always wins.

## 2. Color tokens

### Surfaces and text (theme-aware)

| Token | Light | Dark | Used for |
| --- | --- | --- | --- |
| `ivory` | `#FBF7F0` | `#1B1815` | app background |
| `linen` | `#F4EDE2` | `#262220` | cards, sidebar |
| `parchment` | `#EAE0CF` | `#2E2925` | subtle fills, zebra rows |
| `line` | `#E3D6C2` | `#3B342C` | hairlines, borders |
| `charcoal` | `#2E2A24` | `#F0E9DE` | primary text |
| `cocoa` | `#6D6052` | `#B9AB9A` | secondary text |
| `fawn` | `#A3937E` | `#8C7F6F` | placeholders, disabled |

### Accents (occasion colors and actions)

| Token | Light | Dark | Used for |
| --- | --- | --- | --- |
| `terracotta` | `#BC6A4B` | `#D68F70` | primary actions, birthday |
| `sage` | `#7D9471` | `#9DB28E` | wedding |
| `forest` | `#5A6E52` | `#7E9471` | wedding anniversary |
| `dusty-rose` | `#C28590` | `#D8A6B0` | relationship anniversary |
| `graphite` | `#6E675E` | `#A59C90` | memorial day |
| `honey` | `#D09C3F` | `#E2B45F` | graduation anniversary |
| `plum` | `#8B6B86` | `#AC8FA6` | personal milestone |
| `denim` | `#64798E` | `#8FA8BD` | general event |
| `ember` | `#A8493C` | `#C96A5C` | destructive actions |

Custom occasion types cycle through the accent list in seed order.

### Contrast rules (verified formally in the Phase 9 audit)

- `charcoal`/`cocoa` on `ivory`/`linen` carry all body text.
- Accent tokens are for icons, dots, borders, and large/bold text — never body
  copy.
- Primary buttons: `terracotta` fill with `ivory` text, ≥16px weight 600.

### Occasion type → token map (seeded in migration 001)

birthday→`terracotta` · wedding→`sage` · wedding anniversary→`forest` ·
relationship anniversary→`dusty-rose` · memorial day→`graphite` ·
graduation anniversary→`honey` · personal milestone→`plum` ·
general event→`denim`

## 3. Typography

Bundled locally via `@fontsource` packages — never a CDN.

- **Headings:** Fraunces (OFL) — weights 400, 600. Warm serif for the personal
  feel.
- **UI/body:** Inter (OFL) — weights 400, 500, 600.
- Fallbacks: Georgia (serif), system-ui (sans). If Fraunces feels too
  decorative anywhere, it retreats to display sizes only; UI stays Inter.

| Token | Size/line | Font | Use |
| --- | --- | --- | --- |
| `display` | 32/40 | Fraunces 600 | page titles |
| `title` | 24/32 | Fraunces 600 | section titles |
| `heading` | 20/28 | Inter 600 | card titles |
| `body` | 16/24 | Inter 400 | default |
| `body-sm` | 14/20 | Inter 400 | dense lists, secondary text |
| `caption` | 12/16 | Inter 500 | labels, badges, overlines (+0.04em tracking) |

Dates always render with tabular numerals (`font-variant-numeric: tabular-nums`).

## 4. Spacing (4px base)

| Token | px |
| --- | --- |
| `space-1` … `space-8` | 4, 8, 12, 16, 24, 32, 48, 64 |

Guidance: page padding `space-6`; card padding `space-4`–`space-5`; gaps inside
cards `space-3`; between sections `space-6`; sidebar 240px; content max-width
1040px.

## 5. Radii

`sm` 6 (chips, badges) · `md` 10 (inputs, buttons) · `lg` 14 (cards) ·
`xl` 20 (modals) · `pill` 999 (initials, status pills)

## 6. Elevation

Borders first; one soft shadow: cards
`0 1px 2px rgba(46,42,37,.06), 0 4px 12px rgba(46,42,37,.08)`; modals
`0 8px 32px rgba(46,42,37,.18)`.

## 7. Iconography

- Lucide only, imported from the `lucide-react` npm package (bundled, no remote
  fetch).
- Sizes: 16 (inline with text) · 20 (default for controls/lists) · 24
  (navigation, empty states). Never mixed within one context.
- Stroke width 1.75 everywhere. A single `Icon` wrapper component (Phase 1)
  enforces size and stroke.
- Icons use text tokens or occasion color tokens — never raw hex.

## 8. Motion

- 120ms state changes · 200ms panels/modals · 300ms max page transitions.
- ease-out on entrances, ease-in on exits.
- All animation routed through one helper so reduced-motion collapses it to
  opacity-only.

## 9. Occasion color usage

- Type color appears as an 8px dot, a 3px card left-edge, or a tinted icon —
  never a full-card fill.
- Always paired with the type's Lucide icon and label; color is never the only
  signal.

## Change log

| Version | Date | Change |
| --- | --- | --- |
| 0.1 | 2026-11-23 | Initial system from Phase 0 |
