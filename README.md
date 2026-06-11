# Infrastructure Progress Layout

A single-file browser tool for tracking construction/infrastructure progress on a project layout drawing.

Load your project layout image, pick a service (roadworks, pipe laying, ducts, chambers, etc.), trace routes along the drawing, then log progress by chainage and completed activity. Each service keeps its own routes and progress on the same base drawing.

## Features

- Load any layout drawing (image) as the base
- 27 infrastructure services across disciplines (Earthworks, Roads, Storm Water, Foul Sewer, Water, Power, Telecom, etc.)
- Trace routes by clicking along the centreline, with chainage stationing
- Log progress per route, per side (LHS/RHS/Both for dual-carriageway services), per activity stage
- Colour-coded progress overlay and per-stage / overall percentage summaries
- Export the annotated drawing as PNG
- Export / import the whole board as a JSON file
- Data persists in your browser via `localStorage`

## Hosting

This is a static site — just `index.html` with everything inlined (no build step, no dependencies).

### GitHub Pages

1. Push this repo to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to *Deploy from a branch*, pick the `main` branch and the `/ (root)` folder, then **Save**.
4. Your site will be live at `https://<username>.github.io/<repo>/` within a minute or two.

## Notes

The original code was authored inside the Claude Artifacts runtime, which provides a `window.storage` API for saving. For standalone hosting, a small `localStorage`-backed shim is included so save/load works in a normal browser. Data is stored per-browser and is not shared between devices — use **Export data** / **Import data** to move a board between machines.
