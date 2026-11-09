# User stories

Grouped by the milestones in the [build order](../SPEC.md#build-order). Details behind each story are in the linked documents. Story numbers are fixed once given, so later additions appear out of sequence within a milestone.

**Who the stories are about**

| Role | Description |
|------|-------------|
| **Athlete** | Anyone tracking their own training: gym, running, cycling, swimming, team sports and so on |
| **New athlete** | An athlete in their first days with the app, before any history exists |
| **Product owner** | The person deciding what to improve, based on usage |
| **Coach** | A future role, only prepared for and not built |

## M1 Foundation

### US-01 Sign in with Google
**As a** new athlete, **I want** to sign in with my Google account, **so that** I can start without creating a password.

- Google is offered on the sign-in screen and returns me to the app signed in.
- It works in the browser and in the installed app, on both iPhone and Android.
- If it proves unreliable in the installed iPhone app, a 6-digit email code is available as a second method.

See [Data model](05-data-model.md).

### US-02 Stay signed in
**As an** athlete, **I want** to stay signed in between uses, **so that** logging never starts with a login screen.

- Reopening the app goes straight to the Logi tab.
- An expired session while offline doesn't sign me out.

### US-03 Keep my data private
**As an** athlete, **I want** only me to be able to see my training data, **so that** I can trust the app with it.

- Another signed-in user can't read, change or delete my rows in any table.
- An entry can only point at my own activities and templates.
- Data is hosted in an EU region.

### US-04 Use the app in my language
**As an** athlete, **I want** the app in Estonian or English, **so that** I can use it comfortably.

- The first launch uses Estonian on an Estonian device and English otherwise.
- I can change the language in settings, and the choice is kept across devices.
- Numbers and dates follow the chosen language, such as a decimal comma in Estonian.

See [Languages and units](09-languages-units.md).

## M2 Logging and offline

### US-05 Get started with my sports
**As a** new athlete, **I want** to tap the sports I do from a list, **so that** my first log screen isn't empty.

- A preset list is shown on first run, in my language.
- The sports I choose appear as activity tiles.

### US-06 See my usual activities right away
**As an** athlete, **I want** the app to open on my most recent activities, **so that** I can start logging immediately.

- The Logi tab is the start screen, with no dashboard before it.
- Up to 6 tiles are shown, most recently used first, each with its last value.
- Other activities can be found through search.
- The app is ready to log within 1.5 s on a mid-range phone.

### US-07 Repeat my last workout in two taps
**As an** athlete, **I want** to log the same thing as last time with two taps, **so that** logging takes only seconds.

- Tapping a tile opens a sheet prefilled with that activity's last entry.
- Save is active immediately, and the date defaults to today.
- The median time for a repeat is 5 s or less.
- Saving has no confirmation step or spinner.

See [Logging](02-logging.md).

### US-08 Change a value quickly
**As an** athlete, **I want** to adjust values with one thumb, **so that** a small change doesn't slow me down.

- Steppers change values by ±1 rep, ±2.5 kg, ±0.1 km and ±1 min.
- Tapping a value opens the numeric keyboard.
- Tap targets are at least 48 px, and the main action is in the bottom third of the screen.
- The median time with one changed value is 8 s or less.

### US-09 Log strength training
**As an** athlete doing strength training, **I want** to enter sets of reps and weight, **so that** my strength progress can be tracked.

- Each set has reps and kg.
- "Lisa seeria" adds a set copied from the previous one.
- Bodyweight exercises can be logged with 0 kg.

### US-10 Log distance and time-based training
**As an** athlete doing endurance or time-based sports, **I want** the right fields for my sport, **so that** I only enter what matters.

- Distance activities ask for distance and time.
- Duration activities ask for time only.

### US-11 Log a past day
**As an** athlete, **I want** to log a workout for an earlier day, **so that** I can catch up when I forgot.

- One tap switches the date to Eile (yesterday).
- Any other date can be picked with a date picker.
- The date saved is my local date, even around midnight.

### US-12 Undo a mistake
**As an** athlete, **I want** to undo a save, **so that** I don't need to go looking for an entry made by accident.

- A toast offers **Võta tagasi** for 5 seconds after saving.
- Undo removes the entry completely.

### US-13 Add a note
**As an** athlete, **I want** to add an optional note, **so that** I can remember how a workout went.

- The note field is collapsed by default and adds no step to a normal save.
- A note can be up to 500 characters.

### US-14 Add a new activity
**As an** athlete, **I want** to add an activity in a few seconds, **so that** I can log something I haven't done before.

- Only a name and kind are needed.
- The kind is suggested from the name, in Estonian or English.
- A new activity can be logged within 20 s.
- Two of my activities can't have the same name.

### US-15 Open the app without signal
**As an** athlete, **I want** the app to open and work with no connection, **so that** a basement gym doesn't stop me.

- After the first sign-in, the app opens offline.
- History and progress show my data from the device.
- A new app version takes over on the next launch, never while I'm logging.

See [Offline](07-offline.md).

### US-16 Log offline and sync later
**As an** athlete, **I want** what I log offline to upload by itself, **so that** I don't have to think about connectivity.

- Logging, editing, deleting and creating activities all work offline.
- Queued changes upload in order when the app opens or the connection returns.
- A change retried after a failed upload never creates a duplicate.
- An activity created offline with the same name as one from another device merges with the existing one.

### US-17 See what hasn't synced yet
**As an** athlete, **I want** to see when something is waiting to upload, **so that** I know my data is safe.

- "N ootel" is shown only while something is unsent.
- The indicator disappears after everything has synced.

### US-18 Install the app on my phone
**As an** athlete, **I want** the app on my home screen, **so that** it's one tap away.

- After my first saved entry, the app suggests installing it.
- Android uses the system install prompt.
- iPhone shows a short illustrated guide to Add to Home Screen.

### US-49 Confirm my age
**As a** new athlete, **I want** a single quick age confirmation, **so that** I can start while the app stays limited to adults.

- First run asks me to confirm I am 18 or over, before the sport presets.
- It is asked once.

### US-50 Rate how hard a workout felt
**As an** athlete, **I want** to optionally record how hard a workout felt, **so that** I can see more than just the numbers.

- The log sheet has one row of tappable numbers, which starts empty.
- Ignoring it adds no taps, so a repeat stays at 2 taps.
- The rating is shown with the entry in history.

### US-51 Track my body weight
**As an** athlete, **I want** to log my weight as quickly as a workout, **so that** I can follow its trend.

- Body weight has a tile on the Logi tab, prefilled with the last value.
- Its trend appears on the Areng tab, stated without calling a change good or bad.
- It never produces a "record".

## M3 Templates and history

### US-19 Repeat a whole workout from a template
**As an** athlete with a regular routine, **I want** to save a whole workout in one action, **so that** I don't log each exercise separately.

- Templates are shown above the tiles, most recently used first.
- Opening a template lists all its activities, each prefilled with its last entry.
- **Salvesta kõik** creates one entry per ticked activity, all together or not at all.
- It takes 2 taps and a median of 8 s or less, and it works offline.

See [Logging](02-logging.md).

### US-20 Adjust a template for today
**As an** athlete, **I want** to change or skip exercises in today's workout, **so that** a template fits what I actually did.

- Tapping a row expands it for editing.
- Unticking a row skips it today and leaves the template unchanged.
- Undo removes the whole saved batch.

### US-21 Continue an unfinished workout
**As an** athlete in the middle of a workout, **I want** an open template to survive closing the app, **so that** I don't lose my changes.

- Reopening the app brings back the sheet with my changes.

### US-22 Be offered a template for what I repeat
**As an** athlete, **I want** the app to notice my repeated workouts, **so that** I get a template without having to set one up.

- After 2 or more activities are logged together on 2 different days (at least 80 % overlap), and no template covers them, the save toast offers **"Teed seda tihti. Salvestan malliks?"**.
- One tap creates the template with a suggested name that I can change later.
- If I dismiss the offer, it isn't shown again for that combination.

### US-23 Manage my templates
**As an** athlete, **I want** to create, rename and delete templates, **so that** they match my current routine.

- I can create a template by picking activities.
- Deleting a template keeps all entries saved from it.
- Archived activities are skipped when a template opens.

### US-24 Browse my history
**As an** athlete, **I want** to see past training grouped by day, **so that** I can look back at what I did.

- Entries are grouped by day, newest first.
- All of a day's activities appear together.

### US-25 Repeat or save a past day
**As an** athlete, **I want** to reuse any day from my history, **so that** a good workout is easy to do again.

- Any day offers **Korda** (repeat it now).
- Any day offers **Salvesta malliks** (save as template).

### US-26 Fix or delete an entry
**As an** athlete, **I want** to edit or delete past entries, **so that** my progress is based on correct data.

- I can change any value on an entry, or delete it.
- Progress updates to reflect the change.
- This works offline.

### US-27 Retire an activity
**As an** athlete, **I want** to remove an activity I no longer do, **so that** it stops cluttering the list without erasing my progress.

- An activity with history is archived, not deleted.
- Its entries stay in my history.

## M4 Progress

### US-28 Get something back from every save
**As an** athlete, **I want** instant feedback when I save, **so that** logging feels worthwhile.

- The toast names a new record if there is one, for example "Uus rekord: 62,5 kg".
- Otherwise it compares with last time, for example "+2,5 kg võrreldes eelmise korraga".
- Otherwise it counts the week, for example "Selle nädala 3. treening".
- The message is worked out on the device, so it also appears offline.

See [Retention](03-retention.md).

### US-29 Know at a glance whether I'm improving
**As an** athlete, **I want** one sentence per activity, **so that** I know how I'm doing without interpreting a chart.

- The Areng tab has one card per activity, with a headline sentence and a sparkline.
- For example: "Rinnalt surumine: 3 kuuga +7,5 kg".
- A change smaller than ±1 % reads as "püsib".
- A decline is stated plainly and neutrally.

See [Progress](04-progress.md).

### US-30 See progress measured fairly for my sport
**As an** athlete, **I want** progress measured in a way that suits my sport, **so that** the result is meaningful.

- Strength uses estimated 1RM, or max reps for bodyweight exercises, and is labelled as an estimate.
- Distance sports compare pace only between entries of similar distance (within ±25 % of the period's median).
- Duration activities use minutes per week.
- Lower pace counts as an improvement.
- With 6 or more points, one unusually good or bad day doesn't swing the headline.

### US-31 Explore one activity in detail
**As an** athlete, **I want** a detailed view of an activity, **so that** I can see my progress over time.

- It shows the headline sentence, then a line chart with a 1, 3 or 12 month period.
- It shows personal records and recent entries.
- Values are labelled directly, without a legend.

### US-32 See progress from the start
**As a** new athlete, **I want** to see something useful from my first entries, **so that** I have a reason to continue.

- A comparison appears from the second entry onward.
- With too little data, the app says how many more entries are needed rather than showing an empty chart.

### US-33 Celebrate personal records
**As an** athlete, **I want** my personal bests recognised, **so that** my achievements are visible.

- A new best value is marked as a record, and for strength also heaviest weight.
- An activity's first entry never counts as a record.

### US-34 Keep a weekly streak
**As an** athlete, **I want** a streak counted in weeks, **so that** rest days don't count against me.

- A week counts if it has at least one entry.
- No goal has to be set up.
- There is no "you missed X" messaging.

### US-35 See my consistency over time
**As an** athlete, **I want** to see the last 12 weeks at a glance, **so that** one missed week looks like a small gap and not a reset.

- The progress screen shows a 12-week consistency strip above the activity cards.

### US-52 Set a performance goal
**As an** athlete, **I want** to set a target such as 10 km in under 50 minutes, **so that** I have something specific to work towards.

- I can set a target for any strength or distance activity.
- I can see my current best against the target.
- Reaching it is announced when I save, ahead of other messages.

### US-53 Set a goal with a date
**As an** athlete preparing for something, **I want** to give a target a date, **so that** I know whether I'm on track.

- A performance target can have a date.
- The app shows whether I'm on track.

### US-54 Set a consistency goal
**As an** athlete, **I want** a goal such as 3 workouts per week, **so that** I focus on showing up.

- I can choose a number of workouts per week.
- A workout is a day with at least one training entry, so five exercises on one day count once.
- This week's count is visible on the Areng tab.

### US-55 See my weekly training volume
**As an** athlete, **I want** to see how much I trained each week, **so that** I can tell whether my training load is rising.

- Strength shows weekly tonnage (reps × kg).
- Distance sports show weekly kilometres.

## M5 Reminders

### US-36 Be asked about reminders at the right moment
**As an** athlete, **I want** to be asked about reminders once I'm using the app, **so that** the request makes sense.

- The app never asks on first launch.
- After my third saved entry, a card explains the benefit.
- The browser's permission prompt appears only after I accept the card.
- On iPhone, I'm guided to install the app first.

See [Reminders](08-reminders.md).

### US-37 Choose when I'm reminded
**As an** athlete, **I want** to pick the days and time, **so that** reminders match my training schedule.

- The days are prefilled from when I've actually trained.
- Reminders arrive at my chosen local time, including across daylight saving changes.
- I can change or turn off reminders in settings.

### US-38 Only be reminded when it's useful
**As an** athlete, **I want** no reminder when I've already trained, **so that** notifications don't become noise.

- I get at most one reminder per day.
- No reminder is sent if I've already got an entry that day.
- After 4 reminders in a row with no new entry, it drops to one per week.
- Logging again restores my normal schedule.

### US-39 Go straight to logging from a reminder
**As an** athlete, **I want** a reminder to take me to the right place, **so that** I can log at once.

- The text is specific and in my language, and never guilt-based.
- Tapping it opens the Logi tab, with the relevant template's sheet open if there is one.

### US-40 Get reminders on each of my devices
**As an** athlete with more than one device, **I want** reminders on each device I've enabled, **so that** I see them wherever I am.

- Each device with permission receives the reminder.
- Signing out stops reminders on that device.
- Devices that no longer accept notifications are removed automatically.

## M6 Launch-ready

### US-41 Export my data
**As an** athlete, **I want** to download my training data as a CSV file, **so that** I own a copy of it.

- Export is available in settings.
- It includes all my entries.

### US-42 Delete my account
**As an** athlete, **I want** to delete my account, **so that** my data doesn't stay behind if I leave.

- Deletion is available in settings.
- It permanently removes my account and all my data.

### US-43 Sign out safely
**As an** athlete, **I want** to sign out without losing data by accident, **so that** I can safely hand over or share a device.

- I get a warning first if anything is still unsent.
- Signing out clears my data and queued changes from the device.

### US-44 Measure whether the app is working
**As a** product owner, **I want** to measure logging speed and retention, **so that** I can tell whether the two biggest risks are under control.

- I can see the median logging time, with single entries and template saves measured separately.
- I can see week-4 retention by cohort.
- No separate analytics tool is needed.

See [Retention](03-retention.md).

### US-45 Use the app comfortably on any phone
**As an** athlete, **I want** the app to fit my phone, **so that** it's easy to use with one hand.

- It works from 360 px wide, with no dependence on hover.
- iOS doesn't zoom when a field gets focus.
- Content stays clear of notches and home indicators.
- The theme follows the system's light or dark setting.

## Later (prepared for, not built)

### US-46 Use my own units
**As an** athlete in the UK or US, **I want** weights and distances in the units I use, **so that** the numbers feel natural.

- I can choose weight (kg or lb) and distance (km or miles) separately.
- Existing data needs no migration, and a value entered in lb converts back exactly.
- Records and trends are unaffected by the choice.

See [Languages and units](09-languages-units.md).

### US-47 Share my progress with my coach
**As an** athlete on a team, **I want** to let my coach see my training, **so that** we can work from the same data.

- Sharing is my explicit choice, and I can withdraw it at any time.
- My data stays mine, and the team only gets access to it.

### US-48 Follow my athletes' progress
**As a** coach, **I want** to see the progress of athletes who have shared it with me, **so that** I can guide their training.

- I only see athletes who have given consent.
- Each athlete's progress uses the same calculations and views that they see themselves.

See [Teams](10-teams.md).

### US-56 Subscribe for advanced progress
**As an** athlete who wants more detail, **I want** to subscribe, **so that** I get full charts, long periods, volume and activity detail.

- Logging, templates, reminders, goals and headline progress stay free.
- Price, payment provider and the treatment of early users are still to be decided.

See [Business](13-business.md).

### US-57 Import workouts from my watch
**As a** runner with a watch or Strava, **I want** recorded workouts to appear by themselves, **so that** I don't enter them twice.

- An imported workout appears as a normal entry.
- Importing the same workout again never creates a duplicate.
