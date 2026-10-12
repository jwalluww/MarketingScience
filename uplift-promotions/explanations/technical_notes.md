# BlueWing Air Uplift Model — Technical Notes

*10/12/2026*
*Justin Wall*

---

## Objective

Alex wants to know which customers should receive a discount and which should not for his upcoming promotion. We are determining the average treatment effect of sending a promo to select customers, and the conditional average treatment effect of sending to customers selected by our model.

---

## Data

### Source & Design
The data for this model was collected in a 50/50 randomized control test for 20,000 customers on a single day. Half of the customers received an email with a 15% discount for TakeOff Tuesday, and half received a normal email with no discount code.

### Balance Check
We balanced our test on 5 numeric features using standardized mean difference, plus the loyalty tier mix. All SMDs are below 0.10.
                              smd         result
tenure_months            0.003609  well balanced
bookings_last_12mo       0.014332  well balanced
days_since_last_booking -0.018793  well balanced
avg_fare_last_year       0.008757  well balanced
email_engagement_score   0.000303  well balanced

### Feature List
We used these 6 features for our uplift model (7 if you break loyalty status out into binary variables and drop one).
- tenure_months
- loyalty_tier_silver
- loyalty_tier_gold
- bookings_last_12mo
- days_since_last_booking
- avg_fare_last_year
- email_engagement_score

---

## EDA

### Naive Lift
Below are the results from our original AB test - the treatment booked at a 22.9% higher rate than the control. Absolute lift 4.8 percentage points (95% CI 3.6 to 6.0).
Control: 20.905%  |  Treatment: 25.698%
Absolute lift: 4.793%  |  Relative lift: 22.9%
z-stat: 8.02  |  p-value: 0.0000

### Heterogeneity by Loyalty Tier
Below are the breakouts for each group by each feature. 

treatment                  feature  control  treatment pct_diff
0                    tenure_months    42.16      42.25    0.21%
1               bookings_last_12mo     1.61       1.63    1.33%
2          days_since_last_booking    91.76      90.05   -1.87%
3               avg_fare_last_year   200.11     200.61    0.25%
4           email_engagement_score     0.45       0.45    0.01%


-------------------------------


treatment    control treatment    diff
loyalty_tier                          
Basic         59.59%    58.74%  -0.85%
Gold           9.26%    10.55%   1.29%
Silver        31.15%    30.71%  -0.44%


treatment	0	1
loyalty_tier		
Basic	0.140	0.209
Gold	0.442	0.344
Silver	0.271	0.320

Basic and silver have higher conversion rates for the treatment, gold has higher conversion rates for the control.


---

## Modeling Approach

### Methods
We modeled with the full suite of uplift modeling techniques.

S-learner: A single model with the treatment indicator as a feature in the model. Score the population setting the treatment indicator to 1, then scoring again at 0. Subtract the 0 scores from the scores for the final uplift model score. The weighting was 50/50 which makes this model a viable baseline, but treatment indicator could get underestimated.
T-learner: Two models, one model for the treatment and one for the control.Score the population using the treatment model and the control model. Subtract the control model scores from the treatment model scores for the final uplift model score. The estimates are noisier because the model only ever sees half the population.
X-learner: The same as the T-Learner, but uses a second stage model that imputes individual treatment effects and reweights by propensity. This one works well with unbalanced groups, which we don't struggle with so it may be overkill.
R-learner: Removes the effect of the features from both outcome and treatment, then regresses the leftover outcome on the leftover treatment. Uses the known 0.5 propensity.
Doubly Robust learner: Combines an outcome model with propensity weighting. It stays accurate if either piece is right. Uses the known 0.5 propensity.
Causal forest: Ensemble of trees  that splits on treatment effect heterogeneity directly instead of splitting on outcome & subtracting afterwrad.
Uplift trees: Model that splits directly on treatment effect divergence instead of fitting outcome model & subtrating.
Policy learning: Not estimating CATE, learns a depth-3 rule that maximizes expected revenue.

### Implementation Notes / Gotchas
Here are a few modeling issues that caught me up while I was training:
- R & DR leaners need the propensity as an array (np.full(n, 0.5)), not a scaler (0.5).
- CausalML's uplift forest classifier prediction function already returns the uplpift scores, no need to subtract or index any columns.
- Uplift trees need string treatment labels.
- CausalML throws ChainedAssignmentError warnings from inside library - filtered them out.
- PolicyTree needs an (n,2) reward matrix [0, tau_revenue]. A 1D target silently recommends no discount for everyone.
- Set random_state on every model so results don't shift between runs.
- The IPW values already estimate the full population, scale by list size only, never by 2.

---

## Validation

### Track 1 — No Ground Truth (production-realistic; basis for results.md)
I ran 5 validation techniques, just to understand how each of them could help determine the winning model.
- Value-Based Backtest (IPW-corrected): Sort customers by predicted tau, and at several targeting depths, compute IPW-corrected net revenue given promo codes.
- Transformed-Outcome Proxy (MSE / correlation): Over exagerating each customer and averaging them together to cancel out the noise so the effect remains.
- Qini Score: Ranking across the entire population based on the outcome (conversion).
- Bootstrap Stability Check (top-decile Jaccard): Looking for a large overlap of top decile customers to show model stability.
- Face Validity: For the winning models, check feature profile and see if the customer feels like a persuadable.

### Track 2 — Private Ground Truth (synthetic data only, personal learning)
I have the true segments, but did not do any validation on them, as I want this to be a 100% no ground truth project.

### Model Leaderboard
|    |   Backtest peak ($/customer) |   TO MSE |   TO corr |   Qini |
|:---|-----------------------------:|---------:|----------:|-------:|
| S  |                       48.052 |    0.926 |     0.02  |  0.131 |
| T  |                       46.348 |    0.973 |     0.016 |  0.09  |
| X  |                       46.97  |    0.947 |     0.011 |  0.077 |
| R  |                       46.895 |    0.993 |     0.007 |  0.087 |
| DR |                       47.231 |    0.965 |     0.017 |  0.06  |
| UT |                       50.005 |    0.918 |     0.062 |  0.182 |
| CF |                       48.92  |    0.921 |     0.039 |  0.183 |

---

## Model Selection
I chose the causal forest for the following reasons:
1. It scored in the top 2 in the value-based back test, which ultimately determined by send list for Alex.
2. It scored in the top 2 in Qini & transformed outcome as well.
3. Causal forest top decile looked more like a persuadable customer than the uplift tree top decile, which looked more like an underwhelming customer.
4. Causal forest also has confidence intervals which were used to create the segments.

---

## Segmentation Methodology
We are going to use the value-based backtest to determine the customer list for this model, and we used the 90% CI from the causal forest to create the segments.

The uplift practice isn't complete without explaning lost cause, sure thing, persuadable, and sleeping dogs. To determine these segments I used the causal forest model, and took the statistically significantly higher than 0 customers as persuadables, statistically signficantly lower than 0 customers as sleeping dogs, above baseline purchase propensity (about 21%) as sure things, and below baseline purchase propensity as lost causes.

---

## Limitations & Assumptions
This model is specific to 15% off via email for a one-day promotion.
In addition, we do not know whether or how discounts alter future customer behavior.
Segments are thresholds on a continous estimate. Customers can easily flip back and forth segments between runs (which is why it isn't used to create the send list).

---

## Future Work
Run randomized control test on campaigns that vary discount size & channel, so one model can learn each effect. We cannot pool past campaigns to replicate this. We can also estimate effects for several discount levels and find the cheapest per customer. Keep a random 10% of each campaign out as control, and randomly send to half of the 6% exclusion. Re-score before each campaig and retrain when holdout lift drops. We can also do multi-offer targeting, where we send mutiple offers out and model which offer returns the highest expected net revenue after discount cost.

---