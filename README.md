# Quiz Studio Online: setup guide

This is the **online app**: the quiz app with **Sign in with Google** (Gmail) and cloud sync.
Your Exam Log, saved exams, unfinished progress and uploaded question sets follow your account to any device.

It is a single file (`index.html`) that you host for free on **GitHub Pages**, plus a free **Firebase** project that handles Google sign-in and stores each person's data.
The original local app (`pdf-quiz-maker.html`) is a separate file and is not affected.

Setup takes about 10–15 minutes and costs nothing.

---

## Part 1: Create the Firebase project

1. Go to <https://console.firebase.google.com> and sign in with your Google account.
2. **Add project** → give it a name (for example `quiz-studio-online`) → you can turn Google Analytics off → **Create project**.
3. **Build → Authentication → Get started → Sign-in method → Google → Enable.**
   Choose a *project support email* (your Gmail) → **Save**.
4. **Build → Firestore Database → Create database.**
   Pick **Production mode**, choose a location near you (it can't be changed later) → **Enable**.
5. In Firestore, open the **Rules** tab, delete what is there, paste the contents of **`firestore.rules`** (included next to this file) → **Publish**.
   These rules make sure every person can only read and write their own data.
6. Click the **gear icon → Project settings → General**. Under **Your apps** click the web icon **`</>`**, give it a nickname, and **Register app** (you do *not* need Firebase Hosting).
   Firebase shows a `firebaseConfig` block. Keep that page open.

## Part 2: Paste the config into the app

7. Open **`index.html`** in a text editor (VS Code or Notepad).
8. Search for **`PASTE_`**. You will find this block near the top of the script:

   ```js
   const FIREBASE_CONFIG = {
     apiKey: "PASTE_API_KEY",
     authDomain: "PASTE_PROJECT_ID.firebaseapp.com",
     projectId: "PASTE_PROJECT_ID",
     appId: "PASTE_APP_ID"
   };
   ```
   Replace the four values with the matching ones from Firebase (`apiKey`, `authDomain`, `projectId`, `appId`). Save the file.

   The `apiKey` here is **not a secret**; it only identifies your project. Your data is protected by the sign-in and the rules from step 5.

## Part 3: Put it on GitHub Pages

9. On <https://github.com> click **New repository** → name it (for example `quiz-studio-online`) → **Public** → **Create repository**.
10. **Add file → Upload files** → drag in **`index.html`** (you may also add `firestore.rules` and `README.md`) → **Commit changes**.
11. **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: `main` / `(root)` → Save.**
12. After a minute or two the page shows your address:
    `https://YOUR-USERNAME.github.io/quiz-studio-online/`

## Part 4: Allow your site to use Google sign-in

13. Back in Firebase: **Authentication → Settings → Authorized domains → Add domain**.
    Enter just `YOUR-USERNAME.github.io` (no `https://`, no path) → **Add**.
14. Open your GitHub Pages address and press **Continue with Google**. 🎉

---

## Trying it before you deploy

- **Demo mode (no Firebase needed):** open `index.html` and press **Try demo mode**. Everything works, but data stays on that device and nothing syncs.
  You can also open the file with `?demo=1` at the end of its address.
- **Google sign-in on your own computer:** Google sign-in does not work when you double-click the file (`file://`). Use VS Code's *Live Server* and open `http://localhost:5500`
  (`localhost` is already an authorized domain in Firebase).

## What is stored, and where

| Data | Where in Firestore |
|---|---|
| Your name, email, photo, Exam Log password check | `users/{your id}` |
| Unfinished exams, saved exams, small settings | `users/{your id}/kv/…` |
| Exam Log entries (summary + full details) | `users/{your id}/attempts/…` and `attemptdata/…` |
| Uploaded question sets (your library) | `users/{your id}/bankdata/…` |

The PDF or Word file itself is never uploaded. Only the questions the app reads from it are saved to your library.
**Menu → Delete my online data** removes all of this for the signed-in account.

## Troubleshooting

| What you see | Fix |
|---|---|
| "This website address isn't allowed in Firebase yet" | Do step 13. Add exactly your `*.github.io` domain. |
| "Google sign-in isn't switched on" | Do step 3 (Sign-in method → Google → Enable). |
| "Couldn't load your data … permission" | The rules were not published. Redo step 5. |
| "Couldn't load the Google/Firebase libraries" | No internet, or an ad blocker is blocking `gstatic.com`. Allow it and reload. |
| Sign-in window never opens | Allow pop-ups for the site. The app also tries a redirect sign-in automatically. |
| Setup screen still shows | The `PASTE_` values are still in `index.html`. Redo step 8 and upload the file again. |

## Good to know

- **Exam Log password:** it protects the delete buttons inside the app. Anyone who has access to your Google account could still reach the data in Firebase, so keep that account secure.
- **Free plan limits:** Firebase's free Spark plan allows about 50,000 reads and 20,000 writes per day and 1 GiB of storage, which is plenty for personal study.
- **Updating the app later:** replace `index.html` in your repository (keep your `FIREBASE_CONFIG` values) and the site updates in a minute or two.
# quiz-studio-online-web
