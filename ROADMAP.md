# Keepsake: Project Pathway

A phase-by-phase plan for building Keepsake, from an empty folder to a tested, packaged release. This document contains no code. It describes what to build, in what order, why that order, and how to know each phase is done.

---

## How to Use This Document

- Work through the phases in order. Each one builds on the previous.
- Each phase has: **Goal**, **Tasks**, **Deliverables**, **Exit Criteria** (the "definition of done"), and **Watch Out For** (common traps).
- Do not start a phase until the previous phase's exit criteria are met. Skipping ahead is the most common cause of rework in projects like this.
- Rough time estimates assume one developer working part-time. Adjust to your pace.

## Roadmap at a Glance

| Phase | Name | Outcome | Est. Time |
| --- | --- | --- | --- |
| 0 | Planning and Decisions | Clear rules, data model, and screen sketches | 1 week |
| 1 | Foundation | Running app shell, database, and migrations | 1-2 weeks |
| 2 | People Management | Full create/edit/delete/search for people | 1-2 weeks |
| 3 | Occasions and Recurrence | Occasions linked to people, recurring correctly | 2 weeks |
| 4 | Date Engine and Calendar | Month, week, agenda, and year views | 2-3 weeks |
| 5 | Dashboard / Upcoming | "What's coming up" home screen | 1 week |
| 6 | Notifications | Scheduling, delivery, history, reconciliation | 2-3 weeks |
| 7 | Native Desktop Integration | Tray, autostart, window state, OS notifications | 1-2 weeks |
| 8 | Data Management | Backup, restore, JSON and ICS import/export | 2 weeks |
| 9 | Polish and Cross-Platform QA | Accessibility, theming, testing on all OSes | 2-3 weeks |
| 10 | Release | Installers, versioning, documentation | 1 week |

---

## Guiding Principles (Apply to Every Phase)

1. **Dates come first.** Every decision should favor correct date handling over feature count.
2. **Rust owns the logic.** React displays and collects input; Rust calculates, validates, and stores.
3. **Offline always.** No network calls, no CDN fonts, no remote icons, no telemetry. Bundle everything locally.
4. **Privacy by default.** Nothing leaves the device unless the user explicitly exports it.
5. **Small, working slices.** Finish a thin end-to-end version of each feature (UI to Rust to database and back) before adding extras.
6. **Test the hard parts early.** Leap days, time zones, daylight saving, and year boundaries should have tests from the day you write date logic.

---

## Phase 0: Planning and Decisions

**Goal:** Remove ambiguity before writing anything, so later phases do not stall on open questions.

### Tasks

1. **Write down the product rules.** Resolve the ambiguous cases in plain language:
   - What happens to a February 29 birthday in non-leap years? (Choose Feb 28 or Mar 1, and make it configurable or documented.)
   - How is "age" shown when only a month and day are known (no birth year)?
   - Does an annually recurring occasion with a start year show "Nth anniversary"?
   - Is a date stored as a plain calendar date (no timezone) or as a moment in time? *(Recommended: occasions are plain calendar dates; notification times are local clock times.)*
   - What does "today" mean when the user travels across time zones?
2. **Design the data model on paper.** List every entity and its fields, then draw the relationships:
   - People
   - Occasions
   - Occasion types (built-in and custom)
   - Occasion-to-person links (many-to-many)
   - Notification rules (per occasion, plus global defaults)
   - Notification history (sent, read/unread)
   - Settings (theme, first weekday, time format, timezone, startup behavior)
   - Attachments / profile pictures (stored as local files, referenced in the database)
3. **Sketch the screens.** Low-fidelity wireframes for: Calendar (4 views), People list, Person detail, Occasion editor, Upcoming/Dashboard, Notification center, Settings, Backup/Import/Export.
4. **Define navigation.** Decide the top-level structure (for example, a sidebar with Dashboard, Calendar, People, Notifications, Settings).
5. **Choose the visual system.** Lock the color palette (ivory, beige, terracotta, sage, dusty rose, forest charcoal), type scale, spacing scale, corner radii, and icon sizing. Pick fonts that can be **bundled locally**.
6. **Set up the repository and workflow.** Version control, a branching approach, an issue list that mirrors these phases, and a short contributing/notes file.
7. **Decide the testing strategy.** What gets unit tests (date math, recurrence, notification scheduling), what gets integration tests (database operations), and what gets manual checklists (UI, per-OS behavior).

### Deliverables
- Product rules document (one page)
- Entity-relationship diagram
- Wireframes for all main screens
- Palette, typography, and spacing reference
- Phase-mapped issue/task board

### Exit Criteria
- Every "what happens if..." date question has a written answer.
- Someone else could read the data model and understand it without asking you.

### Watch Out For
- Skipping the leap-day and time-zone decisions. These are painful to change after data exists.
- Over-designing. Wireframes should be rough; the goal is to settle layout and flow, not pixels.

---

## Phase 1: Project Foundation and Database Architecture

**Goal:** A desktop app that launches, talks to its own backend, and has a working, versioned local database.

### Tasks

1. **Scaffold the app.** Create the Tauri v2 project with the React + TypeScript frontend, and configure Tailwind CSS.
2. **Establish the folder structure.** Separate frontend (pages, components, hooks, state, utilities) from backend (commands, database layer, domain logic, services). Keep a clear boundary between them.
3. **Set up the design foundation.**
   - Translate the palette and spacing scale into Tailwind theme settings.
   - Define light and dark theme tokens now, even if only light is used at first.
   - Bundle fonts locally.
   - Set up Lucide icons from the local package, with a single wrapper that enforces consistent size and stroke.
4. **Create the application shell.** Window, sidebar/navigation, routing between empty placeholder pages, and a consistent page layout.
5. **Set up the database layer in Rust.**
   - Decide where the database file lives (the OS-appropriate app data directory).
   - Open a connection on startup; handle first-run creation.
   - Enable foreign-key enforcement and sensible durability settings.
6. **Build the migration system.** Numbered, ordered migrations with a version record in the database. The first migration creates the core tables from your Phase 0 data model. Establish the rule: **never edit an old migration; add a new one.**
7. **Define the frontend-to-backend contract.** Agree on how commands are named, how errors are returned (a consistent error shape), and how types are shared or mirrored between Rust and TypeScript.
8. **Add logging and error handling.** Local log file only (no remote reporting), and a user-friendly error display in the UI.
9. **Set up continuous checks.** Linting, formatting, type-checking, and a test runner for both Rust and TypeScript.
10. **Prove the round trip.** One trivial feature (for example, saving and reading a setting) that travels UI to command to database and back.

### Deliverables
- Launchable app with navigation shell and placeholder pages
- Initialized, migrated local database
- Theme tokens and icon wrapper
- Documented command/error conventions
- Passing lint and test pipeline

### Exit Criteria
- Fresh install creates the database automatically; relaunch reuses it.
- A new migration can be added and applied without losing data.
- Switching between light and dark tokens changes the whole shell.

### Watch Out For
- Letting the UI talk to the database logic ad hoc. Keep every database access behind the Rust layer.
- Forgetting offline rules: an accidental CDN font or remote icon breaks the privacy promise.

---

## Phase 2: People Management

**Goal:** A complete, pleasant directory of people. This is the simplest full feature, so it establishes patterns for all later ones.

### Tasks

1. **Backend operations.** Create, read (single and list), update, and delete people, with validation (required name fields, sensible length limits, valid optional birth date).
2. **Birth date handling.** Support three states: no birth date, month and day only, and full date with year. Store this distinction explicitly.
3. **Relationship labels.** Provide defaults (family, friend, colleague) and allow custom labels.
4. **Profile pictures.**
   - Let the user choose an image file.
   - Copy it into the app's local storage (do not just link to the original path).
   - Resize/optimize for display, and clean up the file if the person is deleted.
   - Provide a graceful placeholder (for example, initials) when no picture exists.
5. **People list screen.** Search, sort (by name, upcoming birthday, relationship), and filter by relationship.
6. **Person detail screen.** Show profile info, notes, and placeholder sections for upcoming and previous occasions (filled in during Phase 3).
7. **Add/edit form.** Fast, forgiving, keyboard-friendly, with clear validation messages.
8. **Delete behavior.** Confirmation step; decide and document what happens to linked occasions (detach vs. delete) once Phase 3 exists.
9. **Empty states.** Friendly, on-brand messaging when no people exist yet.
10. **Tests.** Validation rules, birth-date states, picture cleanup, and search behavior.

### Deliverables
- Working People list, detail, and add/edit flows
- Local picture storage with cleanup
- Test coverage for validation and search

### Exit Criteria
- You can add 100+ people and search/sort remains instant.
- Deleting a person leaves no orphaned picture files.
- All three birth-date states round-trip correctly.

### Watch Out For
- Storing absolute picture paths; they break if the app data folder moves.
- Treating "no year" as year 0 or a placeholder year. Model it explicitly.

---

## Phase 3: Occasion Management and Recurrence

**Goal:** Occasions that can stand alone or link to one or more people, with correct one-time and yearly recurring behavior.

### Tasks

1. **Occasion types.** Seed the built-in types (birthday, wedding anniversary, relationship anniversary, wedding, memorial day, graduation anniversary, personal milestone, general event) and support user-created custom types, each with a subtle distinguishing color and icon.
2. **Occasion backend operations.** Create, read, update, and delete occasions, including title, date, type, description, location, recurrence, and notification preferences.
3. **People linking.** Many-to-many association between occasions and people, with sensible rules (for example, a birthday links to exactly one person; an anniversary links to two).
4. **Recurrence model.** Implement the two supported modes: one-time and annually recurring. Define precisely how "the next occurrence" is calculated from a stored original date and today's date.
5. **Birthday automation.** When a person has a birth date, offer (or automatically maintain) a linked birthday occasion so the user does not enter it twice. Decide whether it is automatic or opt-in, and keep it consistent when the person's birth date changes.
6. **Age and anniversary calculations.** "Turning N" and "Nth anniversary," shown only when a start/birth year exists.
7. **Occasion editor UI.** A quick-add path (title, date, type, person) with an expandable "more options" area for description, location, and notifications.
8. **Person detail integration.** Fill in the upcoming and previous occasions sections created in Phase 2.
9. **Occasion list screen** (temporary or permanent) for reviewing and editing all occasions before the calendar exists.
10. **Tests.** Recurrence across year boundaries, leap-day behavior per the Phase 0 rule, age calculations, and link integrity on delete.

### Deliverables
- Occasion CRUD with built-in and custom types
- Person-occasion linking in both directions
- Verified next-occurrence logic

### Exit Criteria
- A Feb 29 birthday behaves exactly as documented in every year tested.
- Editing a person's birth date keeps the linked birthday occasion correct.
- Adding a typical occasion takes only a few seconds in the UI.

### Watch Out For
- Duplicating birthdays (one from the person, one entered manually).
- Computing recurrence in the frontend. Keep it in the Rust date engine so every screen agrees.

---

## Phase 4: Date Engine and Calendar

**Goal:** The central feature: a calendar the user can trust and enjoy.

### Tasks

1. **Consolidate the date engine.** Gather all date logic into one tested module in Rust: expanding recurring occasions into concrete dates for any requested range, calculating days remaining, resolving the first day of the week, and honoring the user's time zone and locale preferences.
2. **Range queries.** A single backend operation that returns all occasion instances within a date range, already expanded and sorted, so every view uses the same source of truth.
3. **Month view.** Grid with correct week alignment based on the first-day-of-week setting, visual indicators for occasions, handling of multiple occasions per day (for example, a compact "+N more"), and a clear "today" marker.
4. **Week view.** Seven-day layout with the same data source.
5. **Agenda view.** Chronological list grouped by day, ideal for scanning.
6. **Year overview.** Twelve mini-months with density indicators; selecting a day or month drills into the monthly view.
7. **Navigation.** Previous/next, jump to today, jump to a chosen month/year, and keyboard shortcuts.
8. **Filtering.** By occasion type and by person, applied consistently across all four views.
9. **Day detail.** Selecting a day shows its occasions and offers a quick "add occasion here."
10. **Regional preferences.** Honor Monday/Sunday start and date formatting across every view.
11. **Performance pass.** Test with thousands of occasions across many years; ensure navigation stays smooth.
12. **Tests.** Month grids for every first-weekday setting, months starting on each weekday, daylight-saving transition days, leap years, and recurring expansion across decades.

### Deliverables
- Four working calendar views with shared filtering
- A single, well-tested date engine
- Quick add from the calendar

### Exit Criteria
- All views show identical occasions for the same date range.
- Changing the first day of the week reflows every view correctly.
- No visible lag when moving across years with a large dataset.

### Watch Out For
- Time-zone bugs that shift an all-day date by one day. Because occasions are plain calendar dates, avoid converting them through moments in time.
- Separate calculation logic per view. One engine, many views.

---

## Phase 5: Dashboard and Upcoming Occasions

**Goal:** Answer "what's coming up?" the moment the app opens.

### Tasks

1. **Grouped upcoming lists.** Today, tomorrow, this week, this month, and later, each with sensible empty states.
2. **Occasion cards.** Show title, person (with picture), type color/icon, date, days remaining, and age/anniversary count where available.
3. **"Today" emphasis.** Make today's occasions the most prominent element on the dashboard.
4. **Upcoming birthdays and anniversaries highlight.** A dedicated, glanceable section.
5. **Quick actions.** Add an occasion, jump to the calendar, open a person.
6. **Live updates.** The dashboard refreshes at midnight rollover and when data changes, without requiring a restart.
7. **Tests.** Group boundaries (end of week, end of month, end of year) and days-remaining math.

### Deliverables
- Dashboard as the default landing screen
- Reusable occasion card component (also used elsewhere)

### Exit Criteria
- Leaving the app open past midnight updates "today" and "tomorrow" correctly.
- Groupings are correct at week, month, and year boundaries.

### Watch Out For
- Defining "this week" differently from the calendar's first-day-of-week setting. Use the same rule everywhere.

---

## Phase 6: Notification Scheduling and Delivery

**Goal:** Timely, useful reminders that survive restarts, sleep, and missed windows.

### Tasks

1. **Notification model.** Define default rules (for example, 7 days before, 1 day before, on the day) plus per-occasion overrides, each with a custom time of day, and individual enable/disable.
2. **Schedule computation.** Given an occasion and its rules, calculate every upcoming notification moment in the user's local time. Handle edge cases: rules whose time has already passed today, daylight-saving shifts, and occasions created or edited close to their date.
3. **Persistent scheduling state.** Record what has been scheduled, delivered, and read, so the app can recover after closing or crashing.
4. **In-app notification center.**
   - Notification history, newest first.
   - Read/unread states, mark-all-read, and clear.
   - Click-through to the related occasion or person.
   - Unread badge in the navigation.
5. **Native notification delivery.** Send operating-system notifications through Tauri, including permission handling and a clear explanation if permission is denied.
6. **Missed notification reconciliation.** On launch and on wake from sleep, compare what should have fired against what did fire, then deliver or log the missed ones sensibly (for example, grouping several missed items into one summary instead of spamming).
7. **Background timing.** A lightweight scheduler in Rust that wakes at the next due time rather than polling constantly, to keep resource use low.
8. **Settings integration.** Default notification preferences editable in Settings; per-occasion overrides in the occasion editor.
9. **Prevent duplicates.** Guarantee a notification for a given occasion/rule/date fires at most once.
10. **Tests.** Schedule generation across boundaries, reconciliation after long downtime, duplicate prevention, and changed-rule behavior (editing an occasion should replace pending notifications).

### Deliverables
- Working native and in-app notifications
- Reconciliation logic that handles downtime
- Notification center with history

### Exit Criteria
- Closing the app for several days and reopening it produces a sensible catch-up, not a flood and not silence.
- Editing or deleting an occasion cancels its stale pending notifications.
- No duplicate notifications under any tested scenario.

### Watch Out For
- Relying on the app being open at the exact moment. Always design for "it wasn't running."
- Notification volume. The README's principle is "useful rather than intrusive."

---

## Phase 7: Native Desktop Integration

**Goal:** Make Keepsake feel like a real desktop citizen on each platform.

### Tasks

1. **System tray / menu bar.** Icon with a small menu (open, today's occasions, quit). Decide whether closing the window minimizes to the tray.
2. **Start at login.** A user-controlled option, off by default or clearly offered at first run.
3. **Startup behavior.** Options such as launching minimized to the tray, or opening to the dashboard.
4. **Window state restoration.** Remember size, position, and maximized state, with safeguards if a saved position is off-screen (for example, a monitor was removed).
5. **Single-instance behavior.** Launching a second copy focuses the existing window rather than opening another.
6. **Keyboard shortcuts.** Define and document a consistent set.
7. **Native menus and app metadata.** Proper app name, icon, and about information per platform.
8. **Per-platform verification.** Each integration is implemented and verified separately on Windows, macOS, and Linux, as the README states, with a written note of any platform differences.

### Deliverables
- Tray, autostart, window restoration, and single-instance behavior
- Per-platform notes on differences and limitations

### Exit Criteria
- Each integration works on all three target platforms, or its limitation is documented.
- Notifications still fire correctly when the window is hidden to the tray.

### Watch Out For
- Linux fragmentation (different desktop environments handle tray icons and notifications differently). Test on at least two common environments.
- macOS permission prompts and menu bar conventions.

---

## Phase 8: Backup, Restoration, and Calendar Interoperability

**Goal:** Users fully own their data and can move it safely in and out.

### Tasks

1. **SQLite database backup.** Create a consistent snapshot file at a user-chosen location, safe even while the app is running. Include a timestamped default name.
2. **Restore from backup.** Validate the file first (is it a Keepsake database, and is its schema version supported?), take an automatic safety copy of the current data, then restore. Handle version mismatches through the migration system.
3. **JSON export.** A complete, human-readable export of people, occasions, types, notification rules, and settings, including a format version number. Decide how pictures are handled (bundled alongside, or referenced separately).
4. **JSON import.** Validate structure and version, detect duplicates, and offer clear choices (merge vs. replace). Report what was imported and what was skipped, and never partially corrupt existing data (use all-or-nothing transactions).
5. **ICS export.** Produce standards-compliant calendar files, with correct handling of yearly recurrence and all-day events, so the file opens correctly in other calendar apps.
6. **ICS import.** Parse common calendar files, map events to occasions, handle recurrence rules supported by the app, and clearly report anything unsupported.
7. **Attachments and pictures in backups.** Make sure backups capture the local picture and attachment files, not just the database.
8. **Scheduled or reminded backups (optional).** A gentle prompt if no backup has been made in a long time.
9. **Migration verification.** Test upgrading from every earlier schema version using saved sample databases.
10. **Tests.** Round-trip tests (export then import yields identical data), corrupted-file handling, and large-file behavior.

### Deliverables
- Backup/restore, JSON import/export, ICS import/export
- Migration test fixtures

### Exit Criteria
- Export, wipe, and import reproduces the original data exactly.
- Invalid or damaged files fail safely with a clear message and no data loss.
- An exported ICS file opens correctly in at least two other calendar applications.

### Watch Out For
- Restoring over the live database without a safety copy.
- ICS time-zone and all-day quirks. Test against real calendar apps, not just the spec.

---

## Phase 9: Visual Refinement, Accessibility, and Cross-Platform Testing

**Goal:** Turn a working app into a polished, dependable one.

### Tasks

1. **Visual refinement.** Review every screen against the design philosophy: generous spacing, clear hierarchy, rounded surfaces, subtle borders, consistent Lucide icons, gentle transitions, and minimal clutter.
2. **Dark theme completion.** Verify every component, state, and chart in dark mode; check contrast.
3. **Customization.** Complete the warm, customizable appearance options promised in Settings.
4. **Accessibility pass.**
   - Full keyboard navigation and visible focus states.
   - Screen reader labels and sensible reading order.
   - Color contrast that meets accessibility guidelines.
   - Occasion type distinguished by more than color alone (icon or label).
   - Respect the operating system's reduced-motion preference.
5. **Localization readiness.** Confirm date, time, and first-weekday formatting across several locales, including 12/24-hour time.
6. **Empty, loading, and error states.** Every screen should handle all three gracefully.
7. **Performance profiling.** Startup time, memory use, database size growth, and responsiveness with large datasets.
8. **Privacy audit.** Confirm there are no network requests at all (monitor traffic during a full session), no telemetry, and no externally loaded fonts or images.
9. **Cross-platform test matrix.** Run a written checklist on Windows, macOS, and Linux covering install, first run, every feature, notifications, tray, backup/restore, and uninstall.
10. **Bug-bash.** Use the app daily for a couple of weeks with real data and log every rough edge.

### Deliverables
- Polished UI in both themes
- Accessibility and privacy audit results
- Completed cross-platform test matrix

### Exit Criteria
- No known data-loss or date-correctness bugs.
- The app is fully usable by keyboard alone.
- Network monitoring shows zero outbound requests.

### Watch Out For
- Treating polish as optional. For a personal, emotional app, the feel is part of the product.

---

## Phase 10: Packaging and Release

**Goal:** Ship something others can install and trust.

### Tasks

1. **Versioning scheme.** Adopt semantic versioning, tie it to database schema versions, and keep a changelog.
2. **Installers per platform.** Produce native installers/packages for Windows, macOS, and Linux.
3. **Code signing and notarization.** Plan for Windows signing and macOS signing/notarization so users do not hit security warnings. Budget time and cost for this early; it is slow the first time.
4. **App icons and metadata.** Final icons at all required sizes and store-style descriptions.
5. **Upgrade testing.** Install an older version with real data, upgrade, and confirm data and settings survive.
6. **Documentation.** User guide (adding people and occasions, notifications, backups), a short privacy statement, and developer setup instructions.
7. **First-run experience.** A brief welcome that explains the app, offers notification permission, and optionally imports existing data.
8. **Release checklist.** A repeatable, written process so future releases are routine.
9. **Feedback channel.** Decide how users report bugs without compromising the no-telemetry promise (for example, an issue tracker or a manual "copy diagnostic info" button).

### Deliverables
- Installable builds for all three platforms
- User and developer documentation
- Release checklist and changelog

### Exit Criteria
- A person who has never seen the project can install it, add a birthday, and receive a notification.
- An upgrade from the previous version preserves all data.

---

## Cross-Cutting Concerns

These run through every phase rather than belonging to one.

### Date Correctness
- Keep one date engine and test it relentlessly.
- Maintain a permanent "nasty dates" test list: Feb 29, Dec 31, Jan 1, daylight-saving transition days, and the end of every month.

### Data Safety
- All schema changes go through migrations.
- Multi-step writes happen in transactions.
- Take automatic safety copies before risky operations (restore, import-replace, migration).

### Privacy
- No network access in the app's capabilities or permissions configuration.
- Local files only, in the OS-appropriate app data folder.
- Be careful that logs never contain personal details beyond what is needed.

### Quality
- Every bug fix gets a regression test.
- Keep the linting, type-checking, and test pipeline green at all times.

### Scope Control
- Anything listed under the README's "Future Possibilities" is out of scope until after Phase 10.
- When a new idea appears, add it to a "later" list instead of the current phase.

---

## Suggested Milestones

| Milestone | Reached After | What You Can Do |
| --- | --- | --- |
| **M1: Skeleton** | Phase 1 | App launches, database persists |
| **M2: Usable Directory** | Phase 2 | Manage people end to end |
| **M3: Real Occasions** | Phase 3 | Track birthdays and anniversaries |
| **M4: Calendar Alpha** | Phase 4-5 | See everything on a calendar and dashboard |
| **M5: Reminders** | Phase 6-7 | Get notified; app lives in the tray |
| **M6: Safe and Portable** | Phase 8 | Back up, restore, import, export |
| **M7: Release Candidate** | Phase 9 | Polished, tested, accessible |
| **M8: 1.0** | Phase 10 | Public release |

Consider using the app yourself with real birthdays from **Milestone M4** onward. Daily use exposes problems no checklist will.

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Time-zone or leap-day bugs | High | High | Phase 0 rules; one tested date engine |
| Missed or duplicate notifications | Medium | High | Persistent state, reconciliation, dedupe tests |
| Linux desktop inconsistencies | High | Medium | Test on multiple desktop environments early |
| Data loss during restore/import | Low | Very High | Safety copies, transactions, validation |
| Scope creep from "Future Possibilities" | High | Medium | Strict "later" list |
| Code-signing delays at release | Medium | Medium | Start account setup during Phase 8 |
| Accidental network usage | Low | High | Network monitoring in Phase 9 audit; no network permissions |

---

## Final Checklist Before Calling It 1.0

- [ ] All README core features implemented or consciously deferred and documented
- [ ] Date engine passes the full "nasty dates" suite
- [ ] Notifications verified after sleep, restart, and multi-day downtime
- [ ] Backup, restore, JSON, and ICS round trips verified
- [ ] Zero network requests confirmed
- [ ] Light and dark themes complete
- [ ] Accessibility pass complete
- [ ] Tested on Windows, macOS, and Linux
- [ ] Upgrade path tested from prior versions
- [ ] Documentation and changelog written
