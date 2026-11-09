# Technical Notes: Kettlerock Price Elasticity Modeling Decisions
*Prepared for: Data Science Manager Review*

> How to use this outline: each section has a prompt in a blockquote. Replace it with your writing. Tables are pre-filled with current results where I have them, mark anything stale.

## The Question and Why Elasticity

> Fill in: the business question (what happens to demand and revenue if price moves), why a log-log demand model (the coefficient is the elasticity), and why Bayesian for the final model (partial pooling for thin-data SKUs, honest uncertainty). 3 to 4 sentences.

## The Data

> Fill in: grain (SKU x region x week), 22 SKUs, 3 regions, 104 weeks, 8 categories. Then the traps built in, since they shaped every modeling choice:
> - Seasonal SKUs entering and leaving the assortment
> - Amber with a declining price trend, THC with a rising one
> - One flat-price SKU (almost no price variation)
> - Competitor (Hawkstone) prices only for IPA, Lager, Stout
> - Synthetic, so cleaner than real life
>
> Also state the within-SKU price variation fact: only 2.5% of price variance is within a SKU x region, and the within-entity correlation of price with promo is -0.42. This is the single most important fact for why everything is hard.

## Why the Naive Models Failed

> Fill in: M1 gave a POSITIVE elasticity (+0.07) because premium SKUs are priced high and sell more (cross-SKU confounding). M2 added controls and category slopes but still has no SKU effects, so IPA, Lager, Stout and Wheat came out near zero or positive, and Amber hit -7 because price and trend are nearly collinear inside Amber. Include the lesson: a better R-squared (0.68) did not mean a better elasticity.

## The Modeling Ladder

Each model fixes a specific problem in the one before it.

| Model | What it adds | Problem it addresses | What I expect to see |
|---|---|---|---|
| M1 Pooled OLS | Nothing, log-log baseline | None, it is the strawman | Wrong sign |
| M2 OLS + controls, by category | Season, promo, region, trend, category slopes | Omitted controls | Better, still biased |
| M3 Fixed effects, SKU x region | Entity intercepts | Between-SKU confounding | Sensible negatives |
| M4 2SLS, Hawkstone instrument | Instrumented price | Price responding to demand | Close to M3, wider CIs |
| M5 GAM, by category | Flexible price curve | Is elasticity constant? | Mostly straight lines |
| M6 Double ML, by category | ML nuisance models | Nonlinear confounding | Unstable where price tracks trend |
| M7 Hierarchical Bayes, by SKU | Partial pooling + uncertainty | Thin data per SKU | Agrees with M3, honest intervals |

> Fill in: a short paragraph on why the plan was "pooled, then by category, then SKU only in the Bayesian model", and why SKU-level estimates for M2 to M6 were not worth the noise.

## Identification: Where the Signal Comes From

> Fill in three short pieces:
> - Fixed effects: what within-entity variation means, in your own words (your demeaning explanation works well here).
> - The instrument: why Hawkstone, the first-stage F (IPA 94, Lager 78, Stout 113), and the row-level vs category-week correlation distinction (r about 0.1 vs 0.6 to 0.9) and why the category-week one is the right check.
> - **Be upfront:** Hawkstone prices were generated to track Kettlerock's own prices, so relevance is strong by construction and exclusion is assumed, not demonstrated. In real data the argument is shared input costs (barley, aluminum, freight).
>
> Also a line on standard errors: HC3 for OLS, entity-clustered for the panel (66 clusters), robust for per-category 2SLS because there are only 6 to 15 clusters per category.

## Amber and THC: The Hard Cases

> Fill in: price is nearly a function of time for both, so after controlling trend there is almost no independent price variation. Show the evidence: the estimates swing across models (Amber -2.2 to -5.5, THC 0 to -11) and DML falls apart. State your decision: keep them in the fit, report them as low-confidence, recommend a controlled price test. Explain why dropping them for "bad results" would not be defensible, and what the defensible reason would be (identification failure, shown by cross-model instability).

## Results Across Models

Elasticities by category. M1 pooled: +0.072. M7 values are from the first clean run, update after the season-effect fix.

| Category | M2 OLS | M3 FE (Spec A) | M4 2SLS | M5 GAM | M6 DML | M7 HB |
|---|---|---|---|---|---|---|
| Amber | -7.06 | -2.75 | n/a | -2.31 | -5.46 (CI spans 0) | -2.18 |
| IPA | +0.07 | -1.67 | -1.52 | -1.51 | -1.13 | -1.69 |
| Lager | +0.28 | -1.76 | -2.27 | -1.25 | -1.72 | -1.65 |
| Mixed | -1.03 | -1.61 | n/a | -1.53 | -2.31 | -1.56 |
| Sour | -1.59 | -0.71 | n/a | -1.81 | -1.44 | -0.83 |
| Stout | +0.00 | -1.08 | -1.66 | -1.05 | -0.74 | -1.13 |
| THC | -0.28 | -0.14 | n/a | +0.00 | -10.98 | -0.75 |
| Wheat | +1.29 | -1.76 | n/a | -2.47 | -0.38 (CI spans 0) | -1.83 |

> Fill in: where the models agree (M3 and M7 nearly identical on 6 of 8), where they disagree (Sour, Wheat, Lager across GAM/DML), and what you make of it. Remember: agreement between models is weak evidence, they share assumptions. Add the forest-plot figure here.

## Model-Specific Notes

> Fill in a short paragraph each, only the surprising parts:
> - **Fixed effects:** the original pooled slope (+0.43) was a specification problem, fixed by category slopes plus Amber/THC trends (Spec A vs week effects in Spec B agree). `is_promo` went to about 0 once price slopes were right, promos work through price in this data.
> - **2SLS:** the by-category version, not the pooled -1.45. First-stage F as a gate, not a result.
> - **GAM:** used as a linearity check. Mostly straight lines, the bumps with upward slopes are noise or SKU composition on the x-axis. No CI on the average elasticity.
> - **Double ML:** the `X` vs `W` bug (controls passed as heterogeneity features), the missing entity controls, and why it still fails on Amber/THC.

## The Hierarchical Bayes Model

> Fill in: the structure in plain terms.
> - SKU elasticities partially pooled to a category mean (non-centered), prior -1.5 with sd 0.5
> - SKU x region intercepts (same as M3), category-specific trends, season effects (sum-to-zero)
> - Centered log-price and trend for sampling
>
> Then the sampling story, it is a good one: first run had 12 divergences and max-treedepth hits (uncentered predictors, missing structure), second run had 1 divergence but R-hat 1.02 on `alpha` and `gamma_season` (non-identified offset between intercepts and season effects), fixed with `ZeroSumNormal`. Include the final diagnostics table (divergences, max R-hat, min ESS).
>
> Note the prior's pull: THC and Amber shrink toward -1.5. Say which numbers are partly prior.

## Validation

Five checks. Fill in each with what you did and what you found.

> - **Convergence:** R-hat, ESS, divergences, trace plots.
> - **Posterior predictive check:** does simulated log-units cover the observed data, overall and for Amber/THC specifically?
> - **Time-based holdout:** train on the first ~90 weeks, predict the last ~14. RMSE and interval coverage (does the 90% interval cover about 90%?) for M3 vs M7.
> - **Prior sensitivity:** rerun with a wider elasticity prior (sd 1.0) and a different mean. Which categories move? (Expect THC and Amber.)
> - **Cross-model triangulation:** the comparison table above, summarized as agreement rate.
>
> Optional capstone: open the ground-truth file only at the end and check parameter recovery. Decide whether to include it, since a real project would not have it.

## The Revenue Simulator

> Fill in: how it works and its assumptions.
>
> Core relationship: revenue change from a price change of `p` (as a decimal, 0.05 = +5%) with elasticity `e` is `(1 + p)^(1 + e) - 1`. Here `e` is the category elasticity, `1 + p` is the new price relative to the old one, and the exponent `1 + e` combines the price effect (+1) with the volume effect (`e`). When `e` is below -1 the exponent is negative, so raising price loses revenue.
>
> Say that it samples `e` from the posterior so every scenario has a range, not a point. State the limit: only trust moves inside the price range we have seen (roughly +/-10%), because within-SKU variation is small. Add 2 to 3 example scenarios and a chart.

## Cross-Price Effects (if you do it)

> Fill in or cut: does a price change in one category move sales in another (cannibalization or halo)? Which method (add other-category average price to the model), what you found, and why it matters for the recommendation.

## Decisions Log

| Decision | Alternative | Why | Risk |
|---|---|---|---|
| By category for M2 to M6, SKU only in HB | SKU level everywhere | Stable, one comparable table | Hides SKU variation outside HB |
| Keep Amber/THC in the fit | Drop them | Dropping for bad results is not defensible | Their estimates are low-confidence |
| Hawkstone as instrument | No IV | Addresses demand-driven pricing | Valid by construction only |
| `trend_amber`, `trend_thc` terms | Global trend | Opposite-direction trends | More parameters |
| Skipped looking at ground truth until the end | Tuning toward truth | Avoids fitting to the answer | Cannot report recovery until the end |

> Fill in: add or edit rows as you go.

## Known Limitations

> Fill in. Starting points: synthetic data (cleaner than real), only 2.5% within-SKU price variation, instrument validity by construction, Amber and THC not reliably identified, Hawkstone only covers 3 categories, GAM and DML have no clean CI, one flat-price SKU gets no SKU-level estimate of its own.

## If I Had More Time

> Fill in, in no particular order. Candidates: SKU-specific trends in the HB, a proper controlled price test design for Amber and THC, a time-aware cross-fitting scheme for DML, cross-price elasticities, a Hill-style flexible price response inside the Bayesian model, a dashboard for the simulator.

## Path to Production

> Fill in: data pipeline for weekly sales and competitor prices, quarterly refit using the prior posterior as the next priors, simulator behind a dashboard, monitoring (interval coverage on the next quarter's actuals, drift in within-SKU price variation).