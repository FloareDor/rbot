# nav2 tuning

another side project while the real stack isn't ready.

idea: build a headless sim loop that scores how good our nav2 params are, then
use that to auto-tune them, instead of hand-tweaking configs and eyeballing it
in rviz forever.

based on the approach from this article:
https://kodorobotics.com/nav2-tuning-systems-approach

## the approach (from the article)

don't jump straight to automated optimization. that's step 3, not step 1.

1. **build the benchmark first.** three levels of test courses:
   - level 1: straight lines, rotations, gentle turns — catches controller bugs
   - level 2: narrow passages, obstacles — catches costmap bugs
   - level 3: full real-ish scenarios — catches everything else
2. **build the metrics.** time to goal, path length, min distance to
   obstacles, how smooth the velocity/steering is, how much it oscillates.
3. **only then** bolt on auto-tuning (like Optuna) to search params against
   those metrics. automating tuning before you have real benchmarks/metrics
   just means you're optimizing blind.

## status

just getting set up. no code yet, need to pick a sim + course format and
start with level 1 benchmarks.
