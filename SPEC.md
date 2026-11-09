# Spordirakendus: product and build spec

A mobile-first web app where athletes log their training and see whether they are improving.
Stack: React + Supabase, installable on the phone as a PWA, usable offline, with push reminders. Languages: Estonian and English.
A commercial product with a free tier and a subscription, aimed at the Estonian market first. "Spordirakendus" is a working title.

## Documents

| Document | Covers |
|----------|--------|
| [Product](docs/01-product.md) | Priorities, targets, scope and the core model |
| [Logging](docs/02-logging.md) | The logging flow and workout templates |
| [Retention](docs/03-retention.md) | What keeps people logging, and how it is measured |
| [Progress](docs/04-progress.md) | Progress screens and calculation rules |
| [Data model](docs/05-data-model.md) | Supabase schema, access rules, auth and privacy |
| [Frontend](docs/06-frontend.md) | Tech choices, screens and mobile requirements |
| [Offline](docs/07-offline.md) | Service worker, sync rules and install prompt |
| [Reminders](docs/08-reminders.md) | Push notifications: permission, sending and limits |
| [Languages and units](docs/09-languages-units.md) | Estonian and English, and later British and American units |
| [Teams](docs/10-teams.md) | Rules that keep team support possible later |
| [User stories](docs/11-user-stories.md) | User stories with acceptance criteria, by milestone |
| [Roadmap](docs/12-roadmap.md) | Implementation tasks in order, release stages, dependencies and risks |
| [Business](docs/13-business.md) | Free and paid plans, payments and legal prerequisites |

## Build order

| Milestone | Delivers | Done when |
|-----------|----------|-----------|
| **M1 Foundation** | Vite project, Supabase migrations and RLS, Google sign-in, translation and units layers, app shell, service worker precache | Two test users can't read each other's data, and Google sign-in works in the installed app on a real iPhone and Android phone |
| **M2 Logging and offline** | Tiles, prefilled sheet, steppers, undo, 18+ confirmation, first-run presets, effort rating, body weight, write queue, install guide | Repeat log in ≤ 5 s on a real phone, and entries made in airplane mode sync exactly once and in order |
| **M3 Templates and history** | History with edit, Korda, templates, the repeated-workout offer | A template with 5 activities saves in ≤ 8 s, offline too, and undo removes the whole batch |
| **M4 Progress** | `progress.ts` with tests, save-toast messages, overview, detail charts, streak, goals, training volume, free/paid gate (everything unlocked for now) | Every rule in [Progress](docs/04-progress.md) is covered by a unit test |
| **M5 Reminders** | Web Push on the Edge runtime, permission flow, settings, scheduled sending, back-off | A reminder arrives on the installed app on iPhone and Android at the chosen local time, and is skipped if the user has already logged that day |
| **M6 Launch-ready** | CSV export, account deletion, complete translations, legal pages, performance pass | Targets in [Product](docs/01-product.md) are met and the whole app works in both languages |
| **M7 Subscriptions** (soon after launch) | Payments and the paid plan, once retention is proven | Decided when the open questions on payments are settled |
| **Later** | Strava and watch imports, British and American units, teams | Decided from user demand |

## Decisions made

| Topic | Decision |
|-------|----------|
| Nature of the product | Commercial |
| Who builds it | The product owner with Claude, with no deadline |
| Hosting | Firebase Hosting, for the static app only. Data, sign-in and reminders stay on Supabase |
| Market | Estonia first, English as the second language |
| Business model | Free tier and subscription. Only advanced progress is paid |
| Payments | Go live soon after public launch, not in the first release |
| Company | To be set up before payments |
| Focus athletes | Gym and strength, running and endurance. Time-only sports get basic support |
| Recording a workout | Separate quick entries, with no start or finish step |
| Imports | Manual entry at launch, Strava and watch imports afterwards, data model ready for them |
| Sign-in | Google only. A 6-digit email code is added only if the iPhone test in M1 fails |
| Minimum age | 18 |
| Main strength number | Estimated 1RM |
| Progress also includes | Goals (performance, dated and consistency), body weight, effort rating, training volume |
| Effort rating | Optional, in the log sheet |
| Body weight | Entered through a tile on the Logi tab |

## Open questions

These are undecided. Where the docs need a working value, it is marked here as a placeholder.

| Question | Needed by |
|----------|-----------|
| Effort scale. 1–10 (RPE) is a placeholder in the schema | M2 |
| Does the body weight tile always stay visible, or move by recent use like other tiles? | M2 |
| Does logging body weight count as training for the streak and reminders? Placeholder: no | M2 |
| Is a strength goal such as "100 kg" met by actually lifting it, or by the estimated 1RM? | M4 |
| How "on track" is judged for a goal with a date | M4 |
| Do warm-up sets count towards training volume? | M4 |
| What effort rating is used for, beyond being shown on the entry | M4 |
| Exactly where the free and paid line falls (see [Business](docs/13-business.md)) | M4 |
| The week-4 retention target. 30 % is my guess | Closed beta |
| The real product name | Closed beta |
| Does the privacy policy need the company to exist, so that it has to be set up before public launch? | M6 |
| Legal review of handling body weight and training data as health data | M6 |
| Subscription price and billing periods | M7 |
| Payment provider | M7 |
| How people who used the app during the free period are treated when paid plans start | M6, since anything said at launch is a promise |
| Does "British or American versions" mean units (assumed so far), or also separate English text? | Before that work starts |
| Would coaches only read athletes' data, or also enter it? | Before team work starts |
| Detailed numbers I proposed and you haven't reviewed: stepper sizes, 6 tiles, ±25 % pace range, the template offer rule, reminder back-off | When each is built |
