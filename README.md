# Travel Log System – RCM & MRCFI (GitHub Pages + Google Apps Script)

index.html  -> goes on GitHub (the page people use)
Code.gs     -> Apps Script (the database/back end, stores everything in a Google Sheet)

## A. Apps Script (back end)
1. Open your script project. Replace ALL of Code.gs with the new Code.gs.
2. Delete the old HTML file (Index / index) – it is no longer used.
3. Project Settings > tick "Show appsscript.json" > paste the new appsscript.json > Save.
4. At the top of Code.gs change  const NEW_PASSCODE = 'CHANGE-ME';  to your own passcode (6+ characters). Save.
5. Pick `setPasscode` in the toolbar > Run > Authorize. (Log should say "Passcode saved".)
6. Pick `showDatabaseUrl` > Run. This creates your database Google Sheet.
7. Deploy > Manage deployments > pencil > Version: New version
   Execute as: Me   Who has access: Anyone   > Deploy.
   (The /exec link stays the same.)
8. Optional: run `seedFromTemplates` to load the two sample logs from your templates.

## B. GitHub (front end)
1. Open your TravelLog repository > index.html > pencil (edit) – or "Add file > Upload files".
2. Replace the old index.html with the new one > Commit.
3. Wait ~1 minute, then open https://rotaryclubofmakati.github.io/TravelLog/ and enter your passcode.

If you ever create a NEW deployment, its /exec link changes: update API_URL near the top of the <script> in index.html.

## Security notes
- "Anyone" access means the link itself is public; the passcode is what protects your data. Use a strong one.
- 10 wrong passcodes lock the system for 10 minutes. To change the passcode: edit NEW_PASSCODE and run setPasscode again.
- Your data lives only in your Google Sheet (owned by your account), never on GitHub.
