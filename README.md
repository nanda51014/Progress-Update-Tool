# Infrastructure Progress Layout

Log construction progress by chainage, directly on the project layout drawing. A single-file web tool for roads and infrastructure sites: trace or auto-detect your alignments, record each completed activity from CH to CH (LHS / RHS / both), and share one live board across the team — covering 30 wet, dry, road and structures service layers.

**Rev 03 · 02-Jul-26**

## Repo contents

| File | What it is |
|---|---|
| `index.html` | Landing page (served automatically by GitHub Pages) |
| `app.html` | The tool itself — one self-contained HTML file |
| `database.rules.json` | Starter Firebase Realtime Database rules |
| `README.md` | This file |

## Quick start (local, no setup)

Open `app.html` in a browser. With no Firebase config, the tool runs in **local mode**: everything is saved in that browser's localStorage. No accounts, no server, works offline. Full functionality except multi-user sync.

## Deploy on GitHub Pages

1. Fork/clone this repo.
2. Repo → Settings → Pages → deploy from the `main` branch, root folder.
3. `https://<user>.github.io/<repo>/` serves the landing page; the tool is at `/app.html`.

## Team sync (Firebase)

Shared live boards need a free Firebase Realtime Database:

1. [Firebase console](https://console.firebase.google.com) → create a project → Build → **Realtime Database** → create.
2. Project settings → Your apps → add a **Web app** → copy the SDK config object.
3. Paste the values into the `firebaseConfig` block near the top of `app.html` (replacing the `PASTE_…` placeholders).
4. **Apply database rules before sharing the link** — see below.

Everyone opening the same URL shares the same board. Separate projects live under URL hashes (`app.html#r2200`, `app.html#sector-6`) and are switchable from the toolbar's project menu.

## Securing the shared board — read this before publishing

The Firebase config values in `app.html` are **public by design**; hiding them provides no security. Access control lives entirely in your **database rules**. If you publish this page with the Firebase console's default open rules, anyone who finds the URL can read and overwrite every board.

`database.rules.json` in this repo is a minimal starter: it confines the app to the `boards/` branch and caps write size. Paste it in Firebase console → Realtime Database → Rules → Publish.

For anything beyond a small trusted team, tighten further — options in increasing order of effort:

- **Unguessable board IDs**: use a long random hash per project (`app.html#r2200-x7k2p9…`) instead of guessable names. Cheap, and stops casual discovery, but the data is still world-writable to anyone with the link.
- **Firebase Anonymous Auth + `auth != null` rules**: one-line sign-in, blocks raw REST scraping.
- **Email/Google sign-in with an allow-list**: proper access control; requires adding a small auth flow to the app.

Also note: whatever drawing you load is stored (as an image) in the database. Don't put confidential drawings on a board whose rules you haven't locked down.

## Using the tool

1. **Services…** — tick the services in this project's scope; only those appear in the Layout selector.
2. **Load drawing** — image or PDF (first page rendered). Each service layer can carry its own drawing; the base drawing is the fallback.
3. **+ Add route** — name, start/end chainage, then click along the centreline (`Enter` finishes, `Esc` cancels). Or **Auto-detect routes**: pick the network line colour on the drawing and review what it finds; OCR reads chainage labels best-effort.
4. **Update progress** — route, side, from/to chainage (typed or picked on the drawing), completed activity. Progress is stage-weighted: each metre earns credit for every stage it has passed.
5. **Outputs** — annotated PNG with title strip and legend, printable report (all-layers summary + active-layer detail + full log), CSV of every entry across all layers.

Editing/Viewing mode toggle protects the board from accidental edits; every route and entry is attributed by name.

## Rev history

**Rev 03 — 02-Jul-26**
- Pinch-zoom on touch devices (tablet/site use); two-finger gesture alongside wheel zoom and drag pan.
- Keyboard: `Esc` cancels tracing/picking/eyedrop or closes the top modal; `Enter` finishes a trace.
- Delete-entry now asks for confirmation (was one click, permanent, on a shared board).
- PNG export gains a title strip: layer, overall %, board, cut-off date, author, stage legend — deck-ready without editing.
- All user-facing dates in DD-MMM-YY (log, report, CSV); ISO retained internally and in filenames for sorting.
- Update-progress dialog shows the selected route's chainage range.
- Rev/date marker in the toolbar.
- Firebase config replaced with placeholders for public release; security guidance and starter rules added.

**Rev 02** — per-service drawings, auto-detect routes (colour + OCR), project scope picker, multi-project boards via URL hash, edit/view modes, installed-quantity targets, all-layer PDF report, CSV export.

**Rev 01** — base tool: trace routes, chainage-based stage logging, LHS/RHS carriageway split, shared Firebase sync, PNG export.

## License

MIT — use it on your jobs.
