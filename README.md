# 2026 FIFA World Cup Bracket Predictor

A single-file, zero-dependency bracket predictor for the 2026 FIFA World Cup. All 48 teams, all 12 official groups, full knockout bracket from Round of 32 to the Final.

**[▶ Open the app](https://infrabeam1927.github.io/Bracket-Predictor/)**

---

## Features

- **Group stage ranking** — drag and drop (or use ▲▼ buttons) to rank the 4 teams in each of the 12 groups
- **Third-place wildcards** — pick 8 of the 12 third-place finishers to advance to the Round of 32
- **Interactive knockout bracket** — click any team to advance them; click a winner again to undo; changes cascade automatically through subsequent rounds
- **Champion reveal** — animated banner when the Final is decided
- **Persistent state** — progress is saved to `localStorage` and survives page reloads
- **Mobile-friendly** — horizontal scroll on small screens keeps the bracket readable

## 2026 Format

| Stage | Teams |
|---|---|
| Group stage | 48 teams across 12 groups (A–L), 4 teams each |
| Round of 32 | 24 group qualifiers (top 2 per group) + 8 best third-place teams |
| Round of 16 → Final | Standard single-elimination |

## Deployment

The app is a single `index.html` file served directly by GitHub Pages — no build step, no dependencies.

To deploy your own copy:

1. Fork this repository
2. Go to **Settings → Pages → Deploy from branch → `main` → `/ (root)`**
3. GitHub Pages will serve the file at `https://<your-username>.github.io/Bracket-Predictor/`

## Making changes

All logic lives in `index.html`. See [`CLAUDE.md`](CLAUDE.md) for the full architecture reference — state shape, bracket advancement logic, CSS conventions, and how to add teams or rounds.
