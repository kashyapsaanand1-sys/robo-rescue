# AISC Robo Rescue

Files: `index.html` (whole game), `Code.gs` (Google Apps Script backend), `README.md`.

## How saving works (and why it is honest)
The page sends a `POST` (`Content-Type: text/plain`, so no CORS preflight) to your Apps Script Web App and **reads the JSON reply**. Apps Script web apps answer cross-origin reads for this kind of request, so the page only shows "SAVED" when the server returns `{ok:true}`. No `no-cors` is used. Any failure (bad URL, timeout, validation error) shows "NOT SAVED" with a retry button. The server de-duplicates by ticket ID, so retries never create duplicate rows. The registration is also kept in the browser (`localStorage`), so "MY TICKETS" on the title screen restores tickets and retry after a closed tab.

## Setup (10 minutes)
1. Create a new Google Sheet. Open **Extensions > Apps Script**, delete the sample code, paste all of `Code.gs`.
2. Change `ADMIN_KEY` to your own secret. Save.
3. Select function `setup`, click **Run**, approve permissions (creates the 3 tabs).
4. Select `healthCheck`, Run, check **Execution log** shows row counts.
5. Select `testRegistration`, Run. The log must show a first send `ok:true` and a second send `duplicate:true`. It deletes its own test rows afterwards.
6. **Deploy > New deployment > Web app**. Execute as: **Me**. Who has access: **Anyone**. Deploy and copy the URL ending in `/exec`.
7. In `index.html`, find `CFG={ENDPOINT:'PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE'}` and paste the URL.
8. After any later `Code.gs` edit: **Deploy > Manage deployments > Edit > New version**.

## GitHub Pages
Upload `index.html` to the root of `kashyapsaanand1-sys/robo-rescue` (replace the old file), commit, and wait for Pages to rebuild: https://kashyapsaanand1-sys.github.io/robo-rescue/

## Admin / test mode
Open `...?admin=1` or tap the "AISC ROBO RESCUE" heading 5 times. Options: sheet health check (read-only), ticket tests for 1/2/3 players and a test play-through (none of these write to the Sheet), and Excel export (asks for `ADMIN_KEY`, downloads `.xlsx` with Master List / Teams / Single Players, USNs as text).

## Verify a real registration
Play the game with a real (or your own) registration, wait for the green "REGISTRATION SAVED" box, then check MASTER LIST (one row per player) and TEAMS or SINGLE PLAYERS. Delete your own test rows afterwards.

## Notes
- Fonts load from Google Fonts and SheetJS from cdnjs; if offline, the game still runs with fallback fonts but Excel export needs internet.
- Never put your Sheet ID or `ADMIN_KEY` in `index.html`.
