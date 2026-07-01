# Geo-Based Incrementality Testing
**Justin Wall | Marketing Science Portfolio**

## Overview
A geo-based incrementality study built to answer a real business question: did this promotion actually drive new revenue, or would those customers have bought anyway? I created a fictional DTC company, built a realistic synthetic dataset with a known ground truth, and applied causal inference methods to estimate the true incremental lift. I then compared that against what a naive analysis would have reported.

## Business Context
The ask came from Megan Hartley, VP of Marketing at Luma Candle Co., following a 5-week regional promotional campaign run across Southeast and Mid-Atlantic DMAs in March 2025. Channel was reporting strong numbers, but the exec team wanted to know how much of that lift was real before committing to a national rollout budget. The full brief is in [explanations/email.md](explanations/email.md). Three questions drove the analysis:

- What was the true incremental revenue lift attributable to the promotion?
- How does that compare to the naive pre/post read the channel team reported?
- How confident can we be in these numbers?

## Why Geo Incrementality
Platform-reported revenue lift almost always overcounts. It can't distinguish between customers who bought because of the promo and customers who would have bought anyway. Geo-based incrementality testing solves this by comparing treated markets to control markets that didn't receive the promotion, isolating the causal effect from trend, seasonality, and organic demand. It's one of the most credible measurement methods available when you can't run a randomized controlled trial at the individual level. And while this wasn't our particular use-case, it's the best way to validate the output from a Marketing Mix Model.

## Key Findings
Full findings and the response to Megan are in [explanations/results.md](explanations/results.md). Summary:

- The naive pre/post estimate reported by the channel team: **57.4%** or 28,483 / market / week
- The DiD causal estimate (what the promo actually drove): **13.0%** or $5,911 / market / week
- The bias gap what the naive read overcounted by: **35.4pp**
- Ground truth baked into the synthetic dataset: **22.0%**
- Recommendation: The promotion drove real, measurable lift, but warrants further testing ahead of a national rollout due to the non-randomized method for selecting control markets.

## Modeling Notes
Four methods were attempted: naive pre/post, Difference-in-Differences (DiD), Synthetic Control, and CausalImpact.

**DiD** (CausalPy/PyMC) is the primary causal estimate. One technical obstacle worth noting: the pooled DiD coefficient is an additive dollar figure divided by the pre-period average to get a percentage, which understates the true percentage lift when the pre-period average has grown due to trend. So the 9pp undersell of the lift was expected. The ATT in absolute dollars is the more reliable number, and the percentage should be interpreted carefully against the correct baseline. The Bayesian aspect gives us a credible interval to report to leadership for uncertainty quantification.

**Synthetic Control** was implemented but produced degenerate results: the WeightedSumFitter with a flat Dirichlet(1,...,1) prior collapsed onto a single donor market (Columbus, OH, and St. Louis after Columbus was removed) regardless of which market was chosen or which aggregation method was used (sum vs. mean). This is a known limitation of unregularized weighted-sum approaches in correlated donor pools, and is not due to the data. Ridge-regularized SC or Synthetic DiD would be the correct fix, but we moved on due to time-constraints.

**CausalImpact** (Python) was skipped due to hard incompatibilities with pandas 2.0+. The library has not been maintained since 2023 and has multiple breaking changes in its core logic. The R implementation by Google's Kay Brodersen is the production-grade version of this method. Once again, we moved on due to time-constraints.

## Tech Stack
- Python, CausalPy, PyMC, ArviZ
- pandas, numpy, matplotlib
- causalimpact (attempted — pandas 3.0 incompatible)

## Repo Structure
```
explanations/
    email.md      -- The original ask from Megan
    results.md    -- Findings and recommendation
data/
    luma_geo_data.csv   -- Synthetic dataset (78 weeks, 12 DMAs, 2024-2025)
notebooks/
    geo_incrementality_luma.ipynb  -- Full analysis: EDA, DiD, SC, results
outputs/
    eda_overview.png
    method_comparison.png
```