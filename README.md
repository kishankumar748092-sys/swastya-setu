# SwasthyaSetu — Module 1: Patient Registration

A working React scaffold of the 7-screen patient registration flow (Welcome →
Registration Type → Community/Role → Basic Info → Health Info → Review →
Success), matching the design mockup.

## Run it

```bash
npm install
npm run dev
```

Then open the URL Vite prints (usually http://localhost:5173).

## How it's organized

```
src/
├── App.jsx                        # switches between the 7 screens by step index
├── context/PatientFormContext.jsx # shared form state (single source of truth)
├── api/patients.js                # mocked registerPatient() — swap for a real fetch later
└── components/registration/
    ├── StepShell.jsx    # logo header + progress bar wrapper
    ├── Welcome.jsx       # screen 1
    ├── RegistrationType.jsx   # screen 2
    ├── CommunityRole.jsx      # screen 3
    ├── BasicInfo.jsx          # screen 4
    ├── HealthInfo.jsx         # screen 5
    ├── Review.jsx              # screen 6
    └── Success.jsx             # screen 7
```

## The one thing to understand before extending this

Every screen reads and writes to **one shared `formData` object** via
`usePatientForm()` (see `PatientFormContext.jsx`). Nothing is submitted to a
server until the Review screen. This means:

- Adding a new field to the form = add it to `initialFormData` in
  `PatientFormContext.jsx`, then read/write it from whichever screen needs it.
- The `formData` shape here is what should become the JSON body of the
  backend's `POST /api/patients` endpoint — agree this shape with whoever's
  building the backend so nobody has to reshape data later.

## Wiring up the real backend

Right now `src/api/patients.js` fakes a network call and returns a random
patient ID. Once the backend has a working `POST /api/patients` endpoint,
uncomment the real `fetch` version at the top of that file and delete the
mock below it. Nothing in the screens needs to change.

## What's now included (Modules 1, 2 & 3)

The app has 3 routes, switchable via the top nav bar:

- `/` — Module 1: Patient Registration (the 7-screen flow)
- `/dashboard` — Module 2: ASHA Worker Dashboard (stats, recent patient,
  timeline, alerts & care gaps)
- `/care-routing` — Module 3: Care Routing & Facility Recommendation
  (care options, ranked facility matches)

Modules 2 and 3 currently read from `src/data/mockData.js` — one file with
fake patient/facility data standing in for the backend. This mirrors exactly
what `GET /api/patients`, `GET /api/facilities`, etc. should return later.

## Login / role selection (new)

The app now opens on a login screen (`src/components/auth/Login.jsx`) where
you pick a role — Patient, ASHA/ANM Worker, or PHC/District Admin — and type
a name. **This is not real authentication.** There's no password, OTP, or ID
check happening — it's a mock gate that exists so the app can demonstrate
role-based access the way your architecture diagram describes. Real
verification (OTP for patients, ASHA ID lookup for health workers) is
backend work that hasn't been built yet.

Each role sees a different set of screens (see `src/context/AuthContext.jsx`
and the routing logic in `src/App.jsx`):

- **Patient** → `/my-dashboard` — their own record and care timeline
  (`src/components/patient/PatientDashboard.jsx`)
- **ASHA Worker** → the full 3-tab flow from before (Registration, Dashboard,
  Care Routing)
- **Admin** → the same Dashboard component, reused as a facility-wide view

## The multi-language selector is not functional yet

The language dropdown on the Welcome screen updates `formData.language` in
state, but nothing in the app currently reads that value to swap the
displayed text. Making it real requires a translation dictionary (e.g. a
`translations.js` file keyed by language) that every component pulls its
text from, rather than hardcoded English strings. That's a separate,
non-trivial task — flagging it here so it doesn't get assumed as "done."

## Wiring the real backend for Modules 2 & 3

Once those endpoints exist:
1. Create `src/api/patients.js` style fetch wrappers for facilities/alerts
   (following the pattern already in `src/api/patients.js`).
2. In `Dashboard.jsx` and `CareRouting.jsx`, replace the mock data imports
   with `useState` + `useEffect` calls to those fetch functions.
3. Delete `mockData.js` once nothing imports it anymore.

Nothing about the layout or styling needs to change — only where the data
comes from.


## If localhost shows a blank/white screen

1. Stop the dev server with `Ctrl+C`.
2. From this exact folder (`swasthyasetu-frontend`), run:
   ```bash
   npm install
   npm run dev
   ```
3. Open the Vite URL, normally `http://localhost:5173`.
4. If the page is still blank, press `F12` → **Console**. The app now has an error boundary that displays the runtime error instead of silently showing a blank page.
5. If dependencies are corrupted, delete `node_modules` and `package-lock.json`, then run `npm install` again.

This project is a Vite + React app, not a Next.js app, so do not start it with `next dev`.
