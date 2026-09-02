# 00 - Architecture

## System overview

- Native iOS app. Swift, SwiftUI, SwiftData. iOS 17+.
- Everything runs on the device. There is no server, no account, no sync.
- Four layers, dependencies point downward only:
  1. Views (SwiftUI, one feature folder per tab)
  2. Derived layer (pure functions, computed on demand, nothing stored)
  3. Store (one write path for all mutations)
  4. Persistence (SwiftData on SQLite) plus adapters (HealthKit)

## The dayKey spine

- Every loggable entity carries an indexed `dayKey` string, format `yyyy-MM-dd`, from the device-local calendar.
- `dayKey` is frozen at log time. Records also keep their absolute `date`. Timezone changes do not rewrite history.
- Sleep uses wake-day attribution: last night belongs to today's dayKey.
- Weekly rows use `weekKey`, ISO format `yyyy-Www`.
- This is the only unification contract. No cross-domain foreign keys, ever. Cross-domain questions are answered by joining on dayKey in the derived layer at read time.

## Data flow

- Reads: views query SwiftData directly or call derived services.
- Writes: every mutation goes through one store service. Views never write to the model context directly. A test will grep-enforce this once the store exists.
- Derived layer: three services, all pure and stateless: daily summaries, cross-domain correlations, streaks. Domain math (nutrition targets, training volume, estimated 1RM, sleep assembly) lives in pure helper types beside them. Nothing derived is ever persisted.
- Performance budget: any derived computation over a 90-day window must feel instant in the UI. A benchmark test guards this once the derived layer exists.

## Extensibility seam

- `DataSourceAdapter` protocol: source key, supported domains, per-domain read and write capability, idempotent import over a day range.
- `HealthKitAdapter` is the first and only conformance. A second provider is a new conformance plus registry entry, nothing else changes.
- Imports are idempotent via `externalId`. Re-importing a range never duplicates records.

## Entity resolution and provenance

- Records that can arrive from more than one source carry `source`, `externalId`, and `canonicalId`.
- When two records describe the same real-world event, reconciliation picks a winner by the source-of-truth rules in CLAUDE.md. The loser gets `canonicalId` set to the winner. That is the superseded flag.
- Superseded records are never deleted. They stay for provenance and evaluation integrity.

## Feature flags

- `AppConfiguration`, an @Observable class seeded from `UserDefaults` at init and written back on change, injected through the SwiftUI environment at the app root. Amended in phase 2A: the original @AppStorage struct could not drive live updates from inside an environment value (decision 6).
- Views read flags from the environment only. No view touches `UserDefaults` directly. Grep-enforced by a test.

## Storage mechanics

- SwiftData store on device. Versioned schema from day one; every schema change ships with a migration stage.
- Safety: the store file is backed up before every open (keep a small rotation). A failed open shows a recovery screen with export, retry, and start-fresh. Never auto-wipe.
- Full-fidelity versioned JSON export and import of every entity and field. Round trip enforced by a test.
- Widgets (later phase) read a snapshot via an app group. The widget never computes health data.

## Processing rules

- All analytics run on device, on demand, at read time.
- No background processing except local notifications and the HealthKit refresh on foreground.
- No networking anywhere in the local-only arm, grep-enforced by a test. The live-api arm (decision 10 amendment, 2026-07-28) allows reference catalog queries only; its guard allowlists the catalog client instead of flat-banning networking. Health records never go outbound on either arm.

## Target folder structure

```
Pell/Pell/
  App/        PellApp, RootTabView, AppConfiguration
  Models/     SwiftData entities, one file per entity
  Store/      the single write path, persistence setup, migrations
  Derived/    daily summaries, correlations, streaks, domain math
  Adapters/   DataSourceAdapter, HealthKitAdapter
  Features/   Today, Diary, Workouts, Journal, Trends, Profile
  Design/     theme tokens, shared components, copy catalog
```
