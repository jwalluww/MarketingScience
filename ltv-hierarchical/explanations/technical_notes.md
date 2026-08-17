# Technical Notes: Ridgeline Outfitters LTV Study
*Prepared for: Data Science Manager Review*

## 1. Objective
Maya requested a way to understand how much to spend to acquire and retain customers to differentiate between one-time purchasers & repeat purchasers. We were initially spending the same amount to acquire each customers but needed a way to differentiate who to include in these expensive paid search campaigns. It was determined that we develop a forward looking LTV model to predict the future value of each customer and whether they are worth the $60 CAC.


## 2. Data Summary
The data is simple, 1000 customers, 5,282 orders, data from 1/1/2022 to 12/31/2024, slightly right skewed with some high outliers in spend. Average per transaction sales of $181, median sales of $132, max sales of $800, and 46% one-time purchasers.


## 3. Why BG/NBD + Gamma-Gamma
The decision to go Bayesian over ML comes down to two factors. Firstly, the request from Maya specifically calls out requiring in her words "confidence ranges". While ML has confidence intervals, Bayesian credible intervals are more intuitive. Secondly, the primary features driving LTV are RFM-based, which defeats the purpose of ML which is to use non-RFM features to enhance predictability.

Let's review the proper way to read a BG-NBD model
0. What do the parameters mean...
- r: How purchase rates vary across customers. Low r means more heterogeneity where some customers buy a lot and some buy very little, high r means customers are similar to each other.
- alpha: helps set the expected purchase rate per time unit. r/alpha in this case gives you the purchases per cusotmer per week on average. So we have 0.037, or 1 every 6 months.
- phi: dropout probability, value near 0 means avg customer has very low per-transaction probability of churning.
- kappa: how tightly customers are clustered around the mean dropout probability; high kappa means customers are similar in churn behavior, low means some churn faster than others.
- a: beta distribution parameter over dropout probabilities - derived directly from phi and kappa. Shows how many customers have high churn probability. When it's close to zero, it's saying nearly nobody churns. We know from our model that isn't true, but we had a degenerate corner problem.
- b: beta distribution parameter over dropout probabilities - shows how many customers have low churn probability. Faced the same issues as a in our model.
1. R-Hat column -> check these parameters and ensure they are 1.01 or below. If not, chains didn't mix and nothing else means anything.
- Alpha: 1.00
- Phi: 1.00
- kappa: 1.00
- r: 1.00
- a: 1.00
- b: 1.00
2. ESS Bulk & ESS Tail -> bulk & tail above 400 per chain, so 1600 for 4 chains. Bulk tells you how well the sampler explored the center of the distribution; tail tells you how well the sampler explored the extremes which is important for the credible interval. Both healthy means your uncertainty estimates are trustworthy.
- alpha: ess_bulk=2259, ess_tail=2385
- r: ess_bulk=2149, ess_tail=2151
- kappa: ess_bulk=2780, ess_tail=2094
- a: ess_bulk=2169, ess_tail=1606
3. Identifiability check -> SD relative to mean. r and alpha are solid, the dropout side of things are poorly or not identified, which means our model is not certain about churn due to the heavy one-timer population.
- r: sd is 6% of mean -> Well Identified
- alpha: sd is 8% of mean -> Well Identified
- phi: sd is 100% of mean -> Poorly Identified
- kappa: sd is 676% of mean -> Not Identified
- a: sd is 284% of mean -> Not Identified
- b: sd is 676% of mean -> Not Identified
4. HDI, the credible interval. Tight means confident, wide means uncertainty, touching 0 means the coefficient might not even matter to the model, like churn here.
- r: 0.324 - 0.408 -> tight
- alpha: 8.229 - 11.298 -> tight
- kappa: 1.000 - 8.405 -> wide but above 0
- a: 0.000 - 0.016 -> touching 0
- b: 0.997 - 8.403 -> wide but above 0
5. MCSE, how much sampling noise in estimates. MCSE is the standard error of the posterior mean/sd estimates due to finite sampling. For r it's 0, so the mean estimate is precise. For kappa it's large, meaning the uncertainty estimate is uncertain, we would need way more draws to know how uncertain kappa is. We can continue, just know kappa is an issue.
r: mcse_mean=0.000, mcse_sd=0.000 -> great!
kappa: mcse_mean=0.543, mcse_sd=10.840 -> not great
6. Summary -> Purchase process is well identified & trustworthy. r=0.364 means customers make one purchase every three weeks when active and alpha=9.74 describes how that varies across customers. the dropout process is not identified, a, b, kappa are all over the place. We cannot estimate the rate of churn nor how quickly they will churn.


## 4. The BG/NBD Model — Purchase Process
The beta-geometric negative binomial distribution model is meant to predict purchase frequency & churn probability We had a clean run for BG/NBD with R-Hats at 1.0, ESS at 2000+, r & alpha looking, but a & b have issues. a = 0.006 with a wide HDI, the dropout parameter is close to 0. Kappa-dropout = 4.045 with sd of 17 means the model is uncertain about the dropout process. The model is good, it just means we don't have clear signals as to when a customer will drop out; low purchase rate vs churn isn't distinguished. Hazard of having a business with lots of one-timers. So essentially the dropout parameters showed high uncertainty. 


## 5. The Gamma-Gamma Model — Spend Process
Let's review the proper way to read a gamma-gamma model
Firstly, gamma gamma answers how much customers will spend each time they buy. Assuming each customers has their own true average spend level. One gamm si for the transaction level noise around the customers mean and the other is te population distribution of the personal means. 
0. What do the parameters mean...
- p: how much individual transactions vary around a customers own personal mean spend, within customer noise. Clsoe to 1 means moderate transaction level variability. Higher p means tighter clustering around the customer mean, lower p means larger per transaction swings.
- q: how personal spend means are distributed across the customer population. higher q means population spend is more concentrated - people are more alike. lower q means wider range of customer spends. it works with v to set the actual dollar.
- v: the rate parameter of the population level gamma. together wtih q, sets the average spend level across customer base. v/ maps back to dollars when multiplied by mv_mean -> expected spend per transaction at the population level.
1. What is this customer's personal spend fingerprint?
    - estimated from their past monetary value
    - shrunk towards teh popuation mean for sparse data customers
    - that's teh partial pooling hierarchical potion
2. given the fingerprint spend, what will they spend next time?
    - sample from personal gamma with shape p
    - average across many transactions = their expected spend per order


## 6. The lifetimes Obstacle
Lifetimes BetaGeoFitter failed to converge with both default and custom initial parameters. The dropout parameters (a, b) collapsed to near zero regardless of penalizer or starting values, indicating a degenerate likelihood surface rather than a tuning issue. With this largely one-time-purchase population, the optimizer found a corner solution where no one churns.
This likely occurred because the T values are so large and the number of one-time purchasers is too many. We attempted to switch Lifetimes to weeks instead of days, but the issue continued.
We switched to PyMC-Marketing for full Bayesian estimation with priors that act like guardrails and prevent parameter collapse. In this library we his an issue as well (stated below in #7) where we started in days and switched to weeks once again.
In addition, I would not deploy a Lifetimes model due to the library not being maintained.


## 7. The Gamma-Gamma Non-Identifiability Obstacle
Gamma-Gamma non-convergence on raw monetary values (r_hat ~2.27, ESS ~5-7 across multiple runs). Root cause: q and v are non-identifiable at dollar scale, default diffuse priors allow the sampler to explore degenerate q/v combinations in the trillions. Scaling monetary value by its mean resolved the geometry. Predictions must be multiplied back by mv_mean to recover dollar estimates.


## 8. Model Validation — Holdout Period
The holdout period is the last 6 months of 2024. The holdout mean is 0.873 and the predicted mean is 0.968. The model is over-predicting by 0.095 purchases per customer on average. The bias for the model is overpredicting because the dropout aspect isn't as strong - so the model says one-timers may return when they do not. This optimistic view of customers may place CAC numbers on the high side, but better to over-spend and actually gain the customers than under-spend and miss out on the customers. This is the reason we are suggesting to the business to use the lower bound of the estimates to ensure costs for customer acquisition aren't too high.


## 9. Known Limitations
One known limitation of Bayesian method is the inability to use features to help predict LTV. As long as the primary factors driving LTV are RFM, then Bayesian models will work just fine. Another limitation, not of the model but of the data, is the dropout process. With 46% one-time customers, the model had a hard time predicting who is going to churn, so that portion of the BG/NBD model is unreliable.

## 10. Deployment, Scoring & Monitoring
*How this model gets used after the notebook closes.*

### Retraining
The model can be retrained quarterly to reflect changes in customer behavior, macro conditions, and the overall business trend. Retraining can also be timely given how slow MCMC is on a full customer base. Each time we retrain, we can save the model as a NetCDF (.nc), which will score the customers each time we call it.

### Scoring
We can score this model using the posterior and avoid MCMC each time we need fresh scores, weekly or monthly depending on usage. A full customer base only takes seconds to score. The scores will live in a Snowflake table where we can query them easily. 

### Monitoring & Drift Detection
We can monitor the features of this model between our training time period to our production time period to ensure the features remain consistent.

### Recommended Cadence
The model should be scored every few months, to pick up any new customer trends and avoid the messiness and noise of every day purchasing. 

## 11. If I Had More Time / Resources

1. Build ML model on top of BG/NBD to help detect churn, or PyMC-Marketing 1.0 also offers Pareto/NBD and Modified BG/NBD as alternatives to standard BG/NBD; given the dropout parameter identifiability issues encountered here, testing against Modified BG/NBD would be a natural extension.
2. Run testing on LTV model to determine it's accuracy & precision.
3. Clustering the final scores to create segments to be more precise than quarter splits.
4. Build uplift model on top of this model to help define marketing strategy.
5. Recalibrate ROAS/CAC via add_cost_per_target_calibration functino from the MMM module in PYMC-Marketing. It calibrates a marketing mix model against actual cost-per-acquisition estimates. This is the natural "next project" extension, instead of static CAC thresholds from LTV alone, I'd have a full MMM that optimizes spend allocation across channels while respecting CAC ceilings by segment.
6. Experiment Calibration and lift test integration, which fine-tunes a model using empirical experiment results. This connects directly to LTV not being be a short-term metric, but it can be used for longer experiments. This is PyMC-Marketing's built-in mechanism for reconciling model predictions with actual test results.