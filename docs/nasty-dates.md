# Keepsake — Nasty Dates

**Status:** v0.1 (Phase 0)
**Related:** `product-rules.md` (R#), `testing-strategy.md`

The permanent fixture list for all date logic. **Append-only**: cases are never
removed. Every date-engine unit test iterates this list; new date bugs get a
new ID and a regression test.

## Leap day (R5, R6, R9)

- **ND-L1** Birthday Feb 29, non-leap year → celebrated Feb 28; shows in month
  grid on the 28th.
- **ND-L2** Birthday Feb 29, leap year → celebrated Feb 29.
- **ND-L3** Next-occurrence queried on Mar 1 of a non-leap year → next Feb 28
  (next year, or Feb 29 if leap).
- **ND-L4** Next-occurrence queried on Feb 27 of a non-leap year → Feb 28,
  this year.
- **ND-L5** Yearly anniversary Feb 29 with start year → anniversary numbering
  follows celebrated date.
- **ND-L6** Age: born Feb 29, 2000 → turns 25 on Feb 28, 2025; is 24 on Feb
  27, 2025; turns 28 on Feb 29, 2028 (R6).
- **ND-L7** Feb 29 yearly occasion expands correctly across 1950–2050 (leap
  sequence + fallback).

## Year boundaries

- **ND-Y1** Yearly occasion Dec 31 → next occurrence computed across Dec
  31→Jan 1.
- **ND-Y2** Days-remaining for a Jan 1 occasion, queried Dec 31 → 1.
- **ND-Y3** Due-date arithmetic crosses years: Jan 1 occasion, 7-days-before →
  due Dec 25 of the prior year.
- **ND-Y4** Dashboard "this week" spanning Dec 30–Jan 4 groups correctly (R15).

## Month ends

- **ND-M1** Mar 1 occasion, 1-day-before → Feb 28 (non-leap) / Feb 29 (leap).
- **ND-M2** 31st-of-month occasions render in 30-day months' grids without
  spillover.
- **ND-M3** Range query spanning a month boundary returns every occasion exactly
  once, sorted.

## DST and time (R2, R19)

- **ND-D1** Notification due 09:00 on a spring-forward day fires at 09:00 local
  (a 23-hour day), never shifted.
- **ND-D2** Notification due 09:00 on a fall-back day fires once (a 25-hour
  day).
- **ND-D3** Local midnight rollover on DST transition days flips "today" on the
  civil date only.
- **ND-D4** Rules whose 09:00 already passed today are not scheduled for today
  at creation; next occurrence's rules apply. *(Confirm exact behavior in Phase
  6.)*

## Timezones (R2)

- **ND-T1** Pinned IANA timezone: OS timezone change does not move "today".
- **ND-T2** `system` setting: OS timezone change re-evaluates "today" at the
  next recomputation.
- **ND-T3** Civil dates never shift by one day through any timestamp conversion
  (the R1 invariant).

## Dashboard group boundaries (R15, R16)

- **ND-G1** Today is the week's last day → "This week" group empty, no crash.
- **ND-G2** Tomorrow falls in the next week → still "Tomorrow" (first matching
  group wins).
- **ND-G3** Week-end beyond month-end (e.g., week ends Nov 2) → "This month"
  empty; Nov 3+ is "Later".
- **ND-G4** Today Dec 30 → "This week" spans the year boundary; "This month"
  empty; Dec 31 occasion appears in "This week".
- **ND-G5** Today = last day of month → "This month" contains only today's
  leftovers; later occasions are "Later".
- **ND-G6** Today Feb 28 in a non-leap year with a Feb 29 birthday → occasion
  is in "Today", days-remaining 0, turns N today.
- **ND-G7** Midnight rollover while the app is open re-groups Today/Tomorrow
  without restart.

## Display and input (R21)

- **ND-A1** 100th birthday renders sanely; centenarian arithmetic exact.
- **ND-A2** Wedding same-day (start year = this year) → shows the event, not
  "0th anniversary".
- **ND-A3** Occasion created with a far-past one-time date (e.g., memorial
  1965) → absent from upcoming, present in previous-occasions.
- **ND-A4** Locale input parse failures fall back to ISO; ISO round-trips
  losslessly.

## Change log

| Version | Date | Change |
| --- | --- | --- |
| 0.1 | 2026-11-23 | Initial fixture list from Phase 0 |
