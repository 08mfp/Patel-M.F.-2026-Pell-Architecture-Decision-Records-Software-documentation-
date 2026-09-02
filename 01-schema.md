# 01 - Schema

## The dayKey contract

1. Every loggable entity has `dayKey: String`, format `yyyy-MM-dd`, device-local calendar. It is the query key for every range read. No database index yet: the #Index macro needs iOS 18 and the target is iOS 17, and the derived benchmark keeps unindexed scans honest at evaluation scale. Revisit when the deployment target moves.
2. `dayKey` is computed once at log time and frozen. The absolute `date` is stored beside it.
3. Sleep attributes to the wake day: last night's sleep carries today's dayKey.
4. Day-aggregate entities (steps, energy) use dayKey as their unique identity.
5. Weekly entities use `weekKey: String`, ISO format `yyyy-Www`, unique per row.
6. No entity in one domain references an entity in another domain. Cross-domain joins happen on dayKey in the derived layer only.

## Conventions

- `localId: String` (UUID) on every user-created entity. Stable across export and import.
- Canonical units in storage: kg, ml, minutes, kcal. Display layer converts for imperial users.
- `source: String` on anything importable: `manual` or an adapter key such as `healthkit`.
- Entity resolution fields on anything that can arrive from more than one source: `externalId: String?` (the provider's identifier) and `canonicalId: String?` (set to the winning record's localId when superseded; nil means live).
- Superseded records are never deleted.
- Relationships (SwiftData) exist only within a domain, for example session to sets, plan to days.

## Entity catalog

### Profile

| Entity | Fields |
|---|---|
| UserProfile (one row) | name, sex, age, heightCm, goalType (lose, maintain, gain), activityLevel, usesMetricHeight, usesMetricWeight, calorieGoalOverride?, proteinGoalOverride?, carbsGoalOverride?, fatGoalOverride?, sleepTargetHours, workoutsPerWeekTarget, stepsTargetPerDay, waterTargetMl, goalWeightKg? |

Feature flags do not live here. They live in AppConfiguration (@AppStorage). Research-arm switches (gamification) are flags, not profile fields.

### Nutrition

| Entity | Fields |
|---|---|
| FoodEntry | localId, name, calories, protein, carbs, fat, fiber, sugar, sodium, meal (breakfast, lunch, snack, dinner), note?, date, dayKey, source, externalId?, canonicalId?, catalogId?, gramsLogged?, servingText?, userAdjusted, suppressed |
| WaterEntry | localId, ml, date, dayKey, source, externalId?, suppressed |
| MealPreset | localId, name, createdAt |
| MealPresetItem | localId, presetLocalId, sortOrder, name, seven nutrient fields |
| PlannedMeal | localId, dayKey, meal, status (planned, confirmed, discarded), sourceKind (manual, suggested, preset), name, seven nutrient fields, note?, confirmedEntryId?, orderIndex |
| FavouriteFood | localId, nameKey, displayName, createdAt |

`suppressed` supports the day-exclusive nutrition policy: a manual log on a day suppresses that day's imported aggregate instead of deleting it. Values are kept, only the flag moves (a mirror keeps its values or it is not a mirror), and deleting the last manual row releases the suppression (decision 87).

`FavouriteFood` (SchemaV9, decision 167) is a pin over a history name, not a food library row: `nameKey` is the lowercased trimmed name, the same join key history dedupe uses, and the store upserts by it so a name is starred once however it is typed. Values resolve from the log at render, so deleting the pin changes nothing else. It has no dayKey because it is not loggable, and it travels in the export (format 4).

`catalogId` is the namespaced reference to the food catalog item a log came from ("usda:...", "off:..."), added inert at SchemaV3. It is separate from `externalId`, which stays reserved for user-data import identity: a catalog-sourced log is still a manual entry. `gramsLogged` and `servingText` (also inert from V3) record what quantity was logged, because catalog nutrient data is per 100 g and computed totals alone cannot be re-edited by quantity.

### Training

| Entity | Fields |
|---|---|
| WorkoutSession (strength) | localId, name, minutes, kcal, kcalIsEstimate, note?, date, dayKey, endedAt?, sessionRPE?, source, externalId?, canonicalId?, sets (relationship) |
| ActivityEntry (general activity) | localId, activityType, name, minutes, kcal, kcalIsEstimate, perceivedExertion?, distanceKm?, note?, date, dayKey, source, externalId?, canonicalId? |
| ExerciseSet | localId, sessionLocalId, exerciseId, exerciseName, orderIndex, setIndex, weightKg, reps, seconds?, rpe?, isWarmup, completed, date, dayKey |
| WorkoutPlan | localId, name, note?, isActive, createdAt, days (relationship) |
| PlanDay | localId, planLocalId, orderIndex, name, weekday? |
| PlanExercise | localId, dayLocalId, exerciseId, exerciseName, orderIndex, targetSets, targetReps, isSuperset, restSeconds?, targetSeconds? |
| WeekPlan | weekKey (unique), planLocalId |

Training notes:

- Strength sessions and general activities are separate entities because their reconciliation winners differ (decision 29). Nothing infers kind from field shape; mixed lists are a UI concern.
- WorkoutSession.endedAt nil means the session is in progress. Live logging autosaves against the open row; date is the start, minutes stamped at finish and editable on quick-logged sessions (decision 30).
- ExerciseSet hangs off its session via a cascade delete relationship and keeps sessionLocalId for export fidelity. A session date edit recomputes dayKey on the session and every one of its sets in one store operation (decision 31).
- exerciseId is a stable catalog key frozen into each set; its format is locked in the slice 05 spec so a future catalog swap never rewrites logged sets.

### Sleep

| Entity | Fields |
|---|---|
| SleepRecord | localId, hours, quality (1-5), note?, date (wake day), dayKey, bedtime?, wakeTime?, inBedMin?, coreMin?, deepMin?, remMin?, awakeMin?, source, externalId?, canonicalId? |

One manual SleepRecord per dayKey, fetch or create, repeat logs update the row (decision 32). Imported nights are separate rows reconciled by the sleep policy.

### Journal (mood, habits, context)

| Entity | Fields |
|---|---|
| JournalEntry | localId, date, dayKey, moodScore (1-5), energy? (1-5), stress? (1-5), note? |
| WeeklyPulse | weekKey (unique), score (1-10), loggedAt |
| HabitDefinition | localId, name, symbol, colorKey, intent (build, reduce, observe), weeklyCap?, isActive, sortOrder, createdAt |
| HabitLog | localId, habitId, dayKey, didDo, loggedAt |
| ContextKindDefinition | key, name, symbol, isBuiltIn, isActive, sortOrder |
| ContextTag | localId, dayKey, kindKey, note?, date |
| LifeEvent | localId, label, note?, date, dayKey, createdAt |

### Body

| Entity | Fields |
|---|---|
| WeightEntry | localId, kg, note?, date, dayKey, source, externalId?, canonicalId? |

One manual WeightEntry per dayKey, fetch or create, repeat weigh-ins update the row (decision 32). Imported readings are separate rows reconciled by the weight policy.

No body-composition entities (body fat, lean mass). Excluded by the tone and content rules unless I decide otherwise.

### Imported day aggregates

| Entity | Fields |
|---|---|
| StepDay | dayKey (unique), count, date, updatedAt |
| EnergyDay | dayKey (unique), activeKcal, basalKcal, exerciseMinutes, updatedAt |

Always source healthkit, latest wins, upserted on refresh.

### System

| Entity | Fields |
|---|---|
| PendingHKWrite | localId, kind, payloadJSON, createdAt, attempts, lastError? |
| BadgeAward | badgeId (unique), date |

PendingHKWrite is the write-back outbox: failed HealthKit mirrors queue here and drain on foreground. BadgeAward stores first-earned moments only; badge progress itself is derived, never stored. BadgeAward is written only when the gamification flag is on.

### Deferred entities (blocked on the excluded-pending-decision list)

BodyFatEntry, VitalsDay (HRV, resting HR), coach tracking entities. None of these enters the schema until the corresponding feature decision is made.

## Reconciliation policies

| Domain | Winner | Match key |
|---|---|---|
| Strength workouts (WorkoutSession) | Manual (first-party) | dayKey plus duration overlap |
| General activities (ActivityEntry) | HealthKit import | dayKey plus compatible type plus time overlap |
| Sleep | Manual | dayKey (wake day) |
| Weight | Manual | dayKey |
| Nutrition, water | Day-exclusive: imports land as one aggregate row per day; a manual log suppresses the day's import, the last manual delete releases it; food and water decide independently (decisions 86, 87) | dayKey |
| Steps, energy | Latest import wins | dayKey |

The losing record gets canonicalId set and stays in the store. Deleting a winner releases its claim.

## What is never stored
- Daily summaries, correlations, streaks (the three derived services)
- Nutrition targets from the profile formula (Mifflin-St Jeor), training volume, estimated 1RM, sleep debt, badge progress
- Anything computable from stored records

Backend phase:
- Slice 02: DayKey and weekKey utilities, versioned container, UserProfile, store skeleton (the single write path). Approves this document.
- Slice 03: core logging entities: FoodEntry, WaterEntry, JournalEntry, with store operations and tests.
- Slice 04: developer tooling: DEBUG-only developer console screen, JSON fixtures, launch arguments for seeding and reset. Compiled out of Release builds.
- Slice 05: WorkoutSession, ExerciseSet, ActivityEntry, SleepRecord, WeightEntry, with store operations and tests.
- Slice 06: derived services: daily summaries, streaks, cross-domain correlations. Benchmark test for the performance budget.
- Slice 07: remaining domain entities: plan entities, MealPreset, MealPresetItem, PlannedMeal, HabitDefinition, HabitLog, ContextKindDefinition, ContextTag, LifeEvent, WeeklyPulse.
- Slice 08: DataSourceAdapter protocol, HealthKitAdapter, StepDay, EnergyDay, reconciliation engine, PendingHKWrite outbox.
- Slice 09: JSON export and import, round trip test-enforced.


- One or more slices per tab, roughly: Journal, Diary, Workouts, Today, Trends, Profile.
- Gamification (BadgeAward plus its UI) lands behind the flag late in the frontend phase.
