# ⭐ Cosmic WAR

A two-player literacy card game built on the **Wilson Reading System (WRS) Substeps 2.1–2.5**, playable on one or two devices. Supports pass-and-play, online multiplayer (via Firebase), and a vs-computer mode.

---

## Modes

| Mode | Devices | Internet needed? |
|---|---|---|
| vs Computer | 1 | No |
| Pass & Play | 1 | No |
| Online Multiplayer | 2 | Yes (Firebase) |

---

## Quick Start (no internet needed)

1. Download or clone this repo
2. Open `index.html` in any browser
3. Pick **vs Computer** or **Pass & Play** — no setup required

---

## Online Multiplayer Setup (Firebase)

Online play requires a free Firebase Realtime Database. This takes about 5 minutes.

### Step 1 — Create a Firebase project

1. Go to [https://console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → give it a name (e.g. `cosmic-war`) → Continue
3. Disable Google Analytics if you don't need it → **Create project**

### Step 2 — Add a Realtime Database

1. In the left sidebar, click **Build → Realtime Database**
2. Click **Create Database**
3. Choose a location (e.g. `us-central1`) → **Next**
4. Select **Start in test mode** (allows read/write for 30 days) → **Enable**

> For a permanent classroom deployment, set proper security rules later. See [Firebase docs](https://firebase.google.com/docs/database/security).

### Step 3 — Get your config

1. Click the **gear icon** (top left) → **Project settings**
2. Scroll down to **Your apps** → click the `</>` (Web) icon
3. Register the app (any nickname) → copy the `firebaseConfig` object

### Step 4 — Paste your config into the game

Open `index.html` and find this section near the top of the `<script>` tag:

```js
const FIREBASE_CONFIG = {
  apiKey:            "YOUR_API_KEY",
  authDomain:        "YOUR_PROJECT.firebaseapp.com",
  databaseURL:       "https://YOUR_PROJECT-default-rtdb.firebaseio.com",
  projectId:         "YOUR_PROJECT_ID",
  storageBucket:     "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId:             "YOUR_APP_ID"
};
```

Replace each value with the ones from your Firebase project. Save the file.

---

## Deploy to GitHub Pages

1. Push this repo to GitHub
2. Go to your repo → **Settings → Pages**
3. Under **Source**, select `main` branch → `/ (root)` → **Save**
4. Your game will be live at:
   `https://YOUR_USERNAME.github.io/cosmic-war/`

> Firebase's free Spark plan supports thousands of simultaneous connections — more than enough for a classroom.

---

## Gameplay Reference

### Basic rules
- Both players flip a card simultaneously
- **Higher card wins** (Ace = 1 low, King = 13 high)
- The winner must **tap the sounds** and **read a word** from their card before claiming it
- If cards are **equal value → Spelling WAR**: each player places 3 face-down, flips a 4th; the winner must correctly **spell a word** to win the entire pot

### Word choices (per card)
| Choice | Difficulty | Notes |
|---|---|---|
| 1 | Simple | Base word |
| 2 | Intermediate | Base word + suffix |
| 3 ⭐ | Cosmic Bonus | Complex word or Latin base |

Teachers should encourage Choice 3 for extra challenge.

### Tapping guide
- **Single sounds**: tap index finger to thumb once per sound
- **Welded sounds** (e.g. `-ank`, `-ing`, `-old`): tap index + middle + ring fingers together to thumb — one tap for the whole welded unit
- Suffixes are never tapped

### Suits → Substeps
| Suit | Substep | Focus |
|---|---|---|
| ♠ Spades | 2.1 & 2.2 | Welded sounds, 4-sound closed syllables, blends |
| ♣ Clubs | 2.3 | Closed syllable exceptions (-ild, -ind, -old, -olt, -ost) |
| ♥ Hearts | 2.4 | 5-sound syllables, primary Latin bases |
| ♦ Diamonds | 2.5 | 3-letter blends, 6 sounds, Latin 'ct' blends |

---

## Files

```
cosmic-war/
└── index.html    ← entire game (HTML + CSS + JS, no build step)
└── README.md     ← this file
```

The game is intentionally a single file with no dependencies to install — just open and play.

---

## Customizing

All word data lives in the `DECK_DATA` array near the top of the `<script>` tag in `index.html`. Each card looks like:

```js
{suit:'spades', value:5, label:'5', w1:'belt', w2:'belts', w3:'best'}
```

Edit, add, or remove cards freely to match your curriculum.

---

## License

Free to use and adapt for educational purposes.
