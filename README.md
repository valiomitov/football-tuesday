# ⚽ Football Tuesday

A small web app for the weekly game: keep the squad list, tick who turned up, split balanced teams (6v6 or 7v7) using positions, ratings and player pairs, show the line-ups on a pitch, and track appearances.

- **Hosting:** GitHub Pages (free)
- **Database:** Firebase Firestore on the free Spark plan (no card needed)
- **Editing:** only organisers who sign in with Google. Everyone else can view.

If the Firebase config is left empty, the app runs in **demo mode** with sample players and keeps its data only in the browser.

---

## 1. Put it on GitHub Pages

1. Create a new public repository, e.g. `football-tuesday`.
2. Upload `index.html` (plus this README and `firestore.rules` if you want).
3. In the repo, go to **Settings → Pages → Source: Deploy from a branch → `main` / root → Save**.
4. After a minute the site is live at `https://<your-username>.github.io/football-tuesday/`.

The app works straight away in demo mode.

## 2. Create the Firebase database

1. Go to https://console.firebase.google.com, choose **Add project**, give it a name (Google Analytics is optional), and create it. It starts on the free Spark plan.
2. **Build → Firestore Database → Create database**. Pick a location close to you (e.g. `eur3` or `europe-west`) and start in **production mode**.
3. **Build → Authentication → Get started → Sign-in method → Google → Enable**.
4. **Authentication → Settings → Authorized domains → Add domain:** `<your-username>.github.io`
5. **Project settings (⚙️) → Your apps → Web (`</>`)**. Register an app (no hosting needed) and copy the `firebaseConfig` values.

## 3. Connect the app

At the top of the `<script>` in `index.html`:

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza...",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project",
  appId: "1:123:web:abc"
};
const ADMIN_EMAILS = ["valentin@gmail.com"];   // organisers
```

These keys are meant to be public. Security comes from the rules in the next step.

You can also rename the teams and change their colours in `TEAMS`.

## 4. Lock down the database (important)

Open `firestore.rules`, put the same organiser emails in it, then paste it into **Firestore → Rules → Publish**.

Result: everyone can **read**, and only listed organisers can **write**. Without this step, anyone who finds the URL could change or delete your data.

---

## How the team split works

1. Players who are paired **⚖️ "split apart"** (two players of similar level) go one to each team.
2. Players paired **🤝 "keep together"** always land on the same team.
3. Everyone else fills the teams so their sizes stay even.
4. The app tries 3,000 random splits like this and keeps the fairest one, judged by:
   - total rating difference (weighted most),
   - balance of GK / DEF / MID / FWD,
   - each team gets a goalkeeper when two are available.

Press **Re-roll** for a different, equally fair split. You can tap two players on the pitch to swap them by hand.

## Weekly use

1. **Match day:** tick who is there, press **Split teams**, share to Viber.
2. Press **Save match** to record appearances.
3. After the game, go to **History** and enter the score. Wins, draws and losses then appear in **Stats**.

## Free-tier usage

The Spark plan allows 50,000 reads and 20,000 writes per day. A weekly game with 15–30 players uses well under 1% of that.
