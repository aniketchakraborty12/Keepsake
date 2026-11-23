# Keepsake — Testing Strategy

**Status:** v0.1 (Phase 0)
**Related:** `nasty-dates.md` (permanent fixture list), `product-rules.md`

## Principles

- Rust owns the logic, so the hardest tests live in Rust.
- Every date-math test runs against the full `nasty-dates.md` fixture list.
- A regression bug fix is not done until it has a test (with a new ND id if
  date-related).
- UI is verified by manual checklists, not brittle UI automation; pure TS
  utilities (display formatting) may get light unit tests.

## Layers

| Layer | Scope | Tooling | Phases |
| --- | --- | --- | --- |
| Unit (Rust) | Date engine: next occurrence, recurrence expansion, leap handling, age/anniversary math, days-remaining, week boundaries, "today"/timezone | `cargo test`; property-based cases with `proptest` | 3–5 |
| Unit (Rust) | Notification schedule computation: offsets, times, dedupe, catch-up grouping | `cargo test` | 6 |
| Unit (Rust) | Validation: birth-date states, link cardinality, rule/flag consistency | `cargo test` | 2–3 |
| Integration (Rust) | Migrations (fresh + upgrade), CRUD, cascades (person/occasion delete, type RESTRICT), auto-birthday lifecycle, settings round-trip | `cargo test` + `tempfile` per-test DB | 1–3, 6, 8 |
| Integration (Rust) | Backup/restore, JSON and ICS round-trips, corrupted-file handling | `cargo test` with fixture files | 8 |
| Manual | Every screen's happy path + empty/loading/error states; per-OS matrix (install, notifications, tray, backup) | `docs/manual-checklists.md`, appended per phase | all; matrix in 9 |
| Bug-bash | Daily use with real (own) data from M4 onward | notes in `NOTES.md` | 4+ |

## Coverage targets (pragmatic)

- Date engine and notification scheduling: effectively 100% of branches.
- Database layer: every command has at least one integration test.
- UI components: no target.

## Fixtures and data policy

- Fixtures use invented names only; real personal data never enters the repo.
- Migration tests keep a saved sample database per historical schema version
  (from Phase 8).
- The `nasty-dates.md` list is append-only; cases are never removed, only marked
  fixed.

## Pipeline (set up in Phase 1, defined here)

`cargo fmt --check` · `cargo clippy` · `cargo test` · `pnpm lint` ·
`pnpm typecheck` · `pnpm test` — all green before merge. Property tests and
the full nasty-dates suite run in CI, not locally-only.

## Change log

| Version | Date | Change |
| --- | --- | --- |
| 0.1 | 2026-11-23 | Initial strategy from Phase 0 |
