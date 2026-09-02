# 03 - Rejected

1. 5-tab plus FAB navigation. Rejected during prototyping in favour of the six-tab bar. Reason: a floating action button privileges one logging action over four equal domains and hides the sixth surface.
2. Client-server architecture. Reason: health data leaving the device weakens the privacy claim under evaluation, complicates the ethics route, and adds accounts, auth, and infrastructure with no research value.
3. HTTP or localhost debug endpoints. Reason: even DEBUG-only server plumbing breaks the flat "no networking anywhere" guarantee that a simple guard test can enforce. Debugging needs are met locally: the store designed as a formal API, unit tests as the probe, direct SQLite inspection of the simulator store, and a DEBUG-only developer console with fixtures (schema doc, slice 04).
4. Native five-plus-More tab navigation. Reason: the locked navigation is six visible tabs. The automatic More screen would demote Trends and Profile to a second-class surface.


## Slice 08 rejections

- Background delivery (HKObserverQuery): entitlement, silent-launch, and test-surface cost with nothing in the evaluation needing data before the app opens. Foreground plus manual covers the honest case. Revisit only if a future study design needs passive capture.
- Direct API adapters (Strava, Garmin) and file import (GPX/FIT): OAuth tokens on device and a new outbound surface carrying health data requests would change the privacy stance and the ethics route. HealthKit is the hub; every major fitness app already writes to it. Reopening this is a recorded privacy amendment first (decision 51). "Works with Strava" means Strava data arriving through Apple Health with its origin named in sourceDetail, not an account connection.
- Sleep stages as metric registry extractors: consumer stage detection is low-validity (Chinoy 2021), so correlating on deep or REM minutes would put a weak signal into a cross-domain lens and hand the evaluation an easy target. Display only.
- Naps as stored records: no nap surface exists in the locked scope; the assembly counts them in the SyncLog (skippedAsNap) so nothing is silently dropped, and the count says whether a nap surface is ever worth a spec discussion.
- Conflict review UI (the Fellegi-Sunter clerical-review arm): a review queue entity plus surface is new scope a backend slice should not smuggle in. The middle band resolves to no-merge with both records live and an ambiguous count in the SyncLog; if the counts show the band matters in practice, a review surface becomes a spec discussion.
- A low-coverage suppression floor on rollup displays: hiding thin data is a judgment call that edges toward evaluative framing; every total travels with its day-count denominator instead, which is pure description.

## Slice 09 rejections

- Merge restore: combining two divergent histories needs a per-entity conflict policy no test can pin down, and a half-merged store is the worst possible artifact for the evaluation. Replace is provable with one byte-identical test; the file's replace confirmation names the row counts.
- Backup encryption (optional passphrase): no server means no key recovery, so a forgotten passphrase is a destroyed backup. The privacy disclosure names the file as plain unencrypted JSON instead; honesty over a lock with no locksmith.
- Export from the recovery screen: a store that will not open cannot be read by the codec, so an export button there cannot honestly promise anything. The pre-open backups already hold the last three file sets; 01-schema wording corrected to match.
- SchemaV6 now (lastAutoExportAt, lastRestoreAt on SyncState): inert fields ride a schema rev that is already being cut (decisions 33 and 36 precedent), and slice 09 otherwise needs no rev. They ride the next real one; the weekly auto-export scheduler owns that decision.

## Slice 11 capture deferrals (2026-07-31)

- SavedFood entity (a stored favourites table for catalog foods): deferred, not settled. Frequent foods derive from history instead: a logged row carrying catalogId and a positive gramsLogged reconstructs its per-100 values and reopens the quantity sheet at a fresh quantity. Reason: the entity would cost a schema rev after V6, export codec coverage, and a privacy center erase row, all for a view the logged history already derives. If derived reuse proves insufficient in evaluation, the entity becomes a spec discussion.

## Slice 12 omissions (2026-08-02)

- Distance on the activity log sheet: ActivityEntry.distanceKm exists since slice 05 and the store updates it, but the sheet does not expose it. Reason: the locked feature list names duration, exertion, and the editable MET estimate; distance feeds no computed number yet (the MET formula is duration-based), and adding the field later is one form row.
- An online exercise text search beyond wger exercises (equipment browsing, workout log sync, anything account-shaped): rejected. Reason: the amendment covers reference catalog queries only, and an account surface would change the privacy story.
- A record-celebrating summary header ("New PR!") and any advisory annotation on the progress charts (target lines, deload flags): rejected at the copy audit. Reason: the tone rules; a record is a dated fact and the charts stay descriptive.

- ExerciseDB as the catalog source (2026-08-02): rejected. The exercisedb.io dataset is a paid product at 299 dollars for the mobile tier, and the open source repo licences only its server code under AGPL while saying nothing about the data and shipping no dump. Reason: bundling records with no written licence into the deliverable is a risk the ethics file should not carry, and free-exercise-db is public domain.
- Rewriting imported instructions into the curated voice (2026-08-02): rejected. 57 imported rows use second person procedural phrasing where the curated 40 use a plain imperative. Reason: editing 57 blocks of third party movement instructions risks changing what the instruction actually tells someone to do, which is a worse outcome than an inconsistent voice. Recorded in the copy audit instead.
- Importing the stretching, plyometrics and cardio categories (198 records): rejected for this pass. Reason: the catalog feeds the set logger, which records weight and reps. Those movements are logged through the activity picker and METTable. A separate mechanism for them is open, not specced.

- A 3D muscle avatar (SceneKit or RealityKit with a rigged mesh): rejected, not deferred. Reason: it needs a licensed anatomical 3D model, which is the same provenance trap that moved the catalog off wger and off ExerciseDB, and it adds a framework, app size, a performance cost, and a rotatable view that is much harder to describe to VoiceOver than a static diagram. The 2D figure stays 2D. Improving its anatomy is issue #97 on the time permitting milestone.
- Drawing finer muscle heads (upper versus lower chest, front versus lateral versus rear delts, lats versus rhomboids, long versus lateral triceps) on the current data: rejected. Reason: the catalog records one primary muscle from a 17 value vocabulary, so most of those regions could never fill. A figure implying detail the data does not carry is false precision. Finer regions need richer data first, tracked in issue #97.

## Slice 13 omissions (2026-08-02)

Prototype home-screen features consciously not carried into Today. Each was in the demo's `DashboardView` and each is either rejected outright or owned by a later slice.

- Readiness dial and readiness hero lens: already rejected (no-auto-inference). The sleep lens is duration, quality, average and shortfall, with no state read off them.
- Discovery nudge card with cooldowns and per-module dismissal: stays on the excluded-pending-a-decision list. Borderline against no-auto-triggering.
- First-run starter hint card: not built. The empty states already name what is absent on every surface, so a separate prompting card adds only nagging.
- Streak chip, longest-streak figure, badge awarding on the home path: gamification slice, behind the flag (decision 112). The demo awarded badges from inside a view-update observer, which is a store write during rendering.
- Weekly challenge on the hero: same, and the demo computed it on every recompute while never reading it.
- Swipe-anywhere day navigation: rejected in favour of the hero pager owning the horizontal swipe (decision 117). The demo had both and the day gesture won, leaving its hero pager drivable only by chevrons.
- Nutrient detail sheet behind the energy row: not built. The Diary summary card carries the seven nutrients one tab away. Revisit only if the evaluation asks for it.
- HealthKit pull-to-refresh and step mirroring driven from Today: rejected. The sync engine owns refresh triggers; mirroring that only happens when a user visits one screen makes the data depend on navigation.
- A crediting toggle for workout calories ("Add workout calories to daily goal"): rejected. The target already carries the activity multiplier, so the honest arithmetic has no toggle. The demo shipped one that changed labels only (decision 113).
- Module gating of Today sections and tiles: not rejected, deferred. App modules are Profile slice scope. The layout codec already preserves ids it was not offered, which is exactly what gating will need.

## Slice 14 rejections (2026-08-04)

Trends-adjacent features consciously not built, each considered during the 2026-08-03 planning and the coach catalogue review.

- Wellbeing composite (one score summarising mood, sleep, energy): rejected outright. It is auto-inference of a wellbeing state, the exact thing the locked rules exist to prevent, and the prototype's composite fed its readiness framing. The mood lens shows the three series and a plain spread number instead.
- The prototype's indexed side-by-side composite on the progress lens (several metrics indexed to 100 on one chart): rejected. Indexing unrelated domains onto one axis invites reading them as one score; the honest version of indexing survives only in the two-lift strength compare, where both series are the same quantity.
- "Best days" framing and the top-quartile split variant: rejected. Two split rules on one screen answer the same question two ways, and "best" is a verdict. The median split is the one rule everywhere (decision 126), and the ranked surface is named "Largest splits" for what it counts, not what it praises.
- Coach snooze control: removed at the catalogue review. A snooze is deferred nagging with a timer; the two-dismissal mute and the tier gate are the honest controls, and both already exist in the design.
- Coach digest (a periodic summary message): removed at the catalogue review. The monthly report is the digest, it is user-invoked, and a pushed summary would drift toward engagement mechanics the restraint rules exist to prevent.
- Confidence bands on pattern messages and charts: removed at the catalogue review. Bands imply an inferential model the median split and shuffle count deliberately avoid claiming; the shuffle count in plain words is the qualifier that survives scrutiny.
- A second sheet-level edit path for sessions and weight inside Trends: never proposed seriously, recorded because the prototype did it. Edits route through the owning tabs' sheets and store operations, so a date change cascades per decision 31 instead of orphaning sets.

## Slice 15 rejections (2026-08-05)

Profile-adjacent features consciously not built, each considered during the 2026-08-05 planning against the recorded prototype survey.

- Per-domain insights consent toggles (the prototype's third axis between module hiding and erasure, restricting what the engines may read while the data stays stored): not carried. Three overlapping consent surfaces multiply evaluation arms without the study asking for it; modules hide, the privacy centre erases, and that two-concept model is the one the study explains. Revisit only if the ethics route wants a processing-restriction control.
- Voice cues in the guided workout: deferred, not built. No consumer exists, and building one means audio session design, interruption behaviour, and spoken copy the tone audit would have to cover. Haptics and sounds shipped instead, each with a named consumer.
- Goal progress and projection copy ("42% there", "about 9 weeks left at this rate", "Trending away from your goal"): rejected outright. Projection is prescriptive framing and "trending away" is a verdict; the targets screen shows values and recommendations only. The recomp goal type and the body-fat target stepper attached to it in the prototype stay excluded under the body-composition rule.
- The prototype's module change log (a capped audit trail of module flips with attribution, riding the export): not carried. It is a research instrument the rebuild's evaluation design has not asked for; the modules themselves are presentation state, not health data.
- Deleting exported backup files inside master erase: rejected as a default. Erase-and-restore-later is a legitimate sequence and the files are the only recovery copies; complete removal exists as its own separately warned action instead (decision 138).

## Slice 16 follow-up rejections (2026-08-08)

- Protein and behaviours quick-stat tiles: removed from the Today grid (decision 158). Both restate a figure that already has a home. Protein sits on the Diary summary card beside carbs and fat, where the three read as one macro split rather than one number out of context. Behaviours are set in the check-in sheet and reported back as day facts under the insight callout. The grid is the surface where a duplicated tile costs a real row above the fold, so the six that carry a figure nothing else shows are the ones that stay. Both remain in the feature scope as concepts, not as tiles; nothing was deleted from the derived layer, and `DaySummary` still computes `protein` and `habitsLogged` for the surfaces that do use them.
- Rewriting historical moodScore midpoints on the schema 8 migration: rejected. Rows written before the mood became optional hold a 3 that may be a real 3 or the old default standing in for an unset one, and nothing distinguishes them. Blanking them all would destroy real check-ins; keeping them all is at worst the status quo. The migration is lightweight and touches no row (decision 157).

## Food tab rebuild rejections (2026-08-09)

Board screens and details from the food design handover deliberately not built, each with the reason.

- Skeleton loading states per list screen: not built. Every diary surface reads the local store synchronously, so there is no loading moment to skeleton; a shimmer over data that is already there fabricates latency. The one genuinely async surface, online catalog search, shows its own loading state on the search button.
- The global amber offline banner ("You're offline. Entries are saved on this phone and will sync later"): not built. Its copy promises syncing that does not exist (decision 168), and a general banner needs an always on network monitor in a local only app whose single connectivity dependent surface already states its own offline condition at point of use.
- The describe a meal "working" state (three dot progress, "Reading your description…", skeleton proposal rows): not built. The parse is deterministic, on device, and instant; a progress state would stage latency that is not happening, which is the same honesty problem as the skeletons.
- Dimming the source chip while a saved meal drags on the planner: not built. SwiftUI's draggable exposes no drag began state, so the dim needs a UIKit drag delegate rework the polish does not justify. The tilted floating copy and the drop label in the targeted card carry the affordance.
- A bespoke 272pt confirmation alert component: not built. The board's alert is the native alert's anatomy; the native .alert with the specimen copy is the platform way and stays consistent with the rest of the app.

## Workouts rebuild rejections (2026-08-10)

Board screens and details from the Workouts design handover deliberately not built, each with the reason.

- The top-right icon pill on the Workouts home: not built (decision 182). It is a custom control standing in for a nav menu the platform already provides, and everything it opens is an ordinary push with a back chevron. Four native toolbar items give the same four routes and keep Dynamic Type, tap target, and VoiceOver behaviour we would otherwise rebuild by hand.
- Light / Moderate / Vigorous exertion on Log activity: not built (decision 190). The stored scale is 1 to 10, clamped by the import validator and printed as "effort N of 10" on the Today timeline. Three bands would either change what an already logged number means or paint a lossy display over it, and the user ruled against touching the backend for it. Standing board deviation.
- Every "syncing later" reference in the handover: struck on sight. Nothing in this app syncs, and copy promising it would misdescribe the privacy model, the same call recorded as decision 168 for the food board.
- Skeleton loading states per list screen: not built (decision 187). Every workouts surface reads the local store synchronously; there is no loading moment to skeleton, and a shimmer over data that is already on screen stages latency that is not happening.
- Dimming the source chip while a session chip drags on the planner: not built. SwiftUI's `draggable` exposes no drag-began state, so the dim needs a UIKit drag delegate rework the polish does not justify. This is the same call already recorded for the food planner on 2026-08-09. The tilted floating copy and the dashed drop zone naming the target weekday carry the affordance.
- The board's custom reorder mode (Cancel / Reorder / Done, tilted lifted row, dashed insertion gap): not built as drawn. The plan editor's native edit mode with grabbers does the same job through the platform's own reorder, and the board's footnote copy shipped with it so the consequence is still stated. A hand-built reorder would be a second drag system in a tab that already has one.
- A per-week schedule entity to make planner drops apply to one week only: rejected (decision 188). A plan day carries an optional weekday and the week pin chooses the governing plan; that is the whole model. Adding a per-week override table to serve one gesture would fork scheduling into two sources that could disagree, and the honest version of the gesture is the one that says it edits the plan.
- Finer muscle heads behind the board's twelve balance rows and seventeen figure shapes: not built (decision 176). The catalog carries one primary muscle per exercise, so the extra rows would be drawn from data that does not exist. The figure subdivides visually over the ten keys without the counting layer knowing.

## Trends rebuild rejections (2026-08-11)

- The board's 13a compare with previous design (deltas beside headlines, "was" lines, a dashed prior series): not built. The control was removed by ruling instead (decision 221); comparisons ride inside the cards.
- The one time migration banner: dropped (confirmed 2026-08-13), its config key with it. A permanent explainer for a one time layout change outlives its usefulness inside a week.
- The behaviour detail's two trailing navigation rows (compare with another metric, see the days you marked it): not built.
- Life event shifts feeding the patterns highlights badge: not built; the badge counts qualifying correlation splits and training findings.
- Pixel porting the board's SVG charts: not done, per the README's own recreate in the app's environment note. The charts are SwiftUI paths on the existing chart layer.

## Journal rebuild rejections (2026-08-11)

- Journal replacing More in the tab bar: rejected. It conflicts the locked four tab navigation and would leave Trends and Profile homeless.
- The board's "held back from patterns" pill: not built. Tags never exclude a day from correlations; they are their own binary metric and streak skip days, and only sick, injury, travel and not tracking silence a coach run.
- The board's removal of weekly pulse, the day story, and On this day: not followed; all three stay.
- Habit delete: never (decision 226). The board's Delete becomes hide via the existing pause.
- The Ask something else free text field: dropped (decision 227). A deterministic engine cannot answer free text.
- The hand built month calendar grid: rejected on sight for the wrapped UICalendarView (decision 229).

## Roadmap cuts (2026-08-11)

- Gamification (the streaks surface, tiered badges, weekly challenge, celebration summary, achievements hub): scrapped. Deadline triage: peripheral to the research question, and the remaining build time goes to the coach arm, the wrap work, and the evaluation. The flag and its dormant consumers (the hero streak chip behind HeroStreakRule) stay in the tree, default off.
- Widgets (Today widget, configurable small widget, lock screen accessories, rest timer Live Activity): scrapped, the same triage.
- Section tutorials and the versioned what's new screen (cut 2026-08-13): onboarding's replayable tour covers orientation, and a per section tutorial system plus a release notes screen serve a shipping cadence this project does not have.

## Monthly report rebuild rejections (2026-08-12)

- The Thrive report's readiness surfaces (cover badge, bands page, averages and deltas): not ported. Readiness is an inferred wellbeing state and stays excluded.
- The Thrive report's body-composition lines (body fat, estimated lean mass): not ported. Body-composition framing; plain weight carries the section.
- The Thrive report's Load & recovery page (Banister fitness-fatigue chart, ACWR line, monotony and strain table): not ported. In Pell these exist only as descriptive Trends-lens numbers behind `advancedLoadMetricsEnabled`; the report has no load section.
- HRV, resting heart rate and VO2 report content: not ported. Pell tracks none of these.
- The Thrive report's imperative verdict titles and advisory clauses ("Keep doing", "Could improve"): not ported (decision 197). The generation stays; the instruction voice goes.
- The Thrive builder screen with its Generate gate: not ported (decision 196).
- The prototype's excluded-set section persistence: not ported; the shared LayoutCodec already owns order and visibility.

## Slice 20 onboarding rejections (2026-08-12)

- The prototype's onboarding demo sandbox (DemoScreenHost, in-memory persona seed, sample data chips, try-it flows): not ported (decision 210). Demo and seed data are DEBUG only by the locked rule, and a Release-build sandbox would need a CLAUDE.md amendment the static cards do not.
- The prototype's module preset row (Essentials / Training / Everything): not ported (decision 210). Four domain toggles need no preset, and with modules ahead of goal in the combined flow a goal recommended preset has nothing to key on.
- The prototype's coach setup screen (style arms, tone, body data consent, coach enablement): not ported (decision 211). Pell's coach tiers are gated by in-app declarations per decision 73; onboarding carries one descriptive line naming that the coach exists and is off.
- The prototype's recomp goal card and bodyFatGoalPct: not ported. Body composition framing, already excluded.
- The prototype's weight-free mode toggle: not ported (decision 212). Pell needs no mode; the onboarding weight fields are simply optional and the targets math already has the neutral fallback.
- Per-domain HealthKit permission scoping on the onboarding connect: not built. The adapter's requestAuthorization is one app-wide request; scoping applies at the profile source keys instead, so a food-only user still sees the full permission sheet but only the food domains are pointed at Apple Health. Scoping the request itself would need a per-domain adapter API for one screen.
- A behaviours starter toggle bank: rejected in favour of creation-first with name-only templates (decision 216).

## 2026-08-17 - zero value capture fix (slice 21)

- Importing the Open Food Facts `_prepared_` values unlabelled: rejected (decision 231). It states a portion basis the user cannot see, and the logged entry would keep no record of which basis it holds. Ignoring those rows entirely was also rejected: it throws away complete data for a whole product class.
- Letting a scanned no-data product log with a stated zero: rejected (decision 232). Naming the absence on screen and still writing a counted zero calorie entry is the bug wearing a label.
- Excluding valueless history rows from the meal parser altogether: rejected (decision 233). A simple diary mode user's own logged names would stop being recognised. Filtering inside `FoodSearch.distinctByName` was rejected for the same reason at wider blast radius: it would hide those rows from Add food and meal combos, where reusing them is correct.
- Enforcing the counted mode zero rule inside `PellStore.logFood`: rejected (decision 230). The store cannot see `AppConfiguration`, and simple diary mode writes zeros legitimately, so the check belongs in the calling surfaces. Passing the diary mode into the store would put a presentation flag in the write path.
- Cleaning up existing zero calorie entries: rejected. They are logged records and the provenance rule is mark, never delete. The fix stops them winning matches; it leaves them in the diary.
- Applying `hasNutritionData` to the USDA search arm: not built. Out of scope for a barcode and meal text fix. The portion step's counted mode guard reads the flag rather than the calorie total, so a USDA row that ever arrives without energy would need the same treatment before it is caught.
