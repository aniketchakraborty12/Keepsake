# Keepsake — Product Rules

**Status:** v0.2 (Phase 0)
**Related:** `data-model.md`, `nasty-dates.md`

These rules are binding. Every feature must behave exactly as written here.
Changing a rule requires updating this document first, including the rationale.
Rules are numbered (R1, R2, …) so other documents and issues can reference them.

---

## 1. Dates are civil dates, never moments

**R1.** Every occasion date (birth date, wedding date, anniversary, milestone) is a plain calendar date: optional year, month, day. It carries no timezone and no time-of-day. Occasion dates are never converted through timestamps or UTC.

*Rationale: passing all-day dates through moments in time shifts them by one day
across timezones and DST boundaries — the most common date bug in calendar apps.*

**R2.** "Today" is the calendar date in the *effective timezone*. The timezone
setting is either `system` — the app follows the OS timezone live, re-evaluated
at every recomputation — or an explicit IANA name pinned in Settings. "Today"
is recomputed at app launch, at local midnight rollover of the effective
timezone, on resume from sleep, and whenever the effective timezone changes.
Exactly one Rust function computes it; every screen uses it. DST needs no
special handling: computation is civil-time on IANA names.

**R3.** Record-keeping timestamps (`created_at`, `delivered_at`, …) are UTC
instants for bookkeeping only. They never influence occasion dates, ages,
countdowns, or calendar placement.

## 2. Birth dates have exactly three states

**R4.** A person's birth date is one of: **none**, **month + day**, or
**full date with year**. This is modeled explicitly. Unknown years are NULL —
never a placeholder year (no year 0000, 1900, or similar). Age information is
shown only in the full state.

## 3. February 29

**R5.** In a non-leap year, an occasion that falls on February 29 is celebrated
on **February 28**. This applies uniformly to birthdays and all yearly recurring
occasions. There is one behavior, it is documented here, and it is not
user-configurable in v1.

*Rationale: keeps the celebration inside February and matches the most common
convention. March 1 was considered and rejected. Revisit only if users ask.*

## 4. Ages and anniversaries

**R6.** "Turning N" appears only when the birth year is known. A person reaches
age N on their *celebrated* birthday — so a Feb 29 person turns N on Feb 28 in
non-leap years (per R5). Current age is `currentYear − birthYear` on or after the
celebrated date, and one less before it.

**R7.** "Nth anniversary" appears only when the occasion has a start year. Same
arithmetic. Occasions without a year show the date and days-remaining only.

## 5. Recurrence and the next occurrence

**R8.** Two recurrence modes exist: **once** and **yearly**. A one-time occasion
requires a year; a yearly occasion's year is optional.

**R9.** The *next occurrence* of a **yearly** occasion is the earliest
celebration date on or after today with the occasion's month/day, applying R5
for Feb 29. If it falls today, the next occurrence is today and days-remaining
is 0. The *next occurrence* of a **one-time** occasion is its stored date; a
one-time occasion in the past has no upcoming instance (R10 governs where it
appears). Next occurrences are **never stored or cached** — the Rust date
engine computes them on demand from the stored original date and R2's "today".

**R10.** Past one-time occasions never appear in upcoming lists; they remain
visible on the calendar and in a person's "previous occasions".

## 6. Birthdays are automatic

**R11.** A person with a known birth date always has exactly one birthday
occasion, maintained by the app: creating a birth date creates it; changing
the birth date updates it; removing the birth date removes it (with
confirmation). Auto birthdays are marked in the data model and are not
independently editable or deletable. An auto birthday can be explicitly
*converted* to a manual occasion, after which it no longer follows the
person's birth date.
A person has **at most one birthday occasion**. Creating a manual birthday for
a person who already has an auto birthday is blocked — the UI offers the
conversion above instead. Adding a birth date to a person who already has a
manual birthday converts that occasion into the auto birthday (keeping its
links, rules, and description); no second birthday is created.

*Rationale: the roadmap identifies duplicate birthdays as a known trap; a single
source of truth (the person's birth date) eliminates it.*

## 7. Linking people to occasions

**R12.** Link cardinality, enforced by app validation:
- Birthday → exactly **1** person
- Wedding, wedding anniversary, relationship anniversary → **1–2** people
- Memorial, graduation anniversary, milestone, event, custom → **0 or more**
  (0 = standalone occasion)

Removing people from an occasion in the editor may not take a wedding, wedding
anniversary, or relationship anniversary below one person; to remove everyone,
delete the occasion. (Deleting a *person* via R13 may legitimately leave a
two-person occasion with one.)

## 8. Deleting a person

**R13.** Deleting a person deletes their auto birthday (it cannot exist without
them), **detaches** them from all other linked occasions (those occasions
survive with their remaining links), and deletes their local picture file. The
confirmation dialog states all three consequences.

## 9. Weeks, months, and dashboard groups

**R14.** The week starts on the day set by the first-day-of-week setting
(Monday or Sunday). This one setting drives calendar grids, week navigation,
and every definition of "this week" in the app.

**R15.** Dashboard groups are ordered ranges; an occasion falls into the **first
group that contains its next-occurrence date**:
1. **Today** — today's date
2. **Tomorrow** — today + 1
3. **This week** — today + 2 through end of the current week (may be empty; may
   include days beyond the current month)
4. **This month** — the day after week-end through the end of the current month
   (may be empty)
5. **Later** — everything after

**R16.** Days remaining = celebration date − today in civil days. 0 renders as
"Today", 1 as "Tomorrow"; past dates render as "N days ago" where shown.

## 10. Notifications

**R17.** Global defaults for new occasions: **7 days before**, **1 day
before**, and **on the day**, each at the default local time (09:00). Defaults
are editable in Settings. An occasion **inherits** the defaults until its
rules are customized: the first customization copies the effective rules into
occasion-specific rows and switches the occasion to custom, so customization
always starts from what the user currently sees; deleting custom rules from
then on means *no rules*, not a return to defaults (the master switch also
means "none"). Each occasion has a master notifications on/off switch, and
each rule can be individually disabled.

**R18.** A notification fires **at most once** per (occasion, occurrence,
rule). Editing or deleting an occasion deletes its pending (scheduled)
notifications and reschedules from the occasion's current rules — or from the
global defaults while it inherits; already-delivered history is kept. Changing
a rule's *time* updates the pending row in place (the dedupe key deliberately
omits time-of-day). Editing the global defaults reschedules pending
notifications of inheriting occasions the same way.

**R19.** Rules fire at their local wall-clock time on the due date. A DST
transition never shifts a notification to a different local time — scheduling is
by local clock, never by interval arithmetic.

**R20.** Missed-notification catch-up (at launch or wake): if **1** notification
was missed, it is delivered as-is; if **2 or more** were missed, they are
combined into a **single summary notification**. At most one catch-up batch is
produced per launch.

## 11. Display and input

**R21.** Dates are stored canonically (ISO `YYYY-MM-DD`), displayed per
regional settings (locale format, 12/24-hour time), and accepted as input in
ISO form and the active locale's numeric day/month/year pattern (e.g.
`27/3/2025`), normalized on save. If a locale pattern cannot be parsed, ISO
always works.

**R22.** Every view — month, week, agenda, year, dashboard — renders from the
same date-engine range query. No view keeps private date logic.

**R23.** Everything listed under "Future Possibilities" in the README is out of
scope until after 1.0. New ideas go to the "later" list, not the current phase.

---

## Change log

| Version | Date | Change |
| --- | --- | --- |
| 0.1 | — | Initial rules from Phase 0 |
| 0.2 | 2026-11-23 | R2: live-follow system timezone; R9: one-time next-occurrence defined; R11: one birthday per person + collision precedence; R12: link-minimum on edit; R17: inherit-vs-custom mechanism; R18: reschedule on rule/default edits; R21: input formats defined |

---