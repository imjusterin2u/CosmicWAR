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
- The computer **speaks a word aloud** (text-to-speech) drawn from the winner's card
- The winner spells the word using the **letter tile board** — tap or drag letter tiles into the answer row, in order
  - **Correct** → the winner collects the pot, and the correct spelling is revealed
  - **Incorrect** → the opponent gets a **steal chance**: same word, same tile board. If they spell it correctly, they steal the entire pot. If they also miss it, the cards return to the bottom of both players' stacks (nobody collects)
- If cards are **equal value → WAR**: each player places 3 cards face-down and flips a 4th; the new winner goes through the same dictation → spell → steal sequence for the full pot

### Word selection
Each card has three possible words (the computer picks one at random per round):

| Slot | Difficulty | XP if spelled correctly |
|---|---|---|
| w1 | Simple | +10 XP |
| w2 | Intermediate | +15 XP |
| w3 | Complex / Latin base | +25 XP |

A successful **steal** earns an additional +10 XP bonus.

### Letter tile board
- Tiles include the correct letters of the word **plus a few distractor letters** for added challenge
- Tap a tray tile to place it in the next empty slot, or tap a placed tile to send it back
- Drag-and-drop also works for both placing and removing tiles
- "Clear" empties the board; "Submit" checks the spelling once every slot is filled
- On submission, the correct spelling is always revealed on screen

### Voice
The dictation voice can be changed in **Setup → 🔊 Voice** using any voice installed in your browser/OS. Use the 🔁 Replay button to hear the word again as many times as needed.

### ⚡ Power Word Challenges
At random moments during play (roughly every 15–35 seconds, anytime — even mid-round), a glowing **Power Word** popup appears on screen. Whichever player taps it first gets the challenge:

1. The computer dictates a high-frequency word (her, were, could, should, their, world, etc. — the original rulebook's "Bonus Cards" list)
2. The player spells it on a mini letter-tile board
3. If correct, they then pick the sentence that uses the word correctly from 2–3 multiple-choice options
4. **Success** → steal one random card from the opponent's deck **and** earn +30 XP
5. A miss at either step closes the challenge with no penalty — the popup will return again later

In **Pass & Play** mode, whichever physical player taps the popup first plays the challenge. (Online co-op currently treats Player 1 as the default claimer when triggered — see note in Customizing below if you want to adjust this.)

### 🎓 Student Roster & Persistent Rank
Students no longer type their name freehand. From the home screen, tap **⚙️ Manage student roster** to add each student once — their name then appears in a dropdown for them to select before playing.

- **With Firebase configured**: XP and rank are stored centrally and follow the student across *any* device — a Chromebook today, an iPad tomorrow, same rank either way.
- **Without Firebase configured**: the roster and XP fall back to that browser's local storage only (useful for testing, but won't follow a student across devices).

Only Player 1 (the "Pilot" selected on the home screen) currently has roster-backed XP; the second local player in Pass & Play still uses free-text and won't have persistent rank tracking yet.

To remove a student or fix a typo, open the roster manager and tap **Remove** next to their name — note this also clears their saved XP.

### 🔒 Teacher PIN
Students should never see the roster manager or reports — they're locked behind a PIN. From the home screen, the **🔒 Teacher Tools** link prompts for a 4–6 digit PIN before showing anything.

- **First use**: no PIN exists yet, so the prompt lets you set one on the spot
- **After that**: the same prompt asks for the PIN you set; entering it correctly unlocks the roster and reports for the rest of that browser session
- The PIN is stored alongside your other Firebase data (`settings/teacherPin`) so it's the same PIN on every device, with local-storage fallback if Firebase isn't configured

Students can always reach **vs Computer**, **Pass & Play**, **Host**, and **Join** directly from the home screen with no PIN required — only the teacher-facing tools are gated.

### 📊 Missed-Word Reports (for lesson prep)
Every roster row has a **📊 Report** button. Tapping it opens a breakdown of what that student has gotten wrong, across three tabs:

- **Card Words** — spelling words from the deck, tagged with which substep/suit they came from (e.g. *"2.3 (Exceptions)"*)
- **Power Words** — high-frequency words missed during the spelling step of Power Word challenges
- **Sentence Use** — words the student *spelled correctly* but then used in the wrong sentence during the Power Word challenge's second step — useful for spotting kids who can decode/encode a word but don't yet understand its meaning or usage

All three are sorted by miss count, most-missed first. A word is never removed from the report automatically — the count just keeps growing each time it's missed again, so a one-off slip looks very different from a recurring pattern.

This data lives in the same place as XP — Firebase if configured, local storage as a fallback — so it follows the student the same way their rank does.

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
