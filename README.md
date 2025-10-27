# ShareVolume — Biogen (CIK 0000875045)

This static site summarizes the SEC XBRL concept EntityCommonStockSharesOutstanding for Biogen. It includes a local data.json fallback and supports loading other CIKs via the query string (e.g. ?CIK=0001018724).

Files:
- index.html — main page (dynamic fetch and DOM update)
- style.css — styles
- script.js — fetch logic and DOM updates
- data.json — fallback data for Biogen
- uid.txt — provided attachment identifier
- LICENSE — MIT license

Usage:
- Open index.html: it will try to fetch the SEC JSON for CIK 0000875045 and fall back to the bundled data.json.
- Open index.html?CIK=0001018724 to attempt loading another company's shares outstanding (uses a proxy when necessary).
