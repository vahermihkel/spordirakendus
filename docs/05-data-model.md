# Data model (Supabase)

Values are always stored in kg and metres, whatever units are displayed ([Languages and units](09-languages-units.md)).

Strength sets are JSON on the entry rather than a child table. Each entry is then one row and one upsert, which keeps offline retries atomic without an RPC. Progress is computed on the client, so nothing needs to query individual sets in SQL.

```sql
create table public.profiles (
  user_id       uuid primary key default auth.uid() references auth.users on delete cascade,
  locale        text not null default 'et' check (locale in ('et', 'en')),
  weight_unit   text not null default 'kg' check (weight_unit in ('kg', 'lb')),    -- only kg offered for now
  distance_unit text not null default 'km' check (distance_unit in ('km', 'mi')),  -- only km offered for now
  timezone      text not null default 'Europe/Tallinn',  -- IANA name, refreshed by the client on app open
  age_confirmed_at timestamptz,  -- when the user confirmed being 18 or over
  created_at    timestamptz not null default now()
);

create table public.activities (
  id           uuid primary key default gen_random_uuid(),
  user_id      uuid not null default auth.uid() references auth.users on delete cascade,
  name         text not null check (char_length(name) between 1 and 40),
  kind         text not null check (kind in ('strength', 'distance', 'duration', 'body_weight')),
  pace_display text check (pace_display in ('min_per_km', 'km_per_h', 'min_per_100m')),
  archived_at  timestamptz,
  created_at   timestamptz not null default now(),
  unique (id, user_id)
);
create unique index activities_user_name_idx on public.activities (user_id, lower(name));

create table public.templates (
  id           uuid primary key,  -- generated on the client, like entries
  user_id      uuid not null default auth.uid() references auth.users on delete cascade,
  name         text not null check (char_length(name) between 1 and 40),
  activity_ids uuid[] not null check (cardinality(activity_ids) between 1 and 30),
  last_used_at timestamptz,
  created_at   timestamptz not null default now(),
  unique (id, user_id)
);

create table public.entries (
  id           uuid primary key,  -- generated on the client for idempotent offline retries
  user_id      uuid not null default auth.uid() references auth.users on delete cascade,
  activity_id  uuid not null,
  template_id  uuid,              -- set when saved from a template
  performed_on date not null,     -- the user's local date, never derived from UTC
  sets         jsonb check (sets is null or jsonb_typeof(sets) = 'array'),  -- [{ "reps": 8, "kg": 60 }]
  distance_m   integer check (distance_m > 0),
  duration_s   integer check (duration_s > 0),
  body_weight_kg numeric(6,3) check (body_weight_kg > 0),
  effort       smallint check (effort between 1 and 10),  -- optional; the scale is a placeholder
  note         text check (char_length(note) <= 500),
  entry_ms     integer,           -- sheet open to save tap, for the speed target
  source       text not null default 'manual',  -- ready for later imports (Strava, watches)
  external_id  text,              -- the provider's id for an imported workout
  created_at   timestamptz not null default now(),
  check (sets is not null or duration_s is not null or body_weight_kg is not null),
  -- composite key: an entry can only point at the same user's activity
  foreign key (activity_id, user_id) references public.activities (id, user_id) on delete cascade,
  foreign key (template_id, user_id) references public.templates (id, user_id) on delete set null (template_id)
);
create index entries_user_activity_date_idx
  on public.entries (user_id, activity_id, performed_on desc);
create unique index entries_external_idx
  on public.entries (user_id, source, external_id) where external_id is not null;

create table public.goals (
  id                uuid primary key,  -- generated on the client, like entries
  user_id           uuid not null default auth.uid() references auth.users on delete cascade,
  kind              text not null check (kind in ('performance', 'consistency')),
  activity_id       uuid,
  target_kg         numeric(7,3) check (target_kg > 0),
  target_distance_m integer check (target_distance_m > 0),
  target_duration_s integer check (target_duration_s > 0),
  target_date       date,
  weekly_workouts   smallint check (weekly_workouts between 1 and 7),
  achieved_at       timestamptz,
  archived_at       timestamptz,
  created_at        timestamptz not null default now(),
  check ((kind = 'performance' and activity_id is not null)
      or (kind = 'consistency' and weekly_workouts is not null)),
  foreign key (activity_id, user_id) references public.activities (id, user_id) on delete cascade
);

create table public.reminders (
  user_id       uuid primary key default auth.uid() references auth.users on delete cascade,
  enabled       boolean not null default false,
  weekdays      smallint[] not null default '{1,3,5}',  -- ISO weekdays, 1 = Monday
  local_time    time not null default '18:00',
  last_sent_on  date,
  ignored_count integer not null default 0  -- reminders in a row with no entry since
);

create table public.push_subscriptions (
  endpoint   text primary key,  -- one row per device
  user_id    uuid not null default auth.uid() references auth.users on delete cascade,
  p256dh     text not null,
  auth       text not null,
  created_at timestamptz not null default now()
);
create index push_subscriptions_user_idx on public.push_subscriptions (user_id);

alter table public.profiles           enable row level security;
alter table public.activities         enable row level security;
alter table public.templates          enable row level security;
alter table public.entries            enable row level security;
alter table public.goals              enable row level security;
alter table public.reminders          enable row level security;
alter table public.push_subscriptions enable row level security;

create policy "own profile" on public.profiles for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);
create policy "own activities" on public.activities for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);
create policy "own templates" on public.templates for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);
create policy "own entries" on public.entries for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);
create policy "own goals" on public.goals for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);
create policy "own reminders" on public.reminders for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);
create policy "own push subscriptions" on public.push_subscriptions for all to authenticated
  using ((select auth.uid()) = user_id) with check ((select auth.uid()) = user_id);

grant select, insert, update, delete
  on public.profiles, public.activities, public.templates,
     public.entries, public.goals, public.reminders, public.push_subscriptions
  to authenticated;
```

- `profiles` is created by a client upsert on first launch. A failing database trigger on `auth.users` could block sign-up altogether.
- `templates.activity_ids` is an array so that a template is one row and one offline upsert. Archived activities in it are skipped when the template opens.

- Field requirements per kind (for example `distance` needs both distance and time) are validated in the client.
- Activities with history are archived rather than deleted, so a mistaken tap can't erase progress.
- Account deletion is a `security definer` RPC, callable only by `authenticated`, that removes the caller's `auth.users` row. Data cascades from there.

**Auth: Google sign-in** through Supabase Auth, using the Google provider with the PKCE flow. It needs an OAuth client in Google Cloud, with the Supabase callback URL as its redirect URI. It is the only sign-in method, unless the iPhone test below fails.

**Minimum age: 18.** The user confirms this on first run, and `profiles.age_confirmed_at` records when.

Two iPhone issues have to be handled:
- OAuth redirects can behave badly in an installed PWA, with the flow finishing in a browser layer instead of returning the session to the app. This is tested on a real iPhone in M1. If it proves unreliable, a 6-digit email code is added as a second sign-in method.
- Safari and the installed app don't share a session, so someone who signs in and then installs has to sign in once more. The app therefore suggests installing early ([Offline](07-offline.md)).

**Privacy:** training data can reveal health information. Host the Supabase project in an EU region, collect only what is listed above, and provide export and deletion from day one. Body weight and effort make the data more clearly health-related, so a legal review is on the [open questions](../SPEC.md#open-questions) list before public launch.
