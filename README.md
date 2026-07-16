# Cookie Cats A/B Test: Gate Placement & Player Retention

## Business Question

Cookie Cats is a mobile puzzle game where players hit "gates" — forced pauses in level progression designed to boost retention and monetization. This project analyzes an A/B test run by the game's developers to answer:

**Should the first gate stay at level 30, or move to level 40?**

## Key Finding

Moving the gate from level 30 to level 40 causes a statistically significant drop in 7-day retention (p = 0.0016), but the effect is not uniform across players. Casual players show no meaningful difference, while highly engaged players — the game's most valuable users — show a notably larger retention drop under the level 40 gate.

**Recommendation:** Keep the gate at level 30. The overall effect size is modest, but the risk is concentrated in the segment of players the business can least afford to lose.

## Dataset

- **Source:** [Cookie Cats Mobile Games A/B Testing (Kaggle)](https://www.kaggle.com/datasets/yufengsui/mobile-games-ab-testing)
- **Size:** ~90,000 players, randomly assigned to `gate_30` (control) or `gate_40` (treatment)
- **Columns:** `userid`, `version`, `sum_gamerounds`, `retention_1`, `retention_7`

## Methodology

1. **Data cleaning** — validated group balance, checked for duplicates/nulls, removed one extreme outlier (~49,000 game rounds, likely a bot or test account)
2. **Hypothesis testing** — two-proportion z-tests on `retention_1` and `retention_7`, tested separately since they measure distinct behaviors
3. **Bootstrap resampling** — 10,000-iteration bootstrap as an independent cross-check on the z-test confidence intervals
4. **Segment analysis** — players split into engagement quartiles (low/medium/high/very_high by `sum_gamerounds`), retention re-tested within each segment to check for heterogeneous treatment effects
5. **Practical significance threshold (MDE)** — a 5 percentage point difference was pre-declared as the minimum change that would justify a product decision, separate from statistical significance

## Results

### Retention Day 1
| Group | Rate | Difference | z-stat | p-value | 95% CI |
|---|---|---|---|---|---|
| gate_30 | 44.82% | | | | |
| gate_40 | 44.23% | -0.59pp | 1.787 | 0.074 | [-0.0124, 0.0006] |

Not statistically significant.

### Retention Day 7
| Group | Rate | Difference | z-stat | p-value | 95% CI |
|---|---|---|---|---|---|
| gate_30 | 19.02% | | | | |
| gate_40 | 18.20% | -0.82pp | 3.157 | 0.0016 | [-0.0133, -0.0031] |

Statistically significant. Confirmed independently via bootstrap (95% CI: [-0.0133, -0.0032]).

### Segment Analysis (Retention Day 7 by Engagement Level)
| Segment | gate_30 | gate_40 | Difference | p-value | Significant? |
|---|---|---|---|---|---|
| Low | 1.22% | 1.31% | +0.09pp | 0.518 | No |
| Medium | 4.17% | 4.09% | -0.08pp | 0.777 | No |
| High | 14.77% | 13.22% | -1.55pp | 0.00077 | Yes |
| Very High | 56.75% | 54.25% | -2.50pp | 0.00017 | Yes |

The retention drop under gate_40 is concentrated almost entirely among the game's most engaged players.

## Tech Stack

- **Python** — pandas, numpy, scipy, statsmodels (data cleaning, hypothesis testing, bootstrap resampling)
- **Matplotlib** — statistical visualizations (retention comparison, bootstrap distributions, segment breakdown)
- **Tableau Public** — interactive stakeholder-facing dashboard

## Dashboard

[[View the interactive Tableau dashboard here](https://public.tableau.com/app/profile/nestor.mykhailovych/viz/Cookie_Cats_AB_testing/Dashboard1#2)](#)
