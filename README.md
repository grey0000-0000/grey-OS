# Grey OS — Operating Dashboard

Static GitHub Pages mirror of Grey’s soft-launch board (experiments, launch tasks, decisions).

- **Live API** (Express + `data/state.json`) still runs on the box / Cloudflare tunnel when needed for multi-device sync.
- **Pages** loads `./state.json` when `/api/state` is unavailable; browser edits fall back to `localStorage` only (not shared across devices).
- Soft launch target: **11 Oct 2026**. Host path: Hydrogen on Shopify Oxygen (preview live; production deploy pending).

Published as `grey0000-0000/grey-OS`.
