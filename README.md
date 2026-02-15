# Rural Skill Connect

A full-stack web application that connects rural skills with communities. Built with Next.js (App Router), TypeScript, Tailwind CSS, and Firebase.

## Features

- **User roles** – User (browse skills), Worker (list skills), Admin (manage platform)
- **Authentication** – Sign up, log in, and sign out with Firebase Auth
- **Firestore** – Store users, skills, and profiles
- **Storage** – Upload profile photos and skill images
- **Skills** – Browse, add, and view skills with categories and locations
- **Profile** – Edit profile, bio, and photo

## Tech Stack

- Next.js 16 (App Router)
- TypeScript
- Tailwind CSS
- Firebase (Authentication, Firestore, Storage)

## Getting Started

### 1. Install dependencies

```bash
npm install
```

### 2. Configure Firebase

1. Create a project at [Firebase Console](https://console.firebase.google.com)
2. Enable **Authentication** (Email/Password)
3. Create a **Firestore Database**
4. Create a **Storage** bucket
5. Copy your config and create `.env.local`:

```env
NEXT_PUBLIC_FIREBASE_API_KEY=...
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=...
NEXT_PUBLIC_FIREBASE_PROJECT_ID=...
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=...
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=...
NEXT_PUBLIC_FIREBASE_APP_ID=...
```

### 3. Firestore Security Rules

Add rules in Firebase Console:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    match /skills/{skillId} {
      allow read: if true;
      allow create: if request.auth != null;
      allow update, delete: if request.auth != null && resource.data.userId == request.auth.uid;
    }
  }
}
```

### 4. Storage Security Rules

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /profiles/{userId}/{allPaths=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    match /skills/{userId}/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### 5. Admin access

To create an admin account, sign up and choose "Admin" role. Enter the invite code: `ruraladmin2024` (change this in `lib/constants.ts` for production).

### 6. Run the app

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Project Structure

```
app/
  auth/login/     – Login page
  auth/signup/    – Signup page
  dashboard/      – User dashboard
  profile/        – Profile with photo upload
  skills/         – Browse skills
  skills/add/     – Add a skill
  skills/[id]/    – Skill detail
components/       – Reusable components
contexts/         – Auth context
lib/              – Firebase config
types/            – TypeScript types
```

## Deploy Firebase Rules

After installing the [Firebase CLI](https://firebase.google.com/docs/cli), run:

```bash
firebase login
firebase use your-project-id
firebase deploy --only firestore
firebase deploy --only storage
```

## Deploy the App

- **Vercel** (recommended): Connect your repo and add env vars
- **Self-hosted**: `npm run build && npm run start`

## License

MIT
