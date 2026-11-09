# Logging flow

The **Logi** tab is the app's start screen. There is no dashboard to get past.

```
┌──────────────────────────┐      ┌──────────────────────────┐
│ Sel nädalal: 2 treeningut│      │ Rinnalt surumine   Täna ▾│
│ Mallid: [Jalapäev] [Selg]│      │ Eelmine kord: 3 × 8 × 60 │
│ ┌────────┐ ┌────────┐    │      │                          │
│ │ Jooks  │ │ Rinnalt│    │ tap  │  1.  [−] 8 [+]  [−] 60 [+]│
│ │ 5,2 km │ │ 60 kg  │    │ ───► │  2.  [−] 8 [+]  [−] 60 [+]│
│ └────────┘ └────────┘    │      │  3.  [−] 8 [+]  [−] 60 [+]│
│ ┌────────┐ ┌────────┐    │      │  + Lisa seeria           │
│ │ Kükk   │ │ Jooga  │    │      │                          │
│ │ 80 kg  │ │ 45 min │    │      │ ┌──────────────────────┐ │
│ └────────┘ └────────┘    │      │ │      SALVESTA        │ │
│ + Uus / otsi             │      │ └──────────────────────┘ │
└──────────────────────────┘      └──────────────────────────┘
```

**Rules**
1. Up to 6 activity tiles, most recently used first. Each shows its last value. Search and "new" sit below them.
2. Tapping a tile opens a bottom sheet **prefilled with the last entry** for that activity. Save is immediately active.
3. Date defaults to today. One tap switches to Eile (yesterday), and a date picker is the last resort.
4. Steppers do most of the work: ±1 rep, ±2.5 kg, ±0.1 km, ±1 min. Tapping a value opens the numeric keyboard.
5. "Lisa seeria" copies the previous set.
6. Save has no confirmation and no spinner. The sheet closes at once, the write is optimistic, and a toast offers **Võta tagasi** (undo) for 5 seconds.
7. Notes are collapsed behind a small link, and optional.
8. Only name and kind are needed for a new activity. Kind is guessed from the name where possible (for example "jooks" → distance).
9. First run: the user confirms they are 18 or over, then taps the sports they do from a preset list, so the first log screen is not empty.
10. **Effort rating** is one optional row of tappable numbers in the sheet. It starts empty rather than prefilled, because a copied rating would say nothing, and ignoring it adds no taps to a repeat.
11. **Body weight** has its own tile, prefilled with the last value. It takes one of the 6 tile places. A body weight entry isn't a workout, so as a placeholder it doesn't count towards streaks or stop a reminder (see the [open questions](../SPEC.md#open-questions)).

Logging never waits for the network. [Offline](07-offline.md) covers how offline works.

## Templates: repeating a whole workout

A template (*mall*) is a named list of activities, such as "Jalapäev". Templates sit above the activity tiles, most recently used first. A single activity doesn't need one, because its tile already repeats it.

**Using a template**
1. Tapping a template opens one sheet listing all its activities, each prefilled with **that activity's last entry**. The template stores which activities to do, not their values, so it moves along with the user's progress instead of freezing old weights.
2. Tapping a row expands it for adjusting. Unticking a row skips that activity today.
3. **Salvesta kõik** creates one entry per ticked activity in a single atomic upsert. Undo removes the whole batch.
4. The open sheet is saved on the device, so closing the app mid-workout loses nothing.

**How the app offers one**
- When 2 or more activities have been logged together on 2 different days (at least 80 % overlap) and no template covers them, the save toast adds **"Teed seda tihti. Salvestan malliks?"**. One tap creates it with a suggested name, which can be changed later.
- A dismissed suggestion isn't shown again for that combination of activities.
- Any day in history has **Korda** (repeat it now) and **Salvesta malliks** (save as template).
- Templates can also be created by hand by picking activities.
