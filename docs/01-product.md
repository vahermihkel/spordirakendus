# Product

## What matters, in order

| # | Priority | What it means for the build |
|---|----------|-----------------------------|
| 1 | **Logging takes seconds** | Repeating a usual workout is 2 taps. Every other feature gives way to this. |
| 2 | **People keep logging** | This is the main risk. Every save gives something back, and missing a week is not punished. |
| 3 | **Progress is obvious** | This is the largest piece of work. The app answers "am I getting better?" in one sentence per activity, before showing any chart. |

### Targets

| Metric | Target | How it is measured |
|--------|--------|--------------------|
| Repeat an earlier workout | ≤ 5 s, 2 taps | `entries.entry_ms`, median |
| Repeat with one value changed | ≤ 8 s | `entries.entry_ms`, median |
| Repeat a whole workout from a template | ≤ 8 s, 2 taps | `entries.entry_ms`, median per template save |
| Log a brand-new activity | ≤ 20 s | Manual test on a phone |
| App open to ready to log | < 1.5 s on a mid-range phone | Lighthouse mobile, cached PWA shell |
| Users still logging in week 4 | ≥ 30 % (initial guess, revise after the first cohort) | SQL query, [Retention](03-retention.md) |

## Users and scope

**For:** adults (18 and over) in Estonia first who want to track their training and development. The focus is on **gym and strength** and on **running and other endurance sports**. Time-only sports such as yoga or team sports can be logged and count towards streaks, but get no special design effort.

**In the first version**
- Google sign-in, with a session that stays logged in
- Quick logging for three kinds of activity (strength, distance, duration)
- Workout templates, which the app offers when it sees a workout being repeated
- Full offline use through a service worker, syncing later
- Push notification reminders
- Estonian and English interface
- Progress overview and per-activity detail
- Goals: performance targets, targets with a date, and consistency goals
- Body weight, effort rating and weekly training volume
- History with edit and delete
- CSV export and account deletion
- Installable PWA

**Prepared for, but not built:** subscriptions ([Business](13-business.md)), Strava and watch imports, British and American units ([Languages and units](09-languages-units.md)) and teams ([Teams](10-teams.md)).

**Not in the first version:** social features, coaching or training plans, native apps, body measurements other than weight, sign-in with anything other than Google.

## Core model decisions

**One entry = one activity on one day.** There is no "start workout / finish workout" flow, because it adds steps and leaves sessions half-open. Someone doing five exercises at the gym makes five quick entries, and history groups them by day. A template ([Logging](02-logging.md)) saves those five entries in one action, so a repeated workout needs no session concept either.

**Three activity kinds cover all sports.** The kind decides the input fields and the progress metric.

| Kind | Examples | Input | Headline progress metric |
|------|----------|-------|--------------------------|
| `strength` | Bench press, pull-ups | Sets of reps × kg | Estimated 1RM, or max reps for bodyweight |
| `distance` | Running, cycling, swimming | Distance + time | Pace at a similar distance |
| `duration` | Yoga, football, climbing (basic support) | Time | Minutes per week |

Body weight isn't a sport, but it uses the same tile and entry mechanism through a fourth kind, `body_weight`, so it needs no separate logging flow.
