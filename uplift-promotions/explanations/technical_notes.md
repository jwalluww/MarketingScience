# BlueWing Air Uplift Model — Technical Notes

*9/23/2026*
*Justin Wall*

---

## Objective

*[Problem framing — CATE estimation for promo targeting]*
Alex wants to know which customers should receive a discount and which should not for his upcoming promotion. We are determining the conditional average treatment effect of sending a promo to select customers.

---

## Data

### Source & Design
*[RCT, 50/50 split, n=, discount %, outcome window]*

### Balance Check
*[SMD table across features]*

### Feature List
*[Final feature set used for modeling]*

---

## EDA

### Naive Lift
*[Control vs. treatment conversion rate, CI, z-test]*

### Heterogeneity by Loyalty Tier
*[Raw conversion by tier x arm]*

---

## Modeling Approach

### Methods
*[S / T / X / R / DR-learner, Causal Forest, Uplift Trees, Policy Tree — one line each on why included]*

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

---

## Segmentation Methodology

*[CI-based movable/non-movable split + economic break-even filter]*

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