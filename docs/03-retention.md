# Retention

## Keeping people logging

1. **Immediate reward.** The save toast always says something specific, and computes it locally so it is instant:
   - "Uus rekord: 62,5 kg" when it is a personal best (never on an activity's first entry)
   - "+2,5 kg võrreldes eelmise korraga"
   - Otherwise "Selle nädala 3. treening"
2. **Weekly streak, not daily.** Rest days are part of training, so a daily streak would punish correct behaviour. A week counts if it has at least one entry, and no goal setup is needed.
3. **12-week consistency strip** on the progress screen, so one missed week looks like a small gap and not a reset.
4. **Early signal.** The first comparison appears at the second entry, not after weeks of data.
5. **Low friction to stay in.** Sign-in once, a persistent session, and a home screen icon.
6. **No guilt.** No "you missed X" messaging. Declines in progress are stated plainly and neutrally.

7. **Reminders** on the days the user chooses, sent only if they haven't already logged that day ([Reminders](08-reminders.md)).

## Measuring retention

Admin queries run in the Supabase SQL editor, so no analytics tool is needed at the start.

```sql
-- Share of each signup-week cohort that logged something in week 4 after their first entry
with firsts as (
  select user_id, min(performed_on) as first_on from public.entries group by user_id
)
select date_trunc('week', f.first_on)::date as cohort,
       count(distinct f.user_id) as users,
       round(100.0 * count(distinct e.user_id) / count(distinct f.user_id)) as week4_pct
from firsts f
left join public.entries e
  on e.user_id = f.user_id
 and e.performed_on >= f.first_on + 28
 and e.performed_on <  f.first_on + 35
group by 1 order by 1;

-- Median logging time for single entries (template saves share one timing, so they are measured apart)
select percentile_cont(0.5) within group (order by entry_ms) as median_ms
from public.entries where entry_ms is not null and template_id is null;
```
