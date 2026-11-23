# Keepsake — Wireframes

**Status:** v0.1 (Phase 0)
**Related:** `product-rules.md` (R#), `design-system.md`

Low-fidelity on purpose: these settle layout and flow, not pixels. Every
screen must implement empty, loading, and error states (Phase 9 verifies).

## 1. Navigation and shell

Sidebar (fixed): **Dashboard, Calendar, People, Notifications, Settings**.
Notifications shows an unread badge. A global **+ Add occasion** button sits
top-right on every page. Backup/restore lives inside Settings → Data.

```
┌──────────┬─────────────────────────────────────────────┐
│ Keepsake │  <page title>                 [ + Add (N) ] │
│          ├─────────────────────────────────────────────┤
│ Dashboard│                                             │
│ Calendar │                                             │
│ People   │                <content>                    │
│ Alerts ③ │                                             │
│ Settings │                                             │
└──────────┴─────────────────────────────────────────────┘
```

Keyboard shortcuts (proposal, finalized in Phase 7): `N` new occasion ·
`P` new person · `T` today · `←`/`→` previous/next period ·
`M`/`W`/`A`/`Y` calendar views · `/` search · `Esc` close · `?` shortcut list.

## 2. Screen inventory

| Screen | Wireframe | Built in |
| --- | --- | --- |
| Calendar — month/week/agenda/year | §3.1–3.4 | Phase 4 |
| Day detail (shared panel) | §3.5 | Phase 4 |
| People list | §3.6 | Phase 2 |
| Person detail | §3.7 | Phase 2 |
| Occasion editor | §3.8 | Phase 3 |
| Dashboard / Upcoming | §3.9 | Phase 5 |
| Notification center | §3.10 | Phase 6 |
| Settings | §3.11 | Phase 1 shell, filled per phase |
| Backup & import/export | §3.12 | Phase 8 |

## 3. Wireframes

### 3.1 Month view

```
  ◀  March 2025  ▶        [ Today ]     Month · Week · Agenda · Year
  Filters: [Type ▾] [Person ▾] (Reset)

  ┌────────┬────────┬────────┬────────┬────────┬────────┬────────┐
  │ Mon 24 │ Tue 25 │ Wed 26 │ Thu 27 │ Fri 28 │ Sat 1  │ Sun 2  │
  ├────────┼────────┼────────┼────────┼────────┼────────┼────────┤
  │ •Anna  │        │ •Kim   │        │ •Mesa  │ •River │        │
  │  27th  │        │  +2    │        │  40th  │  12th  │        │
  └────────┴────────┴────────┴────────┴────────┴────────┴────────┘
```

Weekday order follows the first-day setting (R14). Up to two occasion chips
per cell, then "+N" → opens day detail. Today's cell is outlined and tinted.
Clicking a day opens the day-detail panel.

### 3.2 Week view

```
  ◀  24 – 30 March ▶      [ Today ]     Week
  ┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐
  │ Mon 24  │ Tue 25  │ Wed 26  │ Thu 27  │ Fri 28  │ Sat 1   │ Sun 2   │
  │─────────│─────────│─────────│─────────│─────────│─────────│─────────│
  │ •Anna   │         │ •Kim&Jo │         │ •Mesa   │ •River  │         │
  │  27th   │         │  30th   │         │  40th   │  turns12│         │
  └─────────┴─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘
```

The week view uses the same data source as month (R22).

### 3.3 Agenda view

```
  Agenda · March 2025                 [Type ▾] [Person ▾]

  TODAY — Saturday 1 March
    • River's birthday · turns 12 · today
  TOMORROW — Sunday 2 March
    Nothing planned.
  MONDAY 3 March
    • Kim & Jo — wedding anniversary · 30th · in 2 days
  WEDNESDAY 5 March
    • Mesa — graduation anniversary · 40th · in 4 days
```

Continuous chronological scroll grouped by day; month jump control.

### 3.4 Year overview

```
  ◀  2025 ▶
  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
  │ Jan    │ │ Feb    │ │ Mar •  │ │ Apr    │   … 12 mini-months
  │ · week │ │ · week │ │ · week │ │ · week │      in a 3×4 grid
  │   rows │ │   rows │ │   rows │ │   rows │
  └────────┘ └────────┘ └────────┘ └────────┘
```

One dot per day with ≥1 occasion (density shading by count). Month title
click → Month view; day click → day detail.

### 3.5 Day detail (shared panel)

```
  Saturday 1 March 2025                     [ + Add occasion here ]
  • River's birthday · turns 12        [open person]
  • Neighborhood dinner (event)
```

Used by month, week, year, and dashboard date clicks. "Add here" pre-fills the
editor's date.

### 3.6 People list

```
  People                                  [ + Add person ]
  [ Search names…  / ]   Sort [Name ▾]   Filter [Relationship ▾]

  ┌────────────────────────────────────────────────────┐
  │ (AR) Anna Rivera              Family               │
  │      Birthday Mar 27 · in 26 days                  │
  ├────────────────────────────────────────────────────┤
  │ (KM) Kim Mesa                 Friend               │
  │      —                                             │
  └────────────────────────────────────────────────────┘
```

Initials avatar when no picture (R13 governs picture storage). Sorts: name,
upcoming occasion, relationship.

### 3.7 Person detail

```
  ‹ People                 Anna Rivera            [Edit] [Delete]
  ┌────────────────────────────────────────────────────┐
  │ (AR)  Anna Rivera · Family                         │
  │       Birthday March 27, 1998 · turns 27 in 2025   │
  ├────────────────────────────────────────────────────┤
  │ UPCOMING                                           │
  │ • Birthday · Mar 27 · in 26 days · turns 27        │
  │ PREVIOUS                                           │
  │ • Birthday · Mar 27, 2024 · turned 26              │
  │ NOTES                                              │
  │ Prefers handmade gifts.                            │
  └────────────────────────────────────────────────────┘
```

Delete dialog states all R13 consequences before confirming.

### 3.8 Occasion editor (modal)

```
  ┌ New occasion ─────────────────────────────── ✕ ┐
  │  Title      [ River's birthday              ]   │
  │  Date       [ 01 / 03 / 2025 ]  (R21 input)     │
  │  Type       [ Birthday ▾ ]    ◉ Yearly ○ Once   │
  │  People     (Anna Rivera ×) [+ link person]     │
  │  ─ More options ─────────────────── ▸ open     │
  │     Description [ … ]    Location [ … ]         │
  │     Notifications  ◉ Inherit defaults           │
  │                    ○ Custom → rows editable     │
  │        [✓] 7 days before  at [09:00]            │
  │        [✓] 1 day before   at [09:00]            │
  │        [✓] On the day     at [09:00]            │
  │        Master switch [ON]                       │
  │                             [Cancel]  [Save]    │
  └─────────────────────────────────────────────────┘
```

Quick-add = the top four fields; "More options" is collapsed by default.
Auto birthdays open read-only with a banner: "Managed from Anna's profile —
edit her birth date, or convert to manual."

### 3.9 Dashboard / Upcoming (default landing)

```
  Upcoming                                 [ + Add occasion ]

  TODAY — Saturday 1 March
  ┌──────────────────────────────────────────────────┐
  │ • River's birthday              turns 12 today   │
  │   Sibling                       [Open person]    │
  └──────────────────────────────────────────────────┘

  TOMORROW — Sunday 2 March
     Nothing planned.  [Add one]

  THIS WEEK
     • Kim & Jo — 30th wedding anniversary · in 2 days

  THIS MONTH
     • Mesa — graduation anniversary · in 27 days

  LATER  (next 5 ▾)
```

Groups and ranges are exactly R15/R16. Today is the visual peak.

### 3.10 Notification center

```
  Notifications                 [Mark all read]  [Clear]

  ●  River's birthday is today                    09:00
        → click opens the occasion / person
  ○  Kim & Jo anniversary in 7 days        Feb 22 · read
  ○  Anna's birthday in 7 days             Feb 18 · read
```

Newest first; unread dot drives the sidebar badge.

### 3.11 Settings

```
  Settings
  ── Appearance ──      Theme (System / Light / Dark)
  ── Dates & region ──  First day (Mon/Sun) · Time format (12h/24h)
                        Timezone (System ▾ / IANA) · Locale (System ▾)
  ── Notifications ──   Default rules editor (R17)
  ── Startup ──         Open to Dashboard · Start at login (off by default)
  ── Data ──            [ Backup & restore… ]
```

### 3.12 Backup & import/export

```
  Data
  Backup    [ Backup now ]            Last backup: never
  Restore   [ Choose file… ]   → validate → safety copy → restore
  JSON      [ Export… ]  [ Import… ]        (merge or replace)
  ICS       [ Export… ]  [ Import… ]
  Note: backups include the pictures/ folder.
```

## 4. Key flows

1. **Quick add occasion** — anywhere: `N` or "+ Add" → top four fields → Save
   (≤10s).
2. **Add from a day** — day detail → "Add occasion here" → editor with date
   pre-filled.
3. **Convert auto birthday** — person detail or auto-birthday banner →
   becomes manual (R11).
4. **Add birth date to person with manual birthday** — adoption prompt (R11).
5. **Delete person** — dialog lists: auto birthday removed, links detached,
   picture deleted (R13).

## Change log

| Version | Date | Change |
| --- | --- | --- |
| 0.1 | 2026-11-23 | Initial wireframes from Phase 0 |
