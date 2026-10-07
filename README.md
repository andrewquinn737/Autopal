# AutoPal

Car maintenance made simple: a garage of your vehicles, upcoming and completed
maintenance with receipts, a budget estimate, a map of nearby mechanics, and a
library of DIY guides.

Plain HTML + CSS + ES modules (no build step), Supabase backend, static hosting on Vercel.

## Layout

| Path | What it is |
|---|---|
| `index.html`, `css/app.css` | App shell and styles (light/dark tokens on `:root`) |
| `js/app.js` | Hash router, bottom bar, auth redirects |
| `js/data.js` | Every database/storage call |
| `js/auth.js` | Session + sign-in (username via the `username-sign-in` Edge Function) |
| `js/components.js` | Task rows, complete-task sheet (receipt + undo), vehicle cards |
| `js/views/*.js` | One module per screen |
| `sw.js` | Service worker. **Bump `CACHE` on every release.** |
| `harness/` | Local test harness (not deployed) |

## Supabase (project `netlkrgpzgalzntsqdbd`)

- Tables: `profiles`, `notification_settings`, `vehicles`, `vehicle_photos`,
  `maintenance_tasks`, `maintenance_catalog`, `mechanic_shops`, `guides`; view `vehicle_task_summary`.
- Every user table has RLS scoped to the owner; catalog, shops and guides are read-only reference data.
- Triggers: new auth user → profile + settings; new vehicle → standard maintenance schedule;
  completing a standard task → schedules the next one (undo removes it).
- Private storage buckets: `vehicle-photos`, `receipts`, `avatars` (paths start with the user's id).
- Edge Function `username-sign-in` (verify_jwt off by design: callers aren't signed in yet).
- Mechanic shops are fictional sample data.

## Test locally

```bash
python3 harness/serve.py 5180
```

Open `http://localhost:5180/?scenario=full` (also `empty`, `signedout`). The harness swaps
`js/supabaseClient.js` for an in-memory stub, so nothing touches the real database.
In the stub, sign in as `andrew` / `correct-horse`.

## Deploy

Push to `main`; Vercel serves the repo root as a static site (no build command).
