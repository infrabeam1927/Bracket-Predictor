# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-file, zero-dependency World Cup 2026 Bracket Predictor deployed via GitHub Pages. Everything — HTML, CSS, and JavaScript — lives in `index.html`. There is no build step, no package manager, and no external files (the page loads standalone).

## Deployment

GitHub Pages serves `index.html` directly from the `main` branch root. To deploy a change: commit and push `index.html` to `main`. No CI, no build command needed.

## Architecture

The entire app is one self-contained file with three logical sections:

```
<style>   CSS design system + component styles
<body>    Three tab panels (HTML structure only, no logic)
<script>  All data, state, and rendering logic
```

### Data layer (`TEAM_DATA`, `R32_SEEDS`)

- `TEAM_DATA` — object keyed `A`–`L`, each an array of `{n, f}` (name, flag emoji) for 4 teams.
- `R32_SEEDS` — 16 pairs of seed strings (`'P1A'` = 1st place Group A, `'3P0'` = 1st selected third-place wildcard) that define the Round of 32 matchups.

### State (`S`)

Single object persisted to `localStorage` under `'wc26_v1'`:

```js
{
  rankings: { A: ['USA', 'Mexico', ...], ... },  // user-ordered team names per group
  picks:    ['B', 'D', ...],                      // up to 8 group letters (third-place wildcards)
  bracket:  {                                     // one array per round
    r32: [{t1, f1, t2, f2, w}, ...],             // 16 matches
    r16: [...],                                   // 8 matches
    qf:  [...],                                   // 4 matches
    sf:  [...],                                   // 2 matches
    fin: [...],                                   // 1 match
  },
  ready: false,   // true once genBracket() has run at least once
}
```

Every mutation calls `save()` immediately after.

### Rendering

All rendering is **full re-render on every state change** — no virtual DOM or diffing. The three render roots are:

| Function | Target element | Triggered by |
|---|---|---|
| `renderGroups()` | `#groupsGrid` | boot, every drag/move |
| `renderThird()` | `#thirdGrid` | switching to tab ②, every toggle |
| `renderBracket()` | `#bracketEl` | bracket generation, every pick |

### Bracket advancement logic

`pickWin(round, mi, name, flag)` — picks a winner for match `mi` in `round`:
1. Calls `clearDown(round, mi)` to wipe the old winner from all downstream slots (recursive).
2. Sets `m.w = name` on the current match.
3. Calls `nextSlot(round, mi)` (returns `{round, idx, slot}`) to find where the winner goes next and fills that slot.

`clearDown(round, mi)` — removes `m.w` from the next round's slot; if the next match also picked that team as winner, cascades recursively.

`nextSlot(round, mi)` — stateless helper: next round is `ROUNDS[ri+1]`, next match index is `Math.floor(mi/2)`, slot is `mi % 2`. This works because pairs of adjacent matches always feed one next-round match.

### Tab navigation

`goTab(name)` swaps `.active` on both tab panels and nav buttons. It also calls `renderThird()` when switching to tab ② and `renderBracket()` (if `S.ready`) when switching to tab ③, so data is always fresh.

## Key constants

| Constant | Purpose |
|---|---|
| `GROUPS` | `['A'..'L']` — iteration order for all group loops |
| `ROUNDS` | `['r32','r16','qf','sf','fin']` — bracket round order |
| `ROUND_SIZE` | Match count per round (`{r32:16, r16:8, qf:4, sf:2, fin:1}`) |
| `ROUND_LABELS` | Display names for bracket column headers |

## CSS conventions

- All colours are CSS custom properties on `:root` (`--gold`, `--green`, `--border`, etc.).
- Component states are modifier classes (`.won`, `.lost`, `.tbd`, `.picked`, `.maxed`, `.dragging`, `.drag-over`).
- The bracket uses a fixed `height: 840px` container with `justify-content: space-around` on each `.matches-wrap` column — this naturally centres each round's matches between their feeders without JS positioning.
- Horizontal scroll on `.bracket-scroll` handles small screens; `min-width: 960px` keeps the bracket readable.

## Making changes

**Adding or renaming a team:** Edit `TEAM_DATA` only. Rankings default to `TEAM_DATA` order, so existing `localStorage` for that group will show stale names — consider bumping the storage key (`'wc26_v1'` → `'wc26_v2'`).

**Changing R32 matchups:** Edit `R32_SEEDS`. Each entry is `[homeSeed, awaySeed]`; the array index determines which R16 match the winner feeds into (`Math.floor(i/2)`).

**Adding a sixth round:** Add the round key to `ROUNDS`, `ROUND_LABELS`, and `ROUND_SIZE`. `nextSlot()` and `clearDown()` will handle the new round automatically.

**Resetting saved state during development:** Open browser DevTools → Application → Local Storage → delete `wc26_v1`, or call `resetAll()` from the console.
