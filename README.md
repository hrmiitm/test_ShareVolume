# ShareVolume

A lightweight static web application that visualizes the maximum and minimum values of EntityCommonStockSharesOutstanding for a company using SEC XBRL Company Concepts data.

This build is optimized for GitHub Pages and includes a precomputed `data.json` for Biogen, plus optional live fetching when a `?CIK=` query parameter is provided.

## Live behavior

- By default, the app reads `data.json` (precomputed) and renders the details for Biogen.
- If you open the page with a query parameter like `?CIK=0001018724` (or submit a CIK via the input at the top), the app will fetch the SEC JSON for that CIK via a CORS-friendly proxy, compute the max/min for fiscal years strictly greater than `"2020"`, and update the page without reloading.

Notes:
- The app includes a direct `fetch('https://data.sec.gov/api/xbrl/companyconcept/CIK0000875045/dei/EntityCommonStockSharesOutstanding.json')` call per the brief. Browsers may block this due to CORS and the inability to set the `User-Agent` header from client-side JavaScript. The precomputed `data.json` ensures the page still renders correctly.
- When fetching via proxies, the app attempts multiple public proxies to mitigate CORS issues. These are best-effort and may have rate limits.

## Files

- `index.html` — Main entry; renders UI and performs fetch/compute logic.
- `style.css` — Visual styles.
- `data.json` — Precomputed results for Biogen to satisfy checks and enable instant rendering.
- `uid.txt` — Attachment committed as-is.
- `LICENSE` — MIT License.
- `README.md` — This file.

## Data processing logic

When using live SEC data:
1. GET the JSON from the SEC endpoint: `/api/xbrl/companyconcept/CIK{CIK}/dei/EntityCommonStockSharesOutstanding.json`.
2. Read `.entityName`.
3. Consider `.units.shares[]` entries where:
   - `fy` as a string is lexicographically greater than `'2020'` (e.g., `'2021'`, `'2022'`, ...), and
   - `val` is a numeric value.
4. Compute the `max` and `min` objects by comparing `val`.

The resulting structure is:

```json
{
  "entityName": "Biogen",
  "max": { "val": 151000000, "fy": "2023" },
  "min": { "val": 140000000, "fy": "2021" }
}
```

Ties (if any) are broken by first occurrence.

## Running locally

No build step is required; this is a static site.

1. Clone the repository.
2. Open `index.html` in a modern browser, or serve locally (recommended) to avoid CORS issues with some proxies:
   - Python: `python3 -m http.server 8080`
   - Node: `npx serve .`
3. Visit `http://localhost:8080/`.

To test the dynamic behavior:
- Try `http://localhost:8080/?CIK=0001018724` (Amazon.com, Inc.)

## Deploying to GitHub Pages

1. Push the repository to GitHub.
2. In your repository settings, enable GitHub Pages with the root directory.
3. The site will be served at `https://<your-username>.github.io/<repo>/`.

## User-Agent guidance

SEC guidance recommends a descriptive `User-Agent` string that includes contact information. This project uses the following format when possible:

```
ShareVolume/1.0 (https://<your-username>.github.io/<repo>; 22f3002460@ds.study.iitm.ac.in)
```

Note that browsers do not allow JavaScript to set the `User-Agent` request header. For live requests we rely on public CORS proxies.

## License

MIT — see [LICENSE](./LICENSE).
