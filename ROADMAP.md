# Bracket Predictor — Roadmap

All work targets **5 July 2026** (before the World Cup Round of 16 begins).

```
Jun 23   Jun 24   Jun 25   Jun 26   Jun 27   Jun 28   Jun 29   Jun 30   Jul 1    Jul 2    Jul 3    Jul 4    Jul 5
  |        |        |        |        |        |        |        |        |        |        |        |        |
  ┌────────────────────────────────────┐
  │  PHASE 1 — Core Polish (23–26 Jun) │
  └────────────────────────────────────┘
  [#4 Reset dialog        23–24]
           [#1 GF/GA entry      24–25]
                    [#2 Tiebreaker  25–26]
                    [#3 Mobile scroll 25–26]

                             ┌──────────────────────────────────────┐
                             │  PHASE 2 — UX & Sharing (26–29 Jun)  │
                             └──────────────────────────────────────┘
                             [#8 Dark/light mode  26–27]
                                      [#5 URL share    27–28]
                                      [#7 Score input  27–28]
                                               [#6 Export PNG 28–29]

                                                        ┌────────────────────────────────┐
                                                        │  PHASE 3 — Live Data (29–2 Jul) │
                                                        └────────────────────────────────┘
                                                        [#9 GitHub Actions standings.json  29–1 Jul]
                                                                                 [#10 Auto-lock  1 Jul]
                                                                                 [#11 Live badge 1–2 Jul]

                                                                                          ┌──────────────────────────────┐
                                                                                          │  PHASE 4 — Advanced (2–5 Jul) │
                                                                                          └──────────────────────────────┘
                                                                                          [#12 Multi-bracket  2–3]
                                                                                                   [#13 Scoring    3–4]
                                                                                                            [#14 PWA 4–5]
```

## Issue index

| # | Title | Phase | Start | Due |
|---|-------|-------|-------|-----|
| [#4](../../issues/4) | Reset bracket confirmation dialog | 1 | 23 Jun | 24 Jun |
| [#1](../../issues/1) | Enter real group-stage scores (GF/GA) | 1 | 24 Jun | 25 Jun |
| [#2](../../issues/2) | Tiebreaker display in group standings | 1 | 25 Jun | 26 Jun |
| [#3](../../issues/3) | Better mobile bracket scrolling | 1 | 25 Jun | 26 Jun |
| [#8](../../issues/8) | Dark / light mode toggle | 2 | 26 Jun | 27 Jun |
| [#5](../../issues/5) | Share bracket via URL hash | 2 | 27 Jun | 28 Jun |
| [#7](../../issues/7) | Score predictions (scorelines) | 2 | 27 Jun | 28 Jun |
| [#6](../../issues/6) | Export bracket as PNG / share card | 2 | 28 Jun | 29 Jun |
| [#9](../../issues/9) | Auto-update standings from live FIFA data | 3 | 29 Jun | 1 Jul |
| [#10](../../issues/10) | Auto-lock groups when complete | 3 | 1 Jul | 1 Jul |
| [#11](../../issues/11) | Live match status indicator | 3 | 1 Jul | 2 Jul |
| [#12](../../issues/12) | Multi-bracket: save multiple predictions | 4 | 2 Jul | 3 Jul |
| [#13](../../issues/13) | Scoring system: track prediction accuracy | 4 | 3 Jul | 4 Jul |
| [#14](../../issues/14) | PWA: installable offline app | 4 | 4 Jul | 5 Jul |

## Phase goals

| Phase | Window | Goal |
|-------|--------|------|
| 1 — Core Polish | 23–26 Jun | Fix the gaps users hit today: missing GD, risky reset, mobile friction |
| 2 — UX & Sharing | 26–29 Jun | Make the bracket shareable and more expressive before knockout rounds start |
| 3 — Live Data | 29 Jun–2 Jul | Feed real standings in automatically; lock completed groups |
| 4 — Advanced | 2–5 Jul | Score tracking, multiple brackets, PWA install — ready for the Round of 16 |
