# Draft Room Oracle

A live fantasy football draft assistant. Single HTML file, no build, no server —
open `index.html` and it works, including offline once loaded.

## What it actually does

Most draft tools hand you a ranking. This one answers a different question:

> **Which available player maximizes the expected points of my final starting
> lineup, given everything already off the board?**

That is the only number it optimizes. Everything else feeds it.

### The engine

- **Scoring-aware projections.** Season totals come from positional-rank point
  curves and are re-derived for your exact settings — PPR / half / standard, TE
  premium, 4- vs 6-point passing TDs. Change the format and every number moves.
- **Dynamic replacement level.** VORP is computed against the *unfilled starter
  slots remaining across the whole league*, recalculated on every pick. When the
  room drains running backs, RB replacement level drops and RB value rises on its
  own — no hard-coded scarcity rules.
- **Survival model.** Each player's chance of lasting until your next pick comes
  from a normal CDF over ADP, conditioned on him still being here now, with
  uncertainty that widens deeper into the board.
- **Monte Carlo rollouts.** For each shortlisted candidate the app simulates the
  rest of the draft to the end of your roster. Opponents pick from an ADP window
  tilted by their own real roster needs (including nobody taking a kicker in
  round 4); you pick greedily by marginal lineup value. Candidates are compared
  under **common random numbers** — identical simulated draft rooms — so a 2-point
  edge means something at 47 rollouts instead of needing thousands.
- **Tiers from real gaps.** Tier breaks are found from actual discontinuities in
  the projection curve, which is what makes a cliff warning trustworthy.

When it is not your turn, it flips to preview: it simulates the intervening picks
and reports who is actually likely to reach you, and how often.

## On a phone

The layout is phone-first: a sticky pick bar, then a bottom tab bar — **Pick /
Board / Charts / Team** — so the recommendation is above the fold and every
control is a real tap target.

## Charts

- **Positional value curve** — small multiples, one panel per position, showing
  projected points against rank among the players *still available*. All four
  share one scale, so the height of a curve above its replacement line is
  directly comparable across positions: that gap is VORP, drawn.
- **Draft grid** — the full board, one column per team with snake order
  resolved, cells tinted by position.
- **Pick tempo** — the last 24 picks as coloured blocks; a run is a block of one
  colour.
- **Starters vs. league average** — diverging bars per starting slot; unfilled
  slots count at waiver level so the comparison is defined from pick one.

The categorical palette is validated for colour-vision deficiency and contrast
in both themes (OKLab ΔE on all pairs, ≥3:1 against the surface).

## Sending someone a copy

The app is a single self-contained file. To hand it to someone who cannot open
the hosted version:

```sh
cp index.html "Draft Room Oracle.html"
```

They save it and open it in any browser — no install, no account, and it runs
with no network at all (verified with all HTTP blocked: the engine, the board,
the charts and the glossary all work; the only external reference is a Google
Fonts stylesheet, which is non-blocking and degrades to system fonts).

Copies are snapshots and do not update, so regenerate one after any change.

## Saving

Three layers, so a draft is never lost:

1. **This browser** — every pick writes to local storage immediately.
2. **The page's own store** — when available it syncs server-side, so a draft
   survives closing the tab and follows you between phone and laptop.
3. **Backup** — download a JSON file, or copy the backup text and paste it back
   into any browser to restore.

## Using it during a draft

1. **Settings** — teams, your slot, rounds, scoring, starting lineup. Four league
   profiles are saved independently in your browser.
2. **Paste your platform's board** (Settings → Load your platform's board). The
   built-in pool is a September 2026 consensus baseline; your league's own ADP is
   better. One player per line, most formats parse.
3. **Type each pick as it happens** and press Enter. `/` focuses the box,
   Ctrl/Cmd-Z undoes, `Skip` handles a pick you can't identify.

The tool never claims certainty it doesn't have: it shows the size of its edge,
and tells you when the top two are a coin flip.
