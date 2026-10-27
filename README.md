# RCM Admin Systems Portal – Setup Tutorial (Apps Script + Google Sheet)

**You do NOT need Firestore/Firebase anymore.** The Google Sheet is now the database, and your Apps Script
(`.../exec` URL) is the backend that checks who is allowed in. Ignore/delete `firestore.rules` if you downloaded it earlier.

How it works:
Google sign-in (in the browser) → portal sends the Google ID token to your Apps Script → script verifies it with Google,
looks the email up in the **Users** sheet → returns role + allowed systems → portal shows the tabs.

---

## Step 1. Create the Google Sheet (the user list)
1. Go to https://sheets.google.com → **Blank spreadsheet** → name it `RCM Portal Users`.
2. Open **Extensions → Apps Script**. (Opening Apps Script from the Sheet binds them together.)

> Your existing script URL was made earlier. Easiest path: open that same script, and in `Code.gs` set
> `const SHEET_ID = 'the-id-from-your-sheet-url'` (the long text between `/d/` and `/edit`).

## Step 2. Get a Google OAuth Client ID (free, 5 min)
1. https://console.cloud.google.com → create/select a project (e.g. `RCM Portal`).
2. **APIs & Services → OAuth consent screen** → User type **External** → fill app name `RCM Admin Portal`, your email → Save.
   Then **Publish app** (so any Gmail on your list can sign in, not only test users).
3. **APIs & Services → Credentials → Create credentials → OAuth client ID** → type **Web application**.
4. Under **Authorized JavaScript origins** add: `https://rotaryclubofmakati.github.io`
   (add `http://localhost:8000` too if you want to test locally).
5. **Create** → copy the **Client ID** (ends in `.apps.googleusercontent.com`).

## Step 3. Paste the backend code
1. In Apps Script, replace everything in `Code.gs` with the provided `Code.gs`.
2. Edit the 3 settings at the top:
   - `CLIENT_ID` → the Client ID from Step 2
   - `FIRST_ADMIN` → the Gmail of the first administrator (lowercase)
   - `SHEET_ID` → leave `''` if you opened Apps Script from the Sheet; otherwise paste the Sheet ID
3. Pick the function **setup** in the toolbar dropdown → **Run** → approve the permissions
   (Advanced → Go to project → Allow). This creates the `Users` tab and adds the first admin.

## Step 4. Deploy (keeps your same URL)
1. **Deploy → Manage deployments → ✏ Edit** on your existing deployment.
2. **Version: New version**. Settings: **Execute as: Me**, **Who has access: Anyone**. → **Deploy**.
   (Security is handled by the Google token check inside the script, so "Anyone" is required for the portal to reach it.)
3. Test: open your `/exec` URL in a browser; you should see `{"ok":true,"service":"RCM Admin Portal API"}`.

> Every time you change `Code.gs`, repeat this step with **New version** or the live URL keeps running the old code.

## Step 5. Configure and host the portal
1. In `index.html`, set `CLIENT_ID` (same value as Step 3). `API_URL` is already filled with your URL.
2. Create a repo in your GitHub org (e.g. `AdminPortal`), upload `index.html`, then **Settings → Pages → Deploy from branch `main` / root**.
3. Open `https://rotaryclubofmakati.github.io/AdminPortal/` and sign in with the first admin's Google account.

## Step 6. Day-to-day use
- Admin opens **⚙ Manage Users** → type a Gmail → choose Member/Admin → tick which systems they can open → **Add user**.
- To block someone: **Disable** (keeps the record) or **Remove**.
- You can also edit the **Users** sheet directly. Columns: `email, role (admin/member), active (TRUE/FALSE), apps (social,nametag,travel), …`.
- Add a new system later: add a line to `APPS` in `index.html` (its `id` is what goes in the `apps` column).

## Troubleshooting
| Message | Fix |
|---|---|
| "Cannot reach the server" | Deployment must be **Anyone**, and you must have deployed a **New version** |
| "not authorized" | Email isn't in the Users sheet, or `active` is FALSE |
| Google button missing / error 400 `origin_mismatch` | Add `https://rotaryclubofmakati.github.io` to Authorized JavaScript origins |
| "Your sign-in expired" | Google tokens last ~1 hour; just sign in again |
| Tool tab is blank | That site may block embedding; use **Open in new tab ↗** (your github.io sites normally embed fine) |

## Security note
The portal controls who can open the portal and which tabs they see. The three tools are still public
GitHub Pages URLs, so someone with a direct link can open them outside the portal.
