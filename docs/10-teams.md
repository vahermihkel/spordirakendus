# Prepared for teams

The first version is for athletes watching their own data, and no team tables or screens are built. These rules keep teams an addition rather than a rewrite:
- Data always belongs to the athlete, through `user_id`. A team is later given access to it and never owns it.
- **Client queries always filter by an explicit athlete id** instead of relying on RLS to narrow the rows. Once a coach policy exists, RLS would otherwise return every athlete on the team into the coach's own views.
- `progress.ts` takes entries as input and knows nothing about the signed-in user, so the same code can render any athlete's progress.
- Screens receive the athlete id as a parameter rather than reading the session directly.

The likely later shape is `teams`, `team_members (team_id, user_id, role)` and an additional `select` policy on `entries` and `activities`. Postgres combines policies with OR, so existing ones stay unchanged. Sharing has to be an explicit, revocable consent by each athlete, since this is potentially health data.
