# Uplift Modeling Study Guide

Notation used throughout:
- **x** = a customer's features (tenure, tier, bookings, engagement, etc.)
- **W** = treatment (1 = got the code, 0 = didn't)
- **Y** = outcome (1 = booked, 0 = didn't)
- **e** = probability of getting treatment (0.5 in this RCT)
- **tau(x)** = uplift = P(book | x, W=1) minus P(book | x, W=0)

---

## Part 1: The Seven Uplift Models

### S-learner (Single model)
- **What it does:** Fits one outcome model with the features plus W as just another input.
- **Uplift score:** Predict for each customer with W=1, predict again with W=0, subtract. `tau = mu(x, 1) - mu(x, 0)`, where mu is the fitted outcome model.
- **Works well when:** The treatment effect is large relative to everything else, and you want a quick baseline.
- **Fails when:** The treatment is a weak signal. Regularization or trees can ignore W, so effects shrink toward zero and heterogeneity is missed.
- **In this project:** Mid-pack baseline.

### T-learner (Two models)
- **What it does:** Fits one model on treated customers only and one on control only.
- **Uplift score:** `tau = mu1(x) - mu0(x)`, treated model minus control model.
- **Works well when:** Treated and control behave very differently, and each arm has plenty of data.
- **Fails when:** Each model sees only half the data, and their errors don't cancel when subtracted. With a ~20% conversion rate and a small effect, the noise in the difference swamps the signal.
- **In this project:** Near the bottom of the rankings.

### X-learner
- **What it does:** Starts like a T-learner, then adds a second stage. It imputes each customer's missing outcome using the other arm's model, treating the gap as an individual effect. It fits models on those imputed effects.
- **Uplift score:** `tau = g(x) * tau0(x) + (1 - g(x)) * tau1(x)`, where tau1 and tau0 are second-stage effect models fit on treated and control customers, and g(x) is the propensity (0.5 here), used to weight them.
- **Works well when:** Arms are unbalanced (say 90/10) and the effect is smooth.
- **Fails when:** Arms are balanced and outcomes are noisy binary. The extra stages add variance without adding benefit, which is exactly this project's situation.
- **In this project:** Near the bottom. The notes called it overkill.

### R-learner (Residual on residual)
- **What it does:** Removes the part of Y explained by x, removes the part of W explained by x, then regresses the leftover outcome on the leftover treatment.
- **Uplift score:** Finds tau(x) minimizing `sum( (Y - m(x)) - (W - e) * tau(x) )^2`, where m(x) is the expected outcome given x. The fitted tau(x) is the score.
- **Works well when:** The nuisance models (m and e) are good. Errors in them have only a second-order effect on tau.
- **Fails when:** Residualized treatment `W - e` is tiny (propensity near 0 or 1), or the nuisance models are poor. It is high-variance in practice.
- **In this project:** Decent ranking but poorly scaled. You passed the known 0.5 propensity.

### Doubly Robust (DR) learner
- **What it does:** Builds a corrected pseudo-outcome per customer that combines the outcome models with inverse-propensity weighting, then regresses that on x.
- **Uplift score:** Pseudo-outcome `phi = mu1(x) - mu0(x) + W(Y - mu1(x))/e - (1 - W)(Y - mu0(x))/(1 - e)`. Then fit a model of phi on x. Its prediction is tau.
- **Works well when:** Either the outcome models or the propensity is right (that's the "doubly robust" part). With a known RCT propensity, it is protected.
- **Fails when:** Effects are small and outcomes noisy. Dividing by e inflates variance, so the pseudo-outcome is very noisy.
- **In this project:** Worst ranker in the earlier run.

### Causal Forest (EconML `CausalForestDML`)
- **What it does:** First residualizes Y and W (the DML step), then grows an ensemble of trees whose splits maximize differences in treatment effect between child nodes. It is honest: one half of the sample picks splits, the other half estimates the effects in each leaf.
- **Uplift score:** The average of leaf effect estimates across trees for that customer.
- **Works well when:** There is real, nonlinear heterogeneity and a decent sample. It handles interactions automatically and gives confidence intervals (`effect_interval`).
- **Fails when:** Signal is weak or the sample is small. Estimates get noisy and unstable, and top-ranked customers are partly winners of a noisy lottery.
- **In this project:** Top-two on the validation metrics, which is why it was chosen. Its top-decile overlap across bootstrap refits was low, because part of the true heterogeneity isn't observable.

### Uplift Random Forest (CausalML `UpliftRandomForestClassifier`)
- **What it does:** Trees split to maximize the divergence between treated and control outcome distributions in the child nodes (KL, chi-squared or Euclidean).
- **Uplift score:** In each leaf, treated conversion rate minus control conversion rate. Averaged across trees. `predict()` returns this directly.
- **Works well when:** The outcome is binary, you want a model built to find uplift, and you want interpretable splits.
- **Fails when:** You need confidence intervals (it has none), or the data is noisy. Leaf estimates come from small samples, so it can overfit.
- **In this project:** Top ranker on Qini and the proxy, but its top decile drifted toward low-engagement customers, which fails the "persuadables look typical" check.

---

## Part 2: The Five Validation Methods

No single check proves a model right. They test different things (ranking, dollars, calibration, variance, plausibility), so trust comes from agreement.

### 1. Value-based backtest (IPW)
- **How it works:** Rank customers by predicted tau. For each depth (top 5%, 10%, ... 100%), the policy is "send to these, not to the rest". Keep only customers whose actual arm matches what the policy would have done. Weight each by `1/e` (treated) or `1/(1 - e)` (control). Average their revenue.
- **What it validates:** Real dollars. It is the only check that tells you how many customers to target and what it's worth.
- **Works when:** Randomized data with a known propensity. It directly answers the business question.
- **Doesn't work when:** Samples are small (only matched customers count, so differences of a few thousand dollars are noise). Choosing the best depth on the same data is optimistic.

### 2. Transformed outcome
- **How it works:** Make a per-customer stand-in for the true effect: `Z = Y * (W - e) / (e * (1 - e))`. It takes only the values -2, 0, +2 here. On average, Z equals tau(x). Compare each model's tau to Z using MSE and correlation.
- **What it validates:** Whether predicted effects are close to the true effects, on average.
- **Works when:** An RCT with a decent sample. It lets you rank models against each other.
- **Doesn't work when:** You read the absolute numbers. Z is so noisy (variance about 0.93) that MSE is mostly noise. Only the differences between models mean anything.

### 3. Qini score
- **How it works:** Sort customers by predicted tau. At each fraction targeted, compute cumulative incremental conversions: treated conversions minus control conversions (scaled for arm size). Plot against fraction targeted. The Qini score is the area between this curve and the straight random-targeting line.
- **What it validates:** Ranking quality: do the customers you put first really have the biggest effect?
- **Works when:** You have a fixed budget and care about who comes first.
- **Doesn't work when:** Dollars matter. It uses conversions, so it ignores price and discount cost. It also says nothing about whether tau is correctly scaled.

### 4. Stability check (bootstrap top-decile overlap)
- **How it works:** Resample the training data, refit the model several times, and take each fit's top 10%. Compute the Jaccard overlap between each pair: `|A intersect B| / |A union B|`, where A and B are two top-decile sets.
- **What it validates:** Whether the model picks the same people regardless of sampling luck.
- **Works when:** It catches overfit models whose lists change with every refit.
- **Doesn't work when:** Low overlap can also come from real heterogeneity the features can't see. A model that confidently picks the wrong people consistently also scores well. A few resamples (3) make the number itself noisy.

### 5. Face validity
- **How it works:** Profile the top-decile customers by feature averages and ask whether they look like the people the business logic says would respond.
- **What it validates:** Plausibility: no flipped signs, no bugs, no absurd picks.
- **Works when:** It catches obvious failures fast.
- **Doesn't work when:** It confirms your prior. It can't prove an effect, and two very different models can both look sensible.

---

## Part 3: The Policy Idea and Where It Fits

**The chain.** Uplift models *predict*: how much does a code change this customer's chance of booking? A business must *decide*: should this customer get a code, given the code costs money? A policy is the decision rule that sits on top of the predictions:

1. **Predict** tau(x) with the model.
2. **Compare to cost.** A 15% discount only pays if the extra bookings make up for the margin lost on customers who'd book anyway.
3. **Decide** who gets the code.
4. **Optionally simplify** the decision into a rule a person can follow.

**The economic break-even.** Let `p0` be the customer's baseline booking probability, `tau` the uplift, and `P` the full price. Not sending earns `P * p0`. Sending earns `0.85 * P * (p0 + tau)`, because they pay 85% of the price. Sending pays when:

`0.85 * (p0 + tau) > p0`, which simplifies to `tau / p0 > 0.15 / 0.85`, about 17.6%.

So the relative lift (`tau / p0`) must beat about 17.6%. A Sure Thing with a high `p0` needs a big absolute tau to justify a discount. This rule uses the model's tau and p0 estimates directly, so it inherits their noise.

**In this project.** The break-even was only a filter on confirmed persuadables (and removed nobody). The actual send list used the value backtest to choose depth, since it measures dollars on observed data and doesn't depend on noisy tau/p0 ratios.

**The policy tree (EconML `PolicyTree`).** Instead of scoring each customer, it learns a short if/then rule (depth 3 here) that maximizes expected revenue. You give it a reward table with one row per customer and two columns: the value of not sending (0) and the incremental dollars from sending (tau from a revenue-based causal forest). It finds the splits that capture the most dollars.

**Where it fits.** Think of it as a compression of the model:
- The model gives the best list, but needs to be rerun and rescored.
- The tree gives a rule anyone can apply from a spreadsheet, at the cost of precision.
- It's a fallback for quick campaigns when the model isn't available. It does not replace the model, and it needs its own backtest before anyone trusts it.
