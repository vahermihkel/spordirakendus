# Frontend

| Concern | Choice |
|---------|--------|
| Build | Vite, React, TypeScript |
| Routing | React Router, 3 tabs plus sign-in and settings |
| Data and offline | TanStack Query with a cache persisted to IndexedDB and resumable paused mutations |
| Backend client | `@supabase/supabase-js`, anon key only in the browser |
| PWA and service worker | `vite-plugin-pwa` in `injectManifest` mode, with Workbox |
| Translations | `react-i18next` |
| Styling | Tailwind CSS |
| Charts | Recharts, loaded lazily so it doesn't slow the log screen |
| Tests | Vitest for `progress.ts`, Playwright in a phone viewport for the logging flow |
| Hosting | Firebase Hosting (static build), deployed from GitHub with preview links through the Firebase Action. Other Firebase products aren't used |

**Screens:** Logi, Areng, Ajalugu (bottom navigation), sign-in, and settings (language, reminders, templates, export, sign out, delete account).

**Mobile requirements**
- Designed at 360 px wide and used with one hand: primary actions in the bottom third
- Tap targets at least 48 px, including steppers
- Input font size at least 16 px, so iOS doesn't zoom on focus
- `inputmode="decimal"` or `"numeric"` on number fields
- Safe-area insets for notches and home indicators
- Light and dark theme following the system
- No layout that depends on hover
