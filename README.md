# Infrastructure Progress Layout

A single-file browser tool for tracking construction/infrastructure progress on a project layout drawing.

Load your project layout image, pick a service (roadworks, pipe laying, ducts, chambers, etc.), trace routes along the drawing, then log progress by chainage and completed activity. Each service keeps its own routes and progress on the same base drawing.

## Features

- Load any layout drawing (image) as the base
- Layered **Layout** selector — drill down by category (Earthworks, Wet Services, Dry Services, Road Works, Structures) → type (Pipe Laying, Chambers, Cable/Duct, Carriageway, Footpath…) → discipline (Storm Water, Sewer, CCTV, Street Lighting…)
- **Per-project service scope** — when a project is created you tick which services it actually contains (road, storm water, sewer, bridge…); only those appear in the Layout selector. Change the scope anytime via **Services…**
- Trace routes by clicking along the centreline, with chainage stationing
- Log progress per route, per side (LHS/RHS/Both for dual-carriageway services), per activity stage
- Colour-coded progress overlay and per-stage / overall percentage summaries
- **Installed quantity vs. target** per layer — set a target (e.g. 2,400 m of pipe, 35 manholes) and see how much is fully complete against it
- **Roles & audit trail** — a per-browser View/Edit mode (viewers can't change data), and every progress entry is stamped with who logged it
- **Reporting** — one-click printable PDF report (summary, per-activity/route tables, annotated drawing, progress log) and a CSV export of all entries for Excel
- Export the annotated drawing as PNG
- Export / import the whole board as a JSON file
- **Shared multi-user syncing** via Firebase — everyone on the same board sees each other's routes and progress
- Falls back to per-browser `localStorage` when no backend is configured

## Hosting

This is a static site — just `index.html` with everything inlined (no build step, no dependencies).

### GitHub Pages

1. Push this repo to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to *Deploy from a branch*, pick the `main` branch and the `/ (root)` folder, then **Save**.
4. Your site will be live at `https://<username>.github.io/<repo>/` within a minute or two.

## Shared multi-user syncing (Firebase)

To let multiple people share the same live board, the tool uses a **Firebase Realtime Database** backend. It's free for this scale and takes about 5 minutes to set up. Until you add a config, the tool runs in local-only mode (per-browser, no sharing).

### 1. Create a Firebase project

1. Go to <https://console.firebase.google.com/> and **Add project** (any name). You can skip Google Analytics.
2. In the left sidebar open **Build → Realtime Database → Create Database**.
   - Pick a location.
   - Start in **test mode** (or use the rules below).

### 2. Set the database rules

In **Realtime Database → Rules**, paste this and **Publish**:

```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

> ⚠️ These rules make the board **publicly readable and writable by anyone who has your site URL**. That's fine for an internal team tool, but don't put confidential data in it. To lock it down later, add Firebase Authentication and tighten the rules.

### 3. Get your web config

1. In **Project settings** (gear icon) → **General** → scroll to **Your apps** → click the **Web** icon (`</>`) to register a web app (any nickname; you do *not* need Firebase Hosting).
2. Copy the `firebaseConfig` object it shows you.

### 4. Paste it into `index.html`

Near the top of `index.html` find the `const firebaseConfig = { ... }` block and replace the `PASTE_…` placeholders with your real values (make sure `databaseURL` is included — it's the `https://…firebaseio.com` one). Commit and push.

That's it — open the published site in two browsers and you'll see progress sync between them.

### Multiple projects (separate boards)

The tool is reusable across many projects from the **same hosted site** — no need to copy or redeploy. Each project is an isolated shared board.

Use the **📁 project switcher** in the toolbar (next to the title) to:

- See which project you're currently in
- **Create a new project** (starts completely blank — your own "raw" copy)
- Jump between projects you've opened before

Under the hood each project is just a URL hash, so you can also share a direct link to a specific project:

- `https://<user>.github.io/<repo>/` — the **Default project**
- `https://<user>.github.io/<repo>/#marina-towers`
- `https://<user>.github.io/<repo>/#site-b`

Everyone who opens the **same link** collaborates on that board; different projects never mix. Share each project's link with the people who should work on it. The switcher remembers projects per-browser; removing one from the list does **not** delete the shared board — anyone with the link can still open it.

## How sync behaves

- Changes you make are written to the cloud immediately.
- Other users' changes appear within about 10 seconds (the app polls), or instantly when they press **Sync**.
- A `localStorage` mirror is kept as an offline fallback, and **Export data / Import data** still works for manual backups or moving a board as a file.

## Notes

The original code was authored inside the Claude Artifacts runtime, which provides a `window.storage` API for saving. For standalone hosting that API is reimplemented on top of Firebase (shared) with a `localStorage` fallback (local), so the same save/sync logic works on a normal website.
