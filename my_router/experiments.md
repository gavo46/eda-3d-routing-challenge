# Experiment log

## Setup
Python 3.13, macOS. From the repo root:

    python3 my_router/router.py --suite benchmarks --out-dir my_router/runs/<name>
    python3 -m m3d.cli score-suite --suite benchmarks --submission-dir my_router/runs/<name>

Built with AI assistance (Claude). Experiments 4 and 5 are my own changes.

## Results (intro tier, 20 cases)

| # | Change | Aggregate | Legal | Runtime | Notes |
|---|--------|----------:|:-----:|--------:|-------|
| 0 | Example router, unchanged | 0.9437 | 20/20 | — | Starting point. Grows each tree by attaching the nearest sink, which minimizes wire, not delay. |
| 1 | Shortest-path tree from the driver, smallest net first | 0.9850 | 20/20 | — | The objective sums driver-to-sink delay, so each sink should get its own shortest path. |
| 2 | Same, largest net first | 1.0240 | 20/20 | — | Ordering matters a lot: big nets need the cheap middle layers most. |
| 3 | + reroute each net with the others fixed, until no gain; best of two orders | 1.0337 | 20/20 | — | Can never make a net worse, since its old route is still available. |
| 4 | + rip up a net and its blockers, reroute, keep if total drops; each net's ideal route cached once | 1.0539 | 20/20 | 672s | Ideal routes ignore other wires and pins never move, so caching them halves runtime with identical results. |
| 5 | + third ordering: largest ideal delay first | 1.0568 | 20/20 | 1063s | Orders by what's actually scored. Small gain; improved cases 4, 5, 7 and others, none worse. |

## What I learned
- The objective rewards each sink's own delay, not total wire, so a shortest-path tree from the driver beats a compact tree.
- Net order has a large effect on the first pass; local repair only partly makes up for a bad start.
- Gains are concentrated on small and medium cases (1–14: 4–15% better than baseline). Large cases (15–20) are near baseline, and 17 and 19 are slightly below.
- The likely cause: the rip-up step gets a flat 20 seconds regardless of case size, so it runs out of time on large cases.

## Next steps
- Scale the rip-up time budget with case size.
- Make each rip-up attempt cheaper by skipping groups that already failed.
- Try ordering by ideal-route conflicts.
- Run on the hard tier.