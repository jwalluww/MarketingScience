# Technical Notes: Luma Candle Co. Geo Incrementality Study
*Prepared for: Data Science Manager Review*

## 1. Objective
[1 short paragraph. We ran a geo incrementality test to isolate the causal effect of a regional promotional campaign. The goal was to separate true incremental lift from organic trend and seasonality using a proper counterfactual — something a naive pre/post analysis can't do.]

## 2. Data Summary
- 78 weeks of weekly DMA-level revenue (January 2024 — June 2025)
- 12 DMAs total: 5 treated (Southeast/Mid-Atlantic), 7 control (Midwest)
- Promo window: March 3 — March 31, 2025 (5 weeks)
- Ground truth baked in: +22% lift on treated DMAs during promo, +4% post-promo hangover (3 weeks)
- Realistic noise, DMA-level trend, and seasonality (Q4 spike, Q1 dip) included
- [Note any known limitations — e.g. synthetic data, no spillover modeled, no spend data included]

## 3. Why Difference-in-Differences
[Short paragraph. DiD is the right tool here because: panel data with clear pre/post and treated/control structure, parallel trends assumption is testable and holds visually, output is directly interpretable as ATT. Note that it's the industry-standard causal estimator for this type of geo experiment.]

## 4. The Parallel Trends Assumption
- What it requires and why it matters for DiD validity
- How you validated it (visual check, normalized pre-period index plot)
- Whether it held in this dataset and how confidently
- [Fill in your own assessment from the EDA chart]

## 5. The DiD Specification
- Formula used: `revenue ~ 1 + treated_int + post + treated_int:post`
- Why Patsy interaction syntax matters here (CausalPy parses formula to identify causal coefficient)
- The `treated_int:post` coefficient is the ATT — explain what it represents
- Why the percentage lift understates the true 22%: denominator inflation from trend growth in pre-period average. The absolute dollar ATT is the cleaner number; the % is sensitive to which baseline you divide by.
- ATT vs ATE: this model estimates the effect on the treated markets specifically. Generalizing to national rollout requires random market assignment across a representative DMA universe.

## 6. The Synthetic Control Obstacle
- What WeightedSumFitter does: Dirichlet(1,...,1) prior on weights, constrained to sum to 1
- What went wrong: weights collapsed onto a single donor (Columbus, OH first run, St. Louis on second) regardless of sum vs mean aggregation
- Why this happened: flat Dirichlet prior doesn't penalize corner solutions; with 7 highly correlated donors (all 0.83-0.94 correlated with treated), the likelihood surface has near-ties between corner solutions and sampling lands on whichever wins by a hair
- Confirmed via: pre-period fit (residuals of 100K+ when they should be near zero), weight inspection (0.9999 on one market, 1e-5 on all others)
- The correct fix: ridge-regularized synthetic control or Synthetic DiD (both available in CausalPy) — not attempted here in the interest of scope
- Why this is worth documenting: this failure mode is real and common in practice, not a setup error

## 7. The CausalImpact Obstacle
- What CausalImpact does conceptually (BSTS, counterfactual via control market covariates)
- Why it was skipped: two hard incompatibilities with pandas 2.0+
  1. `pd.core.dtypes.common.is_datetime_or_timedelta_dtype` removed in pandas 2.0
  2. Integer positional indexing behavior changed in pandas 3.0, breaking internal standardization logic
- Package last released 2023, no active maintenance
- The R version (`CausalImpact` by Kay Brodersen at Google) is the production-grade implementation
- PyMC-native BSTS or statsmodels `UnobservedComponents` are the correct Python alternatives

## 8. Validation
- What was checked: parallel trends (visual), DiD coefficient sign and magnitude vs ground truth, 94% HDI coverage, R-hat and ESS via ArviZ summary
- What was NOT validated: out-of-sample geo holdout (synthetic data, so ground truth is known), real revenue data
- [Fill in your actual R-hat and ESS numbers from the model summary]

## 9. Known Limitations
- Synthetic dataset: ground truth is known by construction, so this is a methods validation exercise, not a real business measurement
- Single pooled DiD coefficient: doesn't capture heterogeneous treatment effects across DMAs — Atlanta at $48K base behaves differently than Richmond at $22K base, but the model treats them as one
- ATT only: control markets are Midwest DMAs, not a nationally representative sample. Generalizing lift estimates to Pacific Northwest or rural markets is an extrapolation.
- Post-promo hangover is in the data (+4%, 3 weeks) but not explicitly modeled — the DiD window ends at March 31 so this doesn't contaminate the estimate, but it's worth tracking in a real test

## 10. If I Had More Time / Resources
- Implement ridge-regularized synthetic control or Synthetic DiD to fix the donor collapse problem
- Build a CausalImpact-equivalent directly in PyMC (BSTS model with control covariates) rather than relying on the unmaintained Python port
- Design a proper prospective geo experiment using matched-market selection (Google's matched_markets library) with random assignment to estimate ATE rather than ATT
- Run placebo tests: fake treatment date in the pre-period should return no significant effect — strong validation for DiD
- Multi-market DiD with market fixed effects or log-transformed outcome to handle heterogeneous DMA scales