# Ledger — Shared Budget Tracker

A single-page budget tracker with a login screen. Everyone signs in with
the **same email/password** (a "team account"), and all entries are shared
and sync live via Firebase.

## 1. Create the Firebase project

1. Go to [console.firebase.google.com](https://console.firebase.google.com) → **Add project**. Free tier is enough for this.
2. Once created, click the **`</>`** (web) icon to register a web app. Skip Firebase Hosting when asked — you'll use GitHub Pages instead.
3. Copy the `firebaseConfig` object it shows you.
4. Open `firebase-config.js` in this folder and paste your values in, replacing the `YOUR_...` placeholders. Keep the variable name `firebaseConfig` as is — `index.html` loads this file and expects that name.

## 2. Turn on Authentication

1. In the Firebase console, go to **Build → Authentication → Sign-in method**.
2. Enable **Email/Password**.
3. Go to the **Users** tab and click **Add user**. Create *one* account, e.g.:
   - Email: `budget@yourfamily.com` (doesn't need to be real, just needs to be valid-looking)
   - Password: whatever you want the shared password to be
4. Share that single email + password with the people you want to have access. There's no public sign-up button in the app, so nobody else can create their own account — only the one(s) you create manually here can log in.

## 3. Turn on Firestore (the database)

1. Go to **Build → Firestore Database → Create database**. Start in **production mode**.
2. Go to the **Rules** tab and replace the rules with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /transactions/{entry} {
      allow read, write: if request.auth != null;
    }
  }
}
```

This means: only someone who has signed in (with the shared account) can read or write budget entries. Click **Publish**.

## 4. Put it on GitHub

1. Create a new repository on GitHub (e.g. `budget-tracker`).
2. Upload both `index.html` and `firebase-config.js` to it (drag-and-drop on the GitHub website works, or `git add`/`commit`/`push` if you're using git locally). They need to sit in the same folder.
3. Go to the repo's **Settings → Pages**.
4. Under "Build and deployment", set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`. Save.
5. GitHub will give you a URL like `https://yourusername.github.io/budget-tracker/` within a minute or two. That's your live app.

## 5. Using it

- Anyone with the shared email/password can go to that URL and sign in.
- Add income or expenses with the toggle, a description, an amount, and an optional category.
- Every entry appears live for everyone signed in — no refresh needed.
- Click the `×` next to an entry to delete it.

## Notes

- **Changing the password later**: Firebase console → Authentication → Users → click the user → reset password.
- **Revoking access**: since everyone shares one login, the only way to cut someone off is to change the shared password and re-share the new one with people who should keep access.
- **Currency**: amounts display with a `$` sign. To change it, edit the `fmt()` function near the bottom of `index.html`.
- Your Firebase config values (`apiKey` etc.) are not secret — Firestore security comes from the **rules**, not from hiding the config. Don't loosen the rules above (e.g. to `allow read, write: if true;`) or anyone on the internet could read/edit the budget.
