# Languages and units

**Languages:** Estonian and English from the start, with one translation file per language and no strings in components. The first launch follows the device (Estonian if the device is in Estonian, otherwise English), settings can override it, and the choice is stored in `profiles.locale` so reminders use it too. Preset activity names are translated when created, and after that an activity's name is the user's own text. Kind guessing ([Logging](02-logging.md)) recognises names in both languages.

**Units:** the app offers kg and km only. British and American versions can be added without a data migration if these rules are followed from the start:
- Stored values are always kg and metres.
- Every conversion, label and stepper size goes through one module, `src/lib/units.ts`, and components never hardcode "kg" or "km".
- Weight and distance are separate settings (`weight_unit`, `distance_unit`), because the variants differ. A typical American setup is lb + miles, and a British one kg + miles.
- `pace_display` names the metric form, and the units module maps it (min/km → min/mile, km/h → mph, min/100 m → min/100 yd).
- Strength values are saved with enough decimals that a lb value converts back exactly.

Enabling them later means adding the choice to settings and the stepper sizes (for example ±5 lb). Trend percentages and records are unit-independent. British and American English can share the one `en` translation file, with dates formatted through `Intl`.
