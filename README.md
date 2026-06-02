# MedFinderAdmin

> **Concept project — not currently active.** A Firebase backend is required to run this app. See [Firebase Setup](#firebase-setup) below.

MedFinderAdmin is an Angular web application that provides administrative access to the MedFinder platform. It is the backend management companion to [MedFinder](https://github.com/khatan498/MedFinder), an Android app that allows patients to search for doctors and book appointments.

Healthcare organizations use this admin panel to manage their data on the platform — adding and updating hospitals, departments, and doctor profiles, and overseeing patient appointments across their organization.

## Features

- Secure admin login with role-based access control
- Manage hospitals and their associated departments
- Add, edit, and remove doctor profiles including schedules and availability
- View and manage upcoming appointments across all providers
- Review and update past appointment records
- Manage user accounts registered on the platform
- Admin account settings

## Tech Stack

- **Framework:** Angular 18
- **UI:** Angular Material
- **Backend:** Firebase — Realtime Database, Authentication, Storage, Cloud Functions, Hosting

## Firebase Setup

This app requires a Firebase project with several services enabled. Before running:

1. Create a project at [console.firebase.google.com](https://console.firebase.google.com)
2. Enable **Authentication**, **Realtime Database**, **Storage**, and **Functions**
3. Copy the config template and fill in your project's values:
    ```sh
    cp src/environments/firebase-config.template.ts src/environments/firebase-config.ts
    ```
4. Find the required values in Firebase Console → Project Settings → Your apps → SDK setup and configuration
5. Run `firebase login` then `firebase init` and associate the project with your Firebase project

## Local Development

Install dependencies:
```sh
npm install
```

Start the development server:
```sh
ng serve
```

Navigate to `http://localhost:4200/`. The app reloads automatically on source changes.

Build for production:
```sh
ng build
```

Deploy to Firebase Hosting:
```sh
firebase deploy
```

## Project Structure

- `src/app/components/` — Page and dialog components
- `src/app/services/` — Firebase data access and authentication services
- `src/app/models/` — TypeScript data models
- `src/app/guards/` — Route guards for role-based access control
- `src/environments/` — Environment config (firebase-config.ts is gitignored; use the provided template)
- `functions/` — Firebase Cloud Functions

## License

This project is licensed under the MIT License.
