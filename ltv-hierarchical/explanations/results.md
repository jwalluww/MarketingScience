# Geo Incrementality Measurement: Findings & Recommendation
**To:** Megan Hartley, VP of Marketing, Luma Candle Co.
**From:** Data Science
**Re:** Spring Refresh Promo, Incremental Lift Analysis

---

Hi Megan, findings below. Short version at the top, detail underneath.

## The Short Version

Luma Candle's 5-week Spring Refresh Promo created 13% lift in sales in the test markets, or an increase of around $5,911 in sales per market per week. The quick read completed initially showed a 79% lift in sales or an increase of around $28,483 in sales per market per week, which was overstated by 66pp due to the promo coinciding with March seasonal demand to shop. While this may appear successful, a 20% discount requires a 25% volume lift to break even on gross revenue, and we did not hit that number. My recommendation is to retest next year to properly measure the viabiility of a National Rollout.

---

## What the Channel Team Reported vs. What Actually Happened

The Channel Team used the pre-period for the treated markets and compared it versus the post-period, and the post-period happened to include a large jump in sales across the company due to March seasonal demand to shop. We used a set of control markets that were purchasing at the same rate as the treated markets up until the promotion and compared them across the pre-period and post-period to determine the lift. The control markets also purchased higher during March seasonal demand, but the lift was not as large as the treated markets which we believe is due to the 20% off promo.

| Method | Estimated Lift | Notes |
|---|---|---|
| Naive Pre/Post (channel report) | 79% | No counterfactual — includes organic trend |
| Difference-in-Differences (causal) | 13% | Controlled for what control markets did |

In these situations, the naive pre/post lift can include factors outside of the promo and not entirely due to the promo. This could have vastly overstated the promotions effectiveness and altered business strategy due to an inaccurate measurement. In this case, we would have recommended a national rollout when there should be further testing done instead.

---

## How Confident Are We?

There are three important caveats to this analysis that must be brought up to level-set expectations of this promo analysis.

Firstly, the lift we measured is determining whether the promo worked in driving response, meaning we used the promotional period to measure the revenue, but did not measure revenue any further out than that. The promo could have pulled transations forward instead of convincing customers to make an additional purchase. We should run this analysis further out to determine whether this is the case and the promo is worth the labor hours and cost to set up. Quick note that there is a 4% post-promo lift in the treatment group, which provides evidence against the promotion pulling transactions forward.

Secondly, the lift we measured was for the treated markets, which might not extrapolate across the entire country as uniformly as expected. Assuming we selected those markets due to their size and revelancy, we cannot project them across the country as some smaller markets might react differently to the promotional email. Given the size of the markets and that most of our customers come from large markets, this isn't a total show-stopper, but when we create a holdout group of markets for the next test, we will select them to be representative of the markets across the nation.

Thirdly, while lift is an important metric, we need to account for revenue as well (unless our primary goal was something else, like obtaining new customers). Given that the discount for the candles was 20%, we would need a 25% lift in conversion to break even on gross revenue, and we only saw a 13% lift in conversion.

In favor of the promo, our test results came back with high precision. There is a 95% probability that the lift in our promo is between 5,823 and $5,996. If the scenario next year is similar, we will see a similar figure.

---

## Does This Justify a National Rollout?

This promo analysis does not justify a national rollout. We should holdout a group of carefully selected markets during the next promo to determine the exact lift nationwide, measure well past the promotional period, and analyze not only conversion lift, but revenue lift, LTV lift, and customer acquisition.

---

## What We Tried and What We Learned Along the Way

We attempted 4 methodologies for measuring lift; the naive approach completed by the channel team and replicated by us, Bayesian Difference-in-Differences for uncertainty calculations, Bayesian Synthetic Control, and Bayesian Synthetic Time Series using causal impact. Naive lift performed as expected & overstated lift due to seasonality. DiD performed admirably as the gold standard for Geo Testing Measurement, giving us a reliable 13% lift. Synthetic Control produced unreliable results due to the model only using a single control market as its comparison (A known issue of synthetic control). This method should be continued as a second source of lift to validate DiD, but we should remain aware that with the Dirichlet priors and correlted control markets; next time through we can use a ridge-penalized variant. BSTS was attempted but skipped due to software version issues encountered during the analysis. As the Python version is unmaintained, next time around we would use a sub-process through R on the production-grade variant so we could test this method.

---

## Recommended Next Steps

- Inform leadership that we will not roll this out nationally next year, but run an additional test instead
- Design next years geo test properly (random market assignment, matched pairs, adequate n, use this test as a power calculation)
- Rerun on a larger market set to estimate ATE, not just ATT, which is the average treatment effect so we generalize the results to all markets, not just treated markets.
- Analyze the post-promo hangover period next test
- Order our success criteria between conversion lift, revenue lift, LTV lift, and customer acquisition.

---

*Analysis conducted using a Bayesian Difference-in-Differences model (CausalPy/PyMC). Results reflect posterior mean estimates with 94% credible intervals. Synthetic control and CausalImpact were attempted; limitations documented in the notebook and technical notes.*