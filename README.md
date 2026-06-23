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

> **Works on school networks too.** The Firebase SDK is bundled directly inside `index.html` rather than loaded from Google's CDN (`gstatic.com`) — many school content filters block that domain, which would otherwise make online multiplayer silently fail with no clear error. Bundling it means there's no outside network request for a filter to block.

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

Open `index.html` and find this section near the top of the first `<script>` tag:

```js
const FC = {
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

### Troubleshooting: "Firebase connection failed"

If Host/Join Game shows an error after following the steps above, the popup now includes the actual error detail (and logs more in the browser console via F12) rather than a generic message. A few common causes:

- **Wrong/missing config values** — double check every field in Step 4 matches your Firebase project exactly
- **Realtime Database not created yet** — Step 2 is a separate setup from just creating the Firebase project
- **Security rules expired or too strict** — test-mode rules expire automatically after 30 days; check the **Rules** tab in your Realtime Database console for an expiration date



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

### 🤖 Clear "Computer's Turn" indicator
In **vs Computer** mode, it's easy for a student to lose track of whose turn it is — especially during the spelling step, where watching a tile board you're not actually controlling can be confusing. Whenever the computer is dictating or spelling (including during a steal attempt), the screen makes this unmistakable:

- A bold red **"COMPUTER'S TURN — JUST WATCH!"** banner replaces the live dictation/tile board entirely
- A simple bouncing-bars animation plays instead, with status text like *"Listening to the word…"* or *"Spelling the word…"*
- The student's own card visually dims while the computer's card stays at full brightness, reinforcing who's currently acting

This only ever applies in **vs Computer** mode — **Pass & Play** and online multiplayer are unaffected, since both players are real people who need to see the live board on their turn.

### ⚔️ The WAR ceremony
When cards tie, the screen takes over with a dedicated WAR moment instead of resolving instantly:

1. Three cards animate face-down into each player's pile, one at a time, with a sound on each drop
2. Once both piles are down, a glowing gold card appears with **"TAP TO REVEAL THE DECIDING CARD"**
3. Either player taps it — the 4th card flips, and the result (who wins, or another DOUBLE WAR if it ties again) plays out right there
4. The winner then moves into the usual dictation → spell → steal sequence for the entire pot

This applies to **vs Computer** and **Pass & Play** the same way — tapping the flip card is a shared moment, not tied to whose "turn" it is, since a WAR standoff belongs to both players equally.

*Note: in online multiplayer, this ceremony currently plays out locally on whichever device is active in that moment rather than syncing the animation live between both players' screens — the final result still syncs correctly once the WAR resolves and dictation begins.*

With all four suits in play, a natural tie only happens on roughly 6% of flips — so by pure chance, more than half of all games would go their first several rounds without a single WAR. To make sure the WAR mechanic actually gets used early in every session, the deck is automatically arranged (right after dealing, before the first flip) so that **a WAR is guaranteed within the first 5 rounds**, unless one happens naturally even sooner. The rest of the deck stays fully random — only the minimum necessary swap happens to make this guarantee work, and the swap is invisible to players.

If you select **only one suit** in Setup, this guarantee can't apply — with just 13 cards and each value appearing exactly once, a tie is mathematically impossible regardless of shuffling. Selecting two or more suits restores the guarantee.

### Word selection
Each card has three possible words (the computer picks one at random per round):

| Slot | Difficulty | XP if spelled correctly |
|---|---|---|
| w1 | Simple | +10 XP |
| w2 | Intermediate | +15 XP |
| w3 | Complex / Latin base | +25 XP |

A successful **steal** earns an additional +10 XP bonus.

### Wilson-style tile board
Tiles follow the real Wilson Reading System color convention — **white tile face, colored border** — not a colored fill, matching the physical board:

| Border color | Category | Examples |
|---|---|---|
| 🟠 Salmon/peach | Vowels | a, e, i, o, u |
| 🟡 Yellow | Consonants, digraphs, blends | b, ch, sh, th, wh, ck, qu |
| 🟢 Green | Welded sounds & closed-syllable exceptions | ang, ank, ing, ink, ong, onk, ung, unk, all, ild, ind, old, olt, ost |
| 🟡🟫 Dark gold (italic) | Suffixes | -s, -es |

This applies to **both** tile boards in the game, but with one important difference:

- **Card words** (the deck's spelling words) follow real WRS phonetic patterns, so they're chunked into welded sounds, digraphs, and blends — the same color-coded categories above.
- **Power Words** are high-frequency *irregular* words taught as whole-word exceptions, not phonetic patterns — Wilson doesn't chunk them, so neither does this board. Each letter stays separate (e.g. *"many"* → m · a · n · y, not m · an · y), while still using the same vowel/consonant border-color coding so the visual language stays consistent.

The dictated word is automatically broken into the same chunks a student would pull on a physical Wilson board — for example *"brand"* becomes four tiles: **b · r · an · d** (the welded **an** is one tile, not two letters). A handful of same-category distractor tiles are mixed into the tray, so the task is genuinely "find the right tile," not just "put these in order."

A few welded-sound spellings are only "welded" in certain words — the same letters can spell a regular short-vowel pattern elsewhere. For example, **ost** is a long-o closed-syllable exception in *most* and *post(s)*, but a perfectly regular short-o blend in *frost(s)* — so the tile board correctly keeps *frost* as **f · r · o · s · t**, not **f · r · ost**. If you ever spot a similar mismatch on another word, it's a quick fix — just let me know which word and which chunk looks wrong.

### Heteronyms (same spelling, different sound)
**Wind** is a true heteronym — it can sound like *"wind a clock"* (short i) or *"the wind blew"* (long i), and this deck's closed-syllable-exception unit intends the **long-i** reading, grouped with *wild/bind/mild*. The spelling/tile board always correctly shows **w · ind** either way — that part isn't affected. The only uncertain part is what the **computer voice says aloud**, since browser text-to-speech doesn't reliably default to the long-i pronunciation on its own.

To work around that, the game silently substitutes a same-sounding word when speaking "wind" or "winds" aloud (the spelling target itself never changes). This is a first guess, not a guarantee — TTS behavior varies by voice and browser, so **if "winds" sounds off when you test it, let me know and I'll swap the substitute word** (it's a one-line fix, defined near the top of the speech logic in `index.html`).

When the dictated word is a base-plus-suffix form (e.g. *bank → banks*, *brush → brushes*), the suffix is carved off as its own distinct tile showing **-s** or **-es** with the dash — visually separate from the base word's sound tiles, with a dark gold border instead of the light yellow used for regular consonant tiles. This mirrors how Wilson marks suffixes as a different category from the base sound pattern.

This check looks across **all three** word choices on a card, not just Choice 1 — the rulebook's own layout occasionally places a base word in Choice 2 with its suffixed form in Choice 3 (e.g. the card with *winds / bind / binds* has its true base, "bind," sitting in the Choice 2 slot). The game checks every slot so the suffix tile shows up correctly regardless of which column the base word happens to land in.

For the **Latin base cards** (Jack/Queen/King on Hearts and Diamonds — Substeps 2.4 and 2.5), a small ⚡ label appears above the tray noting it's a base, matching how Wilson introduces Base Elements at that stage. Earlier substeps never show this label.

- Tap a tray tile to place it in the next empty slot, or tap a placed tile to send it back
- Drag-and-drop also works for both placing and removing tiles
- "Clear" empties the board; "Submit" checks the spelling once every slot is filled
- On submission, the correct spelling is always revealed on screen

### Voice
The dictation voice can be changed from the **🔊 Voice** dropdown on the home screen, or from the same dropdown inside **Setup** — both stay in sync. Your choice is saved in the browser and stays selected the next time you open the game, so you only need to set it once.

The list shows every voice your browser/OS has installed. On Mac/iOS, voices like **Samantha** tend to sound more natural than the default; on Chromebooks or Windows the available names will differ since they come from the operating system, not from this game. Use the 🔁 Replay button during a round to hear the word again as many times as needed.

### ⚡ Power Word Challenges
At random moments during play (roughly every 5–7 minutes, anytime — even mid-round), a glowing **Power Word** popup appears on screen.

**Who gets it:**
- **vs Computer**: the human player always gets it — the AI doesn't compete for popups
- **Pass & Play**: whoever is currently the active player in that round gets first crack. If the popup fires between rounds (no one's "up" yet), it's assigned randomly to either player, since there's no real way to race for a tap when only one person is looking at the screen at a time
- **Online**: whoever taps first wins the race, the same way it works for everyone sharing one screen

**The challenge:**
1. The computer dictates a high-frequency word (her, were, could, should, their, world, etc. — the original rulebook's "Bonus Cards" list)
2. The player spells it on a mini letter-tile board (plain letter tiles, not WRS-chunked, since these are irregular sight words)
3. If correct, they then pick the sentence that uses the word correctly from 2–3 multiple-choice options
4. **Success on both steps** → steal one random card from the opponent's deck **and** earn +30 XP

**Steal chance:** A miss at either step — wrong spelling, or correct spelling but the wrong sentence — passes the **same word** to the other player as a steal attempt. The stealer must complete the full challenge (spell it, then pick the right sentence) to claim the bonus; missing either step on the steal attempt simply closes the challenge with no penalty to anyone. (In **vs Computer** mode, there's no steal step, since the AI can't attempt it — a miss just closes the challenge.)

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
