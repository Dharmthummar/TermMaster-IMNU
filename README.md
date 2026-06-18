# Term Companion

A mobile-first class-schedule web app. Students sign in with Google, link their roll
number once, and get their personal Term IV timetable in two layouts (Agenda + Calendar),
with one-tap **Add to Google Calendar** and a downloadable `.ics`. You (admin) get an
**Accounts** list of everyone who signs in, a **cloud Excel-update** portal, and an
**Ads** manager — all free.

- **Frontend:** one static file (`index.html`) → host free on GitHub Pages.
- **Login + database:** Firebase (Google by default) → free Spark plan, easily handles 450 students.

---

## Try it first (no setup)

Open `index.html` — it runs in **demo mode**: no login, you're given admin access, and it
uses the bundled schedule data so you can preview the design, both layouts, and the admin
console immediately. To turn on real Google login, do the steps below.

---

## Part 1 — Firebase (Google login + database)

1. Go to <https://console.firebase.google.com> → **Add project** (any name). Disable
   Analytics if you like. This is free.
2. **Build → Authentication → Get started → Sign-in method → Google → Enable** → Save.
3. **Build → Firestore Database → Create database → Start in production mode** → pick a
   location → Enable.
4. **Firestore → Rules** tab: paste the contents of `firestore.rules`, change the email to
   **your** Google email, and **Publish**.
5. **Project settings (⚙️) → General → Your apps → Web app (`</>`)**. Register an app. Copy
   the `firebaseConfig` values.
6. Open `index.html`, find the **CONFIG** block near the top of the `<script type="module">`,
   and paste your values:
   ```js
   const CONFIG = {
     firebase: {
       apiKey: "…", authDomain: "…", projectId: "…",
       storageBucket: "…", messagingSenderId: "…", appId: "…"
     },
     adminEmails: ["22bcm016@nirmauni.ac.in"]   // same email as in firestore.rules
   };
   ```
   > The Firebase web config is meant to be public — it's safe in a static file. Your data is
   > protected by the Firestore **rules**, not by hiding these keys.

---

## Part 2 — GitHub Pages (free hosting)

1. Create a new GitHub repo and upload `index.html` (and `firestore.rules`, `README.md`).
2. Repo **Settings → Pages → Build and deployment → Source: Deploy from a branch → `main` / root** → Save.
3. After a minute your site is live at `https://<your-username>.github.io/<repo>/`.
4. **Important:** back in Firebase → **Authentication → Settings → Authorized domains → Add domain**,
   add `<your-username>.github.io`. (Google sign-in only works on authorized domains.)

---

## Part 3 — Publish your schedule

1. Open your live site, sign in with your **admin** Google account.
2. You'll be asked to link a roll number once (pick any — admins see everything regardless).
3. Go to **Admin → Update**, drop the `.zip` of Excel files (or individual `.xlsx`),
   check the parsed counts, then **Publish to cloud**.
4. Every student now sees that schedule. Re-upload and re-publish any time to update it.
5. **Admin → Ads** to edit the banner / in-feed / pop-up ads, then **Publish ads**.
6. **Admin → Accounts** shows everyone who has signed in (name, email, linked roll, logins,
   last seen) with search and **Export CSV**.

---

## How students use it

Sign in with Google → search their name/roll once to link → personal timetable. They can
flip between **Agenda** and **Calendar** layouts, tap any class to add it to Google Calendar,
or download the whole week as an `.ics` (which gives reminders, and the week-summary event is
titled like your schedule email).

---

## Notes & limits

- **Free tier headroom:** 450 students signing in and reading the schedule is a tiny fraction
  of Firebase's free daily limits.
- **Automated emails to a list** (vs. calendar reminders) would need a small Cloud Function or
  Apps Script — not included here, but easy to add later.
- **Data model in Firestore:** `users/{uid}` (one per account), `app/schedule` (the published
  timetable), `app/ads` (ad config).
