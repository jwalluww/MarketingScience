# BlueWing Air Uplift Model — Technical Notes

*9/23/2026*
*Justin Wall*

---

## Objective

*[Problem framing — CATE estimation for promo targeting]*
Alex wants to know which customers should receive a discount and which should not for his upcoming promotion. We are determining the average treatment effect of sending a promo to select customers, and the conditional average treatment effect of sending to customers selected by our model.

---

## Data

### Source & Design
*[RCT, 50/50 split, n=, discount %, outcome window]*
The data for this model was collected in a 50/50 randomized control test for 20,000 customers on a Tuesday. Half of the customers received an email with a 15% discount for TakeOff Tuesday, and half received a normal email or nothing at all (unsure about this).

### Balance Check
*[SMD table across features]*
We balanced our test on 6 features: RFM, email engagement, customer tenture, and loyalty status.
                              smd         result
tenure_months            0.003609  well balanced
bookings_last_12mo       0.014332  well balanced
days_since_last_booking -0.018793  well balanced
avg_fare_last_year       0.008757  well balanced
email_engagement_score   0.000303  well balanced

### Feature List
*[Final feature set used for modeling]*
We used these 6 features for our uplift model (7 if you break loyalty status out into binary variables and drop one).
'tenure_months', 'loyalty_tier_silver', 'loyalty_tier_gold', 'bookings_last_12mo', 
        'days_since_last_booking', 'avg_fare_last_year', 'email_engagement_score'
---

## EDA

### Naive Lift
*[Control vs. treatment conversion rate, CI, z-test]*
Below are the results from our original AB test - the treatment booked at a 22.9% higher rate than the control.
Control: 20.905%  |  Treatment: 25.698%
Absolute lift: 4.793%  |  Relative lift: 22.9%
z-stat: 8.02  |  p-value: 0.0000

### Heterogeneity by Loyalty Tier
*[Raw conversion by tier x arm]*
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

---

## Modeling Approach

### Methods
*[S / T / X / R / DR-learner, Causal Forest, Uplift Trees, Policy Tree — one line each on why included]*
We modeled with the full suite of uplift modeling techniques.

S-learner: Build a model using the 

S-learner — One combined damage-estimating formula that just has a "was there a storm" checkbox mixed in with everything else. Fast, but if that checkbox isn't weighted right, the model can quietly bury the storm's real impact under everything else.
T-learner — Two totally separate adjusting teams: one only ever estimates non-storm wear-and-tear, the other only ever estimates storm claims. Subtract their averages to isolate the storm's contribution. Downside: each team only ever sees half the case history, so their estimates are noisier.
X-learner — Same two teams, but then they cross-check each other's blind spots — "what would team B have guessed on team A's case" — and weight the answer by how confident each team actually is on that claim type. Useful when storm claims are way rarer than routine ones, so the small team doesn't get overpowered.
R-learner — Strip out the "normal" expected repair cost trend first, then look only at what's left over and attribute that leftover specifically to the storm. Cleaner accounting trick for isolating the shock from the background noise.
Doubly Robust learner — Like requiring two independent sign-offs: one model on "how likely was this a storm claim," one on "what's the expected cost," combined so if either one's a little off, the final number is still protected.
Causal forest — A full committee of your most senior adjusters, each comparing this claim against similar ones (same roof age, same region, same policy tier) and voting — and instead of just giving you a number, they give you a confidence range. This is the one you'd actually want backing you up in an appeal or a dispute, because "we're 90% confident it's between $X and $Y" holds up a lot better than a bare guess.
Uplift trees — A decision-tree flowchart built from scratch specifically to sort claims into "the storm genuinely mattered here" versus "it didn't" — like a purpose-built triage checklist, not a repurposed cost-estimation form.
Policy learning — The last step, where all that analysis turns into an actual field rule: "if roof age > 15 and wind speed > 60mph, auto-approve without a site visit." This is where the model stops being research and becomes something a frontline adjuster can literally check a box against.

### Implementation Notes / Gotchas
*[Known propensity → array shape requirement, UT predict() output shape, ConvergenceWarning fix, CausalML internal ChainedAssignmentError]*

---

## Validation

### Track 1 — No Ground Truth (production-realistic; basis for results.md)
- Qini Score
- Value-Based Backtest (IPW-corrected)
- Transformed-Outcome Proxy (MSE / correlation)
- Bootstrap Stability Check (top-decile Jaccard)
- Face Validity

### Track 2 — Private Ground Truth (synthetic data only, personal learning)
- PEHE vs. true CATE
- Predicted vs. true segment agreement

### Model Leaderboard
*[Table: all 8 methods × all metrics]*

---

## Model Selection

*[Causal Forest — rationale: convergent top-2 across metrics, honest CIs, face-validity match]*
I ended up choosing the causal forest because it was one of the two best models across every metric, it comes with confidence intervals, and 

---

## Segmentation Methodology

*[CI-based movable/non-movable split + economic break-even filter]*
We are going to use the value-based backtest to determine the customer list for this model, but it's an interesting & useful exercise to determine the segments around this model. The uplift practice isn't complete without explaning lost cause, sure thing, persuadable, and sleeping dogs. To determine these segments I used the causal forest model, and took the statistically significantly higher than 0 customers as persuadables

---

## Policy Learning

*[Revenue-based causal forest → reward matrix → PolicyTree]*

*[Policy tree plot]*

*[Backtested value vs. blanket-send]*

---

## Limitations & Assumptions

*[Campaign-specificity — not generalizable across coupon value/channel without redesign]*

*[Single-snapshot design can't observe cross-campaign habit formation]*

*[Segment boundaries reflect a continuous effect, not discrete truths]*

---

## Future Work

*[Multi-campaign pooling with proper factorial design]*

*[Dose-response / multi-valued treatment CATE]*

*[Permanent holdout for ongoing decay monitoring]*

---

## Appendix

*[Package versions: EconML, CausalML]*

*[Key references]*