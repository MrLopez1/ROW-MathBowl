# ABSH Math Bowl — digital invitation

Invitation page and online registration for the ABSH Math Bowl Regional
Competition hosted by Rainbow of Wisdom School, La Ceiba, Honduras.

Schools open the link, read the invitation, and confirm attendance with their
student and teacher rosters. Only the organizer can read what comes in.

No command line, no installs. The page is hosted free on GitHub Pages; the
registrations live in Firebase.

| File | What it is |
|---|---|
| `index.html` | The entire page. Nothing else to build. |
| `firestore.rules` | Who may read and write the database. You paste this into Firebase. |
| `.nojekyll` | Tells GitHub to serve the files untouched. Optional — if your computer hides dotfiles and you cannot see it, skip it; nothing here needs it. |

---

## Setup, once

### 1. Create the Firebase project

1. Go to <https://console.firebase.google.com> and click **Add project**.
   Name it something like `rowhn-mathbowl`. Google Analytics is not needed.
2. Left menu → **Build → Firestore Database → Create database**.
   Choose **Production mode** and a location (`nam5` is fine).
3. Left menu → **Build → Authentication → Get started** → choose **Google**
   in the provider list → toggle **Enable** → pick a support email → **Save**.

### 2. Publish the security rules

1. In the Firebase console open **Firestore Database** → the **Rules** tab.
2. Select everything in the editor and delete it.
3. Open `firestore.rules` from this folder, copy all of it, paste it in.
4. Click **Publish**.

This is the step that keeps student names private. Do not skip it, and do not
leave the database in test mode.

### 3. Get your Firebase settings

1. Click the gear icon → **Project settings**.
2. Scroll to **Your apps** → click the web icon `</>`.
3. Nickname it `invitation`, leave Firebase Hosting unticked, **Register app**.
4. Copy the `firebaseConfig` values it shows you.

### 4. Paste them into the page

Open `index.html` in any text editor (Notepad, TextEdit, VS Code). Near the
bottom, just after `<script type="module">`, is a block marked **CONFIG**.
Replace every `PASTE_...` value with yours:

```js
const firebaseConfig = {
  apiKey:            "AIza...",
  authDomain:        "rowhn-mathbowl.firebaseapp.com",
  projectId:         "rowhn-mathbowl",
  storageBucket:     "rowhn-mathbowl.appspot.com",
  messagingSenderId: "123456789012",
  appId:             "1:1234...:web:abcd..."
};

const ADMIN_EMAILS = ["nahum@rowhn.com"];
```

`ADMIN_EMAILS` is who may open the organizer view. Add more addresses if
somebody else needs to see the registrations — but they must also be added to
the list inside `firestore.rules`, which is the one that actually protects the
data. Change both, or the second organizer will see an empty screen.

These config values are not secrets. They name the project; they grant nothing.
Access is decided by the rules you published in step 2. It is safe for the
repository to be public.

### 5. Put it on GitHub Pages

1. On GitHub click **New repository**. Name it `mathbowl`.
   Set it to **Public** — free Pages requires it. Create.
2. On the empty repository page click **uploading an existing file**.
3. Drag in the **files** — `index.html`, `README.md`, `firestore.rules`, and
   `.nojekyll` if you can see it. Not the folder itself: `index.html` has to sit
   at the top of the repository or Pages will not find it. **Commit changes**.
4. Go to **Settings → Pages**. Under *Build and deployment* choose
   **Deploy from a branch**, branch `main`, folder `/ (root)`. **Save**.
5. Wait about a minute and refresh. The URL appears at the top, in the form
   `https://<your-username>.github.io/mathbowl/`.

### 6. Let Firebase trust that address

Google sign-in refuses to run on a domain Firebase does not know, so the
organizer view will not open until you do this.

1. Firebase console → **Authentication** → **Settings** tab → **Authorized domains**.
2. **Add domain** → type `<your-username>.github.io` → **Add**.

---

## Check it before sending the link to anyone

1. Open your Pages URL. The red *"not connected to a database"* banner must
   **not** appear. If it does, the config in step 4 was not saved.
2. Fill the form in for a made-up school and submit it.
3. Scroll to the bottom → **Organizer view** → **Sign in with Google**.
   Your test entry should be listed.
4. Open the same URL in a private window, do not sign in, and confirm the
   organizer view refuses you. This proves the rules are working.
5. Remove the test entry with its **Remove** button.

---

## Changing things later

**Event details** — date, deadline, fee, bank accounts, categories, the letter
text, the contact details: all of it is in Organizer view → *Event details*.
Saving there updates the page for every school immediately. No editing files,
no re-uploading.

**Categories** are one per line, written as
`Name | lowest grade | highest grade | description`. The grade dropdown the
schools see is built from these lines, so widening a category widens the
dropdown by itself.

**The page itself** — edit `index.html` on GitHub (open the file, click the
pencil, commit) and Pages rebuilds in about a minute.

**Registrations** are one record per school, keyed by a simplified form of the
school's name. A school that submits again with the same name replaces its own
entry instead of creating a duplicate.

**Export** — Organizer view → *Export* gives two tab-separated tables: one row
per school, and one row per student or teacher. Select, copy, paste into Excel
or Google Sheets.

---

## Worth knowing

- **Anyone with the link can register.** That suits a known circle of ABSH
  schools. There is no per-school password.
- **A school could overwrite another school's entry** by typing that school's
  name into the form. Harmless among colleagues, but take an export every week
  or two once registrations open — it doubles as your backup.
- **Nobody but an organizer can read the list**, including somebody who reads
  the page source. That is enforced by Firebase, not by the page.
- **The free tier covers this comfortably.** Firestore allows 50,000 reads and
  20,000 writes per day free; this event will use a few hundred writes in total.
- **After the competition**, consider deleting the data: Firebase console →
  Firestore Database → `registrations` → delete. Those rosters are minors' names.

## If something goes wrong

**Red banner across the top of the page.** The Firebase config in `index.html`
still has `PASTE_` values, or the file was uploaded before you edited it.

**"Sign-in did not complete" with `auth/unauthorized-domain`.** Step 6 was
skipped, or the domain was typed wrong. It is just
`<your-username>.github.io`, with no `https://` and no `/mathbowl` after it.

**Signed in, but told the account is not registered as an organizer.** The
address you signed in with is not in `ADMIN_EMAILS` in `index.html`.

**Signed in and accepted, but the list says it could not read the
registrations.** The address is in `ADMIN_EMAILS` but not in `firestore.rules`,
or the rules were never published. Redo step 2.

**A school says the form will not send.** Ask which message it showed. Anything
about a missing name, grade, or role is the form asking them to finish a row.
"That did not save" is a connection problem or unpublished rules.
