# ShareVolume

A lightweight static web app that visualizes the highest and lowest values of a company's common stock shares outstanding (XBRL tag: `dei:EntityCommonStockSharesOutstanding`) from the SEC API.

- Default company: Biogen (BIIB), CIK 0000875045
- Deployed on GitHub Pages; no server required
- Supports live override via `?CIK=XXXXXXXXXX` query parameter (10-digit CIK)

## Live data source

SEC XBRL Company Concept endpoint (example for Biogen):

```
https://data.sec.gov/api/xbrl/companyconcept/CIK0000875045/dei/EntityCommonStockSharesOutstanding.json
```

Per SEC guidance, clients should identify themselves with a descriptive User-Agent. Web browsers do not allow setting the `User-Agent` header from JavaScript. This project uses a CORS-friendly proxy (AIPipe/Jina: `https://r.jina.ai/http://…`) when fetching live data in the browser, and includes contact info in the `From` header.

## How it works

1. On initial load, the app fetches the bundled `data.json` and renders:
   - The entity name into the `<title>` and `<h1 id="share-entity-name">`
   - The maximum and minimum shares outstanding with their fiscal years into elements with IDs:
     - `share-max-value`, `share-max-fy`
     - `share-min-value`, `share-min-fy`
2. If the page URL has a `?CIK=XXXXXXXXXX` parameter, the app fetches that company's concept JSON from the SEC via a proxy, filters entries where `fy > "2020"` and `val` is numeric, computes the min/max by `val`, and updates the DOM in place (no reload).

## Repository contents

- `index.html` — Main entry point and UI
- `assets/app.js` — Client-side logic and SEC data processing
- `assets/style.css` — Styling
- `data.json` — Bundled precomputed data for Biogen
- `uid.txt` — Provided attachment (committed as-is)
- `LICENSE` — MIT License
- `.nojekyll` — Disable Jekyll on GitHub Pages

## Development & deployment

This is a static site. To develop locally, you can serve the folder with any static server (or using VS Code's Live Server extension). For GitHub Pages:

1. Create a GitHub repository and add these files.
2. Enable GitHub Pages in the repo settings (source: main branch root).
3. Visit the published URL.

### Using the app

- Open the site without parameters to view Biogen (from bundled `data.json`).
- To test another company, either:
  - Use the form on the page and submit a 10-digit CIK, or
  - Append `?CIK=0001018724` (example) to the URL.

The page updates dynamically without reloading.

## Data processing notes

- The app filters entries at `json.units.shares[]` where `val` is a number and `String(fy) > "2020"`.
- In case of ties for min/max `val`, the first occurrence wins.

## Privacy & rate limits

- Requests to the SEC API may be rate limited. The app uses a read-only proxy (`r.jina.ai`) for CORS convenience. For production or server-side fetching, identify your app with a descriptive `User-Agent` and contact email (e.g., `ShareVolume/1.0 (22f3002460@ds.study.iitm.ac.in)`).

## License

MIT — see LICENSE.
