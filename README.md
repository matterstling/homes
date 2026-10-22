# Home Finding

A single-file web app for evaluating and comparing homes during the Erstling family Seattle home search.

## Features

- Password-protected overlay (deterrence-level auth)
- Must-have pass/fail gates — any failed must-have eliminates the home automatically
- Below / Meets / Exceeds scoring for strong preferences and nice-to-haves
- Weighted scoring (high = 2x, low = 1x)
- Per-property notes
- Compare tab — eligible homes ranked by score, eliminated homes flagged separately
- Persistent storage via localStorage — survives page refreshes
- Mobile-responsive

## Deployment

This is a fully static single-file site. Same pattern as `draft.matterstling.com`.

### GitHub Pages (recommended)

1. Push this repo to GitHub (e.g. `homes-scorecard`)
2. Go to **Settings → Pages**
3. Set source to `main` branch, `/ (root)`
4. Point `homes.matterstling.com` to it via a CNAME record in your DNS provider:
   - Type: `CNAME`
   - Name: `homes`
   - Value: `<your-github-username>.github.io`
5. Add a `CNAME` file to the repo root containing: `homes.matterstling.com`

GitHub Pages will serve `index.html` automatically.

## Password

The default password is set in the `<script>` block near the top:

```js
const PASSWORD = 'erstling2025';
```

Change it to whatever you'd like before deploying. Since this is client-side only, it's deterrence-level protection — sufficient for keeping the page off casual visitors.

## Updating criteria or weights

All criteria and weights are defined as arrays near the top of the `<script>` block:

- `mustHaves` — binary pass/fail gates
- `scored` — Below/Meets/Exceeds items with `weight: 'high'` or `weight: 'low'`

Add, remove, or reorder entries freely. Saved property data uses the `id` field as the key, so renaming an `id` will lose that criterion's saved scores for existing properties.

## Local development

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

## Move + Rental tab

The first tab is a launcher for the move and rental tracker, a Google Sheet ("Seattle Move + Rental"). Its link is the `#sheetLink` button in the HTML.

- It's a plain `docs.google.com` link. On a phone, iOS and Android open it in the Google Sheets app when it's installed and fall back to the browser otherwise. On phones and tablets the link opens in the same tab (a plain tap is what reliably hands it to the app); on computers it opens a new tab
- The sheet used to be embedded in an iframe, but browsers that block third-party cookies (Safari on iPhone) can't sign in to Google inside a frame, and Sheets in a phone browser is view-only anyway
- Access follows the sheet's own Google sharing settings
- The tracker used to be built into this page (Oct 21–22, 2026). Its last saved state is still in `data.json` under `move`, which the page leaves untouched when it saves homes
