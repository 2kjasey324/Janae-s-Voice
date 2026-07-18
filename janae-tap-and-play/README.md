# Janae's Voice — Tap & Play Reader (Web App)

This is the Phase 4 deliverable from the project plan: a small static
web app with two screens —

- **`index.html`** — the full-screen kiosk view Janae taps cards on.
- **`admin.html`** — a PIN-protected settings page for connecting
  Spotify and mapping each NFC card to a song, playlist, or spoken
  phrase.

No build step, no server-side code. It's plain HTML/CSS/JS so it can
be hosted for free on GitHub Pages, exactly as Phase 3 describes.

## Important assumption about the NFC reader

Safari on iPad doesn't support the browser APIs (Web NFC, Web Serial,
Web HID) that would let a web page talk to a USB card reader directly.
To get around that, this app assumes the reader is set to **keyboard
emulation / "keyboard wedge" mode** — meaning when a card is tapped,
the reader just "types" the card's tag ID followed by Enter, like
someone typed it on a keyboard. Many USB NFC/RFID readers used in
kiosks (including most configurations of the ACR1552U) support this
mode. Check the reader's manual for how to turn it on.

If your reader can only talk over raw USB serial/HID and can't do
keyboard emulation, let me know — the fix is limited to one function
(`listenForReaderInput` in `app.js`) and everything downstream (looking
up the tag, playing music, speaking a phrase) stays the same.

## 1. Set up Spotify

1. Go to <https://developer.spotify.com/dashboard> and log in with
   the Spotify Premium account Janae's music will play from.
2. Click **Create app**.
   - App name: anything, e.g. "Janae's Voice"
   - Redirect URI: you'll fill this in *after* step 2 below, once you
     know your GitHub Pages URL. You can add it later by editing the
     app's settings.
   - Check the box for the **Web API**.
3. Once created, copy the **Client ID** shown on the app's dashboard
   page. You'll paste this into the Settings page later.

## 2. Host it on GitHub Pages

1. Create a new GitHub repository (can be private or public).
2. Upload every file in this folder to the repository (keep them all
   in the root, or all in the same subfolder — just keep them
   together).
3. In the repo, go to **Settings → Pages**, set the source to the
   branch you uploaded to, and save. GitHub will give you a URL like:

   ```
   https://yourusername.github.io/your-repo-name/
   ```

4. Go back to your Spotify app's dashboard settings and add this as a
   **Redirect URI**:

   ```
   https://yourusername.github.io/your-repo-name/admin.html
   ```

   (The Settings page in the app will also show you this exact value
   once you open it, under "Redirect URI to register".)

## 3. Configure the app

1. Open `admin.html` on the hosted site (e.g.
   `https://yourusername.github.io/your-repo-name/admin.html`).
2. The default PIN is **1234** — change it right away under
   "Settings PIN".
3. Paste your Spotify Client ID and click **Save Client ID**, then
   **Connect Spotify**. Log in and approve access.
4. Open the **Spotify app itself on the iPad** and make sure it's
   logged into the same account. The web app doesn't play audio
   itself — it tells the Spotify app what to play, so that app needs
   to be open (playing something, even paused, works fine).

## 4. Add your cards

For each NFC card:

1. Click **+ Add card** in Settings.
2. Tap the card on the reader — its Tag ID should fill in
   automatically. (If it doesn't, the reader may not be in keyboard
   emulation mode — see the note above.)
3. Give it a label and an emoji icon (used on the kiosk reveal
   screen).
4. Choose **Song**, **Playlist**, or **Spoken phrase**:
   - For Song/Playlist, paste a share link from Spotify (right-click
     or long-press a track/playlist → Share → Copy link). Both
     `open.spotify.com/...` links and `spotify:...` URIs work.
   - For a spoken phrase, type the words to be spoken aloud.
5. Click **Test** to try it immediately, then **Save card**.

## 5. Set up the iPad kiosk

1. Open `index.html` on the iPad in Safari, in full screen (add it to
   the Home Screen for a fuller-screen feel — Share → Add to Home
   Screen).
2. Turn on **Guided Access** (Settings → Accessibility → Guided
   Access) and start it on this page, so Janae can't accidentally
   navigate away.
3. Tap a card to test. The screen should light up with that card's
   color, icon, and label, and either start playback in Spotify or
   speak the phrase out loud.

Adding `?debug=1` to the kiosk URL shows a small text readout in the
bottom-left corner with the last tag ID seen — handy for the Phase 1
"verify the reader" testing without needing the browser console.

## Where things are stored

Card mappings, the Spotify connection, and the settings PIN are all
stored in the browser's local storage on whichever device opens
`admin.html` and `index.html`. For the real station, that means doing
setup directly on the iPad (or exporting cards from a laptop and
importing them into the iPad's browser via the Backup section in
Settings).

## Files

| File | Purpose |
|---|---|
| `index.html` / `app.js` | Kiosk screen Janae interacts with |
| `admin.html` / `admin.js` | Settings screen for setup |
| `spotify.js` | Spotify login and playback control |
| `cards.js` | Saves/loads the tag → action mappings |
| `style.css` | Shared visual design for both screens |
