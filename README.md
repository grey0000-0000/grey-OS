# Grey OS — Operating Dashboard

Static GitHub Pages mirror of Grey’s soft-launch board (experiments, launch tasks, decisions).

- **Live API** (Express + `data/state.json`) still runs on the box / Cloudflare tunnel when needed for multi-device sync.
- **Pages** loads `./state.json` when `/api/state` is unavailable. Product-cell edits commit back to `state.json` on `main` when a fine-grained token is saved in that browser (see below). Other edits stay in `localStorage`.
- Soft launch target: **11 Oct 2026**. Host path: Hydrogen on Shopify Oxygen (`www.greyobjects.com` live; Production public).

Published as `grey0000-0000/grey-OS`.

## Products write-back

GitHub Pages is static, so this repo does not contain a token. The Products table commits a cell change to `state.json` on `main` with the GitHub Contents API. The commit message and the diff both show the old value and the new value (`GREY-OBJ-001 title: "French Postcards" → "New title"`).

### What Utkarsh configures

1. GitHub → **Settings → Developer settings → Fine-grained personal access tokens → Generate new token**.
2. Resource owner: `grey0000-0000`.
3. Repository access: **Only select repositories** → `grey-OS` only.
4. Permissions: **Contents: Read and write**. Leave every other permission at No access. Metadata read is added automatically.
5. Open the board → **Products**. Paste the token into **GitHub write-back** and choose **Save in this browser**.
6. The token is stored only in that browser’s `localStorage` key `grey-os-github-token`. It is not written to the repo. **Forget** removes it. Revoke the token on GitHub to cut off access.

Each product edit reads `state.json` from `main`, changes that one field (or adds/removes that row), and commits the result. Without a token, the edit stays in this browser only.
