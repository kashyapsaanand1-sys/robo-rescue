# AISC Robo Rescue — Firebase database + admin dashboard setup

## What this build does
- Uses the AISC logo you supplied. Tap the logo at the top **7 times within 1.8 seconds** to open Admin Login.
- Saves one record per participant only after the player defeats the basic-round boss and the event ticket screen is generated.
- Uses Cloud Firestore for persistent registrations. GitHub Pages itself is static hosting and is not a writable database.
- Admin dashboard shows participant count, registration count, team count, latest 25 records, refresh, and CSV export.
- The dashboard reads from Firestore only after Firebase Authentication login. The password is not embedded in the HTML.

## 1. Create the Firebase project
1. Open https://console.firebase.google.com/ and create a project, e.g. `aisc-robo-rescue`.
2. In Project settings → General → Your apps, add a **Web app**. Register it and copy the Firebase web config.
3. In Build → Firestore Database, create a database. Choose production mode and a region close to India if available.
4. In Build → Authentication → Get started → Sign-in method, enable **Email/Password**.
5. In Authentication → Users, add one user:
   - Email: `saanandthegreat@aisc-robo-admin.com`
   - Password: choose the admin password in Firebase Console. Do not put it in the HTML or GitHub.
   - If the password previously shared in chat is used anywhere else, change it and use a unique password here.

## 2. Add the Firebase web config to the HTML
Open `robo_rescue_firebase_admin.html` in a text editor. Find:
`window.FIREBASE_CONFIG={apiKey:"PASTE_API_KEY",authDomain:"PASTE_PROJECT_ID.firebaseapp.com",projectId:"PASTE_PROJECT_ID",appId:"PASTE_APP_ID"};`

Replace the four placeholder values with the exact `apiKey`, `authDomain`, `projectId`, and `appId` from Firebase Console → Project settings → Your apps → SDK setup and configuration → Config.

These are public client configuration values, not admin secrets. Never put a service-account JSON, private key, or database admin credential in this HTML.

## 3. Set Firestore security rules
In Firebase Console → Firestore Database → Rules, replace the rules with the contents of the included `firestore.rules` file, then click Publish.

The rules allow the public game to **create** validated completed ticket records. They do not allow public reads, listing, updates, or deletes. Only the authenticated admin email above can read registrations. Do not loosen these rules to `allow read, write: if true`.

## 4. Publish on GitHub Pages
1. In your `robo-rescue` GitHub repository, replace `index.html` with the contents of `robo_rescue_firebase_admin.html`.
2. Commit the change and wait for GitHub Pages to publish.
3. Open your GitHub Pages URL in a fresh browser tab. Use the HTTPS site, not a local file preview.

## 5. Test before the event
1. Play through the basic mission using clearly marked TEST details and defeat the basic boss.
2. Confirm the ticket screen says the registration was saved.
3. In Firebase Console → Firestore Database → Data, confirm a `registrations` collection and one document per participant.
4. Tap the AISC logo at the top 7 times quickly. Log in using username `saanandthegreat` and the password you created in Firebase Authentication.
5. Confirm the dashboard counts and download CSV.
6. Delete test documents from Firestore Console before opening real registrations.

## Important notes
- A static GitHub Pages site cannot securely store a writable database by itself. Firestore is the database; GitHub Pages only hosts the HTML.
- The username is mapped in client code to the Firebase Auth email. The password is verified by Firebase Authentication; it is not encrypted in or stored by the page.
- Do not rely on hiding the admin button or minifying HTML for security. Firestore rules are the actual access control.
- Public create access can still be abused by automated bots. For a public event, enable Firebase App Check (Web) and consider adding server-side validation / rate limiting with Cloud Functions if abuse becomes a concern.
- The game stores participant phone numbers and USNs. Restrict access to the Firebase project, export CSV carefully, and delete records when no longer needed.
- If the save status says database not configured, the Firebase config placeholders were not replaced. If it says permission denied, check that Firestore rules are published and the admin email exactly matches the Authentication user.
