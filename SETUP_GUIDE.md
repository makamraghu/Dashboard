# Dashboard Setup & Deployment Guide

## What you need
- A free Google account
- A free GitHub account
- ~15 minutes

---

## STEP 1 — Create a Firebase project

1. Go to https://console.firebase.google.com
2. Click **"Add project"** → name it (e.g. `my-dashboard`) → Continue → Create
3. On the left sidebar, click **"Build" → "Authentication"**
4. Click **"Get started"**
5. Under **Sign-in method**, enable these one by one:
   - **Google** → Enable → Save
   - **GitHub** → Enable → you'll need a Client ID & Secret (see Step 2)
   - **Microsoft** → Enable → you'll need an Application ID & Secret (see Step 3)

---

## STEP 2 — GitHub OAuth App

1. Go to https://github.com/settings/developers → **OAuth Apps → New OAuth App**
2. Fill in:
   - Application name: `My Dashboard`
   - Homepage URL: `https://YOUR_GITHUB_USERNAME.github.io/dashboard`
   - Authorization callback URL: Copy this from Firebase → Authentication → GitHub → the callback URL shown there
3. Click **Register application**
4. Copy the **Client ID** and generate a **Client Secret**
5. Paste both back into Firebase → Authentication → GitHub → Save

---

## STEP 3 — Microsoft OAuth App

1. Go to https://portal.azure.com → search **"App registrations"** → New registration
2. Name: `My Dashboard`
3. Supported account types: **Accounts in any organizational directory and personal Microsoft accounts**
4. Redirect URI: Web → paste the callback URL from Firebase → Authentication → Microsoft
5. Click Register
6. Copy the **Application (client) ID**
7. Go to **Certificates & secrets → New client secret** → copy the secret Value
8. Paste both into Firebase → Authentication → Microsoft → Save

---

## STEP 4 — Enable Firestore Database

1. In Firebase console → **Build → Firestore Database**
2. Click **Create database** → Start in **production mode** → Choose a region → Done
3. Go to **Rules** tab and replace with this:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

4. Click **Publish**

---

## STEP 5 — Get your Firebase config

1. In Firebase console → click the gear ⚙ → **Project settings**
2. Scroll to **"Your apps"** → click **"</> Web"**
3. Register app name (e.g. `dashboard`) → Register
4. Copy the `firebaseConfig` object — it looks like:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "my-dashboard-xxx.firebaseapp.com",
  projectId: "my-dashboard-xxx",
  storageBucket: "my-dashboard-xxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123:web:abc"
};
```

5. Open `index.html` and find the comment:
   ```
   // ── PASTE YOUR FIREBASE CONFIG HERE ──
   ```
   Replace the placeholder values with your actual config.

---

## STEP 6 — Add your GitHub Pages domain to Firebase

1. Firebase console → Authentication → **Settings** → **Authorized domains**
2. Click **Add domain** → enter: `YOUR_GITHUB_USERNAME.github.io`
3. Save

---

## STEP 7 — Publish to GitHub Pages

1. Create a new **public** repository on GitHub named `dashboard`
2. Upload `index.html` to the repository root
3. Go to repo **Settings → Pages**
4. Under **Source**, select **"Deploy from a branch"** → branch: `main`, folder: `/ (root)`
5. Click Save

Your dashboard will be live at:
**https://YOUR_GITHUB_USERNAME.github.io/dashboard**

---

## Quick checklist

- [ ] Firebase project created
- [ ] Google sign-in enabled
- [ ] GitHub OAuth app created & connected
- [ ] Microsoft Azure app registered & connected
- [ ] Firestore database created with security rules
- [ ] `firebaseConfig` pasted into `index.html`
- [ ] GitHub Pages domain added to Firebase authorized domains
- [ ] `index.html` uploaded to GitHub repo
- [ ] GitHub Pages enabled on the repo

---

## Notes

- Each user's data (links, todos, credentials) is stored privately in Firestore — no one else can access it
- Credentials are stored in Firestore (your Firebase project). For production use, consider encrypting them client-side before saving.
- The free Firebase Spark plan supports up to 50,000 reads and 20,000 writes per day — more than enough for personal use.
