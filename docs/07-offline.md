# Offline and the service worker

After the first sign-in, everything except signing in works with no signal: logging, templates, new activities, editing, deleting, and viewing history and progress.

**Two layers with separate jobs**

| Layer | Responsible for |
|-------|-----------------|
| Service worker (Workbox) | Precaching the app shell so it opens offline, and receiving push notifications |
| TanStack Query + IndexedDB | Cached user data and the queue of unsent writes |

The service worker deliberately does not cache Supabase API responses. A second cache of the same data would go stale separately, and the data is private to each user.

**Sync rules**
- All ids are generated on the client, so a retried write is an idempotent upsert and can't create a duplicate.
- Queued writes run strictly in order (a single TanStack mutation `scope`), because an entry can depend on an activity created offline just before it.
- The queue is flushed when the app opens and on the `online` event. Safari doesn't support the Background Sync API, so there is one mechanism for all platforms and nothing relies on it.
- Conflicts are last write wins, since an entry is rarely edited on two devices.
- If an activity created offline collides by name with one from another device, its queued entries are remapped to the existing activity.
- A small "N ootel" (N waiting) indicator appears only while something is unsent.

**Session and storage**
- An expired token while offline must not sign the user out. Writes wait until the token can be refreshed.
- Sign-out clears local data and the queue, and warns first if anything is still unsent.
- The app calls `navigator.storage.persist()` to reduce the chance of the browser evicting data.

**App updates:** a new service worker takes over on the next app launch, never in the middle of logging.

**Install prompt:** Android uses `beforeinstallprompt`. iPhone has no such API, so a short illustrated guide shows Share → Add to Home Screen. It appears after the first saved entry, and is needed on iPhone before reminders can work.
