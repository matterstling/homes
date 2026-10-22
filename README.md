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

## Move and Rental tabs

Two shared checklists for the November 2026 move: **Move** (getting into the leased house) and **Rental** (turning the current house into a long-term rental). Both sit behind the same password and sync through the same Worker and `data.json`.

- Both lists store task state in one place, under `move.tasks` in `data.json`, keyed by task id: `{ done, owner, doneAt, statusNote, noteAt, updatedAt }`. Ids are unique across both lists
- Tasks live in `CHECKLISTS.move` and `CHECKLISTS.rental` in the `<script>` block, each with its own key dates, intro, categories, phases and "Still open" items. Titles and notes can change freely, and a task can move between lists, but changing an `id` drops that task's saved state
- Owners are `matt`, `lauren` or `both` (edit `MOVE_OWNERS` to change names); tap an owner again to unassign. Tasks owned by Both also show under the Matt and Lauren filters
- Each task has one category (`cat`), defined per list in `cats`. Each tab has its own filters: owner, type (category) and Hide done
- A phase with `ordered: true` shows numbered priorities (used for the move-day "Ready by bedtime" list)
- **+ Add task** at the end of each phase adds a task to that phase: title, optional details, type and owner. Added tasks are stored in `move.tasks` like everything else, with a `custom: { list, phase, cat, title, note, createdAt }` field, and sync and merge the same way. They can be ticked off and noted, but there's deliberately no way to edit or remove them (or the built-in tasks) from the page. Phases have a stable `key` so added tasks stay put if a phase title changes; if a phase is removed, its added tasks move to the list's last phase
- Each task has a status note: **+ Add note** / **Edit note** opens an editor, **Save note** (or ⌘/Ctrl + Enter) saves, **Cancel** (or Esc) discards. Saving an empty note removes it. Unsaved drafts survive re-renders, and the page warns before closing with one open
- Notes render as markdown: task status notes, the details on added tasks, and the per-home notes on Compare cards. `mdToHtml` escapes the text first, so a note can't inject markup. It handles headings, bold, italics, strikethrough, inline code and code blocks, links and bare URLs, bulleted, numbered and `- [ ]` checklists, quotes and rules. Notes are stored as the raw text
- If two people save at once, the page reloads the latest file, re-applies its pending changes and saves again
- Saving is blocked if the data failed to load, so a bad load can't overwrite saved homes
