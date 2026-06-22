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

### Data layer (`TEAM_DATA`, `R32_DEFS`, `MATCH_NUMS`)

- `TEAM_DATA` — object keyed `A`–`L`, each an array of `{n, f}` (name, flag emoji) for 4 teams.
- `R32_DEFS` — 16 pairs of slot descriptors defining the official Round of 32 matchups (M73–M88).
  Each slot is either `{t:'P', pos:1|2, g:'X'}` (1st/2nd place of group X) or
  `{t:'3', pool:'ABCDE'}` (a 3rd-place wildcard whose group must be in the pool).
  The array is ordered so `Math.floor(i/2)` always gives the correct R16 slot index.
- `MATCH_NUMS` — official FIFA match numbers per round for display chips:
  `{ r32:[74,77,73,...], r16:[89,90,...], qf:[97,...], sf:[101,102], fin:[104] }`.

### State (`S`)

Single object persisted to `localStorage` under `'wc26_v3'`:

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

### Group stage position highlighting

Each team row in `buildGroupCard` gets a `pos-N` class (1–4). CSS uses this for zone coloring:
- `pos-1`, `pos-2`: green left-stripe (advancing to R32)
- `pos-3`: amber left-stripe + separator gap (3rd-place wildcard candidate)
- `pos-4`: dimmed (eliminated)

The `:not(.drag-over)` guard prevents the zone border from overriding the drag-over gold highlight.

### 3rd-place wildcard assignment (`assign3rdPlaceTeams`)

Each of the 8 wildcard slots in `R32_DEFS` has a `pool` constraint (a string of allowed group letters). A pure greedy approach can reach dead ends, so we use backtracking DFS:
- Slots are sorted by match number (M74, M77, M79, M80, M81, M82, M85, M87) to match the official draw order.
- For each slot, try any unused pick whose group letter is in the pool; recurse; backtrack on failure.
- Returns `{ defIndex: groupLetter }` for all 8 wildcard slots.

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
| `MATCH_NUMS` | Official FIFA match numbers per round slot (display only) |

## CSS conventions

- All colours are CSS custom properties on `:root` (`--gold`, `--green`, `--border`, etc.).
- Component states are modifier classes (`.won`, `.lost`, `.tbd`, `.picked`, `.maxed`, `.dragging`, `.drag-over`).
- Position zone classes (`pos-1` through `pos-4`) on `.team-item` elements drive group-stage color coding.
- The bracket uses a fixed `height: 840px` container with `justify-content: space-around` on each `.matches-wrap` column — this naturally centres each round's matches between their feeders without JS positioning.
- Horizontal scroll on `.bracket-scroll` handles small screens; `min-width: 960px` keeps the bracket readable.

## Making changes

**Adding or renaming a team:** Edit `TEAM_DATA` only. Rankings default to `TEAM_DATA` order, so existing `localStorage` for that group will show stale names — bump the storage key (`'wc26_v3'` → `'wc26_v4'`).

**Changing R32 matchups:** Edit `R32_DEFS` and the corresponding entry in `MATCH_NUMS.r32`. Each entry's array position determines which R16 match the winner feeds into (`Math.floor(i/2)`). Wildcard slots also need a correct `pool` string so `assign3rdPlaceTeams()` can solve the assignment.

**Adding a sixth round:** Add the round key to `ROUNDS`, `ROUND_LABELS`, `ROUND_SIZE`, and `MATCH_NUMS`. `nextSlot()` and `clearDown()` will handle the new round automatically.

**Resetting saved state during development:** Open browser DevTools → Application → Local Storage → delete `wc26_v3`, or call `resetAll()` from the console.
