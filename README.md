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
