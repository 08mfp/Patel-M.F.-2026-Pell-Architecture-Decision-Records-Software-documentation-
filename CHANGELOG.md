# Changelog

Latest changes are at the top of the document


## Slice 12, review fixes (2026-08-02)

- Issue #94 tracks the batch: the PR #93 review's seven bugs and five improvements, one commit closes it.
- A session left open on a past day is no longer resumed silently by start: the day card gains a seventh state naming the open session with resume, quick log, and log activity, and the minimized bar names the day. New sets can no longer land on yesterday through today's start button.
- The set history reads (sets(exerciseId:), sets(from:to:)) count finished unsuppressed sessions only, filtered once on the store; detail page records, focus mode's last-time baseline, the summary PR feed, progress, and the week volume stat all read the same numbers now. The duplicated call-site filters dropped out of WorkoutsView and WorkoutProgressView.
- The wger image host guard matches the exact host or a real subdomain; the suffix match would have passed foreign domains ending in wger.de.
- PREvent is Identifiable from exercise and instant together; the summary and progress record feeds no longer key rows by date alone, which two exercises could share.
- The wger entity decode runs in a fixed order with the ampersand last; dictionary order could double-decode an escaped entity.
- Quick log adopts an empty leftover row when reopened after a kill instead of minting another, and Done stays disabled until a set or a duration exists, so an empty trained day cannot persist.
- Online exercise search cancels the in-flight query when the text changes; a stale result can never land under new text.
- The library and the mid-session picker share one copy of the chip row, the bundled ranking (BundledExerciseCatalog.ranked), and the online section (ExerciseSearchShared.swift); about ninety duplicated lines gone.
- The progress view fetches once per render and feeds all four sections; the record feed states its cap when it hits it. The plate calculator bar snaps to the nearest option after a unit switch; the exercise detail page uses the shared weight text helper.
- One test setup updated (setsInRangeFiresObservationOnAWrite finishes the session before counting). 536 unit tests green in a parallel run.

## Slice 12, phase 12G - Copy audit, tests and docs (2026-08-02)

- The recorded copy audit in docs/slices/slice-12-copy-audit.md: every user-facing string in the slice per surface, the notification explainer and body and both attribution lines included. Zero violations; two wording choices recorded ("Best estimates" over any celebrating header, the rest day card stating the plan fact while offering every logging action).
- ExplainerRegistryTests: registry ids unique, every id the workout surfaces wire resolves (a mistyped id renders nothing, silently), entries never empty.
- Release build clean: zero tooling strings in the binary, no dangling slice TODOs, no stale placeholder references.
- Decisions 94 to 99 recorded as built (catalog seam with minted slugs, the wger stance with inline transient images, explainer-then-request rest notification, presentation preferences in AppConfiguration, muscle balance's other bucket, suggested plans as copies).
- Formulas registry grew muscle balance and the plan adherence count; 03-rejected records the distance field omission, the no-account-surface line, and the copy audit's two rejections.
- Full suite green in a parallel run; write guard, DevTools guard, and the three-file no-network guard all green.

## Slice 12, phase 12F - Activities, progress and history search (2026-08-02)

- ActivityLogSheet: the curated picker over the MET table sorted by display name, minutes, optional perceived exertion, note. kcal prefills from the MET estimate and follows the activity and duration until one edit stops the following (an onChange arrives after the update, so the autofill is told apart by comparing against the last autofilled value, a live find fixed in phase); an edited figure is no longer marked estimate. The range hint spans the activity's MET codes; without a weigh-in the footer states plainly that no estimate is made.
- The Activities section on the day view lists the day's entries beside the sessions (minutes, kcal with its estimate mark, exertion, imported label), manual rows edit and swipe delete, and the log action lives in the section so every day state offers it, trained days included (a second live find: the day card's log entry only existed on planned, rest, and empty states).
- WorkoutProgressView behind the chart toolbar entry: the weekly volume bar chart at 8, 13, and 26 week zooms (zero weeks kept visible), muscle balance as descriptive volume shares per primary muscle group with the named other bucket, per-exercise rows with best estimated 1RM into the existing detail page (logged-only slugs get a working page too), and the PR feed newest first. Suppressed and unfinished sessions stay out of every number; no targets drawn anywhere.
- WorkoutSearch pure helper in Shared (case-insensitive contains over session names, set exercise names, and activity names; suppressed excluded; newest first) and WorkoutSearchView behind the search toolbar entry, results jumping the tab to the day.
- Seven explainer entries written against the formulas registry: volume load, estimated 1RM, records, planned days trained, the MET estimate, muscle balance, session RPE; info affordances wired across the quick stats, week overview, session summary, activity sheet, and every progress header.
- WorkoutPlaceholders.swift deleted: the last placeholder surface is gone and the tab is fully live.
- 7 new tests: search by session name, by exercise name, activities with newest-first ordering, suppressed and empty-query emptiness, balance shares with the other bucket, balance emptiness without volume, the range hint naming the band and only the band, display names.
- 534 unit tests green. Verified live in the simulator panel: the walk estimate following duration then saving with its estimate mark, the progress chart and shares over the fixture, and a deadlift search jumping the tab to 28 Jul.

## Slice 12, phase 12E - Plans, assignment and suggested library (2026-08-02)

- PlanListView: this week's governing plan as a plain fact (pinned wins, else the active default, else none stated plainly), a pin menu over the user's plans with pin removal, the plans list with the active badge and day counts, swipe delete behind a confirm naming the day count and the pins that clear, new plan.
- PlanBuilderView: name and note through updatePlan, make active through setActivePlan, the ordered day list with drag reorder, swipe delete, add day. PlanDayEditView: name, the optional weekday pin with a footer naming exactly what a pin does, ordered prescriptions with drag reorder and swipe delete, add through the shared ExercisePickerSheet. PlanExerciseEditSheet: sets and reps steppers, a timed set toggle prescribing seconds instead of reps, superset toggle, and the rest target picker that feeds the live timer.
- SuggestedPlans in Catalog/: four curated plans (full body 3 day, upper lower 4 day, push pull legs 6 day, bodyweight 3 day) built only from bundled slugs with weekday layouts, display names resolved from the bundled catalog at import. importPlan copies through createPlan, addPlanDay, and addPlanExercise; the copy arrives inactive and unpinned, so governing a week stays the user's explicit choice.
- SuggestedPlansView and its detail: read-only days and prescriptions with superset tags, one Add to my plans action per plan.
- The Plans entry joins the tab toolbar beside the exercise library.
- 5 new tests: every suggested slug resolves in the bundled catalog, prescriptions well formed with unique weekdays per plan, import copies the whole shape verbatim with catalog names, editing an import never reaches the library (a re-import matches the data again), and import never activates or pins.
- 527 unit tests green. Verified live in the simulator panel: the library and detail, an import landing inactive beside the active fixture plan, the pinned fixture week still governing over the newly activated import (pin precedence), the builder and day editor over the copy.

## Slice 12, phase 12D - Focus mode and session summary (2026-08-02)

- FocusModeView: one exercise at a time, entered from the live screen's tools menu, opening on the first exercise with work remaining. Same SetRow rows and the same store operations, so warm up and timed sets behave identically to the standard screen. Inline rest countdown reusing the 12C timer bar with the same target resolution, notification, and cancellation. Next and previous walk the session's exercises.
- The last time baseline: the most recent earlier day's completed working sets through sets(exerciseId:), rendered under a dated header as plain lines. A fact, not a target and not a suggestion.
- SessionSummaryView: one layout for the finish summary and the past session detail. Volume load, duration, set count, per exercise breakdown with per exercise volumes, and the best estimates the session produced through the strict TrainingMath PR feed (full per-exercise history, filtered to this session's set dates). A PR line is a dated fact carrying its date. Session RPE renders as an editable row through updateSession, the after the fact path.
- The finish flow: Finish prompts session RPE 1 to 10 with Skip and Cancel, writes once through finishSession(sessionRPE:), then shows the summary; Done closes the cover.
- SessionExerciseSections extracted from the live screen: the exercise and set sections (set rows, add set, add exercise, swap) now shared by live, quick log, and the detail edit, so exactly one write path exists. LiveExercise carries its prescription line.
- QuickLogView replaces the 12B placeholder: creates the finished row on the displayed day at the wall clock time (DayInstant), name, minutes, and kcal fields, the shared exercise sections, no timer anywhere, cancel deletes the fresh row (confirmed once sets exist), done ends in the same summary. The live screen's write path with the clock taken out.
- Past sessions open from the recent list and the trained card into the detail: the same summary plus edit (name, minutes, kcal, note, and sets through the shared sections; Save confirms out of edit mode) and delete with a confirm. Imported sessions keep their source label row on the detail.
- Verified live end to end on an empty store: blank start, picker adds, focus with inline rest and both navigation directions, the RPE prompt into the summary, a backdated quick log whose 62.5 kg bench correctly took the record off today's chronologically later 60 kg session, detail edit, counted delete.
- 521 unit tests green.

## Slice 12, phase 12C - Live logging, rest timer, and plate calculator (2026-08-01)

- LiveSessionView over the single open session: starting from a planned day prefills the prescriptions (re-derived from the day's resolved plan on every open, so resume restores them), an unplanned day opens blank. Exercises add from the catalog picker mirroring the 12A library surfaces (bundled browse, muscle chips, online arm behind its flag). Exercise swap within the same primary muscle group over the bundled mapping; logged sets keep their frozen exerciseId, a section with history keeps rendering and the replacement joins after it.
- SetRow: weight in the profile unit, reps, RPE 1 to 10, completed toggle; timed sets through seconds and warm up marks in the context menu. Every field commit writes through updateSet immediately; autosave is the store's per operation save, nothing buffered, kill-proof (verified by killing the app mid session and resuming from activeSession()).
- Minimize and resume: the session floats as a bar above the tab (name, set count, rest countdown), tapping returns to the full screen cover. Start is a today action; past days retro log through quick log, because a backdated live start would fabricate the finish stamp's duration.
- Rest timer: RestTimerState pure wall clock countdown, started when a set is marked completed. Target from the plan exercise's restSeconds when present, else the adjustable default in AppConfiguration. In app countdown always; one local notification with a fixed identifier fires only if rest ends in the background, foreground end cancels it, a reschedule replaces so never more than one. The body is one neutral note ("Rest over").
- NotificationExplainerSheet: the first timer start presents a one time plain sheet before the system prompt, naming exactly what will be sent and nothing beyond it. The seen flag persists in AppConfiguration; any dismissal counts as seen, denied means in app countdown only, no reprompt.
- PlateCalculatorSheet: unit aware from the profile, per side breakdown over integer-hundredth greedy math, leftover and below-bar as plain facts, bar choice persisted in AppConfiguration.
- AppConfiguration: defaultRestSeconds, plateBarWeightKg, restExplainerSeen, same UserDefaults pattern, device local view state only.
- 16 new tests: plate math in both unit systems (exact cover, repeats, bar only, below bar, leftovers), rest target resolution (plan present, absent, default changed), and the timer state helper (wall clock countdown, round up, isOver at the end, progress bounds, non-positive clamp).

## Slice 12, phase 12B - Workouts today view (2026-08-01)

- WorkoutsView replaces the placeholder: day navigator with forward movement uncapped so planned days preview, the state-aware day card, quick stats, the week overview, recent sessions; the exercise library moves from the 12A temporary row to a toolbar entry.
- WorkoutDayCard: six states resolved from the day's sessions and the governing plan. The open session wins (resume), then finished sessions (trained, one summary line each), then the resolved plan day decides planned, future preview, or rest day; empty otherwise. Prescription rows render sets x reps, timed sets in seconds, supersets marked. Imported sessions carry a plain source label; suppressed rows never render.
- WeekOverviewRow: WorkoutWeek pure helper (the ISO week's seven dayKeys, Monday first, matching the weekKey that picks the governing plan), seven chips planned versus done (done fills, planned rings, unplanned untrained stays a faint dot), adherence as a plain count through the Adherence helper ("2 of 3 planned days trained", "No plan this week").
- WorkoutPlaceholders: live logging, session detail, quick log, and activity log placeholders with /// bullet contracts for 12C to 12F.
- PellStore: sets(from:to:) range read feeding the weekly volume stat; revision touches across the workout fetch family (activeSession, sessions on and range, activities on and range, plans, activePlan, weekPlan, resolvedPlan), the slice 11 diary refresh pattern generalized per the standing note.
- The standard fixture already carries one plan with an assigned week, so the planned and rest states verified live without fixture growth.
- 5 new tests: sets range bounds inclusive and sorted, observation firing on the range read, and the three refresh regressions across the session, activity, and plan fetch families.
- 505 unit tests green in a parallel simulator run.

## Slice 12, phase 12A - Exercise catalog, bundled and online (2026-08-01)

- ExerciseCatalog seam mirroring the food catalog shape: ExerciseCatalogProvider (key, search), CatalogExercise, MuscleGroup.keys as the ten recorded app-owned strings, ExerciseSlug.mint as the deterministic minting rule (lowercase, diacritics folded, non-alphanumeric runs to one hyphen) so external ids map onto ours and never the reverse.
- BundledExerciseCatalog: 40 curated exercises as embedded JSON rows (slug, name, primary, secondary, equipment, instructions), the six fixture lift slugs unchanged so seeded history joins the catalog, bundled: ids, prefix-then-contains search, always available, no flag, no network.
- WgerExerciseCatalog: the third networked file. Explicit submits only, English filter, User-Agent naming me, HTML instructions stripped to plain text (pinned trap), image bytes fetched through the client and handed over transient (foreign image hosts refused without a network call), unknown detail is a plain not-found, no API key exists to compile. onlineExerciseSearchEnabled flag added, default off, beside the food flag.
- ExerciseLibraryView: search over both providers, muscle filter chips, online section behind the flag with the CC BY-SA attribution line. ExerciseDetailView: muscles worked (unmapped online muscles show their wger name verbatim), equipment, instructions, the online image inline, per-exercise history through the new sets(exerciseId:) read when logged sets exist.
- No-network allowlist grows to exactly three files; AsyncImage joins the banned tokens so no view can fetch on its own; the planted-violation red test stands.
- PrivacyDisclosure gains the wger line naming the whole payload; docs/references/wger.md written as built.
- 20 new tests: bundled decode of all forty, slug uniqueness, the six fixture slugs, muscle keys from the recorded set, search ordering, minting pins (determinism, deliberate collision, fixture style, unicode), stubbed URLProtocol client cases (suggestions with base-id dedupe, empty and malformed, empty query never touches the network, detail parse with HTML stripping, not-found, transport failures, image bytes), and the two sets(exerciseId:) store tests.

## Slice 11, review hotfix - Parser ranking, water suppression, camera consent (2026-07-31)

- Pre-merge review of PR #75: four parallel review passes (parser and search, network clients, store and imports, UI surfaces) with every critical verified directly against the head commit before acting. Three criticals documented as inline review comments on the PR, fixed on `slice11-hotfix`, PR #85 into `slice11-diary`.
- Meal parser: best() took the first name in list order per tier and ran every tier for the plural before trying the singular, so common words matched the wrong shipped row ("eggs" to Scrambled eggs, "egg" to Egg noodles, "milk" to Milkshake, chocolate); the sheet's own placeholder sentence misparsed. Now scored tiers, strongest first: whole name, the name before its first comma ("Egg, whole"), the comma base's last word ("Whole milk"), any standalone word, then raw prefix and contains; ties go to list order and the plural and singular compete inside every tier. The golden mini corpus could not see these collisions, so two shipped-catalog goldens now pin eggs, milk, bread, rice, and the placeholder sentence (the catalog has no plain toast row; the toast-headed name is the pinned match). Side effect in line with the documented history-first rule: history now wins across both plural and singular forms before the catalog is consulted.
- Water totals: waterTotal summed every row, so day-exclusive suppression was written but never honoured where water renders; a manual glass beside a 1500 ml imported aggregate showed 1750 ml. Now filters suppressed, regression tested on the mixed day. The existing test asserted the flag but never the total, which is why it stayed green.
- Camera consent: DataScannerViewController.isAvailable is false until access is granted and nothing ever raised the prompt, so scanning was permanently dead on a fresh device install, invisible in the simulator whose only path is the manual field. The sheet now requests consent on first open when authorization is undetermined, then re-evaluates scanner availability.
- Moderate and minor review findings moved out of slice scope to the time permitting improvements milestone as issues #76 to #84.
- 478 plus three new regression tests, green in a parallel run.
- One commit: review criticals: parser ranking, water total suppression, camera consent.

## Slice 11, phase 11L - Capture declarations, copy audit, tests, and docs (2026-07-31)

- INFOPLIST_KEY_NSCameraUsageDescription in both build configurations, worded for barcode reading only, beside the HealthKit pair: "Pell uses the camera to read food barcodes. No images are stored or sent."
- PrivacyDisclosure.swift holds the "what leaves this device" copy as data, one line per outbound or sensor surface (USDA text search, OFF barcode lookup naming the whole payload, the camera, the local-only boundary), TODO(15) for the privacy center to render; the copy audit covers the wording before the surface exists.
- docs/references/open-food-facts.md created beside the USDA notes: license and attribution posture, the no-images rule, the User-Agent contract, rate limits, the endpoint and envelope as built, the field mapping with both pinned traps, the client stance.
- usda-fdc.md corrections: the stale usda: namespace line now reads fdc: as shipped, and the gtinUpc note points at the OFF decision.
- CLAUDE.md amended per the addendum: the Diary scope gains the four capture paths and the live-api amendment wording gains Open Food Facts and the camera as a declared sensor surface.
- Decisions 89 to 93 locked (the draft block promoted unchanged); the SavedFood deferral in 03-rejected confirmed.
- Copy audit appended for the capture surfaces: scan sheet states, confirm cards, variant groups, the disclosure lines, the camera string. Zero tone violations.
- Export consequence: none. No entity and no field was added across 11H to 11L; catalogId, gramsLogged, and servingText already travel, and the coverage guard is untouched.
- 478 unit tests green; Release build clean.

## Slice 11, phase 11K - Re-log catalog foods from history (2026-07-31)

- FoodSearch.HistoryFood grew optional catalogId and gramsLogged; distinctByName carries them through dedupe (decision 93).
- FoodSearch.catalogFood(from:) reconstructs per-100 values as value divided by gramsLogged times 100. Whole-kcal rounding at log time makes the calories approximate, which the quantity preview shows honestly. Nil, zero, or negative gramsLogged means no reconstruction: the row re-logs stored values as plain history, so no divide by zero exists.
- History and recents rows with provenance reopen CatalogQuantitySheet at a fresh quantity, offline, zero network. Plain one-tap rows and chips carry provenance onto the new entry, so a re-logged catalog food stays re-loggable.
- Three tests: reconstruction arithmetic, the guard cases, provenance through dedupe and ranking. 478 unit tests green.
- Two defects found during verification, both fixed:
  - The five sheet modifiers stacked on the add-food list collapsed into one enum-driven presentation slot, sturdier and only one can show at a time.
  - Row buttons with plain style had a dead zone over their Spacer: taps between the name and the kcal text did nothing. Every diary row label gained a rectangle content shape (add-food history and catalog rows, scan result, confirm cards, variant rows, diary entries, history search, presets, planner suggestions).
- Verified live: the Mars history row reopens the quantity sheet with reconstructed per-100 values (450 kcal per 100 g), tapped mid-row, offline.

## Slice 11, phase 11J - Variant disambiguation (2026-07-31)

- CatalogFood grew a category field; the USDA parser carries FDC foodCategory onto it. Bundled and OFF rows leave it nil, so nothing changes for them.
- FoodVariantGrouping, pure: a chain is an FDC restaurant category (Fast Foods, Restaurant Foods) plus a description prefix that is not a generic marker; generic restaurant rows ("Fast foods, hamburger; single patty") group as plain because they are the generic item. The base word variants share comes off the name after any prefix. A sheet is worth showing only when more than one variant exists and at least one carries a chain; otherwise the tap goes straight to quantity.
- FoodVariantSheet: two descriptive sections, homemade or generic against restaurant with the chain named as a caption, every row into the existing quantity sheet. Names sources, ranks nothing, recommends nothing.
- Catalog result taps route through the grouping; bundled and online results pool so bundled prepared dishes join the plain group.
- Seven helper tests over the 2026-07-31 live-test hamburger corpus: chain extraction, generic markers, base words, both-direction grouping, the bundled join, the no-sheet cases.
- Verified live: "hamburger" search opened the variant sheet with the Fast Foods generic rows under homemade or generic and Mcdonald's, Burger King, and Wendy's rows under restaurant, chain captions on, ending in the quantity sheet with a correct preview.

## Slice 11, diary refresh fix (2026-07-31)

- Found during 11I verification, pre-existing since 11C and diary-wide: entries logged from any sheet never appeared until a day navigation. Root cause: the store's context is observation-ignored and a successful write changes no observed property, so a body that only calls fetch methods registers no dependency and never re-renders.
- Fix inside the locked architecture: PellStore gains a revision counter bumped by every successful save, and the diary-facing fetch methods (food, water, tags, kinds, presets, planned meals, profile, allRows) touch it so any body that fetches registers the dependency.
- Regression test: a saved write bumps the counter and fires observation on a tracked fetch. Verified live: a one-tap log renders in the diary list and summary ring immediately, no day navigation.
- Other tabs fetch the same way and may want the same touch in their read methods; noted in status for their slices.

## Slice 11, phase 11I - Natural language meal entry (2026-07-31)

- MealTextParser, pure and deterministic: NLTokenizer word tokens, splits on commas and conjunction words (and, with, plus, then), a leading numeral or quantity word peels off with one as the default, fragments match logged history first then the bundled catalog (exact beats prefix beats contains, plural retries as singular), unmatched fragments carry their text as name-only proposals. A multi-fragment piece naming a whole dish ("fish and chips") outmatches its own split.
- MealTextEntrySheet: sentence field, explicit Preview button, one editable card per proposal (source caption, per-unit kcal times quantity), swipe to delete, tap to edit through the custom form handing values back instead of logging, confirm-all logs every card through logFood. Catalog-matched cards log one serving with catalog provenance; edited cards become plain values. Simple mode logs name and meal only.
- CustomFoodForm grew optional initial values and an onSave hand-back, so the confirm cards stay unwritten until confirm-all.
- Describe a meal entry point beside the scan row; on device, so behind no toggle. Explainer entry for how typed meals are read.
- Eleven golden tests over a fixed corpus: splits, quantities, whole-dish wins, history-before-catalog, plural fallback, unmatched carry-through, a lone quantity word staying a name, degenerate input.
- Verified live: "Two eggs and toast with butter, banana" parsed to four cards, the toast mis-match swipe-deleted, Log 4 wrote correct rows (Scrambled eggs one 122 g serving at 182 kcal).

## Slice 11, phase 11H - Barcode scanning and the Open Food Facts provider (2026-07-31)

- BarcodeCatalogProvider protocol beside the search seam: lookup(barcode:) returning an optional CatalogFood, nil meaning unknown barcode as distinct from a thrown transport error (draft decision 89).
- OpenFoodFactsCatalog: the app's second and last networked file. v3 product GET with a fields filter and the required app User-Agent; per-100g mapping with the two pinned traps (sodium grams to mg, absent kcal falls back to kJ / 4.184); serving_size and serving_quantity onto servingText and servingGrams; off:<barcode> ids; HTTP 404 product_not_found surfaces as plain not-found (draft decision 90).
- Session-only in-memory rescan cache keyed by barcode behind an actor: absorbs repeat recognitions inside the 15 per minute read budget, dies with the process, nothing catalog-shaped reaches disk.
- BarcodeScanSheet: VisionKit DataScannerViewController when supported, manual barcode field as the fallback and the simulator verification path; found, not-found (custom form one tap away), and error states in plain wording; ODbL attribution on the result; funnels into CatalogQuantitySheet. Entry point renders in the add-food sheet behind the online catalog toggle.
- No-network allowlist grown to exactly two files, so the guard stays green this phase.
- Completed the missing code half of the earlier Atwater commit: USDA parse now falls back to nutrient 2047 when 1008 is absent; the test half was already committed and red.
- Six stubbed OpenFoodFactsCatalog tests: envelope parse with the two traps and the User-Agent, kJ fallback, 404 as not-found, non-200 and malformed as errors, cache absorbing a rescan against a dead transport, invalid barcodes never touching the network. 455 unit tests green.
- Verified live in the simulator through the manual field: Mars bar looked up (450 kcal per 100 g), rescan served from the cache, logged twice to breakfast, summary math correct (900 kcal, 8 g protein). One transient diary list refresh gap observed and noted for a separate investigation; not a capture-path issue.

## Slice 11, phase 11G - Copy audit, tests, and docs (2026-07-31)

- The recorded copy audit (docs/slices/slice-11-copy-audit.md): every user-facing string per surface checked, zero tone violations. Negative remaining renders as a signed number, imports never render with framing, errors state facts.
- Found in the full run and fixed: pre-V6 export files failed to decode because the two new profile keys were required. The profile and kind DTOs gained lenient decoders (source keys default manual, colorKey defaults gray), so old files restore with defaults as decision 83 promises.
- Full unit suite green (450 passed); Release build clean of tooling strings; live simulator verification: the migrated store opened, the diary rendered with the ring correctly targetless (no weigh-in on record), and the add-food search showed all three sections with the fixture history, the bundled catalog prefix-ranked, and the online arm waiting for its explicit tap.
- Docs: decisions 83 to 88 recorded (83 carrying the duplicate-checksum finding and the lenient-decode note), 01-schema corrected (suppress-only wording, the grown reconciliation row), usda-fdc.md as-built notes, status closes the slice.
- One commit: slice 11 copy audit, tests and docs.

## Slice 11, phase 11F - Dietary and water imports (2026-07-31)

- The adapter seam grows to seven domains: DietaryDayDTO and WaterDayDTO as day-addressed statistics aggregates, protocol reads with default-empty implementations, HealthKitAdapter statistics-collection reads over the seven dietary types and water volume, FakeAdapter scripting for both.
- Quarantine: negative values and the 300-second future skew, both sides tested.
- PellStore import upserts on the existing entities with day-keyed externalIds (hk-dietary-<day>, hk-water-<day>), idempotent by test; no new entities, exactly as the suppressed field has waited for since slice 3.
- The day-exclusive arm as a pure Reconciliation policy: any manual row on the day suppresses every imported row, values kept; logFood, addWater, deleteFood, and subtractWater reconcile their day, so the last manual delete releases the import; food and water decide independently. subtractWater now targets manual rows only, since a mirror is never the undo target.
- SyncEngine carries both domains through backfill and refresh with SyncLog counts; the console connect button points all seven sources at HealthKit; the console's stale schema label (still reading V3) fixed to the live version.
- 8 new tests: both upserts idempotent, fill-then-suppress, release on last delete, domain independence, subtract ignoring mirrors, quarantine both sides, the engine end to end through the fake with a second refresh proving upsert.
- One commit: dietary and water imports.

## Slice 11, phase 11E - The food catalog, bundled and online (2026-07-31)

- The FoodCatalog seam: CatalogFood with per-100g nutrients and namespaced ids, one provider protocol, CatalogMath for quantity scaling. A catalog hit logs as a manual FoodEntry carrying catalogId, gramsLogged, and servingText through the grown logFood, so the quantity stays editable, decision 36 as designed.
- BundledFoodCatalog: 352 curated common foods as embedded JSON (values per 100 g in the style of USDA SR Legacy public-domain data; approximations, regenerable straight from FDC as a recorded data task), prefix-then-contains search, behind its flag, no network ever.
- USDAFoodCatalog: the app's one outbound surface. Explicit-submit search only, never per keystroke; results transient and discarded with the screen, nothing cached or persisted; compiled key; errors mapped to plain wording; the session injectable so tests stub the transport.
- The no-network guard is born: every Swift file in the app target is scanned for networking symbols with exactly one allowlisted file (USDAFoodCatalog.swift), red-tested on a planted violation and reverted.
- AddFoodSheet gains the Catalog and Online sections behind their toggles, the quantity sheet with serving shortcut and scaled preview, and the per-100g explainer.
- 6 new tests (bundled decode and search, scaling arithmetic including the negative clamp, stubbed-transport success, plain error surfacing with recovery, empty-query short circuit, the guard), serialized around the shared stub.
- One commit: food catalog, bundled and online.

## Slice 11, phase 11D - Presets, copy day, and the planner (2026-07-30)

- Meal presets: browse with item and calorie counts, compose from scratch (the preset is created on first name entry so items land somewhere real), edit items, delete, save a logged meal as a preset from the diary, and one-tap log-to-meal through applyPreset onto the addressed day.
- Copy previous day: manual unsuppressed rows only through the pure FoodSearch.copyable filter, behind a count-and-confirm naming the target day, per-entry through logFood.
- The weekly planner: Monday-first week strip over the pure PlannerWeek helper with planned-slot dots, per-meal slots with confirm (writes the real entry through confirmPlannedMeal), discard keeping values, delete; frequency-recall suggestion rows as plain counts ("4 of the last 5 Mondays") through the existing PlannerMath, one tap plans the suggestion; copy previous day's plan and repeat last week.
- 2 new helper tests (copyable filtering, ISO week assembly).
- One commit: presets, copy day and planner.

## Slice 11, phase 11C - Add-food flow and editing (2026-07-30)

- AddFoodSheet per meal: quick-add chips (most frequent recent names), recents, search-as-you-type over logged history through the pure FoodSearch helper (distinct names newest-values-first, manual unsuppressed rows only, prefix matches ranked before contains), one tap re-logs with the last values. The custom seven-nutrient form logs anything new; simple mode collapses it to name and meal.
- FoodEntryEditSheet: per-field editing through updateFood (date and dayKey stay the store's job), the catalog provenance line when present, delete behind a confirm through deleteFood.
- FoodHistorySearchView: "when did I last eat X" over the whole log, day-grouped, tap jumps the diary to that day.
- DiaryView wires the flow: add buttons per meal group, tap to edit, the history search in the toolbar.
- 3 new FoodSearch tests (distinct with import and suppression exclusion, prefix ranking, frequency with recency ties).
- One commit: add food flow and editing.

## Slice 11, phase 11B - Diary day view, summary, and water (2026-07-30)

- DiaryView replaces the placeholder: shared DayNavigator, entries grouped Breakfast, Lunch, Snack, Dinner in time order, suppressed rows invisible, day tag chips coloured by their kind, and the not-tracking declaration (declare and undo through the built-in tag; a declared day hides its card and entries behind the declaration).
- DiarySummaryCard: calorie ring against the computed target (profile overrides win, else the Mifflin-St Jeor recommendation from the latest unsuppressed weigh-in on or before the day, absent when neither exists), protein, carb, and fat bars against their targets, signed remaining, the seven-nutrient disclosure. Simple mode swaps the card for entry and meal counts; hide-numbers blanks every numeral. Explainers on the target and remaining.
- WaterRow: glass add and subtract against the profile target through the existing store operations.
- Features/Shared gains DayInstant (day-addressed writes from views keep the wall-clock time of day) for the flow phases.
- 5 new helper tests (ring and bar clamps, signed remaining, override precedence, DayInstant day landing); write guard green.
- One commit: diary day view, summary and water.

## Slice 11, phase 11A - SchemaV6, flags, and export growth (2026-07-30)

- SchemaV6, additive: dietary and water source keys on the profile (completing the decision 50 per-domain pattern for the two domains landing in 11F), the slice 09 riders (lastAutoExportAt and lastRestoreAt on SyncState), and colorKey on context kinds. Headline test: a populated V5 store reopens under V6 intact with defaults on migrated rows.
- Found and fixed at the container: passing the staged migration plan now crashes Core Data with "Duplicate version checksums detected" at six versions, because every versioned schema shares the same live model classes and checksums identically. The container opens with automatic lightweight migration instead; the declared chain (six schemas, five stages) remains the version paper trail, and a future non-additive change must bring per-version model snapshots. Verified live: the existing V5 simulator store migrated and opened clean.
- AppConfiguration gains the four diary flags (bundled catalog on, online search on per the live-api arm, simple diary off, hide numbers off), with the default-true seeding pattern and persistence tests.
- Export growth forced by the coverage guard: the profile DTO carries the two new source keys (which also join the restore coupling reset), the kind DTO carries colorKey, builders updated, format pin moves to 6.0.0 with its live-schema guard test.
- The tag colour rider: custom context kinds get the colour picker row and coloured labels in the manager.
- 4 new tests (V5 to V6 headline, flag defaults and persistence); schema tests updated to the no-plan container shape; full clusters green.
- One commit: schema v6, flags and export growth.

## Slice 10, phase 10F - Copy audit, tests, and docs (2026-07-30)

- The recorded copy audit (docs/slices/slice-10-copy-audit.md): every user-facing string in the slice listed per surface and checked against the tone rules. Zero tone violations standing; one clarity fix applied during the audit (the below-floor line gained the unit on its second count, caught live in the simulator).
- Live verification in the simulator against the seeded fixture: the recovery screen with Start fresh exercised for real (the sim carried a pre-V5 store; the set moved aside and the app opened empty, the first live proof of the slice 09 recovery path), the Journal home with check-in card, pulse, and sleep rows, the calendar with the ramp and the fixture's two empty days blank, the insights below-floor states with their info affordances.
- Full unit suite green (423 passed); Release build clean of tooling strings; both guards green.
- Docs: decisions 67 to 72 recorded, status closes the slice.
- One commit: slice 10 copy audit, tests and docs.

## Slice 10, phase 10E - Habit insights and explainers (2026-07-30)

- The explainer system in Features/Shared: Explainer entries (title, plain-language formula account, limits), ExplainerRegistry, ExplainerSheet, and the ExplainerButton info affordance. First five entries written against the formulas registry: the did and didn't averages, the 5-day floor, the weekly cap count, the mood colour legend, the recall default.
- Habit insights: the four default comparisons per habit through the existing HabitInsights engine over a 90-day window, two plain averages with their day counts, binary outcomes read as day counts ("6 of 10 days"), the below-floor state naming the group counts with no averages, no verdicts or significance language anywhere.
- Info affordances wired onto every computed number already shipped: the weekly cap count, the calendar mood legend, the check-in recall footer, and the insight sections.
- One commit: habit insights and explainers.

## Slice 10, phase 10D - Sleep logging and the day story (2026-07-30)

- Sleep log sheet: wake-day addressing with the rule named in the footer ("last night is logged to today"), hours slider 0 to 24 in quarter steps, quality score, note, one manual row per day through the upsert; imported nights stay separate and reconcile under the locked policy.
- The day story sheet: one day stitched across the four domains on the dayKey spine, read-only. Journal (scores, note, done habits, tags), movement (sessions with set counts, in-progress named; activities), sleep (manual first, else the unsuppressed imported night marked Imported), food and water totals, steps and weight. Sections with no data are absent, never zeroed; the sheet links onward to the check-in and sleep editors.
- Calendar taps open the day story; history rows gain a leading swipe to the story while tap keeps editing; the Journal home gains the sleep row showing last night's state.
- One commit: sleep logging and day story.

## Slice 10, phase 10C - Habits, tags, and life events (2026-07-30)

- Habit manager: active list with drag reorder through moveHabit, paused section, curated symbol and colour pickers, intent picker, optional weekly cap with the descriptive count only ("3 of 5 this week", counted by the log's dayKey against the current ISO week, no warning state). Pause and resume; habits are never deleted.
- Context kinds: built-ins as activity toggles (the store refuses their deletion), custom kinds with full CRUD; deleting a custom kind names the count of tagged days going with it.
- Life events: list newest first, create, edit, delete, plain dated anchors.
- Journal home browse section links all three managers.
- One commit: habits, tags and life events.

## Slice 10, phase 10B - History, search, and month calendar (2026-07-30)

- History: recency sections (today, yesterday, this week, then by month) over a pure HistoryGrouping helper, case-insensitive note search and mood filter chips composable, tap to edit through the check-in sheet, swipe to delete behind a confirm.
- PellStore.deleteJournalEntry: the slice's one store addition, delete and save, nothing else.
- Month calendar over the pure CalendarGrid helper (leading blanks from firstWeekday, trailing fill, month pager capped at the current month); one mood dot per day with an entry on the recorded ramp; future days disabled; days without an entry blank, never marked missing; the ramp legend under the grid.
- MoodScale: hue 0.61, saturation 0.55, brightness 0.92 to 0.32 in five recorded steps, deeper means higher, out-of-range clamps.
- OnThisDayCard over the derived lookback, absent when no anniversary entry exists. Journal home gains history and calendar navigation.
- 8 new tests: grid shape, leap February, month-offset clamping, weekday alignment, filter composition, recency grouping, the five distinct ramp steps, delete removes exactly the row.
- One commit: history, search and month calendar.

## Slice 10, phase 10A - Check-in sheet and weekly pulse (2026-07-30)

- Features/Shared seeded non-speculatively: RecallRule (the noon cutoff as a pure function), ScoreRow and OptionalScoreRow (numbered score buttons, the optional variant clears, no colour judgment on any value), DayNavigator with DayLabel (chevrons plus a capped graphical picker; the label always names the addressed day).
- The check-in sheet: recall default (yesterday before noon, today from noon), day switchable and always named, mood required with energy and stress optional and clearable, behaviour toggles writing habit logs only on change, context tag toggles diffed against the day, note. Every write through the existing upsert family, so a second pass edits.
- Journal home replaces the placeholder: the check-in card naming the recall-rule day and showing the logged state, the weekly pulse row (one score per ISO week through logPulse), recent entries opening the sheet on their day.
- 4 new tests: RecallRule both sides of noon, exactly noon, and a London spring-forward morning. Write guard green over Features/Shared.
- One commit: check in sheet and weekly pulse.

## Slice 09, review fixes (2026-07-30)

- The reverse coverage gap closed: the coverage guard proves model-to-DTO field coverage, but nothing guarded DTO-to-model, where a missed builder assignment silently defaults. A mechanical audit found zero misses today; the new full-field round trip test pins it forever: every entity with every optional set, winner and loser pairs included, all nine built-in kinds travelling, byte-identical after restore. A future missed field copy anywhere in the codec or the builders breaks this test.
- Console export filename gains the time component so two same-day exports never overwrite.
- Recorded, not coded, for the Profile slice restore surface: restore should gate behind sync and outbox idle (drain holds fetched rows across awaits, so a mid-drain restore leaves zombies; the engine's per-store in-flight set is the seam to build on); a successful restore should rebuild app state through the launch path because scratch-context restore leaves main-context registered objects stale (the console's refresh covers DEBUG); hand-made files can carry two open sessions or two active plans, invariants the store enforces at write time and the validator does not yet police, impossible in self-exported files.
- One commit: review fixes.

## Slice 09, phase 9E - Round trip proof and docs wrap (2026-07-30)

- The headline suite over the three corpora: standard fixture, sparse logger, and the import corpus driven through the real pipeline (FakeAdapter, sync engine, reconciliation verdicts, sync logs in flight). Export, restore into a fresh store, export again: the second file is byte-identical to the validated form of the first, which is the first file with exactly the two documented restore effects applied (diagnostics discarded, HealthKit coupling reset). Reconciliation verdicts proven to survive verbatim on the restored copy.
- Derived equality: 90-day summaries, all streak streams, the seven locked correlations, and coverage compared before and after a restore, identical on every corpus.
- Codec hardening found by the proof: per-entity sorts now key on the encoded date string, so two rows inside the same millisecond order identically before and after the file's truncation.
- The benchmark: a dense 365-day store exports under 1 s and restores under 3 s, recorded as executable facts beside the derived 500 ms ceiling.
- Release build succeeds with zero tooling strings in the binary. Both guards and the write guard over Export/ green in the full run.
- Docs: decisions 60 to 66 recorded (63 rewritten to the scratch-context reality), four slice 09 rejections logged, 01-schema corrected (dayKey index wording with the iOS 18 revisit note, recovery options wording), status pointing at the frontend phase.
- One commit: slice 09 tests and docs.

## Slice 09, phase 9D - Recovery, fixtures, and console (2026-07-30)

- Persistence.setAsideStoreSet: start fresh moves an unopenable file set whole into a timestamped SetAside folder, nothing deleted, every backup kept. RecoveryView gains the start fresh button behind an explicit confirmation with neutral copy naming exactly what happens; no export action, because the codec cannot read a store that will not open. PellApp wires startFresh as set-aside then a normal launch attempt.
- DevExportSamples (DEBUG): the clean sample payload restoring with zero repairs, plus the hostile set (newer-format, malformed, duplicate-ids, orphans, dangling-refs, out-of-range), each labelled with the rule it trips, embedded strings per the fixture pattern.
- Console export section: export to Documents with row and byte counts shown, restore the clean sample behind count-and-confirm, the import report rendered (imported, repairs by group, coupling reset, discarded diagnostics), the invariant check re-run after a restore.
- 4 new tests: the broken set moves aside with backups intact and the next open starts empty, set-aside no-ops without a store, the clean sample restores repair-free, every hostile file trips its labelled rule. DevTools guard still green over the new file.
- One commit: recovery, fixtures and console.

## Slice 09, phase 9C - Import, validation, and batch restore (2026-07-30)

- ExportCodec decode arm: forward-only versioning. A newer formatVersion refuses with a user-facing message, older files walk an ordered transform chain (empty at format 1, the seam proven by a test-only transform), dates accept ISO 8601 with and without milliseconds, malformed files name the failing spot.
- ImportValidator: the strict pass rejects duplicate identities (localId, weekKey, kind key, aggregate dayKey) because repair cannot reason about a corrupt identity space; the repair pass drops orphans counted per entity (sets, plan days and their cascading exercises, preset items, habit logs, tags, week assignments), releases dangling canonicalId and confirmedEntryId (a confirmed slot returns to planned, the deleteFood rule), drops one-per-period duplicates first-wins with imported sleep and weight rows exempt, and applies the store-boundary clamps, every count surfaced in ImportReport, nothing silent.
- The HealthKit coupling resets at validation: source keys to manual, hkConnectedAt nil, write toggles off, flagged in the report. Diagnostics are read into the report and discarded.
- PellStore.restore: decode, validate, back up the file set through the pre-open machinery, then wipe and insert with one save on a throwaway scratch context. Failure is atomic by abandonment: the scratch context dies with every pending change, so a failed restore can never dirty the live context (found and fixed here: SwiftData rollback() can silently keep pending changes, so restore no longer depends on it). Built-in kinds upsert by key after restore; system rows stay empty.
- ImportBuilders: DTO to model, every field verbatim, dayKey copied from the file and never recomputed.
- 27 new tests across the validator and the store (version rules, every rejection and repair both sides, byte-identical export after restore, relationships rebuilt, provenance verbatim, replace semantics with system rows cleared, coupling reset end to end, read-only save failure leaving the original store intact, pre-restore backup counted). Full suite green (407 passed, the sole flake a UI runner bootstrap crash unrelated to code).
- One commit: import, validation and batch restore.

## Slice 09, phase 9B - Export end to end (2026-07-29)

- Export/ExportCodec.swift: the encode arm. payload() assembles the file from caller-supplied rows with every array in its recorded sort (dayKey then date then localId for the dated entities, parent id then order index for the child entities, createdAt then localId for the created entities, natural key for the keyed ones) and computes the header counts; encode() produces deterministic bytes with sorted keys, pretty printing for hand inspection, and ISO 8601 UTC millisecond dates.
- PellStore export reads: allRows(_:) whole-table fetch plus exportPayload(now:appVersion:) feeding the codec one snapshot. Passive profile read: exporting an empty store invents no profile row. Assembly and encoding stay in Export/ under the write guard.
- Determinism proven the honest way: two independent payload builds from the same store encode to identical bytes, so fetch-order variance is normalized by the codec's sorts, not by luck.
- 6 new tests (byte identity, header counts with all 25 keys, valid empty-store file, recorded sorts, ISO date shape, sorted keys), full suite green in a parallel run.
- One commit: export end to end.

## Slice 09, phase 9A - Export DTOs and coverage guard (2026-07-29)

- Export/ExportDTOs.swift: the payload shape. ExportHeader (formatVersion 1, schemaVersion, appVersion, exportedAt, per-entity counts), one flat Codable DTO per travelling entity (all twenty-four), SyncLogDTO as the diagnostics section, ExportPayload tying them together with lenient array decoding so a hand-made file may omit any array; a missing header still rejects.
- Relationships export flat: sessionLocalId, planLocalId, dayLocalId, presetLocalId are the rebuild seams, no nested encoding.
- StepDay and EnergyDay export DTOs carry the Export suffix because the adapter layer owns the plain DTO names.
- ExportFormat pins currentVersion and the schema version string; the layer stays free of SwiftData and a guard test compares the pin against the live schema so it can never lag.
- The coverage guard (ExportCoverageGuardTests): every SchemaV5 entity must be in the DTO table or on the device-only list (SyncState, PendingHKWrite), and every stored attribute from the live schema must have a matching DTO coding key, both directions. Attribute names come from Schema(versionedSchema:), so macro artifacts never leak and relationships are excluded by construction. Verified red on a planted model field, green on the real schema.
- Write guard extended over Export/: ModelContext, FetchDescriptor, modelContext, UserDefaults all banned; the codec is pure functions over caller-fetched rows.
- 13 new tests (DTO fidelity with every optional set, payload leniency, format pins, the two guard walks), full suite green in a parallel run.
- One commit: export dtos and coverage guard.

## Slice 08, review fixes (2026-07-29)

- Per-domain erase now drops its queued mirrors: training erase removes workout-kind PendingHKWrite rows, body erase removes weight-kind, so erased data can never leave the device through a later drain. Proven by test.
- burnedSource requires actual energy, not just a row: a day carrying only exercise minutes keeps the estimate arm instead of reporting a burn of zero (the fetchEnergy union defaults missing arms to 0).
- The engine gained a per-store in-flight guard on refresh and backfill, enforcing decision 53's no-concurrent-writer premise; lastRefreshAt only stamps on clean runs, so a failed fetch never burns the 60 s debounce.
- Sleep fetches reach 24 hours further back than the other domains so boundary nights assemble whole instead of being truncated and overwriting good rows; pruning still judges the original window. The dead explicit-deleted list for sleep is gone (source sample ids can never match synthesized night ids; set difference is sleep's deletion mechanism).
- Sleep assembly minutes are interval unions, never raw sums: overlapping samples from two sources count once, while the classic split (iPhone inBed beside Watch stages) still combines. Echo now filters at sample level before assembly, and provenance goes to the source contributing the most asleep time. Proven by a mixed-source test.
- Coverage's move split shrank to mean exactly what hasMove means (logged sessions and activities): day aggregates stay out so coverage and co-coverage reconcile, and passive device data never inflates a logging domain.
- The foreground hook and the enqueue guards read the profile without creating it (profileIfPresent), so a foreground on a fresh install writes no row and the seeder's empty-store rule survives a relaunch.
- METTable grew the seven mapped slugs that had no row (badminton, elliptical, stairs, jump rope, martial arts, core, cross training). The console gained the spec's missing connect button (authorize, stamp hkConnectedAt, point the five sources at healthkit) and the invariant check moved behind a button instead of running per render. Stale wipeAll TODO removed.
- Recorded, not coded: imports save once per row and reconcile refetches per day; a known cost against durationMs as evidence, deferred because batching touches the single-write-path pattern and deserves its own decision.
- 4 new tests, one updated, full suite green in a parallel run.
- One commit: pr review fixes.

## Slice 08, phase 8E - Corpus, harness, and wrap (2026-07-29)

- The labelled ground-truth corpus (DevImportCorpus): one scripted day, five manual seeds and ten import DTOs each carrying its expected outcome, every tIoU score hand-checkable. Drives the Ward taxonomy harness, the sensitivity runs, and the console seed-import button.
- Ward harness: the full pipeline over the corpus classifies exactly as labelled: 2 correct matches, 0 false matches, 0 missed matches, 0 merges or fragmentations, 3 ambiguous both-live, 1 cluster resolution, 1 echo, 1 quarantine, invariants clean, second pass a no-op. Table recorded in docs/references/reconciliation-eval.md.
- Sensitivity runs recorded: tIoU at 0.3/0.5/0.7 keeps 4/3/1 matches over the five-pair set; the sleep gap at 1/2/3/4 h gives 1 night with 2/1/0/0 naps. Both parameterized seams default to the shipped constants; the doc explains the choices by method.
- DevInvariants: the console lens over orphaned canonicalIds, suppressed rows without winners, duplicate externalIds. Console gains the sync section: backfill, refresh, seed corpus, invariant counts, recent SyncLog rows; row counts and totals cover all twenty-seven entities.
- wipeAll grows to twenty-seven entities; wipe(domain: training) gains StepDay and EnergyDay; PendingHKWrite, SyncLog, and SyncState are master-erase only; storeIsEmpty counts imports and outbox rows but treats SyncState as furniture.
- Release build succeeds with zero tooling strings in the binary. Docs wrap: decisions 50 to 59 recorded, six slice 08 rejections in 03-rejected, formulas registry gains tIoU, night assembly, the two efficiency formulas, coverage, the de-stubbed MET entry, and the device-reported energy rewording; reconciliation-eval.md carries the report-ready tables.
- 3 new tests, full suite green in a parallel run.
- One commit: slice 08 tests and docs.

## Slice 08, phase 8D - Write-back, energy, and derived growth (2026-07-29)

- Write-back outbox (decision 58): finishSession, logSession, and logWeight enqueue behind the default-off toggles (reading the profile without creating it); the Outbox drains on foreground, gated per row by nextAttemptAt with exponential backoff, attempts capped at 5, lastError retained after the cap, no jitter (one client, recorded). Success stamps the sample UUID as hkMirrorId, feeding the echo backstop. HealthKitAdapter writes via HKWorkoutBuilder and bodyMass samples; adapters without writes throw a typed unsupported error from the protocol defaults.
- METTable (decision 56): sixteen curated activities, point MET plus code-family band, edition recorded per row. kcal = MET x kg x hours from the latest weigh-in; no weigh-in means no estimate; single-code activities collapse to a point.
- Device-reported energy (decision 55): EnergyMath uses EnergyDay active plus basal when a row exists, else the slice 06 estimate; DaySummary carries burnedSource and the no-cross-boundary contract is documented at both.
- SleepMath gains the two named efficiency formulas (decision 54): maintenance (asleep over bedtime-to-wake) and time-in-bed (asleep over inBed, nil when absent). Never stored: computable from stored ingredients.
- DaySummary: suppressed sessions, activities, sleep, and weight rows are now invisible; imported nights and readings fill manual-free days (hours only, never quality) and manual rows win; steps and device energy land as one-line fields. WeekRollup totals travel with day-count denominators. Three registry extractors (steps, activeKcal, exerciseMinutes) join with the nil contract, sixteen metrics total.
- Coverage (decision 59): per-domain days split manual against imported, plus co-coverage (days where 2, 3, 4 core domains hold data together), pure and never stored.
- 9 new tests, two outdated expectations updated to the new contracts, full suite green in a parallel run.
- One commit: write back, energy and derived growth.

## Slice 08, phase 8C - Reconciliation engine (2026-07-29)

- Reconciliation as a pure decision function (decision 53): one day's rows in, the desired end state out; PellStore applies the diff, so passes are idempotent and a released loser is exactly a row absent from the desired state.
- Temporal IoU at 0.5 with one-to-one greedy assignment and the compatibility gate; the review's containment case is a named test (a 20-minute walk inside a 2-hour session scores 0.17 and never merges); threshold proven both sides; unmapped types never auto-match; unresolved overlaps count ambiguous and both records stay live.
- Winners per the locked table: activities import wins, strength manual wins (in-progress sessions never match), sleep and weight manual wins the day; import-only days keep the longest night and the newest reading. Import-versus-import clusters resolve richer data, then earlier start, then lexicographic externalId, proven for all three tie-breaks.
- Losers get canonicalId (the winner's localId) and suppressed, never deleted. deleteActivity and deleteSession reconcile their day so deleting a winner releases its claim.
- Deletion mirroring (decision 57): imported rows vanish when the source reports them deleted (explicit list, reaching outside the refresh window) or when a succeeded fetch no longer returns them (window set difference); claims release through the same desired-state diff.
- Echo backstop: hkMirrorId UUIDs skip import even under a foreign source bundle. Engine wires it all: touched dayKeys reconcile after every window, suppressed and ambiguous counts land in the SyncLog payload.
- 11 new tests, full suite green in a parallel run.
- One commit: reconciliation engine.

## Slice 08, phase 8B - Adapter, registry, and sync engine (2026-07-29)

- DataSourceAdapter protocol (decision 50): stable key, read and write capability sets, typed DTO batches per domain with a deleted-identifier seam. AdapterRegistry static and keyed; "manual" and unknown keys resolve nil, the manual fallback.
- HealthKitAdapter: the one real conformance. Steps and energy via statistics collection queries so Apple merges overlapping sources; workouts, sleep, and weight as samples with sourceRevision captured into sourceDetail; HKTypeMap maps activity types to curated slugs (unmapped stays nil) and flags strength. Entitlement and both usage strings added to the project.
- SleepAssembly (decision 54): periods split at 1 hour of wakefulness, longest period per wake day is the night, the rest count as naps, inBed-only periods quarantine, stage minutes nil-not-zero, timezone carried through. DST attribution proven on a Europe/London night.
- QuarantineGate (decision 52): end-before-start, sleep over 24 h, activities over 48 h, 5-minute future skew, negative counts, missing mass. Counted with reasons, never written.
- SyncEngine: backfill in month chunks newest first (BackfillPlanner, half-open windows), SyncState checkpoint after every chunk, resume proven after a scripted mid-run failure, complete backfill re-runs are no-ops; 14-day refresh with 60 s foreground debounce and manual bypass; echo guard first layer (own bundle id) counted; one SyncLog row per run with countsJSON, rotation newest 50 runs or 90 days.
- PellStore import writes: StepDay and EnergyDay dayKey upserts, activity, session, sleep night, and weight upserts on externalId plus source; manual rows never touched. FakeAdapter in DevTools scripts every domain with window filtering and error injection.
- PellApp registers the HealthKit adapter at launch and triggers the debounced foreground refresh once connected. 16 new tests, full suite green in a parallel run.
- One commit: adapter, registry and sync engine.

## Slice 08, phase 8A - Import entities and SchemaV5 (2026-07-29)

- Five new models: StepDay and EnergyDay (day aggregates, dayKey upsert shape, latest import wins), PendingHKWrite (outbox row with nextAttemptAt), SyncLog (per-run instrumentation, countsJSON, trigger accessor degrading to manual), SyncState (single-row backfill checkpoint and refresh stamp, device state not user data).
- Provenance growth on the importable entities: sourceDetail on WorkoutSession, ActivityEntry, SleepRecord, WeightEntry; suppressed added to the same four (the slice 03 fields turned out to be food and water only, the status note claiming all four was wrong); hkMirrorId on WorkoutSession and WeightEntry for the echo backstop; timezoneId on SleepRecord so bedtime consistency survives DST and travel.
- UserProfile gains the HealthKit settings: hkConnectedAt, five per-domain source keys as plain registry strings defaulting manual, writeBackWorkouts and writeBackWeight defaulting off.
- SchemaV5 (twenty-seven models, 5.0.0), fourth lightweight stage, Persistence builds against V5.
- Headline test: a populated V4 store (every entity, relationships included) reopens under V5 intact, migrated rows carry the new columns at their defaults, the five new tables accept rows, and everything survives a further reopen. Entity defaults, accessor degradation, and localId distinctness pinned. Full suite green in a parallel run.
- One commit: import entities and schema v5.

## Slice 07, review fixes (2026-07-30)

- DaySummary.hasAnyLog now counts habit toggles: a habit-only day is a logged day for the log-daily challenge, while tags alone still never count (context, not logs). Decision 45 records the boundary; pinned by test both ways.
- The claimed per-habit streak composition is now proven: a habit's done days through the unchanged streak engine, current, longest, and history exact.
- Full suite green in a parallel run.
- One commit: FIXED: (slice-07) -> pr review fixes.

## Slice 07, phase 7E - Personas, erase, and wrap (2026-07-30)

- Store/PellStore.swift: wipeAll covers all twenty-two entities; wipe(domain:) per-domain erase with the decision 49 mapping, built-in context kinds surviving journal erase, profile master-erase only, rollback on failure.
- DevTools/DevFixtures.swift: the standard persona grows one plan (three days, six prescriptions, assigned to the current week), two presets (five items), a planner window across all three statuses (the confirmed slot writes a real entry today), three habits with ten logs, three tags including the deliberate rest-day on log-free offset -3, two life events, two pulses. A sparse-logger persona ships as its own JSON block. All persona blocks optional in the DTOs.
- DevTools/DevSeeder.swift: one seeding seam for any decoded persona; habits created first, day loop gains tags and habit logs, then plan, presets, planner (confirm and discard through the real operations), events, pulses; built-in kinds ensured at the end and counted as furniture, not user rows, in the empty-store rule.
- DevTools/DevConsoleView.swift: counts for all twenty-two entities, day view gains tags, habit logs, and planned meals (empty check included), recent rows for tags, habit logs, events, and pulses, one seed button per persona.
- Tests: fixture shape (log-free days with the tagged -3 case, every reference resolving across foods, exercises, habits, and built-in kinds, statuses covering the planner enum, sparse strictly lighter), seeder exact totals and day-by-day match including tags and habit logs, planner-confirmed food counted, window extended to +2 for planner days, built-ins-only stores still seed, sparse seeds through the same seam, wipeAll to zero across twenty-two, and parameterised per-domain erase proving each domain removes exactly its mapping.
- Docs: decisions 43 to 49 recorded, schema doc gains PlanExercise.orderIndex, spec entity counts corrected to twenty-two, status updated with slice 08 next. Formula entries were pre-recorded at spec time.
- Full suite green in a parallel run, benchmark still under the 500 ms ceiling with the grown DaySummary.
- One commit: slice 07 tests and docs.

## Slice 07, phase 7D - Habits, context, and insights (2026-07-30)

- Store/PellStore.swift: habit create, edit, pause, reorder (never delete), logHabit one row per habit per day; the nine built-in context kinds seeded idempotently (ensureBuiltInContextKinds, called from PellApp after any seeding), custom kind CRUD with generated keys and built-in delete refused, tag and untag one row per kind per day with note upsert; life event ops with dayKey recompute; weekly pulse upsert clamped 1 to 10.
- Derived growth: DaySummary gains contextKinds, habitsDone, habitsLogged, srpeLoad. Streaks.skipDays(from:) connects tags to the waiting skip-day input. HabitInsights runs configurable pairs through the correlation engine with the active-days-since-creation denominator and the four scope defaults. Adherence returns plain planned-versus-finished counts. InsightRanking orders results deterministically (splits, tier, sample size, id). OnThisDay maps a key to earlier years. The srpeLoad registry metric lands (thirteen metrics).
- Tests: 27 across three new files plus the registry bump: upserts and refusals, idempotent seeding, precise untag, dayKey recompute, pulse clamps, insight means through the engine, the creation-date cutoff, the floor, tagged-gap streak preservation end to end, ranking order and tie determinism, on-this-day filtering, srpe extraction.
- One commit: habits, context and insights.

## Slice 07, phase 7C - Meal presets and planner (2026-07-30)

- Store/PellStore.swift: preset CRUD with cascade and createPreset(from:) snapshotting logged entries in order; applyPreset writing real entries through logFood; planner planMeal with per-day-and-meal ordering, updatePlannedMeal (dayKey moves included), discard keeping values, confirmPlannedMeal writing and linking the entry, and the deleteFood release hook reverting confirmed slots (decision 47).
- Derived/PlannerMath.swift: frequency recall, plain counts of food names over the last N same-weekdays before a reference day, duplicates per day counted once, suppressed rows excluded.
- Tests: 12 across two files: snapshot order, apply order and values, cascade, meal display ordering, confirm-and-release round trip through deleteFood, discard, day moves, reopen persistence, and the recall counts pinned on a constructed history.
- One commit: meal presets and planner.

## Slice 07, phase 7B - Workout plans (2026-07-30)

- Store/PellStore.swift: plan CRUD with deletePlan cascading days and exercises and clearing week assignments; setActivePlan enforcing exactly one active; day and exercise add, update, move with dense renumbering, delete; assignWeek upsert per weekKey with clearWeek; activePlan, weekPlan(for:), plans(), and resolvedPlan(for:) reading pinned-else-active with a fallback when the pinned plan is gone (decision 48).
- Tests: 8 covering structure round trips, single-active enforcement, cascade plus assignment clearing, dense reordering, upsert, resolution and its fallback, and reopen persistence.
- One commit: workout plans.

## Slice 07, phase 7A - Remaining domain entities and SchemaV4 (2026-07-30)

- Thirteen new model files per the spec field lists: the plan cluster with two cascade relationship chains, the preset cluster, PlannedMeal with a caller-supplied dayKey (planner slots address future days, no date field, documented at the type), habits, context kinds and tags, life events, weekly pulse. Raw enums (PlannedMealStatus, PlannedMealSource, HabitIntent) degrade to neutral defaults.
- Store/PellSchema.swift: SchemaV4 (all twenty-two models, 4.0.0) and the third lightweight stage. Persistence builds against V4.
- Tests: three entity files pinning defaults, accessors, frozen dayKeys, and distinctness; PellSchemaTests moved to the V4 shape (twenty-two entities, four schemas, three stages); MigrationTests headline: a populated V3 store reopens under V4 with every row intact, all thirteen new tables and both relationship chains usable, and survives a further reopen.
- One commit: remaining domain entities.

## Slice 06, review fixes (2026-07-29)

- Streaks.swift and Correlations.swift now pass the injected calendar into DayKey.offset; the parameter was accepted but silently ignored, so the benchmark's UTC injection was fiction (harmless on Gregorian devices, dishonest everywhere). Correlations.run and runLockedPairs gained a calendar parameter so the two files agree.
- The trainingDay metric is renamed moveDay ("Movement day") and the two locked pair ids become move-sleep and move-mood: the extractor always measured any-movement days (which is what the cited exercise literature supports), so the name now matches the number. Strength-only counts (finishedSessions, train-three) keep their names. Spec and formulas registry updated.
- Challenge rotation now indexes on year x 53 + week instead of the concatenated digits, which repeated back to back across the 2026-W53 to 2027-W01 turn; consecutive weeks now stay distinct across year turns, pinned by a 53-week-year test.
- Decision 39 gains the tie-break: a day that both qualifies and is skipped still extends the run, logging always wins, pinned by test.
- Stale "Notes for slice 06" block removed from the status doc.
- Full suite green in a parallel run.
- One commit: FIXED: (slice-06) -> derived layer review fixes.

## Slice 06, phase 6D - Benchmark, guard extension, docs wrap (2026-07-29)

- PellTests/DerivedBenchmarkTests.swift: a deterministic dense 90-day dataset seeded through the store (daily food, water, check-ins, sleep; workouts with completed sets every third day; activities every fourth; weigh-ins every third), fixed calendar and now, no randomness. The headline sweep fetches every range, builds 90 summaries, all five streak streams, and all seven locked correlations under the 500 ms ceiling (decision 41), duration printed. Determinism proven by two independent seeds producing identical summaries, streaks, and correlation results; every locked pair runs against the dataset with group floors respected.
- PellTests/WriteGuardTests.swift: the walk now also covers Derived/ and additionally bans ModelContext and FetchDescriptor there, freezing decision 37 (pure functions, fetching stays with the store).
- Docs: decisions 37 to 42 recorded, status updated with slice 07 (remaining domain entities) next. The formulas registry was complete from spec time.
- Full suite green in a parallel run.
- One commit: slice 06 tests and docs.

## Slice 06, phase 6C - Cross domain correlations (2026-07-29)

- Derived/Correlations.swift: the Metric type (named extractor over DaySummary, nil means no data, never zero) and MetricRegistry with the twelve initial metrics. The engine: same-day and next-day lag with adjacency checked through DayKey.offset so window gaps never pair, median split with ties to the low group, binary metrics split did against didn't, both groups floored at 5 days, output only the two group means with counts (decision 38). The seven locked pairs as registry pairings, one call runs them all; the pair explorer later reuses the engine unchanged.
- 10 tests: known group means, tie handling, binary split, lag adjacency and gap skipping, the floor at 4 versus 5, missing-value exclusion, zero-reads-as-no-data extractors, registry uniqueness, all seven pairs over a constructed window.
- One commit: cross domain correlations.

## Slice 06, phase 6B - Streaks and challenges (2026-07-29)

- Derived/Streaks.swift: one engine walking the day range, closing runs on hard gaps only. Skip days neither break nor extend; a pending today never breaks, so a run ending yesterday stays current (decision 39). StreakSummary carries current with its start day, longest, and full history; allStreams computes the headline plus the four domain streams from one set of summaries.
- Derived/Challenges.swift: deterministic rotation from the weekKey digits over the fixed list, completion thresholds read the WeekRollup (log daily 7, train three, check in five). Pure, inert until the gamification slice, nothing stored: past outcomes recompute from logs.
- 14 tests: today-inclusive and pending-today currents, gap breaks including the run-ended-before-yesterday case, skip preservation without extension including trailing skips, runs splitting around skips, per-domain against headline separation, empty inputs, pinned rotation weeks, completion at every threshold boundary.
- One commit: streaks and challenges.

## Slice 06, phase 6A - Daily summaries and domain math (2026-07-29)

- Derived/DaySummary.swift: the DaySummary value struct and SummaryService, one grouping pass over caller-fetched rows, no store access in the layer (decision 37). Totals across all four domains plus weight, presence flags, coreDomainCount with weight deliberately excluded, qualifiesForStreak (two-domain rule, decision 39), hasAnyLog. In-progress sessions and suppressed rows invisible (decision 40); sleep and weight read the manual row only.
- Derived/WeekRollup.swift: per-week consistency counts (qualifying days, any-log days, per-domain days, finished sessions, checkIns mirroring moodDays) feeding the week lens, week overview, and challenge completion later.
- Derived/TargetsCalculator.swift: Mifflin-St Jeor BMR with the -78 midpoint for unspecified sex, FAO-anchored multipliers 1.40 to 2.00 (decision 42), goal adjustments -500/0/+300 as heuristics, macros protein 1.6 g/kg, fat 30 percent, carbs remainder. Nil weight, nil recommendation.
- Derived/TrainingMath.swift: tonnage over completed working sets, Brzycki e1RM capped at 10 reps (decision 42), PR feed on strict e1RM improvement per exercise, capped-out sets never PR.
- Derived/SleepMath.swift and EnergyMath.swift: deficit-only sleep debt over days with data; energy balance and the BMR-plus-movement burn estimate.
- Store/PellStore.swift: read-only range fetches per entity (dayKey between, date sorted) through one generic helper; the write path untouched.
- 31 tests across five new files: dense-day totals exact, in-progress and suppressed and imported rows invisible, weight-only days never qualify, range build order and inclusivity, rollup counts, every targets number pinned by hand including the macro remainder bound, Brzycki edges and the cap, PR strictness, deficit-only debt.
- Formulas were already recorded in docs/references/formulas.md at spec time; nothing new to record.
- One commit: daily summaries and domain math.

## Slice 05, phase 5E - Tests and docs wrap (2026-07-28)

- Section 6 audit closed: newEntityLocalIdsStayUniqueAndStableAcrossReopen writes one of each slice 05 entity to an on-disk store and proves the five ids distinct and unchanged across reopen; perceivedExertion clamps proven at both ends. Everything else in section 6 already landed in phases 5A to 5D.
- Release configuration builds clean with zero tooling strings in the binary, including the new fixture content.
- Docs: decisions 34 to 36 recorded, status updated with slice 06 (derived services) next.
- Full suite green in a parallel run.
- One commit: slice 05 tests and docs.

## Slice 05, phase 5D - Fixture and console growth (2026-07-28)

- Store/PellStore.swift: wipeAll() covers all nine entities. DevTools/DevSeeder.swift: storeIsEmpty likewise.
- DevTools/DevFixtures.swift: exercises table (six lifts, app-owned slugs) and per-day workout, activity, sleep, and weightKg blocks. Pattern per the spec: six workouts, four activities, ten sleep nights with gaps at -6 and -11, five weigh-ins; offsets -3 and -9 stay empty across every domain; no randomness.
- DevSeeder seeds through the new store operations: sessions through the quick-log path with their sets marked completed, fixed hours (workout 18, activity 9), unknown exercise references throw.
- DevTools/DevConsoleView.swift: schema label reads V3, counts, day view, and recent rows cover all nine entities including a per-set recent list, an in-progress session is labelled. Day view lists every sleep and weight row on the day, no manual-only filter, so imported rows show beside manual ones once they exist.
- Tests: fixture shape (pattern counts, slug format, references resolve, clamps, empty days empty across every domain), seeder exact per-day match across all domains, sets carry their session's id and day, second seed adds nothing, wipeAll to zero across nine entities, wipe then seed restores.
- Verified in the simulator: two -reset-store -seed-fixture boots byte-identical at the day level, 6 workouts, 22 sets, 4 activities, 10 sleep nights, 5 weigh-ins beside the food, water, and journal rows.
- One commit: fixture and console growth.

## Slice 05, phase 5C - Activity, sleep, and weight logging (2026-07-28)

- Store/PellStore.swift activities: logActivity, updateActivity per-field with dayKey recompute on a date change, deleteActivity, activities(on:) sorted. perceivedExertion clamped 1 to 10; minutes, kcal, and distance floored at 0.
- Sleep: logSleep fetch-or-create one manual row per dayKey filtered on source manual, every pass overwrites, hours clamped 0 to 24 and quality 1 to 5 (decisions 23 and 32); sleepRecord(on:) read.
- Weight: logWeight same pattern with kg floored at 0; weightEntry(on:) read.
- Tests: activity round trips, upserts leave one row per day with latest values and separate days separate rows, manual filter proven with planted healthkit-source rows untouched, clamps, nil reads on empty days, persistence across close and reopen.
- One commit: activity, sleep and weight logging.

## Slice 05, phase 5B - Workout logging (2026-07-28)

- Store/PellStore.swift sessions: startSession fetch-or-creates the single open row (decision 34), activeSession, finishSession stamps endedAt and computed minutes, logSession quick log with endedAt from date plus minutes, updateSession per-field with the date-change cascade restamping every set at its own time of day on the new day and recomputing both dayKeys (decision 31), deleteSession cascading to its sets, sessions(on:) sorted. Wall-clock restamp, not a delta shift: a DST hour change can never split a set's key from its session's, pinned by a Europe/London spring-forward test.
- Sets: addSet with orderIndex and setIndex assignment and the day-addressed date stamp, updateSet per-field (exercise swap, completed toggle, explicit-nil clears), deleteSet. sessionRPE and rpe clamped 1 to 10; weights, reps, minutes, kcal floored at 0.
- Tests: open-session lifecycle, a second start resumes the same row including across close and reopen, interleaved set indexing, cascade delete, date-edit cascade across a day boundary, clamps at both ends, persistence across reopen, lastError nil on the happy path.
- One commit: workout logging.

## Slice 05, phase 5A - Entities and SchemaV3 (2026-07-28)

- Models/WorkoutSession.swift, ExerciseSet.swift, ActivityEntry.swift, SleepRecord.swift, WeightEntry.swift: the remaining loggable-domain entities, every field defaulted so a bare init is valid, dayKey frozen in init from a caller-supplied calendar (decision 19), provenance fields present and inert (decision 20). WorkoutSession is strength only with endedAt nil meaning in progress (decisions 29 and 30) and carries the app's first relationship: sets, cascade delete, session as the inverse, sessionLocalId kept on each set for export (decision 31). ActivityEntry holds general activities with a plain-string activityType and inert distanceKm (decision 33). SleepRecord uses wake-day attribution with HealthKit fields nil until slice 08. WeightEntry is plain weight plus note.
- Models/FoodEntry.swift: catalogId, gramsLogged, servingText, nil defaults, inert until catalog foods exist (proposed decision 36).
- Store/PellSchema.swift: SchemaV3 (all nine models, version 3.0.0) and the second lightweight stage, V2 to V3. Persistence builds against V3 through the plan.
- Tests: three new entity files covering exact defaults, dayKey freezing (midnight boundary, non-UTC, frozen across date changes), and localId distinctness; PellSchemaTests moved to the V3 shape (nine entities, three schemas, two stages); FoodEntryTests covers the three new nils; MigrationTests headline: an on-disk V2 store with profile, food, water, and journal rows reopens under V3 with every row intact and nil in the new food fields, the new tables and the relationship usable, then survives a further reopen.
- Verified in the simulator: shell boots on the store under V3 with all nine tables live; -seed-fixture then a plain relaunch lands the fixture and keeps it (38 food, 69 water, 11 journal rows across 12 days).
- One commit: workout, sleep and weight entities.

## Slice 04, phase 4D - Release safety and wrap (2026-07-28)

- PellTests/DevToolsGuardTests.swift: two guards freezing decision 24. Every file under DevTools/ must open with #if DEBUG and close with #endif, whole-file wrap, and an empty walk fails so a moved folder cannot go vacuous. Any DevConsole reference under Features/ must sit inside a live #if DEBUG region, checked by a small conditional-compilation scanner that handles nesting and #else. Verified red on a planted unwrapped file, green on the real tree.
- Release check: the Release configuration builds clean for the simulator with all of DevTools/ compiled out, and the product binary contains zero occurrences of DevConsole, DevSeeder, DevFixtures, DevLaunchOptions, reset-store, seed-fixture, or Developer strings.
- Docs: decisions 24-28 recorded, status updated with slice 05 next.
- Full suite green in a parallel run: 13 fixture and seeder tests, 9 launch option tests, 2 guard tests, plus the whole existing suite.
- One commit: slice 04 tests and docs.

## Slice 04, phase 4C - Developer console screen (2026-07-28)

- DevTools/DevConsoleView.swift: on-device store inspector presented as a sheet. Store info (file URL, schema version, backup folder count), per-entity row counts, a day view chevroning through dayKeys with that day's rows across all domains, recent rows per entity with dayKey, values, and source, and actions: seed fixture, wipe all behind a count-and-confirm dialog. Reads go straight to ModelContext so the screen shows stored truth; mutations only through PellStore (decision 28).
- Features/Profile/ProfileView.swift: a Developer button inside #if DEBUG presenting the console; the row touches neither modelContext nor UserDefaults, so the write guard stays green. Release builds have no Developer row.
- Verified in the simulator: console reachable from Profile, day view shows fixture days and "no rows" on the empty offset -3 day, wipe dialog counts 118 rows and leaves every count at zero, console seed restores the exact fixture state.
- One commit: developer console screen.

## Slice 04, phase 4B - Developer launch options (2026-07-28)

- DevTools/DevLaunchOptions.swift: parses ProcessInfo arguments in a DEBUG-only type. -reset-store deletes the store file set (store, -wal, -shm) before Persistence opens it, backups stay. -seed-fixture seeds the standard fixture after open, empty-store rule applies (decision 27). Unknown arguments are ignored; Release contains none of this code.
- PellApp: LaunchState.start() gained the two hooks inside #if DEBUG, reset before openStore and seed after the store is built; a seeding failure surfaces into lastError. Nothing else in PellApp changed.
- 9 tests: each argument alone, combined, unknown and miscased ignored, no arguments; removeStoreSet deletes the file set and keeps Backups, no-ops on an empty directory; reset then seed on a populated temp store ends in exactly the fixture state with the stale rows gone; seed without reset on a populated store adds nothing.
- Verified in the simulator: two boots with -reset-store -seed-fixture produce byte-identical day-level state (sqlite dump diff of dayKeys, meals, water sums, and check-in values), oldest day exactly 13 days back.
- One commit: developer launch options.

## Slice 04, phase 4A - Sample data and seeding (2026-07-28)

- DevTools/DevFixtures.swift: the standard fixture as JSON embedded in a string constant plus Codable DTOs (decision 25). 14 days addressed by offset from today (0 to -13), offsets -3 and -9 deliberately empty, one day with food but no check-in. Food rows reference a 10-item fixed table spread across the four meals, water 4 to 8 glasses on non-empty days, check-ins cover mood 1 to 5 with some nil energy and stress. No randomness anywhere.
- DevTools/DevSeeder.swift: decodes the fixture and writes every row through PellStore (logFood, addWater, checkIn), oldest day first, so seeded rows pass the same clamps and dayKey path as logged rows. Meal slots map to fixed hours so a seeded day reads like a real timeline. Only seeds when no entity holds a row; calendar and now are injectable so tests are deterministic.
- Store/PellStore.swift: wipeAll() deletes every row of every entity and saves, errors into lastError, row-by-row to avoid batch-delete quirks against in-memory stores. Not DEBUG-gated (decision 26).
- 13 tests. Fixture: decodes with 14 offset days, exactly two empty days, unique food table resolved by every meal reference, all four meal slots used, values inside the store clamps, full mood range with nil energy and stress present. Seeder: exact per-entity totals and per-day rows matching the fixture, dayKeys inside the offset window, second seed adds nothing, a lone profile row counts as non-empty, wipeAll leaves zero rows and a store that still accepts writes, wipe then seed restores the fixture state.
- One commit: sample data and seeding.

## Slice 03, phase 3D - Tests and docs wrap (2026-07-28)

- Spec section 6 audit: the one gap was localId stability across reopen. New test localIdsStayUniqueAndStableAcrossReopen writes one food, water, and check-in row to an on-disk store, proves the three ids distinct, then closes, reopens, and proves all three read back unchanged. Everything else in section 6 already landed in phases 3A-3C.
- Docs aligned: decisions 19-23 recorded, 00-architecture feature-flags wording updated to the @Observable class (had still described the pre-2A @AppStorage struct), status updated with slice 04 (dev console) next.
- Full unit suite green in a parallel run. Slice 03 complete, closing on branch slice3-core-logging with a PR under the slice-03 label and milestone.
- One commit: slice 03 tests and docs.

## Slice 03, phase 3C - Journal check in (2026-07-28)

- Store/PellStore.swift: checkIn(on:) fetch-or-create, one JournalEntry per dayKey enforced at the store boundary; repeat check-ins update the same row, every pass overwrites all fields and nil clears, so the sheet and the journal screen are the same operation (decision 21). Mood, energy, and stress clamped to 1 to 5 at the store boundary (decision 23). journalEntry(on:) read, nil on an empty day.
- 6 tests: repeated check-ins leave exactly one row with the latest values, different days get their own rows, clamping proven at both ends of the range, a past-day check-in lands on that day, nil on an empty day, persistence across close and reopen.
- One commit: journal check in.

## Slice 03, phase 3B - Food and water logging (2026-07-28)

- Store/PellStore.swift: first domain operations. Food: logFood insert plus save, updateFood per-field update with explicit-nil note clearing and dayKey recompute on a date change, deleteFood plain delete (superseded protection starts with imports in slice 08), foodEntries(on:) sorted by date. Water: addWater defaulting to one 250 ml glass, waterTotal summing only the day asked, subtractWater removing the most recent entry on that day and no-oping on an empty day (decision 22).
- Day-addressed writes land on that day at the current time of day, sub-second component kept, so same-day writes stay in logging order.
- Every operation goes through save() and surfaces failures into lastError, never crashes.
- 12 tests: log, fetch, update, delete round trips, per-day water totals, subtract newest and empty-day no-op and other-day safety, past-day addressing, persistence across close and reopen, lastError nil on the happy path.
- One commit: food and water logging.

## Slice 03, phase 3A - Entities and SchemaV2 (2026-07-28)

- Models/FoodEntry.swift, WaterEntry.swift, JournalEntry.swift: the first loggable entities, every field defaulted so a bare init is valid. dayKey computed in init from the date and a caller-supplied calendar, never recomputed (decision 19). Provenance and resolution fields present and inert until slice 08 (decision 20). FoodEntry carries the seven-nutrient model (calories Int, six Doubles) and a Meal enum stored raw, unknown reading as snack. WaterEntry has no canonicalId: water reconciliation is day-exclusive. JournalEntry is manual-only, no source fields, nothing imports mood.
- Store/PellSchema.swift: SchemaV2 (UserProfile plus the three entities, version 2.0.0) and the plan's first stage, lightweight V1 to V2. SchemaV1 untouched, Persistence builds against V2 through the plan.
- Tests: exact spec defaults per entity, meal accessor round trips and unknown-raw degradation, dayKey freezing with fixed calendars including the midnight boundary and a non-UTC calendar, per-row localId distinctness, migration plan shape (schemas [V1, V2], one stage), and the headline: an on-disk V1 store with a profile row reopens under V2 with the profile intact and the new tables usable, then survives a further reopen.
- One commit: food, water and journal entries.

## Slice 02, phase 2E - Write guard and docs alignment (2026-07-26)

- PellTests/WriteGuardTests.swift: walks every Swift file under Features/ from #filePath and fails if any references modelContext or UserDefaults, freezing the flag seam (decision 6) and the single write path (decision 18) as executable law. New screens are guarded automatically, no registration step. An empty walk fails too, so a moved directory cannot make the guard vacuous. Verified both ways: green on the real tree, red on a planted violation.
- Spec section 6 audit: every other foundation test already landed in phases 2A-2D (dayKey and weekKey edges, AppConfiguration isolation and observation, profile defaults and idempotent fetch-or-create, persistence across reopen, migration plan baseline, backup rotation). No gaps.
- Docs aligned with practice: CLAUDE.md workflow rule 3 now says phased delivery with every phase buildable, rule 4 says docs update per phase, rule 5 says plain commit messages in my voice. Decisions 13-18 and the decision 6 amendment were already recorded as phases 2A-2D closed; CLAUDE.md rule 6 wording already matched. Changelog header corrected to one entry per phase.
- Full unit suite green in a parallel run. Slice 02 complete.
- One commit: foundation tests: write guard and docs alignment.

## Slice 02, phase 2D - Store, recovery screen, app wiring (2026-07-26)

- Store/PellStore.swift: @MainActor @Observable single write path wrapping the main context (decision 18). profile() fetch-or-create with the one-row invariant; save() surfaces errors into lastError, never crashes.
- App/RecoveryView.swift: store-failure screen with neutral copy, the error text, and retry; nothing destructive, TODO(store safety slice) for export and start-fresh.
- PellApp: runs the Persistence launcher once at startup; success attaches the container and injects PellStore type-based (no fallback default, decision 18) beside AppConfiguration; failure shows RecoveryView, retry re-runs the launcher.
- 3 tests: fetch-or-create idempotent (same row, count 1), on-disk profile reused after reopen, and a forced real save failure (read-only store file makes the SQLite connection read-only) surfacing into lastError. Finding from the failure-test work: SwiftData silently last-writer-wins on conflicts, allowsSave(false) no-ops, and SQL-level constraint sabotage crashes inside Core Data rather than throwing, so the read-only-file route is the only deterministic catchable failure.
- Verified in the simulator: app boots to the shell with the container live (Pell.store plus sidecars in Application Support/Pell), and a relaunch produces exactly one timestamped pre-open backup.
- Three commits: store single write path; recovery screen; app wiring.

## Slice 02, phase 2C - UserProfile, versioned schema, persistence (2026-07-26)

- Models/UserProfile.swift: SwiftData @Model with full defaults so a bare UserProfile() is valid; enums (Sex, GoalType, ActivityLevel) stored as raw strings with typed accessors that degrade to neutral defaults on unknown values. Independent height/weight unit toggles (decision 15). No dayKey, no flags, no body-composition fields.
- Store/PellSchema.swift: SchemaV1 (UserProfile only) and PellMigrationPlan with V1 as base, stages empty until V2 (decision 16).
- Store/Persistence.swift: launcher returning success(ModelContainer) or failure(Error), never throws; named store at Application Support/Pell; inMemory path for tests and previews; pre-open backup of the SQLite file set into timestamped folders, newest 3 kept (decision 17).
- 11 tests: exact profile defaults, accessor round trips, corrupt raw degradation, container opens against SchemaV1 with plan attached, persist across close and reopen in a temp directory, backup rotation keeps exactly 3 and prunes oldest, first-launch no-op backup, pre-open backup on reopen, in-memory touches no disk, failure path returns .failure.
- Three commits: user profile; versioned schema; persistence with backups. PellApp untouched.

## Slice 02, phase 2B - dayKey and weekKey spine (2026-07-25)

- Models/DayKey.swift: DayKey (from, date(from:), today, offset, range) and WeekKey (ISO 8601 yyyy-Www), pure functions only, no entity or container changes (decision 14).
- Contract documented at the type: a dayKey is computed once at log time and frozen; timezone changes never rewrite history (decision 1).
- Strict parsing: shape check plus round-trip verification, because Calendar.date(from:) silently normalizes invalid components like 2026-13-40.
- 16 tests across DayKeyTests and WeekKeyTests, all on fixed timezones: round trips, midnight boundary, month/year-end offsets, leap day, Europe/London DST days (23 and 25 hours), cross-timezone travel, malformed inputs, ISO year-boundary weeks (2021-01-01 is 2020-W53, 2025-12-29 is 2026-W01). ISO expectations cross-checked against strftime.
- Three commits: ADDED data model and data type; ADDED constraints; dayKey and weekKey spine with edge case tests.

## Slice 02, phase 2A - Shell hardening (2026-07-25)

- Tab content moved from a switch to a ZStack over all six screens: per-tab state now survives tab switches. Hidden tabs are non-interactive and hidden from VoiceOver (decision 13).
- AppConfiguration reworked from an @AppStorage struct to an @Observable class seeded from UserDefaults: a flag flip now re-renders consuming views live (decision 6 amended). Injection stays the keyed environment entry; one instance held with @State at the app root.
- AppConfiguration tests isolated: each test builds its own uniquely named UserDefaults suite and removes it afterwards, parallel safe. Third test added proving observation fires on a flag change.
- Tab bar material extends into the bottom safe area: no seam above the home indicator, checked in light and dark mode.
- Verified: full test suite green, tab switching works in the simulator, no UserDefaults or modelContext references in Features/.

## Slice 01 - App shell (2026-07-25)

- Six-tab shell in the locked order: Today, Diary, Workouts, Journal, Trends, Profile.
- Custom tab bar, not the native TabView item bar: iPhone's native bar caps at five visible items (decision 12).
- AppConfiguration flag struct, @AppStorage backed, injected through the environment at the app root. First flag: gamificationEnabled, default off. Unit tested.
- Placeholder screens for all six tabs, neutral copy only.
- Project settings: iOS 17.0 deployment target, iPhone only, Swift Testing, no third-party dependencies.
- Docs seeded: 02-decisions, 03-rejected, this changelog. Architecture and schema docs were written during planning, ahead of this slice.

## Updated slice 12 - exercise catalog swap (2026-08-02)

Replaced the wger online exercise arm with a bundled offline catalog. The app drops from three networked files to two, both food.

- Exercise search now works with no network connection. 650 exercises: the 40 curated rows unchanged, plus 610 imported from free-exercise-db under the Unlicense.
- Deleted `WgerExerciseCatalog` and its tests, the `onlineExerciseSearchEnabled` flag, the CC BY-SA attribution line, the online search section in the library and the mid session picker, and the online detail path. The no-network allowlist is back to two files.
- Exercise images are bundled assets for 39 curated exercises, 2.2 MB, never fetched.
- Added a reproducible build chain in `scripts/`: the catalog generator, the alias map, the image builder, and a two tier tone scan that gates the import.
- Fixed a latent bug in both catalog literals: JSON escapes were being consumed by Swift, which would have silently emptied the imported catalog. Both are now raw literals with a pinned regression test.
- CLAUDE.md's catalog-queries amendment narrowed to food only.

## Updated slice 12 - muscle visualizer (2026-08-02)

A drawn body figure with fillable muscle regions, on three surfaces.

- `MuscleFigure` renders front and back from hand authored SwiftUI paths. No image asset, no licensed art, no dependency.
- 14 regions: the ten recorded keys plus forearms, adductors, abductors and neck, which the imported catalog carries with no app key.
- Exercise detail fills the primary muscle fully and secondaries lighter, straight from the catalog row.
- Progress muscle balance and the session summary shade each muscle relative to the largest in the window, beside the existing percentage bars rather than replacing them.
- Fill is one accent ramp, never a red to green scale, and no surface calls a muscle underworked.
- Accessibility label names the filled muscles, since colour alone cannot carry the meaning.
- `BundledExerciseCatalog.primaryBySlug` is now computed once instead of rebuilt over 650 rows per render.

## Updated slice 12 - muscle balance heat scale (2026-08-02)

- The muscle figure on progress and the session summary now uses a sequential heat ramp instead of the single accent ramp, with a legend reading "less volume" to "more volume".
- Light mode runs pale to deep, dark mode dim to bright, so the busiest muscle always carries the most contrast against its background.
- The exercise detail page keeps the accent ramp: its two levels mean primary and secondary, not an amount.
- The scale stays warm end to end. Tests assert no green pole at any intensity and that luminance moves monotonically in both schemes, so the ordering survives colour vision deficiency.

## Slice 13 - Today tab (2026-08-02)

The hub tab goes live: one browsed day rendered by a customizable section registry, with the app's first cross-tab router.

### 13A store prerequisites and shared hoists
- The revision touch reaches every read family Today uses: journal, habits, sleep, weight, steps, energy, life events, pulse and the range water read. Sixteen methods that previously left their surfaces stale after a write.
- `PellStore.latestWeightKg(onOrBefore:)` replaces two copy-pasted scans over every weight row.
- Target and remaining resolution moved out of `Features/Diary/DiaryMath` into `Derived/EnergyTargets`, with a BMR composition helper, so Diary and Today render the same numbers by construction.
- `waterEntries(on:)` made public for the timeline; `Meal: Identifiable` moved from a Diary view file to the model.
- `DayLabel` gained short and dated variants; six private day-text helpers across the Workouts files now call them.
- The Journal check-in's sections extracted into `Features/Shared/CheckInSections` with one load-and-save path, ready for the unified sheet to compose.

### 13B day scaffold, sections, coverage and energy line
- `TodayView` replaces the placeholder: day navigator, section stack, one fetch pass per render keyed on the store revision, one enum-driven sheet slot.
- `TodayLayout` is the single order-and-visibility codec for sections, tiles and lenses. Unknown ids drop, new ids land at their default slot, and saving never discards an id the sheet did not offer.
- Coverage strip reads the same presence flags as the streak rule; rest state comes from the declared tag only.
- Energy line: remaining is goal minus eaten, burned shown with its arm named and never credited, absent reasons distinguished between no profile and no weigh-in.
- Day-change observer re-anchors only when the browser was on today.

### 13C quick stat tiles and weigh in sheet
- Eight tiles over the day summary with tap-to-expand logging for mood, water, behaviours and protein; sleep, weight and activity open their sheets.
- Steps is read-only and names Apple Health as its source rather than offering a dead control.
- New weigh-in sheet, day addressed, kilograms canonical with pounds converting both ways.

### 13D hero card and lenses
- A pageable hero owning the horizontal swipe, so day travel stays on the chevrons and the two gestures never compete.
- Four lenses over pure helpers: week consistency as plain day counts, sleep as duration, quality, week average and shortfall, training beside the plan's day count, behaviours as marks.
- No streak figure anywhere; the gamification flag stays unread.

### 13E timeline, quick add and app router
- `TimelineBuilder` merges the day's rows with a stable `(date, kind, localId)` tie break and `localId` identity. Sleep opens the day; habits, tags, steps and device energy sit apart as untimed day facts.
- `AppRouter` carries tab selection and a per-tab day handoff consumed exactly once. Diary and Workouts opt in with one observer each.
- Floating quick add writes on the browsed day; start workout jumps to Workouts rather than opening a second live surface.

### 13F unified check in and insight callout
- `DayCheckInSheet` composes the shared check-in sections plus inline sleep, water and weigh-in, with food and activity nested and planned meals as confirm rows.
- Nothing writes until Done except water, and each domain writes only when entered, so a single tap can no longer fabricate a night.
- Insight callout shows the first ranked non-dismissed split over 90 days, on today only, with dismissal clearing each ISO week.

### 13G copy audit, tests and docs
- Recorded copy audit with three fixes: "yet" removed from three empty states as quietly prescriptive.
- Five new explainers: burned and its two arms, the energy line, coverage, week consistency, sleep shortfall.
- 601 unit tests green, Release build clean of tooling strings, decisions 110 to 119 recorded.

## Slice 14 - Trends tab (2026-08-04)

### 14A store reads, derived math and shared hoists
- Store read audit: `pulses(from:to:)` lands beside `pulse(for:)` with a revision touch and regression tests. The steps and energy range reads already existed from 13A, so the audit adds one read, not three.
- Five new Derived files. `LoadMath`: daily srpe load series with zeros as real rest, the 42-day and 7-day EWMA load averages seeded at zero, the uncoupled ACWR (acute 7 against the prior 21, adjacent and non-overlapping), no bands or thresholds anywhere. `ShuffleCheck`: FNV-1a seeded SplitMix64 re-splits returning plain counts, deterministic by test. `Association`: Spearman rho with average ranks on ties, rho and n inseparable in the result shape. `EventSplit`: before and after means around an anchor day, 28 days each side, anchor day on the after side, floor 5 per side reusing the engine constant. `Scorecard`: week-grain averages with per-row denominators, per-metric fill policy, comparison floor 3 logged days, prior period plus a trailing 4-week average that names how many weeks fed it.
- SleepMath grows bedtime spread (SD in minutes, noon anchored so midnight-crossing bedtimes measure as neighbours, per-record stored timezone, imported nights only with coverage named) and the weekday weekend split as two plain means with night counts. TrainingMath grows rest gaps, RPE distribution over completed working sets, the deload percentage as a plain number, and indexedTo100 for the two-lift compare. WeeklyVolume and MuscleBalance hoist in from the Workouts progress view; call sites read the same names and the existing helper tests pin rendering unchanged.
- Correlations gains the tag metric builder only: binary `tag:<kindKey>` metrics minted from the window's summary tags in stable order, the engine untouched.
- TodayLayout moves to Features/Shared as `LayoutCodec` with functions, semantics and AppConfiguration keys byte identical; TodaySection and DayRollover stay in Today. WeighInSheet hoists to Shared unchanged. AppRouter gains the `TrendsLens` enum (nine locked ids), and a consumed-once Trends destination carrying an exercise slug or a metric pair, consumption-tested.
- Long-history persona: 190 offset days ending today built by fixed arithmetic through the existing seed seam, DEBUG only. Gaps by design: a six-day break behind a travel tag, a three-day break, and per-domain modular misses. Training three days a week with slow progression and a lighter week every eighth, weight drifting down, 24 weekly pulses, two life events. Byte identical across two seeded boots by test, console seed button added.
- 665 unit tests green in a serial run; Today verified live in the simulator over the seeded fixture after the hoists.

### 14B chart kit, window model, cards and prose
- `TrendsWindow`: one model, two named families. Rolling 7, 30, 90 days ending today and paged whole; calendar ISO week, month and hand-derived quarter paged by period. Day keys come back oldest first and clip at today, so a mid-period window's denominators count elapsed days only while `fullInterval` keeps the whole period for the axis. Previous window, bounds for range reads, containsKey. DST and ISO year-boundary edges tested on a fixed London calendar.
- `TrendCharts`: a pure gap segmentation helper (solid runs of consecutive logged days, dashed valueless bridges only where both sides exist, grey tint regions for every unlogged stretch) with the SwiftUI thin above it. Line and bar charts share one dash vocabulary: dashes are never data; bridges, ghosts and derived overlays only. Ghosts are the previous window index-aligned in the same plot, so the y-scale is shared by construction. Reference lines, labelled life event rules, drag-to-scrub snapping to the nearest day, and the coverage caption under every chart.
- `TrendCard`: the one card container with the stable id, explainer chip and the accessory slot defaulting to empty; `CardContext` carries card id, lens, the window value (family, length, shift) and metric ids, identity only.
- `MetricPresentation`: the per-metric registry answering fill, format and unit in one place. Decision 124's four zero-is-real metrics declared, srpeLoad and trainingMinutes joining them on the same rest-is-real logic, everything else gaps; tag metrics resolve by prefix as did/didn't binaries; formats are POSIX-deterministic; weight stays canonical kilograms with unit conversion at the lens.
- `TrendsProse`: every generated sentence as a tested template. Window labels ("Last 30 days", "6 Jun to 5 Jul", "This week", "3 to 9 Aug", "August 2026", "Jul to Sep 2026", years appearing once a range leaves the current year), ghost captions, coverage notes with a unit parameter, the one split-sentence template, rate of change stating only the sign, singular day counts.
- 701 unit tests green in a serial run. No live surface this phase; the placeholder tab is untouched and the kit first renders in 14C.

### 14C trends scaffold, overview lens and customization
- `TrendsView` replaces the placeholder: lens menu over the codec-ordered registry, the window control (family toggle, three relabeling pills that keep their position across a family switch, pager with the prose range label, back to present, compare toggle with the ghost caption), one fetch pass per render registered on the store revision, and the enum sheet slot.
- Lens and window are session state; the tab opens on rolling 30 days (phase call). Any family or length change lands back on the present.
- The one fetch pass spans the window and the scorecard weeks in a single set of range reads, builds summaries once, and slices; PRs read full history since a record is relative to everything earlier.
- Overview lens: the twelve-card catalogue through the shared codec, default pinned weight, volume, sleep, mood, coverage (seeded as a default hidden list in AppConfiguration, pinned to the catalogue by test). The six averaged cards reuse the scorecard's value math so one implementation answers the fill question. The weekly scorecard card anchors to the ISO week containing the window's end (phase call) with previous and trailing-average columns, dashes below floors, and no comparison at all when the current week is below its floor. The report entry is the fixed last section, stub until 14H.
- `TrendsCustomizeSheet`: lenses and overview cards over the shared draft sheet, one save through the codec's merge.
- Inline logging through the toolbar menu: weigh-in (shared sheet), sleep (Journal's), workout quick log (Workouts'), life event (Journal's). No new forms anywhere.
- The router's Trends handoff is consumed once on tab arrival; presets wait in session state for the strength lens and explorer phases.
- 709 unit tests green in a serial run. Verified live by real tap driving: this machine's simulator accepts injected taps on buttons, menus, pickers and tab bars (the HID limitation applies to simctl, not the injection route used here); switches need a short drag. Paging, empty-window states, the customize round trip onto the rendered card set, and the weigh-in sheet's preloaded store value all confirmed on the seeded persona.

### 14D weight, sleep and mood lenses
- The shared derived pass grows compare: with the toggle on, the span extends over the adjacent previous window in the same range reads, and `TrendsData` hands lenses `points(_:)` and index-aligned `ghost(_:)` builders. The sheet slot gains day payloads so a lens tap edits in place.
- `WeightLens`: the trend line over DaySummary (manual wins, suppressed invisible), life event markers, least-squares rate per week through the prose template, scrub-to-select with an edit row opening the shared weigh-in sheet on the selected day, and the window stats card (latest, average, range, count). Kilograms stay canonical; the profile unit converts at render.
- `SleepLens`: duration bars against the profile target line, the quality series with its manual-only caption, the shortfall sentence over the existing deficit-only debt, bedtime spread naming its imported-nights coverage, the weekday weekend split sentence, and a stages card (mean minutes per stage) that renders only when an imported night carries stages.
- `MoodLens`: mood, energy and stress series with ghosts, and the variability card as plain spread numbers beside their check-in counts. No composite anywhere.
- Today's hero card renames its private SleepLens and BehavioursLens to Hero-prefixed names; the Trends lens types own the plain names per the spec's file list.
- New prose templates (spread, shortfall, counted nouns) pinned by test; five explainer entries added and `sleep-debt` generalized from "this week" to "the period shown" so one entry serves both surfaces. 16 new tests.

### 14E strength and training load lenses
- `StrengthLens`: the lift picker over working-set history ordered by most recent training, per-day best estimated 1RM line and per-day lift volume bars over the window, the strict PR feed filtered to the window with its cap stated, and the two-lift compare indexed to 100 at each lift's first shown session, rendered session-indexed so gaps in calendar time never fabricate a line. The router's exercise payload presets the picker, consumed once.
- `TrainingLoadLens`: weekly volume bars over the window's ISO weeks, muscle balance with the shared figure and heat ramp, week structure (training days, rest gap average and longest with an inline explainer, the deload percentage anchored on the last complete ISO week so a mid-week window never reads partial as light), and the RPE distribution with its rating coverage line.
- The advanced section behind `advancedLoadMetricsEnabled` (new flag, default off): the 42-day and 7-day load averages under the "Fitness-fatigue model (Banister)" header and the uncoupled ACWR, computed over a 126-day lead-in so the shown values arrive warmed up. Flag off renders nothing (decision 125).
- Workouts progress gains "Open in Trends" as a per-exercise context menu through the router; the quick view stays on the hoisted math.
- Six explainer entries including the contested-literature ACWR caveat. 9 new tests including the deload week-anchor arithmetic.

### 14F energy and behaviours lenses
- `EnergyLens`: intake bars against the calorie target line, the burn series as bars never lines (no trend may cross an arm switch, decision 55) with the arm named per day counts, and the balance card existing only on days carrying both figures with its denominator in the caption. A missing profile and a missing weigh-in read as different absences. Simple diary and hide-numbers render day counts instead of numbers, the Diary convention.
- `BehavioursLens`: the habit card (picker, done count against the window, the weekly cap as a note, done days by weekday), the did-and-didn't card running HabitInsights over the shared window with the Journal rendering's day-count rule for binary outcomes, tag frequency counts through the kind names, the life events card with the EventSplit changes sentence (metric picker over numeric registry metrics, floors respected, the 28-day bounds stated), and weekly pulse rows beside each window week's logging count.
- An engine-parity test pins that the lens and Journal read one HabitInsights implementation. 8 new tests.

### 14G cross domain lens, explorer, grid and shortlist
- The seven locked pairs render as split cards through the engine with the recorded wording, below-floor states naming the floor.
- The pair explorer: split side over the registry plus the window's tag binaries, outcome side numeric only, same-day or next-day lag, the engine's median split and floors, the sentence through the one split template with above/at-or-below-median group labels and a next-day caption. The router's pair payload presets it, consumed once.
- The moves-together grid: lower-triangle Spearman rho over the explorer metric set, cells shaded by magnitude with numbered rows, the multiple-comparisons caveat above, tap selecting a cell to read rho with its n and an Explore button presetting the pair (binary-first taps swap so the outcome stays numeric).
- The largest-splits shortlist: ranked locked-pair splits capped at five, each row carrying both means, both counts, and the seeded shuffle count in plain words ("3 of 200 random splits produced a gap this large"). The shuffle helper surfaces here and only here this slice.
- `CrossMath.pairPoints` restates the engine's join for the surfaces needing raw pairs, pinned to the engine's own split output by a parity test. The last placeholder dies with the lens switch now exhaustive. 6 new tests.

### 14H monthly report facts, interactive view, pdf and share sheet
- `ReportModel`: months-with-data discovery off dayKey prefixes, the newest-complete-month default, month day keys clipped at today, six-month keys across year boundaries, and the per-month least-squares trajectory. `ReportSection` registry through the shared codec with the day log excluded by default. `ReportTone` single-cased neutral with the arm-naming footer pinned by test (decision 129).
- `ReportBuilder.facts`: one month's facts assembled entirely from the helpers the lenses call (Scorecard values, volume and records, muscle shares, RPE and rest gaps, sleep debt, weekday split and bedtime spread, mood spreads, weight stats and rate, tag counts, pulses, the locked pairs); the equality test pins sampled facts to the direct helper calls over the same inputs, so screen and PDF cannot disagree.
- `ReportView`: month picker over months with data, section toggles through the shared customize sheet, scrubbable calories, sleep and mood charts through the kit, the six-month table with per-metric trajectory sentences and its explainer, the day log rows, and the tone footer. The overview lens's fixed last section now opens it.
- `ReportPages` and `ReportPDF`: A4 cover plus one page per visible section in a fixed light print palette with flat fills, rendered through ImageRenderer into a CGContext PDF, previewed through PDFKit, and shared as a month-named file through the app's first share sheet from the same buffer the preview showed. The six-month page reads the same SixMonthColumn table as the screen.
- Benchmarks green: facts over the long-history persona inside the one-second budget, the PDF smoke test counts its pages. 13 new tests.

### 14I copy audit, tests and docs
- The recorded copy audit at docs/slices/slice-14-copy-audit.md: three in-flight rewordings (the numberless balance line, the habit count order, pulse week labels off ISO numbering onto date ranges through a new weekKeyLabel template), zero standing violations at close.
- Twenty explainer entries across the slice's computed numbers, the registry id test extended over every wired Trends id, and the rest-gaps chip wired inline on the week structure card.
- 762 unit tests green in a serial run; write guard, DevTools guard and the two-file no-network guard green; Release build clean of tooling strings.
- Verified live in the simulator over the seeded fixture: the scaffold, lens switching, window relabeling with the pill toggle, the weight lens end to end (chart, coverage caption, rate sentence, edit row, stats card) and the sleep lens prose cards. The simulator session ran with the device in a rotated state, so remaining lens content rests on the pure-helper tests per the recorded verification note.
- Decisions 120 to 130 recorded; slice 14 rejections logged; docs/coach-catalogue.md landed with the review amendments; CLAUDE.md amended (behaviours lens and advanced load flag in the Trends scope, the merged-surface line, decision 81's Trends half amended to declared slots gated by tier); status updated pointing at Profile.

## Slice 15 - Profile (built 2026-08-05 in one working run, commits pending)

### 15A store and configuration prerequisites
- AppConfiguration: the appearance mode, the modules hidden list, the six reminder keys (enabled plus minutes-from-midnight per reminder), workout haptics and sounds, the weekly auto-export toggle; constants once, seeded per the house pattern, and `resetToFactoryDefaults()` for master erase with its test pinning every value against a fresh instance.
- Appearance applied once at the PellApp root through preferredColorScheme; system passes nil.
- `Features/Shared/ModuleGate.swift`: the four modules (food, move, sleep, mood), the tab rule, the offered-catalogue filter, the surface requirement maps, and the metric-to-module map for pair gating; all pure and tested.
- PellStore: `updateProfile` (the one settings write path, fetch or create, apply, save), `privacyCounts()` over the same entity lists `wipe(domain:)` deletes (pinned by test), `profileCounts()` (distinct logged days, finished sessions, check-ins), `syncStateIfPresent()` passive read, and the two stamps (`stampAutoExport`, `stampRestore`).
- AppRouter grows `requestRelaunch()`; PellApp observes it and re-runs LaunchState, the restore rebuild seam.

### 15B hub, personal info and targets
- ProfileView replaces the last placeholder: hero (name with neutral fallback, the three logging counts with their explainer), section list with trailing current values on every row (the prototype's strongest hub pattern), inline appearance picker, module-gated Food settings and Workout experience rows, Developer row unchanged.
- PersonalInfoView: name (commit on submit and disappear), sex, age 18 to 100, height in the profile unit, and both unit toggles living beside the fields they govern.
- GoalsTargetsView: goal (lose, maintain, gain) and activity pickers; calorie and macro rows as override-over-recommendation with the first touch writing an override; reset to recommended naming the computed value; the named absence with weight-free seed targets when no recommendation exists; sleep, workouts a week, steps, and the water target's first editor; the simple-diary footer substitution. goalWeightKg stays un-surfaced (call 3).

### 15C app modules and gated surfaces
- AppModulesView: four cards with plain hides-summaries and the one-sentence contract footer.
- RootTabView renders module-gated tabs (Diary with food, Workouts with move, Journal when mood and sleep are both off; order preserved); a selection whose tab hid falls back to Today.
- Today: sections, hero lenses, and tiles run the codec over the gated catalogue so layouts survive hide and re-enable; the coverage strip renders visible domains with an honest denominator; quick add gates its entries and disappears when nothing can log; the check-in sheet gates its sections (weight stays); the timeline filters rows by domain; the insight callout skips pairs touching a hidden domain.
- Trends: lens registry and customize sheets over the gated catalogues with an active-lens fallback to overview; overview cards gated; the explorer's split and outcome sets, the grid, and the locked pair cards drop hidden-domain metrics, with picker fallbacks that never land on a hidden metric.

### 15D food, workout and reminder settings
- FoodSettingsView over the four existing flags with footers naming what each does and what travels.
- WorkoutExperienceView: haptics and sounds with real consumers (set completion tap; rest end haptic and chime on natural expiry only, in the bar and the minimized bar), the default rest stepper, and the advanced load metrics toggle closing the slice 14 standing note. WorkoutFeedback owns the consumers.
- RemindersView and ReminderScheduler: three reminders with fixed identifiers, explainer-then-request on first consent (branch on the system status, no seen flag needed), denial reverting the toggle with the footer naming the system path, time pickers bridging minutes through pure tested math, reschedule on change, cancel on off. Notification bodies fixed at schedule time and neutral.

### 15E apple health
- HealthSettingsView: the three-state connection row; connect as explainer, authorization, stamp, then the visible 365-day backfill with a plain summary; seven source pickers writing the profile keys with a refresh on switch; write-back toggles with the queued count, its explainer, and the parked last error; status (last refresh, backfill) with the import-windows explainer; complete disconnect behind a confirm naming all four effects, imported rows kept.

### 15F backups, auto-export and restore
- Export/AutoExport: the weekly foreground check against the SyncState stamp, Documents/Exports with the newest five kept, same-second suffixing, empty-store and XCTest no-ops, and `deleteAllExports` for the privacy centre. PellApp runs it on foreground behind the toggle.
- BackupsView: back up now writing a real rotated file and offering the share sheet, the auto-export toggle with the last-run footer, and restore disabled while a sync or drain is in flight (the new `SyncEngine.isRunning(over:)` and `Outbox.isDraining(over:)` seams) with the row saying why.
- RestoreFlowView: pick, decode and validate with nothing touched, the rendered ImportReport (rows, per-entity counts, repair lines, the coupling-reset note), the destructive confirm promising and running the safety export, restore through the slice 09 path, stampRestore, and relaunch through LaunchState so no stale object survives.
- ImportValidator: the repair pass polices multiple open sessions (newest stays open, others close at their own date) and multiple active plans (newest stays active), counted under clampedRows; three tests both sides.

### 15G privacy centre and help
- PrivacyCenterView: live counts per erase domain greyed at zero with the system-rows footer; the what-leaves section as the computed write-back sentence plus the PrivacyDisclosure lines (its TODO(15) closed); per-domain erase quoting and confirming the count; master erase as rows plus configuration factory reset with notifications cancelled and exported files kept, stated in the dialog; the separate delete-exported-files action behind its own warning page naming the files as the only recovery copies.
- AboutView (what the app is, the dissertation context, version) and the bundled PrivacyPolicy.md rendered through the small tested PolicyBlock parser; the policy ships in the Release bundle by test and by inspection.

### 15H copy audit, tests and docs
- The recorded copy audit at docs/slices/slice-15-copy-audit.md: one in-flight fix (a "yet" phrasing on the absent recommendation, the slice 13 violation class); zero standing violations.
- Four new explainers (logging counts, import windows, auto-export schedule, the outbox queue) beside the reused targets entry; the registry id test extended over the wired Profile ids.
- 788 unit tests green in a serial run (26 new); write guard, DevTools guard, and the two-file no-network guard green; Release build succeeds, clean of tooling strings, with the policy resource present.
- Verified live in the simulator: the hub with live trailing values, module toggles hiding and restoring the Diary tab in place, the privacy centre counts and disclosure, and the weekly auto-export running on first foreground (the backups row showed its stamp).
- Implementation notes: profileCounts fetches full rows because propertiesToFetch crashes on relationship-carrying models; the safety export also stamps lastAutoExportAt, which the restore wipe immediately discards; Today's energy section gates on food alone while the Trends energy lens gates on food or move.

## Slice 16 - Coach (built 2026-08-05 in one working run on the slice 14 branch, commits pending)

### 16A coach entities, schema and export format v2
- Goal, GoalEvent (the renegotiation trail), CoachMessage (with threadKey per the catalogue promotion); SchemaV7 with the sixth lightweight stage and the headline test reopening a populated V6 store with the three tables present, empty, and writable.
- AppConfiguration: coachMode (off default), the two declaration stamps with the copy version, muted categories, the weekly budget (7, bounded 0 to 14 at the UI), the last-run stamp; factory reset and its test grown over all seven keys.
- Export formatVersion 2: three DTOs with recorded sorts and counts, the shipped v1-to-v2 identity transform (tested both directions: a v1 file decodes with empty coach arrays; a format 3 file refuses), validator identity checks plus the GoalEvent orphan drop, coverage guard green over the 30-entity schema, byte-identical round trip and restore-equality tests over the new entities.
- PellStore+Goals (create, renegotiate, status changes, every change writing its event) and PellStore+Coach (record, current-on-day, dismiss, the affordance-only actedAt stamp, feedback with reasons, the gap answer writing its mapped tag through the ordinary upsert, tier changes as system log rows); erase domains goals and coach with counts; console, seeder emptiness, and the future-format sample grown.

### 16B goals in profile and on today
- Derived/GoalProgress: the goal key vocabulary (call 16), elapsed-only periods (day, ISO week, window) clipped at the window and today, count qualifiers per key, the lift goal as best-estimate-against-target, unknown keys as named unresolved. Tests cover periods, the DST week, qualifiers, the value goal, and the unresolved key.
- Features/Shared/GoalSupport: one loader and one label set behind Profile and Today, so a count can never differ between tabs.
- Profile: the goal rows inside Goals and targets (progress sentences, past goals, new goal), GoalDetailView (renegotiation through the event trail, history, retire with confirm), GoalEditSheet (metric picker over built-ins, active habits, and recent lifts; cadence; target; the end-date rule for lift goals).
- Today: the goals section in the registry, present only when goals exist, tap through to Profile, ungated by modules (call 18).

### 16C engine, statistics and tier 1
- Derived/Coach: CoachTypes (the snapshot, config value, message facts, candidates, run with trace, EffectSize), CoachTemplates (every string as a slot template, the audit's finite set, no causal verb), CoachRules (the thirteen tier 1 rules plus gap.question with their floors and module gating; patterns run the seven locked pairs through the engine, effect size with spreads and counts, and the 14A ShuffleCheck, rare posts as pattern.locked, common as the ordinary pattern.null), CoachSelector (disruption, life-event, and coverage-floor run suppressors; mutes; the 30-day two-dismissal quiet; cooldowns; the budget with zero as silence; one posting per run; every drop traced), CoachEngine (tier 0 is the empty run; the counter-example reuses the overall split boundary against the shown pattern's own pair).
- CoachService: the snapshot builder over one fetch pass (summaries, targets, habits, bedtime spread, week tonnage by muscle, life events, goal progress, open plan days, modules), history from the log with the pair riding along, deterministic inputs encoding, and the one-run-per-day foreground gate wired into PellApp.

### 16D tier 2 and elicitation
- CoachTier2Rules: plan.openDays, goal.progress (least-covered first), goal.windowEnd (the option set with the observed rate), goal.candidate (never above the observed baseline, by test), habit.ladder, habit.stack, habit.cueFromData, and suggest.singleAction, the one prescriptive class.
- Restraint as engine logic with tests: two-ignore permanent retirement, the category quiet, the budget, the run cap, tier gating asserted on class over the rich fixture.
- gap.question end to end: the halving triggers, the fixed answer set with the call 7 mapping (rest-day, travel, sick, injury as tags; busy, reduced interest, no reason snapshot-only), the answer recorded in the inputs snapshot beside its question, decline as dismissal with the cooldown as the window.

### 16E surfaces, the declaration gate and the trends slots
- CoachDeclarationSheet with the versioned copy; the settings tier picker gates each step up through it, drops apply at once, every change logs a tier row.
- CoachScreen (ask, today's message, the history), CoachMessageRow (explainer chip, the why-panel naming rule, tier, day, and inputs, feedback with the four reason codes, the affordance as the only actedAt path), GapQuestionSheet, QuestionPickerView (pull mode with the five fixed questions including the renamed what-each-kind-needs, and context mode over a card's metrics).
- Today: the coach card in the section registry (current message only, absent below tier 1); Journal: the coach row at any tier above off; Profile: the coach hub row with the tier as its trailing value, CoachSettingsView (tier, mutes, budget, feed per call 15, the full log including system rows, the counted erase shortcut).
- Trends: the top entry mirrors Today's card (call 11) and the card accessory fills TrendCard's declared slot (call 12): filled bubble when the current pattern touches the card's metrics, plain bubble as ask-in-context; at tier 0 zero coach pixels. ReportTone grows to three tier-mirroring cases (call 14) read at generation; the report carries only the footer.

### 16F copy audit, tests and docs
- The recorded copy audit at docs/slices/slice-16-copy-audit.md over the template set, declarations, answers, and explainers; the tier 2 allowance stated explicitly; zero standing violations.
- docs/references/coach-rules.md: the catalogue as built, the selector pipeline, and what the engine can never do.
- Five new explainers (coach recaps, patterns and random splits, why the coach asks, options and suggestions, goal progress) with the registry tests extended over the wired and rule-declared ids.
- Decisions 141 to 145 recorded (the nine build-revision calls); status updated.
- Deviations recorded honestly: no new coach personas were cut (the standard fixture and the 190-day persona exercise the engine; a dedicated tier 2 persona is noted as follow-up), and golden-file message fixtures are represented by the determinism and no-unfilled-slot tests rather than stored goldens.

### 16 live verification (2026-08-06)
- The live simulator pass over the coach surfaces, by tap driving, closing the standing note left at 16F.
- Coach off is indistinguishable from the app before the slice: zero coach pixels on Today, on both Trends slots, and in Journal, compared directly against tier 2 over the same store. The message log and its erase stay reachable at tier 0, which is right, they are stored data and not a coach surface.
- The declaration gate holds per tier step. Off to reflective raises the tier 1 sheet and Cancel leaves the tier at off. Off to guiding raises the tier 1 sheet, not tier 2, and agreeing lands on reflective, so a step cannot be skipped. Reflective to guiding then raises the tier 2 sheet with its own copy. Drops apply at once with no gate.
- Tier gating on class is visible in the settings: message kinds go from four to six at guiding, Options and Suggestions appearing only there.
- A tier 2 posting (plan.openDays) carried its openWorkouts affordance and read tier Guiding in its why panel.
- Why panels carry rule, kind, tier, day, and the raw inputs, and those inputs match the rendered sentence exactly (before 3.0 over 5 days, after 3.5 over 6, so the EventSplit floor of 5 a side holds).
- Feedback through its four reason codes, dismissal advancing the card to the next message identically on Today and Trends, and the counted log erase.
- Null results are stated plainly rather than invented ("Nothing to report: no tracked pair holds enough logged days for a split here").
- The question picker in both modes: context from the Trends card accessory, which named the weight card, and pull from the feed, which offered a question built from the logged life event.
- Tier changes recorded as tier.change rows, with the cancelled attempt correctly recording nothing.
- Goals with the coach on: a process goal authored in Profile derived its progress live and surfaced in both Profile and the Today section.
- Fixed in the same pass: CoachScreen rendered the current message twice, once under Today and again inside the Messages history. History now excludes the current message, and the empty history line separates an empty log from a log whose only message is today's, with the retention footer kept in every state.
- Left to the suites, not covered by hand: the formatVersion 2 export round trip, the adversarial suite, and the restraint rules that need elapsed days (two-ignore retirement, budget exhaustion, cooldowns).

### 16 restraint note and the first full suite run (2026-08-06)
- The restraint note in the why panel, under "What your controls do": dismissal quiet is per category and ages out, retirement is per rule and does not, and neither was discoverable before acting. CoachRestraintNote states the live count beside the threshold.
- Counts read from the selector's own helpers: dismissalsInWindow and unactedPostings extracted, with categoryQuieted and suggestionRetired calling them, and ignoresToRetire named in place of a bare literal. The copy cannot state a threshold the engine does not enforce, and two of the 14 new tests pin the sentence to the selector's boundary in both directions.
- Copy audit addendum recorded over the six sentences as their own class, outside CoachTemplates.all since they are not message templates.
- Slice 16's first full suite run, which had never happened: 842 tests, 4 failing, 7 issues. None was a product bug.
- Three were tests whose behaviour slice 16 changed without updating them: ReportTone grown from single-cased neutral to three cases at 16E, the goals and coach erase domains added at 16A but never mapped in the erase test's table (and never seeded, so the assertions would have passed on zeroes), and the newer-format assertion still expecting version 2 after 16A moved the export to formatVersion 2.
- The fourth was a fixture that could not fire its rule: gap.question needs five elapsed days in the ISO week and the test's today was a Wednesday. The rule is correct, verified through the run trace on a Friday, where it posts with its affordance and wins the single slot. Split into two tests, one for the firing and one for the silence, so the floor itself is now covered.
- Guards against the same class of drift: everyEraseDomainIsMapped, and the newer-format expectation expressed as ExportFormat.currentVersion + 1.
- 845 tests in 109 suites green in a serial run, exit 0.

## Updated slice 13 - Today tab layout and styling (2026-08-07)

Spec: docs/slices/updated-slice-13-today-layout.md. Copy audit: docs/slices/updated-slice-13-copy-audit.md. Decisions 148 to 156. No schema work, no new entity, no migration, no network.

Slice 13 shipped the Today tab as working structure on default SwiftUI chrome. This pass restyles it to the dashboard design from the prototype, and closes the four content calls that design carried.

### The four content calls (2026-08-07)

- The readiness lens is not built. It is 1 of the design's 6 pager dots and it infers a wellbeing state, which is on the excluded list.
- The streak chip ships behind gamificationEnabled and renders nothing with the flag off.
- The insight headline is the pair name. The prototype's "Sleep debt has built up" asserted a direction and a concern.
- Goal momentum ships as a fifth lens without the target arrow and without the "holding steady" chip.

### What changed

- New `Features/Shared/DashboardStyle.swift`: geometry tokens, the `CardSurface` modifier behind `.card()` and `.pill()`, `DomainStyle` (six tinted domains), `DomainBadge`, `SectionHeading`, `CircleButton`.
- `TodayView` moves from `List` to `ScrollView` over a `LazyVStack`, nav bar hidden, custom header carrying the day strip above the title. Sections declare whether the tab draws their card. Goals and coach draw their own, because both render nothing when empty.
- `DayNavigator` gains a `headline` style and `DayLabel` a `headline(for:)` variant. The six existing call sites are untouched.
- Coverage becomes one pill: the count, a check per domain, a chevron. Rest day shows a bed in the move slot; the visible "rest day" wording is gone.
- The insight callout becomes a tinted pill with a badge, an "IN YOUR DATA" eyebrow, and the pair name as its headline. Dismissal moved to the context menu.
- The hero card owns its chrome: chevrons that wrap, the centred uppercase lens title with its explainer, page dots, and the lens customize control. The energy line renders as its footer with the arithmetic signs between the four figures, and standalone only when hero is hidden.
- New goal momentum lens and `HeroLensMath.goalMomentumView`: the 90-day weigh-in line, the goal weight as a dashed rule, the plain difference, and three week figures. Three absences read differently. New `goal-momentum` explainer.
- Tiles get a TODAY heading with its own control, domain-tinted symbols, a rotating disclosure chevron, and drawn dot rows for mood and water.
- The timeline gets a heading and a connected rail of tinted badges, one card per row.
- The customize sheet becomes shown and hidden groups over two draft arrays, with symbols, captions, and a reset. The three Trends call sites keep their plain rows through defaulted closures.
- The check-in sheet gains a "N of M logged" count with dots and a check mark on each section that holds something.
- The quick add becomes a custom two level expansion with labelled tinted actions and a scrim; check in joins the actions.

### Not built, and why

- The per-lens body visualizations from the design (the week's day-by-domain grid, the sleep bar chart, the training session list) are not in the approved spec, which covered card chrome only. The lens bodies are unchanged apart from their chips.
- The timeline's right-hand value column. `TimelineEvent` has no value field; the figure sits inside `detail`, and splitting it out means rewriting the builder's copy per row kind and its tests.
- The training PR chip. `TrainingMath.personalRecords` needs every set ever logged to tell a real PR from a week-local best, and an all-time fetch per hero render is the wrong cost.
- The water tile still reads ml with one dot per 250 ml glass, rather than switching its displayed unit to glasses.

### Corrected against the prototype source (2026-08-07)

The first pass built from screenshots and invented a palette of system colours, system font styles, and a shadowed card. The prototype has a real design system in `../HealthAppDemo/HealthAppDemo/Theme.swift` and that is what the design actually is. Reworked:

- `Features/Shared/DashboardStyle.swift` deleted, replaced by `Features/Shared/Theme.swift`: the hex palette in light and dark pairs (`bg`, `bgAlt`, `bgSunken`, the `ink` to `ink4` ramp, `hairline`, `track`, `blue`/`blueSoft`/`blueInk`, `protein`/`carbs`/`fat`, `good`, `streak`), `scaledFont` for every fixed size, and `.card(pad:)` as a fill plus a 1pt hairline stroke with no shadow.
- New `Features/Shared/ScreenHeader.swift` plus `DayPickerSheet`: edge slots in a ZStack over a centred eyebrow and title. Today's leading slot is the avatar opening Profile, the trailing slot the sections control. The `DayNavigator.headline` style added in the first pass is reverted; the eyebrow carries the day chevrons now.
- Every surface re-cut to the prototype's real metrics: the coverage strip (15pt bold count at minWidth 34, 13pt marks, 12pt labels), the hero pager header (11pt semibold title at kerning 0.5, 6pt dots, 30pt `bgSunken` pager buttons, 200pt lens height), the energy equation (19pt bold monospaced values, 9pt semibold labels at kerning 0.4, 15pt operators), the tiles (104pt tall, 14pt pad, 13pt icon, 12pt semibold `ink2` title), and the timeline (one continuous 2pt spine behind 28pt outlined nodes, not filled badges).
- The customize sheet rebuilt as a `ScrollView` on `Theme.bg` with a sunken divider row between the shown and hidden halves, `bgSunken` on hidden rows, and a bordered reset button, replacing the native `List` of the first pass.
- The quick add rebuilt to the prototype's row: a bordered label capsule beside a 44pt tinted circle with a 6pt trailing inset, and a plus that rotates 45 degrees rather than swapping to a cross.

Two prototype tokens are deliberately not carried: `Theme.mood`, its red to green ramp, because Pell keeps `MoodScale` for the reasons that rejected a red to green heat scale; and `Theme.bad` on an unlogged coverage day, because a mark says logged or not logged and never how well.

### Customize sheet: drag, arrows, and swipe (2026-08-07)

The sheet had the prototype's look but only one way to work it. It now carries all three of the prototype's interactions, in `Features/Today/TodayCustomizeSheet.swift`.

- New `CustomizeDraft`, a pure struct holding the two halves. The divider is a real `Entry` case, so its index is the shown and hidden split and a drag across it is what hides or shows. Unit tested in `PellTests/CustomizeDraftTests.swift` (20 tests).
- Press and hold drags a row anywhere in the list, divider included, reordering live under the finger through `CustomizeDropDelegate`. The body renders one flat `ForEach` over `draft.entries` rather than two loops with the divider hardcoded between them.
- Swipe a row left to hide or show it, with the action revealed behind the card. Horizontal dominant pans only, so the sheet keeps its own downward dismiss.
- The up and down arrows stay. The prototype has no arrow fallback, and a drag is unreachable with VoiceOver and Switch Control, so both live side by side.
- `hide` now lands a row directly under the line rather than at the bottom of the hidden pile, which is what makes a short drag bring it back.
- The row toggle is a blue eye when shown and a grey `eye.slash` when hidden, replacing the checkmark and plus circles.

The drag ghost, the source row dimmed while it is in flight, is driven by `CustomizeDragState` with an activity watchdog rather than by `performDrop` alone. A drag session can end without delivering either a drop or an exit, which strands the dim and the swipe guard. Only `isActive` expires; the dragged id survives, so a watchdog that fires early can never break a live reorder.

All six call sites (three in `TodayView`, two in `TrendsCustomizeSheet`, one in `ReportView`) pick this up unchanged. Full suite green, 0 failures.

### Post-review fixes: planner nutrition, optional mood, insight tap-through (2026-08-08)

Six items from the slice 16 review, plus one new affordance.

**Planner suggestions carried no nutrition.** `FrequencySuggestion` held a name, a count, and a window, so `WeeklyPlannerView` planned a slot with nothing but the word. The slot showed 0 kcal and confirming it wrote a 0 kcal entry into the diary, silently, from a row whose whole premise is "you logged this four of the last five Mondays". `FrequencySuggestion` now carries a `Nutrition` value copied off the most recent logged entry behind the count, and the plan tap passes all seven nutrients through plus `sourceKind: .suggested`, which the row had also never set. Two tests in `PlannerMathTests` pin the carry and pin that a name logged with no figures stays at zero rather than borrowing another day's.

**Mood has an unset state.** `JournalEntry.moodScore` is `Int?` (decision 157). A check-in with only a note or an energy no longer records the neutral midpoint as though the user had picked it. Schema 8 is the lightweight stage for the widened column; export format 3 is the file contract where the key may be absent, with `v2ToV3` as an identity transform, since every v2 file carried a mood. Read sites treat absent as absent: no calendar dot, no tile-strip dot, no clause in the timeline detail, no match for the mood filter. `CheckInDraft.save` drops the `?? 3` and the `TODO(schema)` with it. New tests cover the note-only write and clearing a mood on a second pass.

**The insight callout opens Trends.** Tapping the "in your data" card switches to the Trends cross-domain lens with the pair explorer preset to the two metrics the card names (decision 160). A card showing only the day's facts opens the lens without a preset. The handoff is the existing `AppRouter.openTrends` seam, so it carries two metric ids and no figures. A chevron replaces the "no tap affordance" the card was documented with. It is a tap gesture rather than a `Button` wrapper because the eyebrow carries an explainer button, and a button nested in a button's label never receives its own taps.

Also: `SchemaV8` and the `v7ToV8` stage, `ExportFormat` at format 3 / schema 8.0.0, and the "future format" fixtures moved from 3 to 4 so the refusal test still tests a refusal.

### Today refinements: a pageable callout, a red to green mood ramp, energy as its own card (2026-08-08)

Four changes, spec in `docs/slices/updated-slice-13-today-refinements.md`. No engine is touched and no number changes meaning.

**The across-domains card pages.** It adapted to data and looked like it did not. `InsightCallout` ran all seven locked pairs over the trailing 90 days, `InsightRanking` sorted them by evidence tier then sample size then id, and the card rendered `.first`. None of those move day to day, so steady logging produced the same headline for weeks while the other six splits were computed on every render and thrown away. Dismissal was the only route to a second comparison, which made a weekly silencing control double as navigation. New `InsightCopy.pages` returns every ranked non-dismissed split with its wording resolved, and `InsightCopy.clamped` holds the index after a dismissal so the next-ranked comparison lands where the last one was. Ranking is untouched, so page one is what the card always showed, pinned by a test against `pick`. The pager sits under the two averages rather than on the eyebrow: the card gained a tap-through to Trends in the same week and its trailing `chevron.right` means "open", so paging chevrons beside it would have been two chevrons meaning two things. No swipe gesture, for the same reason. The day's habit marks and context tags stay outside the pager, since they belong to the browsed day and not to the comparison, which also holds the card height steady across pages.

**Mood dots run red to green** (decision 162, amending 159). `MoodScale` was one hue at five brightnesses, chosen explicitly against a red-to-green judgment, and the prototype's `Theme.mood` was one of two tokens deliberately not carried on 2026-08-07. Reversed: hue is now per-step, 0.00 through 0.36, red to dark green. Decision 159 had already shipped 😞 to 😄 on the same 1 to 5 scale wherever mood is entered, so the entry surface was judging while the display surface refused to. Applies to both call sites, the Today tile strip and the month calendar dots and legend; a tile-local ramp would have left two mood colour languages in one app. Brightness is no longer monotonic, because amber is the brightest hue at full saturation, so depth reads from mood 3 upward only. The `mood-scale-legend` explainer is rewritten, and the test that pinned a strictly descending brightness ramp now pins ascending hue, five distinct colours, and depth across the back half.

**Energy is its own card** (decision 163). `energy` had been a real section in the registry all along, but it rendered standalone only when `hero` was hidden, riding as the hero card's footer otherwise. It was the one section whose customize-sheet row could not move it. The footer arm is gone: `HeroCard` loses `showsEnergyFooter` and its divider, `EnergyLineRow` loses `Style` and the whole `.footer` arm, `TodayView` loses the `sections` parameter that guard was the last consumer of. The `energy-line` explainer moves onto the section head, where hero and tiles already keep their controls.

**The energy card carries a bar.** Four monospaced figures in a row is a table. New `EnergyBar`, pure geometry beside the view the way `InsightCopy` sits beside the callout: the track spans the larger of goal and eaten, so an over-goal day cannot render identically to an exactly-on-goal one, and the goal tick draws only once eaten passes it. Burned is not in the bar. The goal already carries the profile's activity multiplier, so a burned segment would draw exactly the double count `EnergyTargets.remaining` refuses to compute. One colour throughout, because an over-goal day is a fact about the day and not a failure of it. No bar without a goal, and none under `hideNutritionNumbers` or `simpleDiaryMode`, where a fill ratio would be a number drawn instead of written.

## Food tab rebuild from the design handover (2026-08-09)

The full front end rework of the Diary tab and every path food takes into a day, built from the 35 screen design handover in `~/Downloads/design_handoff_food_tracker 2/`. The handover is the spec for this work; the board's dark values went into the theme verbatim and the light side is derived, per the established theme contract. Decisions 165 to 174 record the calls that shaped it.

### Backend first

- `FavouriteFood` joins the schema: a starred pin over a history name, upserted by lowercased `nameKey`, values still resolving from the log. SchemaV9 with a lightweight `v8ToV9` stage. Export format 4: the favourites array joins the contract, `v3ToV4` is an identity transform, duplicate `nameKey`s repair on import with the drop counted, and the coverage, counts, and schema pin guard tests all moved with it. Privacy centre counts and both erase paths include the new table.
- `MealTargets` is a new derived service: each meal takes a fixed fraction band of the daily calorie target (breakfast 20 to 30 percent, lunch 28 to 42, snack 12 to 18, dinner 20 to 30), bounds rounded to the nearest 10 kcal, plus the under, on, over status math and the day over state. Never stored, moves when the target moves. `DayNutrition` assembles one day for every diary surface so the card, the banner, the meal cards, and the sheets agree; both have their own test files.
- Theme gains the diary accent set: calorie green, destructive red pair and tint, favourite amber, water bright, warning wash, and the shared summary card and ring geometry.

### The screens

- Diary: date eyebrow with chevrons and a custom month grid date sheet, Diary and Planner behind one segmented control, week day picker with planned dots, the fixed height summary card (ring, kcal left, macro bars), water strip with tappable 250 ml segments, Not tracking and Hide numbers pills, search bar with barcode button, quick add chips, one card per meal with the band status line. Day states: empty (with log today's plan and copy yesterday), over target (red overshoot arc, kcal over centre, red banner naming the worst macro, red meal card), numbers hidden (the meal timeline card inside the same chrome, so nothing resizes), not tracking (dimmed, fully neutral, no over statement anywhere).
- Planner: the dashed translucent twin of the summary card, saved meal chips that drag onto meal slots (tilted drag preview, drop label in the targeted card), per meal plan cards with the usuals frequency recall, clear day, copy day with multi select and replace warning, repeat last week, and its own targets sheet reading planned so far against the target.
- Add food: saved first search where the online button appears from the first keystroke and always sits above local results; online results grouped by chain with Generic last and See all per group; the portion step between any catalog result and the log (presets, quantity stepper, live ring and macro shares, save to my foods on by default, the committed value in the CTA); filter chips for Recent, Frequent, Meals, and Favourites with quantity steppers; the meal combo composer; the custom food form in the dark card layout with banner and field level validation.
- Describe a meal: example prompts, proposal rows, a dedicated edit sheet per proposal (context banner naming the sentence, quantity stepper, ring and shares, keep or discard), and a batch confirm on Log N. Barcode: bracket overlay with a sweeping scan line, detected product bar with the ODbL attribution, not found and camera blocked states, permission requested on first open and re checked on return from Settings.
- Entry management: read only entry details with the favourite pill, a bespoke edit sheet (meal and date cards, quantity stepper for catalog entries that rescales all seven values, macro rows with energy shares, discard confirm), per meal bottom sheets with the three zone band bar, save as meal.
- Quick add: on the Food tab the pill opens straight into the where then how sheet (ADD TO meal tiles seeded by time of day, HOW rows, LOG AGAIN strip); on Today and Workouts the actions grid remains and Food leads to the same level. In Planner mode every path plans instead of logging and the strip reads PLAN AGAIN.
- Toasts (green success, red removal with a working Undo that re logs the snapshot), the confirmation set (remove, delete combo, clear plan, discard edits, stop tracking, batch log), and the targets info sheet with no exercise line.

### The write intent seam

The add flow, portion step, variant sheet, and custom food form all take a `DiaryWriteIntent`: log writes diary entries, plan writes planner slots with the same values. Titles, CTAs, and toasts follow the intent, and the capture flows that only describe eaten food (barcode, describe) drop out of plan mode. This is what lets Plan food open the full search surface instead of a bare name form.

### Found and fixed while verifying

- The lazy stack recycled diary meal cards into planner mode on the segment switch, because both card sets shared ids; ids are now prefixed per mode.
- The ring caption read "kcal left" on days with no target; it now reads "kcal eaten", and the same caption serves neutral days.
- The over banner said "Protein are"; the verb now follows the macro's name.
- The quick add panel sized every level to the tallest one, leaving the actions grid in dead space once the food level grew; it now sizes to the active level.

### Verification

App builds clean. The full unit suite completed end to end four times this session, finishing at 1,014 tests, 0 failures, including the new MealTargets, FavouriteFood, and DayNutrition files and the updated export guards. Screens verified in the simulator against the board with seeded data: the diary in every day state including over target and not tracking, the planner with drag chips and usuals, the typing and portion step search states, and the direct quick add. UI tests were not run this session.

## Workouts tab rebuild from the design handover (2026-08-10)

The full rework of the Workouts tab and every path training takes into a day, built from the 48 screen design handover in `~/Downloads/Dark-mode iOS nutrition app/` (the folder name says nutrition; it is the Workouts board). The handover is the spec for this work. Decisions 175 to 190 record the calls that shaped it. Every reference to syncing in the handover was ignored, because nothing in this app syncs.

### Backend first (schema V10, export format 5)

- `ExerciseSet` gains `setType`, `segmentIndex`, `distanceMetres`, and `tutSeconds`. `resolvedType` folds the legacy `isWarmup` flag, so rows written before this counted correctly the moment the new engines read them. SchemaV10 with a lightweight stage; on-disk V9 to V10 migration tests added.
- Two new flat tables. `SessionExerciseMeta` holds the superset group, rest override, and note for one exercise inside one session. `ExerciseMeta` holds custom exercises and the user's annotations on catalog slugs (muscles worked, equipment, what it tracks, default sets, form note). Both key off a flat `sessionLocalId` or slug rather than a relationship, and the store owns their cleanup (decision 180).
- Store write paths: typed `addSet` and `updateSet` with `setType` and `isWarmup` mirrored whichever the caller passes, a to-failure set with no rating writing RPE 10 (decision 177), drop segments sharing their chain head's `setIndex`, session meta rows created on first touch and pruned when emptied, `deleteSession` taking its meta rows with it, and custom exercises upserting by minted slug (decision 181). Privacy centre counts and per-domain erase cover both new tables under training; master erase wipes them.
- `ExerciseResolution` merges the user layer over the bundled and imported catalog: overrides applied, customs appended, and one combined muscles-by-slug lookup the counting engines share.
- Derived layer, all computed and nothing stored: `volumeLoad` is set-type aware behind a `countWarmups` flag, with timed and distance rows never joining the kilogram times reps sum (decision 179). The new `HardSets` engine counts fractionally per muscle (decision 175). `MuscleBalance` switches from volume share to hard-set share everywhere it appears (decision 178). `KeyLifts` ranks the last 90 days by trained days with one best estimate point per day. `WeeklyVolume` gains the 4 week rolling average the volume chart draws. Estimated 1RM is guarded to rep-bearing types, and every `isWarmup` reader moved to `resolvedType`.
- Export format 5: the four new set columns ride as optionals so old files still decode, both meta tables get DTOs and payload arrays, `v4ToV5` is an identity transform, and import builders derive `setType` from the legacy warm-up flag on old files. The validator rejects duplicate identities in the new arrays, drops orphaned session meta, keeps first on duplicate session-exercise pairs and slugs, and clamps the new numeric fields.
- New settings in `AppConfiguration`: `countWarmupsInVolume` (off), `autoStartRestEnabled` (on), `trackRPEEnabled` (on), `plateCalculatorEnabled` (on), `availablePlatesKg` (the standard metric rack), and `workoutLiveMode` (list, remembered). All in `resetToFactoryDefaults` and the pinning test.
- The muscle balance explainer is rewritten against the hard-set formula, and a hard-sets explainer added carrying the reference-only band wording (decision 186).

### The screens

- Workouts home: the day card resolving seven states (in progress, open elsewhere, trained, planned, future preview, rest day, empty), quick stat tiles, the week strip with planned versus done marks and the week's volume, activities, recent sessions. Four plain toolbar icons for search, progress, plans, and the library (decision 182) with a settings gear in the leading slot (decision 190).
- Live logging in two modes, user switchable and remembered. List mode is every exercise as a card with a set time strip, the SET / KG / REPS / RPE header, one row per set, and the warm-up and add set actions. Focus mode is one set at a time with big steppers, the form and muscles guide card, the last time baseline as a dated fact, and a CTA that names what follows inside a superset. Both write through the same store operations; focus mode is a presentation, not a second path.
- Two independent clocks. The header pill is total elapsed session time; the green set stopwatch times the current set and records time under tension when the set is logged. `SetClock` anchors on a wall clock through `AppConfiguration`, so backgrounding and relaunch cost nothing.
- The seven set types with their own value columns and counting rules, the generated warm-up ramp at 40 / 60 / 75 / 90 percent of the working weight with reps tapering 10, 8, 5, 3, live supersets with round stopwatch and rest after the round's last member, the shared numeric keypad with nudges and next field, the worded RPE sheet that is always skippable, the rest timer, the plate calculator drawn to scale, and live exercise reorder through `moveSessionExercise`.
- Plans: the plans home, a schedule-first plan detail reading the week before any editing, the plan editor, and the weekly planner with its adherence ring. Session chips drag onto day rows (decision 188).
- Progress: the volume load chart with its rolling average and record dots, muscle balance on the figure pair, hard sets per muscle against the 8 to 20 reference band, key lifts over 90 days, the record feed, and a per-lift detail with three readings of the same sets (best estimate, top set, day volume) plus a full history filterable by all, records, or this year.
- Library and detail: the merged catalog browsable without a search, muscle chips, recently logged leading with each lift's best estimate, exercise detail over the merged row, the custom exercise form, and the muscles-worked assignment sheet for rows the catalog left unmapped.
- Sheets and system: the tab's start training quick add with repeat-a-session prescriptions (decision 183), add exercise, log activity, log a past workout, the exercise action menu, workout search across sessions, activities, exercises, and plans, the marked date picker (decision 189), and the shared workout toasts (success, a record that taps through to its lift, and removal holding an Undo).
- The missing data rule holds across the tab: a section with no data is omitted rather than shown empty, and one card states what is missing and offers the action that fixes it. Exercise detail without a demo or muscle data and focus mode without a guide card are the worked examples.

### Board gaps closed

Four screens the first pass skipped, built after the rest had landed.

- The weekly planner takes drag and drop. Session chips drag onto day rows, the targeted row becomes a dashed drop zone naming the weekday, and a drop writes the plan day's weekday (decision 188). Dropping onto an occupied weekday confirms first, and the sitting day goes back to unscheduled keeping its exercises. A context menu unpins.
- The Workouts date picker becomes a marked month grid: a filled green dot on trained days, a blue ring on planned days, month arrows, a legend, Today and Done. It sits behind a new optional `monthMarks` parameter on the shared `DayNavigator`, so the six other call sites keep the system picker (decision 189). The trained-over-planned rule is `DayPickerMarks.merged`, a pure function with its own tests.
- A gear in the Workouts leading toolbar slot pushes the existing `WorkoutExperienceView`, so Customize Workouts is reachable from the tab as well as from Profile (decision 190).
- The plan day editor carries the board's reorder footnote, stating that order applies to future sessions only and never to sessions already logged.

### Found and fixed while verifying

- `.dropDestination` does not fire on `List` rows. The planner's first drag implementation silently did nothing until `WeeklyPlannerView` was rewritten from a `List` into the tab's ScrollView over cards, matching the working precedent in the Diary planner. The rewrite also brings the screen in line with the board's card layout, which the `List` version never matched.
- The marked picker printed the dayKey's two digit suffix, so every day of the month carried a leading zero.

### Verification

Build clean. The unit suite completes end to end at 1,068 tests, 0 failures. Screens verified in the simulator against the board with seeded data, including the planner drag, the occupied-day replace confirmation with the adherence ring recomputing behind it, and the marked picker's trained and planned dots. UI tests were not run.

## Trends tab rebuild from the design handover (2026-08-11)

Bundle at `~/Downloads/design_handoff_trends/`, plus a second bundle that reinstated the sleep section. Decisions 218 to 225 (recorded 2026-08-13 in the docs wrap); rejections dated 2026-08-11. Committed 2026-08-12 as the trends redesign commit, with the schema and export growth in the backend support commit.

- Four lenses of collapsible sections replace the nine lens picker: Overview, Training, Body & Fuel, Patterns. A collapsed header keeps a headline value; collapse state never persists (decision 218).
- New derived engines: ScorecardPeriods, GlanceMetrics, TrendsFindings, EnergyBalance, WeightMath, MoodBands, SplitGate, HabitWeekly, and for the sleep section SleepTiming and SleepBalance.
- SchemaV11: the bedtime window on UserProfile (target minutes from midnight plus tolerance), lightweight stage; export format 6 (decision 224). trends.glanceOrder and trends.glanceHidden replace the retired nine lens and overview card keys.
- TrendsView rewritten as the four lens shell; eight new Trends files; the nine lens files and the customize sheet deleted with surviving math hoisted into TrendsSupportMath; the router's TrendsLens cut to four; fourteen explainer entries added.
- Review rulings the same day, all recorded: direction colouring restored in full with descriptive copy only (219); the predictions kept with the general calculation note (220); the compare toggle removed since the redesigned cards never drew the ghost (221); the scorecard at four trailing week columns with the six months block (222); the top right sub menu with generate report and talk to coach (225); sleep reinstated as a Patterns section with its three cards (223). The one time migration banner and its config key are not in the final tree (ruled dropped 2026-08-13).
- Standing deviations: life event shifts do not feed the patterns badge; behaviour detail omits the board's two trailing navigation rows; the volume card's spans anchor at today; charts are SwiftUI recreations per the README's own note, not pixel ports.
- Suite 1,111 green at the ruling close; simulator verified on the seeded fixture. Working notes at docs/claude-work-trends-frontend.md and docs/claude-work-trends-sleep-backend.md.

## Journal tab rebuild from the design handover (2026-08-11)

Bundle at `~/Downloads/handoff 2/`. Decisions 226 to 229; rejections dated 2026-08-11. Committed 2026-08-12 as the journal redesign commit. The board's coach screens wait for the coach overhaul; the Journal home's coach entry is removed.

- Backend first: logSleep grows optional bedtime and wake wall clocks (decision 228): a bed clock past the wake clock anchors the evening before, hours derive from the pair, timezoneId stamped, one time alone stores neither, a pass without times clears timing. Bedtime spread's manual source gate dropped, so manual nights feed SleepTiming, SleepBalance and the Today hero with no view change. Nine new or flipped tests; the full field round trip carries a timed manual row.
- JournalView rewritten: Today card with the numeral over five step bars, week strip, habit chips, weekly pulse card, On this day card, Recent, an empty state naming its unlocks, Context tags and Life events as manage rows.
- CheckInSheet merged per the board: ledger tiles, faces and segments with end labels, the bed and wake sleep card on the new shared SleepCheckInState, grouped habit chips, context, note, all on the shared CheckInDraft.
- History gains All / Low / Stressed / Noted pills and the merged month calendar card (decision 229): UICalendarView wrapped, mood as the decoration dot, future days greyed, a day tap opening the day story; the standalone MonthCalendarView deleted, the toolbar cut to two icons.
- The day story rewritten as the board's day detail: scores card with the home's fixed metric colours and the logged time caption, the day on the shared TimelineBuilder rail with a slept trailing, tag chips, note. Edit is the only way into the check-in; History rows and the home's Recent rows both open it.
- Habits grouped by intent with ISO week marks and the cap tick, run summary, hidden group, context menu reorder; habit insights with the run hero, the did / didn't / no log grid, and all four engine comparisons with counts; context tags as In use / Never used / Yours with day counts; life events as timeline cards with the 28 day EventSplit delta, copy matching the engine rather than the board's 30.
- No habit delete: the board's Delete becomes hide via pause (decision 226). Ask something else dropped (decision 227).
- Suites 1,143 and 1,149 green across the two passes; simulator verified dark and light on the seeded fixture. Learned: a Button label with no fill inside a List row needs .contentShape(Rectangle()) or only the glyphs take the tap.

## Shared chrome and navigation redesign (2026-08-12)

The chrome pieces the redesign passes share, cut as their own commit ahead of the per tab commits.

- NavHeader replaces ScreenHeader as the one header anatomy; DayPickerSheet hoisted into Shared as the app wide day picker.
- The workout toast centre hoisted into Shared (WorkoutToasts); SleepCheckInState and its fields land in Shared/SleepCheckIn.swift for both check-in sheets.
- The live set timer survives relaunch through two persisted AppConfiguration keys (live.setTimerStartedAt, live.setTimerExerciseId).
- TimelineBuilder carries kind to domain, label, and the module gate on TimelineEvent.Kind, so Today's rail and the Journal day detail cannot drift.
- ScoreRow and OptionalScoreRow restyled to Theme with optional end labels; explainer registry growth.
- App icon added. .claude/launch.json deleted from the tree and gitignored, closing the 2026-08-09 question.

## Today and Diary refinements (2026-08-12)

- The quick add rewritten as a row list over the domains with per tab entry levels: four levels (actions, meals, training, activity), the Workouts tab opening straight into the training level, back landing on the tab's own opening level, a planned day naming its plan. The inline activity form goes; the full activity sheet is the one editor.
- Diary's own date picker sheet deleted for the shared DayPickerSheet.
- DayCheckInSheet migrated onto the shared sleep check-in state, removing its direct model mutation (raw picker dates on the wrong day, no timezone, bypassed store save) and its note wiping.
- DayTimelineView and TodayView trimmed onto the lifted TimelineBuilder metadata.

## Profile refinements (2026-08-12)

- WorkoutExperienceView rebuilt as the full Customize Workouts screen: bar weight stepper with the editable plate rack, default rest, auto start rest, track RPE, plate calculator, count warm-ups in volume, haptics and sounds, advanced load metrics, with footers stating each consequence. Reached from the Workouts gear and from Profile.
- GoalEditSheet gains an optional end day through the shared DayPickerSheet.

## Monthly report rebuild from the Thrive prototype (2026-08-12)

Spec: docs/slices/updated-slice-14-monthly-report.md. Three phases, one working run.

### Phase 1 - facts, deltas, prose engine

- `ReportDelta` ported: formatted month-over-month movements with a direction verdict, nil against a missing month, "no change" when the formatted difference rounds to zero. Every delta comes off the previous month through the same builder (decision 192).
- The pinned direction table (`ReportDirection`, decision 191): one place decides which way is green. Calories judged by proximity to the plan, weight by goal type behind a 1 kg guard, stress and sleep debt flipped, water and spreads never judged.
- `ReportProse`: the writing engine. Hero KPIs (first-available-wins, capped at five), the vs-last-month block with bars normalised to one shared scale, coverage strip counts, cover summary lines, the month reading composed last (training size, strength, weight), per-section takeaways and callouts, the candidates system partitioned into the three descriptive verdict panels (decision 197), habit week grids, month-over-month habit lines behind the 10-engaged-day guard, habit impact lines through the same correlation engine as Journal (decision 199), day-log rows with week averages, six-month rows with nets, and the weight pace line (decision 198).
- Weight joined the section registry (decision 193), tenth section; the previously unrendered weight facts get a chart, trailing 7-day average, goal dash and rate line.
- `ReportInput` replaces the positional builder signature; `ReportPagePlan` pins the day-log chunk size (26) and the page-count arithmetic.

### Phase 2 - print redesign

- `ReportKit`: the print design system (decision 194). Fixed light Primer-derived palette with Pell blue as accent, KPI cards, count cards, callouts, insight cards, coverage strip, diverging change bars, vs block, sparkline, one table style with fractional column widths, and the page chrome carrying the running header, the tone footer on every sheet, the generated stamp and "Page N of M".
- `ReportPages` rebuilt: a real cover (title, hero row, month reading, coverage strip, vs table, contents with true page numbers), one page per section including weight, day-log pages chunked at 26 rows with shaded week-average rows, and the closing "About these numbers" glossary assembled from the ExplainerRegistry entries the included sections reference (decision 195).
- `ReportPDF` renders at scale 2 so charts stop rasterising soft.

### Phase 3 - interactive redesign

- `ReportView` rebuilt from a stock List into the card language of the redesigned tabs: month picker card, section chips that scroll-animate to their card, hero KPI grid with coloured deltas, coverage and vs cards, fixed-height scrub readouts above every chart ("not logged" for empty bars, layout never jumps), expandable six-month rows with sparklines and month cells (0.18 s ease), verdict panels, and a compact day log.
- The report sections sheet gains a live availability caption per row ("14 training days", "no weigh-ins") through the shared customize sheet's existing caption seam.
- PDF export keeps one buffer start to end and gains the spinner-repaint yield. Screen state is rebuilt on arrival and on month pick, so scrubbing never re-runs the fact build.

### Verification

Build clean. Full unit suite 1,173 tests, 0 failures, including the new delta, direction-table, page-plan, prose and pace-line tests. Verified in the simulator with the long-history persona: hero deltas coloured by the table (weight grey under a maintain goal), the month reading, habit grids, verdict panels, six-month expansion with the trajectory sentence, and the exported 12-page PDF with the fixed light palette, diverging change bars, contents page numbers and the tone footer naming the guiding arm.

### Follow-up batch (2026-08-12, after review on device)

- The vs block's current value takes the row's verdict colour, on screen and in the PDF's "This month" column (decision 200).
- The six-month weight net follows the 0.5 kg down green / 1 kg up red band instead of the goal type (decision 201).
- "About these numbers" moved to page 2 of the PDF; the contents list leads with it (decision 202).
- Six-month sparklines removed on both surfaces; the month cell blocks are the presentation, always visible, with the trajectory sentence under each row and zero slopes reading "no change per month" (decision 203).
- Patterns' cross-domain splits redesigned: title with a signed sign-coloured group difference, labelled value rows with proportional bars; same layout in print (decision 204).
- Sleep gains the timed-night lines: weekday and weekend median bed and wake times, and the bedtime-window on-time count, screen and PDF.
- Habit consistency by week gains its W1 to W5 header row on screen.
- The mood distribution rows no longer wrap their score label vertically; labels pinned to a fixed single-line frame on screen and print.
- Every em dash stripped from report copy; flat estimated-1RM lifts read "held" in grey.
- Full unit suite 1,175 tests, 0 failures; reverified on device including the exported PDF.

### Day log rework (2026-08-12, decision 205)

- The day log is now the full day-by-day overview: per-meal calories with full meal names, a foods line naming what was eaten per meal, training and activity minutes, mood, energy and stress, sleep with bed and wake times and quality, tags, habits and the day's note. Week averages are superseded by a month-averages block compared against the pooled prior six months, coloured by the direction table.
- New builder inputs feed it: month food entries and journal notes, the kind-name map, and a one-span fetch of the prior six months' summaries and food entries.
- Print paginates at seven day blocks per page with the averages on a closing page; the page plan, contents and tests moved with it.
- Full unit suite 1,176 tests, 0 failures; verified on device.
- The day log's averages block reworked (decision 206): one column per month across the past six plus the report month, month-on-month readable left to right, with the change column still comparing this month against the pooled prior six. Screen shows month cells in the six-month style; the PDF table gains a column per month.
- The day log rendered as the supplied mock (decision 207): a DAY / CALORIES / WORKOUTS / MOOD table with proportional calorie bars, movement minutes, and MoodScale-coloured mood chips; a tapped day expands to meal rows (food names beside each meal's calories), detail chips, tags, habits and the note. Print carries the same block always expanded at six days per page.
- The day log's month-by-month averages block removed from screen and PDF (decision 208): the report ends on the last logged day. The prior-six-month span fetch leaves the builder with it. Full unit suite 1,175 tests, 0 failures; verified on device.

## Slice 20 - Onboarding (2026-08-12)

- The first run flow, nine screens from the prototype's sixteen (spec: docs/slices/slice-20-onboarding.md): intro hero (welcome, privacy card with the catalog disclosure, about, study note, policy link), module cards, about you (goal, empty-until-provided stats, optional weigh-in and goal weight, food style), plan (Mifflin-St Jeor hero with explainer, macro fine-tune, gentle targets, the coach line), behaviours (creation-first with name-only template chips), home (arrangement preview over the real layout keys plus appearance), all three reminders under one consent, Apple Health connect scoped to the enabled modules' source keys, and the all set hero.
- The whole feature is one file, Features/Onboarding/OnboardingView.swift: flow engine, screens, styling, copy (decision 217). Three touch points outside it: the hasOnboarded key in AppConfiguration (master erase resets it, decision 214), the PellApp wrap (recovery still wins on store failure; scene-phase passes wait for the stamp), and the Profile replay row (modules and home in a sheet, non-destructive).
- Flow engine as data: steps declare inclusion, conditional steps sit after their decider, the progress bar counts live (7 numbered steps at most, 5 at least). OnboardingFlowTests pins the sequence tables, position math, the self-excluded fallback, home landing parity over the shared codec, and the flag lifecycle.
- DEBUG launch arg `-skip-onboarding` boots straight to the shell for dev and UI-test runs.
- Decisions 209 to 217 recorded; not-ported list in 03-rejected (demo sandbox, presets, coach step, recomp, weight-free toggle, per-domain permission scoping).
- Full unit suite 1,168 tests, 0 failures. Verified live end to end on the simulator: fresh install into the flow, commit-on-continue gating, the computed 2,811 kcal plan from the typed stats, reminder consent chain to the system prompt, HealthKit connect with the backfill landing burned energy on Today, the stamp surviving relaunch, and the replay tour from Profile.
- Review fixes (2026-08-12, same day): the select cards reserve their checkmark slot so selection no longer shifts the text (the jiggle was the appearing tick, not the border); the home step's sections list now mirrors Today's render conditions (no Coach row while the coach is off, no Goals row without an authored goal) and the Quick stats caption names only the tiles that survive the module gate, so step 1's choices read back on step 2; the goal is now optional (no card pre-selected, tap again to clear, footer naming Profile) and the plan's gentle targets fold behind an optional expander; onboarding carries its own preferredColorScheme so the setup steps follow the appearance choice live and the replay sheet no longer ignores it, while the two heroes stay committed to dark. One new test (sections filter); 1,169 green, all four verified live.
- Second review pass (2026-08-12, same day): the intro drops "Who built it" (the beta note's first line carries the research context); "No goal for now" becomes a first-class default-selected card so skipping the goal is visible rather than a footnote; the gentle targets become per-target toggle rows (value dimmed while off, stepper revealed while on) replacing the folded expander, with the footer stating that reference defaults apply either way, per the no-backend ruling. 1,169 tests green, verified live.
- No-scroll restructure (2026-08-12, ruled same day): fourteen screens from nine so no page scrolls at default type on the current iPhone class. Intro splits into welcome and about-this-beta; about you splits into goal, details, and weight; food logging is its own module-gated step again; the plan keeps the kcal hero and fine-tune while the targets rows become an always-included step carrying the coach line; home compacts to three arrange rows opening Today's own customize sheets plus the appearance cards. Per-step commits on Continue, all idempotent. Flow tests updated to the new tables; 1,169 green; all fourteen screens verified unscrolled live.

## Slice 21 - Zero value capture fix (2026-08-17)

Bug report: foods logged through the barcode scanner and through Describe a meal sometimes landed at 0 calories. Four separate mechanisms produced it, and one fed the others. Spec: docs/slices/slice-21-zero-value-capture.md; decisions 230 to 235.

- Open Food Facts parser reads the `_prepared_100g` keys when a row declares no energy as sold, and takes all seven nutrients from one basis (decision 231). Cordials, drink powders and cup soups declare only the made up figures, so the plain keys were reading zero out of complete rows. Verified against the live v3 envelope: barcode 50457243 returns 55 nutriment keys including `energy-kcal_prepared_100g`, and read 0 before this.
- `CatalogFood` carries `hasNutritionData` and `asPrepared`. A row declaring no energy on either basis is absence; a row declaring zero stays a measurement, so water and diet drinks are unaffected.
- The scanner gives a no-values product its own state naming that the database holds no nutrition values, with one button into the custom food form prefilled with the scanned name (decision 232). Previously a known barcode with no nutrition facts returned 200 and presented as a real food at zero. Open Food Facts holds 37,514 UK products in that state.
- The portion step blocks the Add in counted mode when the row's values are absent, labels prepared values on screen, and records the basis in the entry's serving line (decisions 230, 231).
- Describe a meal: name-only cards read "No values" instead of "0 kcal", and counted mode gates Log until every card carries values, matching the rule the custom food form already enforced (decision 230). This was the one counted mode path that could mint a zero calorie food.
- The meal text parser ranks valueless history rows below the bundled catalog (decision 233). History outranked the catalog unconditionally, so one name-only entry set that food to zero for every later parse, permanently. This is what tied the two features together and made the bug read as intermittent.
- Absent sodium falls back to salt divided by 2.5 (decision 234); `CatalogFood.unitGrams` gives every read the 100 g fallback for a nil or zero serving (decision 235).
- Existing zero calorie entries stay in the store. They are logged records and the provenance rule is mark, never delete; the fix stops them winning matches.

## Evaluation instrumentation (2026-08-17)

Three test files added for the dissertation's evaluation chapter. No application code touched except three copy fixes the tone guard found. Ten tests, all green.

- `ToneGuardTests.swift`: the tone and content rules as an executable check. Scans string literals rather than source text, so an identifier can never trip a rule; the scanner handles comments, escapes, interpolation bodies and triple quoted literals, and has its own test for the awkward cases. Five framing classes (motivational, prescriptive, body composition, social comparison, evaluative) plus the typography rules. 2,401 prose literals across 250 first-party files. Two directory exemptions, Catalog and DevTools, each named with its reason in the test; one standing string exception, the mood band footnote, with a test that fails if it ever stops matching so a dead exemption cannot accumulate.
- Three em dash violations found at introduction and fixed: two set prompts in FocusModeView ("Log set, then ..." now), one mood bands footnote in TrendsPatterns (colon). Eight rounds of manual copy audit had each closed clean over the same strings. Standing count is now zero across every class.
- `DerivedLatencySweepTests.swift`: the 90-day benchmark parameterised over six windows, 5 to 730 days, same dense day shape so only n moves. Results: 3.73, 16.29, 48.41, 93.57, 189.12, 386.66 ms. Per-day cost flat at 518 to 543 microseconds from 30 days up. The 730 over 90 ratio is 7.99 against a linear prediction of 8.11, so the O(n) claim is measured rather than asserted. The 500 ms budget holds at two years of dense data, not just at the 90 days it was set for.
- `CoCoverageSweepTests.swift`: co-coverage across three densities by four windows, twelve cells, deterministic and reproducibility pinned by test. Density is a composite over the food, check-in and training dials, named as a composite in the test because the generator has no single density knob. Two-domain coverage sits at 70 to 76 percent in every cell; four-domain coverage falls from 56.7 to 14.4 percent at 90 days and from 53.2 to 6.6 percent at a year. Eleven of twelve cells clear the five-day correlation floor on all seven locked pairs, the exception being medium density at 30 days with one pair short.
- One test asserts the known limitation rather than hiding it: a single logged food row plus a check-in reads as a two-domain day. Co-coverage counts joint presence, not joint quality, and the test fails if that is ever quietly changed.

## Slice 21 - Pack size portion default (2026-08-17)

Follow up bug report: a scanned single pack still defaulted to 100 g, so a 25 g packet of crisps logged at four times its calories. Two shapes, both in the barcode arm, fixed in the parser alone; decision 236.

- Rows with no declared serving offered only the 100 g and Custom presets, and 100 g won.
- Rows where the contributor typed the per 100 g basis in as the serving. Verified against the live database: the Mars bar row declares serving_quantity 100 on a 51 g pack.
- The parser now requests quantity, product_quantity and product_quantity_unit beside the serving fields. A declared serving larger than the pack cannot fit the pack and is dropped. A single serve pack (150 g or under) stands in for a missing serving as "1 pack (25 g)", so the portion step defaults to the pack, offers half of it, and keeps 100 g one tap away.
- Multipacks ("6 x 25 g") and share bags never stand in: neither total is one portion. Prepared rows are exempt from both rules, since a made up serving legitimately outweighs the dry pack.
- Five new parser tests: the junk serving yields to the pack, the single serve stand in on the string quantity shape, share bag and multipack refusal, a serving within the pack stays, prepared rows ignore the pack. All 17 green on the suite's first pass; the standing second pass cache flake on lookupParsesAndMapsTheTwoTraps is unchanged. Tone guard and no network guard green.
- Verified live on the simulator: barcode 5000159407236 resolves over the real API and the portion step opens on 1 pack (51 g), 230 kcal, with 100 g still selectable.
