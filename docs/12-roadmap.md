# Implementation roadmap

How the app gets built, in order. This expands the [build order](../SPEC.md#build-order) into tasks. Story numbers refer to [User stories](11-user-stories.md).

## How the order was chosen

1. **Riskiest unknowns first.** Google sign-in in the installed iPhone app is tested in the first days of M1, and Web Push on the Edge runtime at the start of M5, before anything is built on either.
2. **Logging before everything else.** It is the top priority, and nothing else has data without it.
3. **Real data as early as possible.** Using the app from the end of M2 means the progress screens in M4 are built and judged against real training history.
4. **Retention takes calendar time.** A week-4 number needs at least 4 weeks of real use, so the beta starts before the last two milestones rather than after them.

## Overview

| Milestone | Stories | Release stage reached |
|-----------|---------|-----------------------|
| **Before M1** Setup | | |
| **M1** Foundation | US-01 to 04 | |
| **M2** Logging and offline | US-05 to 18, US-49 to 51 | Personal use |
| **M3** Templates and history | US-19 to 27 | |
| **M4** Progress | US-28 to 35, US-52 to 55 | Closed beta |
| **M5** Reminders | US-36 to 40 | |
| **M6** Launch-ready | US-41 to 45 | Public launch |
| **M7** Subscriptions | US-56 | Paid plan |

There is no deadline, so the order matters and dates don't. The work is done by the product owner and Claude.

### Dependencies

```
Setup ─► M1 Foundation ─► M2 Logging and offline ─┬─► M3 Templates and history ─┬─► M5 Reminders ─► M6 Launch-ready ─► M7 Subscriptions
                                                  └─► M4 Progress ──────────────┘
```

M5 comes after M3 because a reminder can open a template, and after M4 so that the beta is already running.

### Release stages

| Stage | Starts after | Who | What it answers |
|-------|--------------|-----|-----------------|
| **Personal use** | M2 | You, on your own phone | Is logging really fast in an actual gym, with bad signal? |
| **Closed beta** | M4 | A small group of athletes (Google allows up to 100 test users before the app is published) | Do people keep logging, and do they understand their progress? |
| **Public launch** | M6 | Everyone | Week-4 retention baseline from the beta already exists |

## Before M1: setup

These need your accounts, so they can't be done from code alone.

- [ ] Create a Supabase project in an EU region
- [ ] Create a Google Cloud project with an OAuth client and consent screen, in testing mode
- [ ] Create a Firebase project with Hosting enabled (HTTPS is required for service workers and push)
- [ ] Have a real iPhone (iOS 16.4 or later) and a real Android phone available for testing
- [ ] Settle the [open questions](../SPEC.md#open-questions) marked as needed by M1

## M1 Foundation

**Goal:** a deployed, installable, empty app that the right person can sign in to.

- [ ] Create the Vite, React and TypeScript project with Tailwind and React Router
- [ ] Set up Vitest and Playwright with a phone viewport
- [ ] Configure `firebase.json` (single-page rewrite, no caching of the service worker file) and the GitHub Action for deploys and previews, then deploy
- [ ] Add Google sign-in through Supabase
- [ ] Add the web manifest, icons and service worker precache (`injectManifest`)
- [ ] **Decision point:** test sign-in in the installed app on a real iPhone. If unreliable, add the 6-digit email code here
- [ ] Write the initial migration: all tables, indexes, RLS policies and grants from the [Data model](05-data-model.md)
- [ ] Write an automated test that two users can't access each other's rows
- [ ] Create the `profiles` row on first launch, with the timezone refreshed on each app open
- [ ] Add `react-i18next` with Estonian and English files, and `Intl` formatting helpers
- [ ] Add `src/lib/units.ts`, offering kg and km only
- [ ] Build the app shell: bottom navigation, safe areas, light and dark theme, basic sign-out

**Done when:** two test users can't read each other's data, and Google sign-in works in the installed app on a real iPhone and Android phone.

## M2 Logging and offline

**Goal:** the core promise, working in a gym with no signal.

The offline layer comes first, because every screen after it reads and writes through it.

- [ ] Set up TanStack Query with the cache persisted to IndexedDB
- [ ] Build the write queue: client-generated ids, upserts, a single mutation scope so writes stay in order
- [ ] Write the data access layer, with an explicit athlete id on every query ([Teams](10-teams.md))
- [ ] Build first run: the 18+ confirmation, then sport presets
- [ ] Build activity tiles, ordering and search
- [ ] Build the prefilled log sheet for all three kinds of activity
- [ ] Add steppers, the numeric keyboard, and "Lisa seeria"
- [ ] Add the date control (Täna, Eile, date picker), using the local date
- [ ] Add optimistic save with the undo toast
- [ ] Add new activity creation with kind guessing in both languages
- [ ] Add the optional note, the optional effort rating and `entry_ms` timing
- [ ] Add the body weight tile
- [ ] Finish the sync rules: flushing on open and on `online`, the "N ootel" indicator, activity name collisions, expired tokens offline
- [ ] Call `navigator.storage.persist()` and handle app updates on next launch
- [ ] Add the install prompt on Android and the guide on iPhone
- [ ] Write a Playwright test of the repeat flow and of airplane mode

**Done when:** a repeat log takes 5 s or less on a real phone, and entries made in airplane mode sync exactly once and in order.

**Then:** start using the app for your own training.

## M3 Templates and history

**Goal:** a whole workout repeats in one action.

History comes first, because templates can be created from a past day.

- [ ] Build history grouped by day
- [ ] Add editing and deleting entries, and archiving activities
- [ ] Add saving and using templates (data layer, including offline)
- [ ] Build the template sheet: prefilled rows, expanding, unticking
- [ ] Add **Salvesta kõik** as one atomic upsert, and batch undo
- [ ] Keep an open sheet saved on the device
- [ ] Write the repeated-workout detection as a pure function, with unit tests
- [ ] Add the offer to the save toast, and remember dismissals
- [ ] Add **Korda** and **Salvesta malliks** to history
- [ ] Add managing templates in settings (create, rename, delete)

**Done when:** a template with 5 activities saves in 8 s or less, offline too, and undo removes the whole batch.

## M4 Progress

**Goal:** the app answers "am I getting better?" for each activity. This is the largest piece of work in the product.

The calculations come first and are finished before any screen is built, because every screen depends on them being right.

- [ ] Write `src/lib/progress.ts` and its tests: values per kind, trends, thresholds, pace at similar distance
- [ ] Add records, weekly streak and the 12-week consistency data
- [ ] Add save-toast messages (record, comparison, week count)
- [ ] Write the headline sentences in both languages, including steady, declining and pace wording
- [ ] Build the overview: streak, consistency strip, activity cards with sparklines
- [ ] Build activity detail with lazily loaded Recharts: period toggle, records, recent entries, volume chart
- [ ] Add the too-little-data states
- [ ] Add weekly training volume
- [ ] Add goals: performance, dated and consistency, and goal messages in the save toast
- [ ] Add the single access check for advanced progress, allowing everything for now
- [ ] Check the results against your own real data from M2 onward

**Done when:** every rule in [Progress](04-progress.md) is covered by a unit test.

**Then:** choose the real product name, which the beta's Google sign-in screen will show, start the closed beta, and start tracking the first cohort with the [retention queries](03-retention.md).

## M5 Reminders

**Goal:** reminders that arrive at the right time and don't become noise.

- [ ] **Decision point:** send one real push from an Edge Function to an installed iPhone and an Android phone. If the Web Push library doesn't run on the Deno runtime, choose another before continuing
- [ ] Generate VAPID keys and store them in Edge Function secrets
- [ ] Add `push` and `notificationclick` handling to the service worker
- [ ] Build the permission card after the third entry, and the iPhone install step
- [ ] Save device subscriptions, and unsubscribe on sign-out
- [ ] Build reminder settings, with days prefilled from training history
- [ ] Write the `send-reminders` selection: weekday, local time, already logged, already sent
- [ ] Add back-off, translated and specific message text, and removal of dead subscriptions
- [ ] Schedule it with `pg_cron`, with the secret in Vault
- [ ] Add opening the Logi tab or a template from a notification
- [ ] Test across a timezone and a daylight saving change

**Done when:** a reminder arrives on the installed app on iPhone and Android at the chosen local time, and is skipped if the user has already logged that day.

## M6 Launch-ready

**Goal:** everything needed to open the app to the public.

- [ ] Add CSV export
- [ ] Add account deletion through the RPC
- [ ] Finish sign-out: warning about unsent changes, clearing local data
- [ ] Review all translations in both languages
- [ ] Do a performance pass: Lighthouse mobile, 1.5 s to ready, bundle size
- [ ] Check the [mobile requirements](06-frontend.md) on real phones
- [ ] Get the legal review of handling training and body weight data
- [ ] Decide how people from the free period will be treated when paid plans start
- [ ] Publish a privacy policy page, which Google requires for a published consent screen and is needed for handling this data anyway
- [ ] Move the Google consent screen from testing to production
- [ ] Review beta results against the [targets](01-product.md) and fix what they reveal

**Done when:** the targets are met and the whole app works in both languages.

## M7 Subscriptions

**Goal:** the paid plan, soon after launch and once retention has been shown. Tasks get detailed when the open questions on price and provider are settled ([Business](13-business.md)).

- [ ] Set up the company
- [ ] Choose the payment provider, price and billing periods
- [ ] Publish terms of service
- [ ] Connect the single access check to real subscription status
- [ ] Build subscribing, cancelling and the upgrade prompts
- [ ] Apply the decision on people from the free period

## After launch

Decided from usage and demand rather than planned in advance:

- Improvements aimed at wherever the beta and first public cohorts drop off
- Strava and watch imports (US-57), with the data model already prepared
- British and American units ([Languages and units](09-languages-units.md))
- Team and coach access ([Teams](10-teams.md))

## Main risks

| Risk | Impact | Response | When it's known |
|------|--------|----------|-----------------|
| Google sign-in is unreliable in the installed iPhone app | People can't get in | Add email code as a second method | Early M1 |
| Offline queue loses or duplicates entries | Trust is lost, and it's hard to repair | Client-generated ids, strict ordering, airplane-mode tests | M2 |
| Web Push library doesn't run on the Deno runtime | Reminders delayed | Decision point at the start of M5, choose another library | Start of M5 |
| Many iPhone users never install the app | They get no reminders | Install guide after the first entry, and measure how many subscribe | Beta |
| Progress headlines mislead (noisy or unfair comparisons) | The main reason to keep logging is weakened | Rules covered by tests, checked against real data | M4 |
| People stop logging anyway | The central product risk | Beta before launch, retention measured by cohort, changes guided by the data | Beta, 4 weeks in |
| Runners with watches won't enter workouts by hand | Half of the focus audience may leave before imports exist | Watch this group in the beta, and move imports earlier if needed | Beta |
| Goals, body weight and effort slow down logging or clutter it | The top priority suffers | All are optional and add no taps to a repeat, and the speed targets are checked in M2 | M2 |
| The company isn't ready when payments are | Revenue is delayed | Set up in M7 at the latest, and sooner if the privacy policy requires it | M6 |
