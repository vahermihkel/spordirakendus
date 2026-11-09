# Reminders (push notifications)

Standard Web Push with VAPID keys. Android works from the browser or the installed app. iPhone needs iOS 16.4 or later and the app installed on the home screen.

**Asking for permission:** never on first launch, because a refused permission is hard to undo. After the third saved entry, an in-app card explains the benefit and offers days and time, prefilled from the days the user has actually trained. The browser's permission prompt appears only after the user accepts.

**Sending**
1. `pg_cron` calls the Edge Function `send-reminders` every 15 minutes, authenticated with a secret kept in Supabase Vault.
2. The function selects users who have reminders enabled, are on a chosen weekday, and whose local time (`profiles.timezone`) has just passed `local_time`. Stored timezone names keep this correct across daylight saving changes.
3. It skips anyone who already has an entry today or has `last_sent_on` set to today.
4. It sends to each of the user's devices, in the user's language. Subscriptions that return 404 or 410 are deleted.

The VAPID private key and service role key exist only in Edge Function secrets. The Web Push library has to run on Supabase's Deno runtime, so this is tried first in M5, before the rest is built on it.

**Content:** specific and never guilt-based, for example "Jalapäev täna? Eelmine kord 3 harjutust." Tapping it opens the Logi tab, with that template's sheet open when there is one.

**Limits:** at most one reminder per day. After 4 reminders in a row with no new entry, it drops to one per week, and returning to logging restores the schedule. Sign-out unsubscribes that device.
