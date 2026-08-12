# Technical Notes: Ridgeline Outfitters LTV Study
*Prepared for: Data Science Manager Review*

## 1. Objective
Maya requested a way to understand how much to spend to aquire and retain customers to differentiate between one-time purchasers & repeat purchasers. We were initially spending the same amount to acquire each customers but needed a way to differentiate who to include in these expensive paid search campaigns. It was determined that we develop a forward looking LTV model to predict the future value of each customer and whether they are worth the $60 CAC.


## 2. Data Summary
The data is simple, 1000 customers, 5,282 orders, data from 1/1/2022 to 12/31/2024, slightly right skewed with some high outliers in spend. Average per transaction sales of $181, median sales of $132, max sales of $800, and 46% one-time purchasers.


## 3. Why BG/NBD + Gamma-Gamma
The decision to go Bayesian over ML comes down to two factors. Firstly, the request from Maya specifically calls out requiring confidence ranges and while ML has confidence intervals, Bayesian credible intervals are more interpretable. Secondly, the primary features driving LTV are RFM-based, which defeats the purpose of ML which is to use non-RFM features to enhance predictability.


## 4. The BG/NBD Model — Purchase Process
The beta-geometric negative binomial distribution model is meant to predict purchase frequency & churn probability We had a clean run for BG/NBD with R-Hats at 1.0, ESS at 2000+, r & alpha looking, but a & b have issues. a = 0.006 with a wide HDI, the dropout parameter is close to 0. Kappa-dropout = 4.045 with sd of 17 means the model is uncertain about the dropout process. The model is good, it just means we don't have clear signals as to when a customer will drop out; low purchase rate vs churn isn't distinguished. Hazard of having a business with lots of one-timers. So essentially the dropout parameters showed high uncertainty. 


## 5. The Gamma-Gamma Model — Spend Process



## 6. The lifetimes Obstacle
Sure enough, Lifetimes BetaGeoFitter failed to converge with both default and custom initial parameters. The dropout parameters (a, b) collapsed to
effectively zero regardless of penalizer or starting values, indicating a degenerate likelihood surface rather than a tuning problem. With this largely one-time-purchase population, the optimizer found a corner solution where no one churns.
This likely occurred because the T values are so large and the number of one-time purchasers is too many. We moved to weeks instead of days, and tried initial params but ended up moving to PYMC-Marketing because it still wasn't working.
Switching to PyMC-Marketing for full Bayesian estimation with priors that prevent parameter collapse, acting like guardrails. In addition, I would probably not deploy a Lifetimes model due to the library not being maintained.

Rebuilt RFM in weeks (vs days), days caused optimizer failure in lifetimes, weeks preemptively resolving scaling issues in PyMC-Marketing.


## 7. The Gamma-Gamma Non-Identifiability Obstacle



## 8. Model Validation — Holdout Period



## 9. Known Limitations



## 10. Deployment, Scoring & Monitoring
*How this model gets used after the notebook closes.*

### Retraining
### Scoring
### Monitoring & Drift Detection
### Recommended Cadence

## 11. If I Had More Time / Resources

1.
2.
3.