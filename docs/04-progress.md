# Showing progress

The **Areng** tab has two levels.

**Overview:** weekly streak and consistency strip on top, then one card per activity with a headline sentence and a sparkline.

> **Rinnalt surumine:** 3 kuuga +7,5 kg · arvutuslik maksimum 78 kg

**Activity detail:** the same sentence, a line chart with 1 / 3 / 12 month toggle, personal records, recent entries, and a second chart for volume where it applies.

Headline progress is free. Full charts, long periods, volume and detail are the paid part, once subscriptions exist ([Business](13-business.md)).

Any count of "workouts" means **days with at least one training entry**. Five exercises on one day are one workout, even though they are five entries.

## Calculation rules

All of this lives in one pure, unit-tested module (`src/lib/progress.ts`). A user's data is small enough to compute on the device.

**Value per entry.** If an activity has several entries on one day, the best one becomes that day's point.

| Kind | Value |
|------|-------|
| `strength` with weight | Best set by Epley: `kg × (1 + reps / 30)`. A 1-rep set is just `kg`. Labelled "arvutuslik" (estimated). |
| `strength` bodyweight (all sets 0 kg) | Max reps in one set |
| `distance` | Pace, using only entries within ±25 % of the median distance in the selected period, because a 5 km and a 20 km pace aren't comparable. Other entries are dimmed on the chart. |
| `duration` | Total minutes per week |
| `body_weight` | The weight entered |

**The headline trend**, for `strength`, `distance` and `body_weight`, within the selected period (default 3 months):

| Points in period | Comparison |
|------------------|------------|
| 0–1 | No trend. Show "Veel 1 treening ja näed oma arengut". |
| 2–5 | Latest vs first |
| 6 or more | Median of latest 3 vs median of earliest 3, so one unusually good or bad day doesn't swing it |

For `duration`, compare the average minutes per week of the last 4 weeks with the 4 weeks before.

A change smaller than ±1 % reads as "püsib" (holding steady). For pace, lower is better, so the sign and wording are inverted. For body weight, the app can't know which direction the user wants, so the change is stated without calling it good or bad, and there are no records.

**Records:** a new best value per activity, and for `strength` also heaviest weight. An activity's first entry never counts.

**Training volume:** weekly totals on the progress screen, as tonnage (reps × kg) for strength and kilometres for distance sports.

**Effort rating:** shown on the entry and in history. Whether it feeds into progress is undecided.

**Display rules**
- Sentence first, chart second. The chart supports the claim.
- No empty charts. Too little data shows how many entries are still needed.
- Direct value labels at phone width, with no legend to decode.
- Numbers and dates follow the chosen language through `Intl` (decimal comma in Estonian). Weeks start on Monday, and pace reads `5:12 min/km`.

## Goals

Goals are free, and are optional so that they add no setup to getting started.

| Type | Example | Shown as |
|------|---------|----------|
| Performance target | Bench press 100 kg, or 10 km in under 50 min | Current best against the target |
| Target with a date | The same, by a chosen date | The same, plus whether it is on track |
| Consistency goal | 3 workouts per week | This week's count, tied to the weekly streak |

- A distance target is met by one entry of at least the target distance within the target time.
- Reaching a goal is announced in the save toast, ahead of other messages.

Still undecided: whether a strength target is met by actually lifting the weight or by the estimated 1RM, and how "on track" is judged ([open questions](../SPEC.md#open-questions)).
