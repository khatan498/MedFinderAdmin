# MedFinderAdmin — Claude Agent Guide

## Project Overview

Angular 18 admin web app for managing MedFinder platform data.
Concept project — not currently active. Requires a Firebase backend to build and run.
Companion app: [MedFinder](https://github.com/khatan498/MedFinder) (Android patient app).

## Required Setup Before Building

`src/environments/firebase-config.ts` is gitignored and must be created before the app will compile:

```sh
cp src/environments/firebase-config.template.ts src/environments/firebase-config.ts
# Fill in real values from Firebase Console → Project Settings → Your apps
```

Also update `.firebaserc` with your real Firebase project ID, or run `firebase init` to regenerate it.

## Commands

```sh
npm install                        # install dependencies
ng serve                           # dev server at http://localhost:4200
ng build                           # production build (output: dist/)
ng test                            # unit tests via Karma
ng lint                            # lint

firebase deploy                    # deploy hosting + functions
firebase deploy --only hosting     # hosting only
firebase deploy --only functions   # functions only

cd functions && npm run build      # compile Cloud Functions TypeScript
cd functions && npm run lint       # lint Cloud Functions
```

## Architecture

- Standalone Angular 18 components (no NgModules)
- Angular Material for all UI components
- AngularFire (v18) for Firebase integration — initialized in `app.config.ts`
- Role-based access: `AuthGuard` checks the `isSystemAdmin` flag on the user record in Realtime Database
- Cloud Functions handle privileged Auth operations (create/update/delete users) since the client SDK cannot perform these safely
- A hospital admin role is planned but not yet implemented — see commented route in `app.routes.ts`

## Project Structure

```
src/
├── app/
│   ├── components/
│   │   ├── login/                          # Login page
│   │   ├── system-admin-dashboard/         # Shell layout with sidenav
│   │   ├── manage-hospitals/               # Hospital list + CRUD
│   │   ├── manage-departments/             # Department list + CRUD
│   │   ├── manage-doctors/                 # Doctor list + CRUD
│   │   ├── manage-users/                   # User list + CRUD
│   │   ├── manage-upcoming-appointments/   # Upcoming appointments
│   │   ├── manage-past-appointments/       # Past appointments
│   │   ├── update-admin-account/           # Admin profile settings
│   │   ├── edit-*-dialog/                  # MatDialog components for create/edit
│   │   ├── delete-confirmation-dialog/     # Shared delete confirm dialog
│   │   ├── loading-spinner/                # Shared loading overlay
│   │   └── unauthorized/                   # 403 page
│   ├── services/
│   │   ├── auth.service.ts                         # Firebase Auth + role check
│   │   ├── hospital-data-access.service.ts
│   │   ├── department-data-access.service.ts
│   │   ├── doctor-data-access.service.ts
│   │   ├── user-data-access.service.ts
│   │   ├── appointment-data-access.service.ts
│   │   ├── hospital-admin-assignment-data-access.service.ts
│   │   ├── photo-storage.service.ts                # Firebase Storage uploads
│   │   └── loading.service.ts                      # Global loading state
│   ├── models/                     # TypeScript interfaces (Hospital, Doctor, User, etc.)
│   ├── guards/
│   │   └── auth.guard.ts           # Role-based route guard
│   ├── helpers/
│   │   └── time-mapping.helper.ts
│   ├── app.config.ts               # Firebase providers, app initializer
│   └── app.routes.ts               # Route definitions with auth guard
├── environments/
│   ├── firebase-config.template.ts # Committed template — copy and fill in
│   └── firebase-config.ts          # Gitignored — never commit
└── main.ts

functions/
└── src/
    └── index.ts                    # createUser, updateUser, deleteUser (callable functions)
```

## Key Conventions

- Strict TypeScript (`strict: true`) — no implicit any, no untyped returns
- All Firebase Realtime Database and Storage access goes through `src/app/services/` — components never call Firebase directly
- One service per data domain following the naming pattern `*-data-access.service.ts`
- Create/edit operations use Angular Material `MatDialog` with a dedicated `edit-*-dialog` component
- `LoadingService` must be used to show/hide the global loading spinner during async operations
- Components inject services via Angular's DI — no singleton globals
- Photo uploads go through `PhotoStorageService`, not directly via Firebase Storage SDK

## Firebase Data Model (Realtime Database)

Key top-level nodes (based on service and function usage):
- `/users/{uid}` — user records with `isSystemAdmin` boolean flag
- `/hospitals/`, `/departments/`, `/doctors/` — core entity data
- `/appointments/` — appointment records

## Files Never to Commit

- `src/environments/firebase-config.ts` — gitignored, contains Firebase project secrets
- `.firebaserc` contains a placeholder project ID — fill in via `firebase init`, but do not commit real project IDs if keeping this repo public
