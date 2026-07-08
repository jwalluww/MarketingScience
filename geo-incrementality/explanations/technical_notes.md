# Technical Notes: Luma Candle Co. Geo Incrementality Study
*Prepared for: Data Science Manager Review*

## 1. Objective
We ran a geo incrementality test to isolate the causal effect of a regional promotional campaign. The goal was to separate true incremental lift from organic trend and seasonality using a proper counterfactual, something a naive pre/post analysis can't do. The team set up a control group, but did not ensure the control group was repesentative of the United States since they were selected regionally and due to their large populations.

## 2. Data Summary
The data has 78 rows of weekly DMA-level revenue from January 2024 to June 2025 with 12 DMAs total. The 5 treated markets were in the Southeast/Mid-Atlantic and the 7 control markets were in the Midwest. The promotional window runs from March 3 to March 31, 2025, 5 total weeks, and we have several weeks post-promo to check for pull-forward. The ground truth incrementality that is baked in shows +22% lift on treated DMAs during promo and +4% post-promo hangover for 3 weeks. The dataset includes realistic noise, DMA-level trend, and seasonality (Q4 spike, Q1 dip). We do not know the spillover nor the spend on the campaign for labor or capital.

## 3. Why Difference-in-Differences
We tested a few methodologies but ran into issues noted later in the tech doc. The gold-standard and clear winner of this incrementality analysis is difference-in-differences. Our data is panel data with pre and post trends and treated and control markets, which fits the DiD mold perfectly. The parallel trends assumption holds up, though with additional time I would have tested this more rigurously with a regression. The output of the DiD is interpretable as an ATT, and it's the industry-standard causal estimator for this type of geo-experiment when possible. Also held up technically as well - no issues with libraries or data when running.

## 4. The Parallel Trends Assumption
For Difference-in-differences the control markets and treated markets need to be trending the same in the pre-period to ensure they are both feeling the same fluctuations which will ensure the promo is the only difference between the two trend lines, otherwise there could be confounding factors affecting the trends. We validated the parallel trends assumption visually, by checking our plot; however, you could run a pre-period regression, a placebo/falsification test, or a correlation of pre-period trends to confirm. The EDA chart showed very little difference between the trends so I deemed it satisfactory for this analysis.

## 5. The DiD Specification
The formula we used for our DiD regression was this: `revenue ~ 1 + treated_int + post + treated_int:post`, where the `treated_int` is a binary representing whether the market was treated or control, and the variable `post` represents whether the data point was pre or post promo start. We only used data points from the pre-period and promotional period and left out data points from the post-promotional period, which with more time we could have included to measure the long-term effect of the promo. The `treated_int:post` variable has the coefficient which is the ATT - the average treatment effect for the treated. This can tell us the impact of the promotion on the treated markets. There was some growth in the trends for both treated and control markets leading up to the promo, which is why the 22% of actual lift is understated in our analysis. Next time around we should randomize market assignment across a representative DMA universe so the interpretation can be generalizable to any market receiving the promo, and the ATT can actually be the ATE - average treatment effect, which can be more valuable to the business.

## 6. The Synthetic Control Obstacle
When attempting the synthetic control method using CausalPy, we ran into an issue where the algorithm was selecting 1 control market to represent all of the control markets due to the Dirichlet priors in the WeightedSumFitter method.

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