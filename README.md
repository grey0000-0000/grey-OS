# Grey OS — Operating Dashboard

Static GitHub Pages mirror of Grey’s soft-launch board (experiments, launch tasks, decisions).

- **Live API** (Express + `data/state.json`) still runs on the box / Cloudflare tunnel when needed for multi-device sync.
- **Pages** loads `./state.json` when `/api/state` is unavailable. Product edits, task status, and experiment stage/status commit back to `state.json` on `main` when a fine-grained token is saved in that browser (see below). Other edits stay in `localStorage`. On load, a newer task or experiment status saved in this browser is kept instead of being replaced by `state.json`.
- Soft launch target: **11 Oct 2026**. Host path: Hydrogen on Shopify Oxygen (`www.greyobjects.com` live; Production public).

Published as `grey0000-0000/grey-OS`.

## GitHub write-back

GitHub Pages is static, so this repo does not contain a token. One fine-grained token commits product edits, task status, and experiment stage/status to `state.json` on `main` through the GitHub Contents API.

- **Products** — a cell change, or an added or removed row. The commit message shows the old value and the new value (`GREY-OBJ-001 title: "French Postcards" → "New title"`).
- **Task status** — the status dropdown on the task table. Only that task’s `status` is merged into `taskEdits[id]`. Notes, blocker, priority, owner, and due stay as they are.
- **Experiment stage and status** — the stage and status dropdowns. Only the changed field is merged into `edits[id]`. Other fields on that experiment, including notes, stay as they are.

Each commit fetches the latest `state.json` and its sha, merges that one field, sets `updated` to now (ISO) and `updatedBy` to `utkarsh-dashboard` (product commits still use `products-writeback`), and PUTs the file. A 409 or sha conflict retries once with a fresh fetch. Product, task, and experiment writes share one queue so they cannot overwrite each other.

### What Utkarsh configures

1. GitHub → **Settings → Developer settings → Fine-grained personal access tokens → Generate new token**.
2. Resource owner: `grey0000-0000`.
3. Repository access: **Only select repositories** → `grey-OS` only.
4. Permissions: **Contents: Read and write**. Leave every other permission at No access. Metadata read is added automatically.
5. Open the board → **Products** → **Settings** → **GitHub write-back**. Paste the token and choose **Save in this browser**. The same token covers task status and experiment stage/status.
6. The token is stored only in that browser’s `localStorage` key `grey-os-github-token`. It is not written to the repo. **Forget** removes it. Revoke the token on GitHub to cut off access.

Without a token, a task or experiment status change stays in this browser and the table says so. Reload keeps that newer status instead of reverting to `state.json`. With a token, the same change is committed to `main` and shows a short success or failure line under the table.

## Product schema

Each object in `products` is one listing row. The soft-launch list is SKU `GREY-OBJ-001` through `GREY-OBJ-017`.

| Field | Type | Notes |
| --- | --- | --- |
| `id` | string | Stable row id (`product-1` …). |
| `title` | string | Listing title. |
| `description` | string | Existing short line. When `shortDescription` is set, `description` matches it. |
| `sequence` | integer or null | Order within a collection. Empty until assigned. The column header sorts by this integer. |
| `shortDescription` | string | Short listing copy. |
| `fullDescription` | string | Long listing copy. |
| `year` | string | Date or range, free text. |
| `city` | string | |
| `origin` | string | |
| `medium` | string | |
| `dimensions` | string | |
| `saleStatus` | string | Dropdown: Collect, Collected, Make a bid, Request availability, Customize, Not for sale. More options may be added later. |
| `editionSize` | string | |
| `unit` | string | |
| `process` | string | |
| `story` | string | |
| `shippingReturns` | string | |
| `vendor` | string | |
| `collection` | string | `selection` or `one of none` on the current list. |
| `productType` | string | |
| `productReady` | boolean | |
| `tags` | string | Comma-separated. |
| `price` | string | INR. |
| `compareAtPrice` | string | INR. Empty when unset. |
| `sku` | string | |
| `barcode` | string | |
| `inventoryQty` | string | |
| `availableForSale` | boolean | |
| `weight` | string | Grams. |
| `imageSrc` | string | |
| `imageAlt` | string | |

In the Products table, `sequence` through `shippingReturns` are editable columns placed immediately after Description, in the order listed above. `description` stays in its current column.

Default row order is collection, then sequence (empty sequences last), then SKU. A cell edit on any of these fields, including the new ones, commits through the same GitHub write-back as the other product cells.
