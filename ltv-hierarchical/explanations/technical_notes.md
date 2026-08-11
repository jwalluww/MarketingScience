# Technical Notes: Ridgeline Outfitters LTV Study
*Prepared for: Data Science Manager Review*

## 1. Objective
Maya requested a way to understand how much to spend to aquire and retain customers to differentiate between one-time purchasers & repeat purchasers. We were initially spending the same amount to acquire each customers but needed a way to differentiate who to include in these expensive paid search campaigns. It was determined that we develop a forward looking LTV model to predict the future value of each customer and whether they are worth the $60 CAC.


## 2. Data Summary
The data is simple, 1000 customers, 5,282 orders, data from 1/1/2022 to 12/31/2024, slightly right skewed with some high outliers in spend. Average per transaction sales of $181, median sales of $132, max sales of $800, and 46% one-time purchasers.


## 3. Why BG/NBD + Gamma-Gamma
The decision to go Bayesian over ML comes down to two factors. Firstly, the request from Maya specifically calls out requiring confidence ranges and while ML has confidence intervals, Bayesian credible intervals are more interpretable. Secondly, the primary features driving LTV are RFM-based, which defeats the purpose of ML which is to use non-RFM features to enhance predictability.


## 4. The BG/NBD Model — Purchase Process



## 5. The Gamma-Gamma Model — Spend Process



## 6. The lifetimes Obstacle



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