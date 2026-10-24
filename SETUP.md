# PyQuest setup

PyQuest is three files: `index.html` (the game), `config.js` (your Firebase keys) and `icon.png` (Home Screen icon).
It works right away in guest mode. Follow the steps below to put it online and turn on email sign-in.

## 1. Put it online with GitHub Pages (free)

1. On github.com, create a new **public** repository called `pyquest`.
2. Click **Add file → Upload files** and upload `index.html`, `config.js` and `icon.png`. Commit.
3. Go to **Settings → Pages**. Under *Build and deployment*, choose **Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. After about a minute the game is live at `https://varaha18.github.io/pyquest/`.
5. On your iPhone, open that link in **Safari** → Share → **Add to Home Screen**.

At this point everyone can play as a guest. Next, turn on accounts.

## 2. Create a Firebase project

1. Go to https://console.firebase.google.com and click **Create a project** (name it `pyquest`). Google Analytics is optional.
2. On the project home, click the **Web** icon (`</>`) to add a web app. Name it `PyQuest`. Skip Firebase Hosting.
3. Firebase shows a `firebaseConfig = { ... }` block. Copy it.
4. Open `config.js` and replace `null` with that object:

```js
window.PYQUEST_FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "pyquest-xxxx.firebaseapp.com",
  projectId: "pyquest-xxxx",
  storageBucket: "pyquest-xxxx.appspot.com",
  messagingSenderId: "...",
  appId: "..."
};
```

These keys are safe to publish. They identify the project; the security rules in step 4 are what protect the data.

## 3. Turn on sign-in methods

Firebase console → **Build → Authentication → Get started → Sign-in method**:

- Enable **Email/Password**. This covers Gmail, iCloud, Outlook or any other email address.
- Enable **Google** (optional, for the "Continue with Google" button). Pick a support email and save.

Then **Authentication → Settings → Authorized domains → Add domain** and add `varaha18.github.io`.

## 4. Create the database and lock it down

Firebase console → **Build → Firestore Database → Create database**. Choose a location in Europe (e.g. `eur3`) and start in **production mode**.

Open the **Rules** tab, replace everything with this, and click **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid} {
      allow read, write: if request.auth != null && request.auth.uid == uid;
    }
  }
}
```

Each player can only read and write their own progress document.

## 5. Upload the new config.js

Upload the edited `config.js` to the GitHub repo (replace the old one). Refresh the site: the sign-in screen now shows **Sign in / Create account** and **Continue with Google**.

## Notes

- **Sign in with Apple** is not included. It needs a paid Apple Developer account. iCloud email addresses work fine with email/password.
- Survival mode runs real Python 3 in the browser (Skulpt). The first run downloads the engine, so it needs an internet connection once per visit.
- Guest progress stays on the device it was played on. Signed-in progress syncs to every device.
- Opening `index.html` straight from the Files app or a chat app will show a blank screen, because those previews don't run JavaScript. Always open it from the web link.
