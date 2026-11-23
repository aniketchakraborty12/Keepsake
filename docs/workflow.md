# Keepsake — Repository Workflow

**Status:** v0.1 (Phase 0)

## Branching

Trunk-based: `main` is always green (lint, typecheck, tests). Short-lived
branches: `feat/<phase>-<slug>`, `fix/<slug>`, `docs/<slug>`. Merge via PR
(self-review counts) with squash. Small slices over long branches.

## Commits

Conventional commits: `feat:`, `fix:`, `docs:`, `test:`, `refactor:`,
`chore:`.

## Issues and milestones

- One milestone per roadmap phase (0–10); the phase's tasks become its issues at
  phase start.
- Issues reference rules (`R#`), data-model sections, or ND ids where relevant.
- Ideas that are not the current phase get the `later` label — that is the R23
  scope firewall.
- A phase closes when its exit-criteria checklist (copied into the milestone
  description) is fully ticked.

## Decision log

Binding decisions go into `product-rules.md` (change first, then code) with a
change-log entry. `NOTES.md` is the free-form working journal — what was
tried, what broke, what to revisit. Together they are the project's memory.

## Quality gates (wired in Phase 1)

`cargo fmt --check` · `cargo clippy` · `cargo test` · `pnpm lint` ·
`pnpm typecheck` · `pnpm test` — required green before merge.

## Offline guardrail

Every new dependency must be bundleable (no CDN fonts/icons, no postinstall
network fetches, no telemetry). Frontend deps: pnpm only. A dependency that
wants the network at runtime is rejected.

## Versioning

App follows semver from Phase 10. Database schema versions live only in
`schema_migrations` and are independent of app version.

## Change log

| Version | Date | Change |
| --- | --- | --- |
| 0.1 | 2026-11-23 | Initial workflow from Phase 0 |
