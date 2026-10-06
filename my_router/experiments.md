# Experiment log

## Setup
Python 3.13, macOS. From the repo root:

    python3 my_router/router.py --suite benchmarks --out-dir my_router/runs/<name>
    python3 -m m3d.cli score-suite --suite benchmarks --submission-dir my_router/runs/<name>

Built with AI assistance (Claude); see "What I learned" for my own analysis.

## Results (intro tier, 20 cases)

| # | Change | Aggregate | Legal | Runtime | Notes |
|---|--------|----------:|:-----:|--------:|-------|
| 0 | Example router, unchanged | 0.9437 | 20/20 | — | Starting point. Grows each tree by attaching the nearest sink, which minimizes wire, not delay. |
| 1 | Shortest-path tree from the driver, smallest net first | 0.9850 | 20/20 | — | The objective sums driver-to-sink delay, so each sink should get its own shortest path. |
| 2 | Same, largest net first | 1.0240 | 20/20 | — | Ordering matters a lot: big nets need the cheap middle layers most. |
| 3 | + reroute each net with the others fixed, until no gain; best of two orders | 1.0337 | 20/20 | — | Can never make a net worse, since its old route is still available. |
| 4 | + rip up a net and its blockers, reroute, keep if total drops | (pending) | | | Full run tonight. |

## What I learned

## Next steps