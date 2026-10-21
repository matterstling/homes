# Home Scorecard

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

## Move checklist tab

The **Move** tab is a shared checklist for the November 2026 move and turning the current house into a rental. It sits behind the same password and syncs through the same Worker and `data.json`.

- Task state is stored under `move.tasks` in `data.json`, keyed by task id: `{ done, owner, doneAt, statusNote, noteAt, updatedAt }`
- Each task has a status note: **+ Add note** / **Edit note** opens an editor, **Save note** (or ⌘/Ctrl + Enter) saves, **Cancel** (or Esc) discards. Saving an empty note removes it. Unsaved drafts survive re-renders, and the page warns before closing with one open
- Owners are `matt` or `lauren` (edit `MOVE_OWNERS` to change names); tap an owner again to unassign
- Tasks live in `MOVE_PHASES` in the `<script>` block. Titles and notes can change freely, but changing an `id` drops that task's saved state
- If two people save at once, the page reloads the latest file, re-applies its pending changes and saves again
- Saving is blocked if the data failed to load, so a bad load can't overwrite saved homes
