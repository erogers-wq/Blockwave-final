# Blockwave — full setup from scratch

Six files. No build step, no server, nothing installed on your computer.

```
index.html            the whole game
manifest.json         Home Screen metadata
service-worker.js     clears any old cached copy
supabase-setup.sql    run once in Supabase (do not upload)
SETUP.md              this file (do not upload)
icons/
  apple-touch-icon-180.png
  icon-192.png
  icon-512.png
  icon-512-maskable.png
```

---

# PART 1 — Supabase (the shared leaderboard)

Skip this if you only want local scores; the game works without it.

### 1. Make the project
1. Go to **supabase.com** → sign in → **New project**
2. Name it `blockwave`, set any database password, pick the closest region
3. Wait ~1 minute for it to finish

### 2. Make the table
1. Left sidebar → **SQL Editor** → **New query**
2. Open `supabase-setup.sql`, copy everything, paste it in
3. Press **Run**. "Success. No rows returned" is correct.

### 3. Get your two public values
1. Left sidebar → **Project Settings** → **Data API**
2. Copy the **Project URL** — `https://xxxxx.supabase.co`
3. Copy the key marked **anon** / **public** / **publishable**

> Never copy the `service_role` or `secret` key. The public one is meant to
> be shared and is safe in a repository.

### 4. Put them in the game
Open `index.html` in any text editor. The **first thing** near the top of
the body is:

```html
<script>
window.BLOCKWAVE_CONFIG = {
  SUPABASE_URL:      "https://xxxxx.supabase.co",
  SUPABASE_ANON_KEY: "your-public-key"
};
</script>
```

Replace the two values, keeping the quotes and commas. That is the only
edit in the entire file.

---

# PART 2 — GitHub

### Cleanest option: a brand new repository
Strongly recommended if the old one has been misbehaving — a new address
means no old cached copy can possibly load.

1. **github.com** → **New repository**
2. Name it `blockwave`, set **Public**, tick **Add a README**, **Create**
3. **Add file → Upload files**
4. Drag in `index.html`, `manifest.json`, `service-worker.js` **and the
   whole `icons` folder**
5. **Commit changes**
6. **Settings → Pages** → Source: **Deploy from a branch** → Branch:
   **main**, folder: **/ (root)** → **Save**
7. Wait 1–2 minutes. The URL appears at the top of that page:
   `https://YOURNAME.github.io/blockwave/`

Do **not** upload `SETUP.md` or `supabase-setup.sql` — they are for you.

### Replacing files in your existing repository
1. Delete the old `index.html`, `manifest.json`, `service-worker.js` and
   the old `icons` folder first
2. Commit that deletion
3. Then upload the new files and commit again

Deleting first matters: uploading over the top can silently leave old files
behind.

---

# PART 3 — Check it worked

Open your Pages URL in **Safari** on your iPhone.

1. **Bottom-left corner should read `BLOCKWAVE v8`.**
   If it doesn't, you are seeing an old cached file — pull down to refresh,
   or add `?x=1` to the end of the URL.
2. You should land on the **menu**, not in a game.
3. The leaderboard header should say **ONLINE** in green after a few seconds.
4. Set a name (required), play a game, let it finish.
5. In Supabase → **Table Editor → blockwave_scores** you should now see a row.

### Add it to the Home Screen
Safari → **Share** → **Add to Home Screen** → **Add**.

### Test with a second phone
Open the same URL, set a different name, play a game. Both names should
appear on both phones. Standings refresh every 10 minutes, or immediately
via Settings → Online play → **Sync**.

---

# Notes

- **Progress is per-device.** Best score, level, prestige and unlocks live in
  that phone's browser storage, so everyone keeps their own save. Only the
  leaderboard is shared.
- **Caching is off in this build.** The service worker actively removes any
  previously cached copy. That costs offline play for now, in exchange for
  updates always showing up immediately.
- **Nothing runs on your computer.** GitHub serves the files, Supabase holds
  the leaderboard.
