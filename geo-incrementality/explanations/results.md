# Geo Incrementality Measurement: Findings & Recommendation
**To:** Megan Hartley, VP of Marketing, Luma Candle Co.
**From:** Data Science
**Re:** Spring Refresh Promo, Incremental Lift Analysis

---

Hi Megan, findings below. Short version at the top, detail underneath.

## The Short Version



[2-3 sentences. State the true incremental lift %, what the naive read was, the gap between them, and your rollout recommendation. This is what gets read in the hallway.]

---

## What the Channel Team Reported vs. What Actually Happened

[Explain the naive pre/post number — what it was, how it was calculated (treated markets before vs. after), and why it overcounts. Don't be technical, just explain that it can't separate the promo's effect from normal March growth. Then state the DiD number.]

| Method | Estimated Lift | Notes |
|---|---|---|
| Naive Pre/Post (channel report) | [fill in naive_lift_pct]% | No counterfactual — includes organic trend |
| Difference-in-Differences (causal) | [fill in att_pct]% | Controlled for what control markets did |
| Ground truth (synthetic) | 22.0% | Baked into dataset for validation |

[1 paragraph interpreting the gap. What does it mean that the naive read was X% higher? What would have happened if Megan had taken the naive number to leadership?]

---

## How Confident Are We?

[Fill in the DiD 95% credible interval. Explain in plain language what that interval means — not "there is a 95% probability that..." but something like "we'd need to see something really unusual happen in both the data and the model for the true lift to fall outside this range."]

[Note the ATT vs ATE distinction in one sentence — something like: "this estimate applies to markets like the five we tested; whether it generalizes to a national rollout is a separate question."]

---

## Does This Justify a National Rollout?

[Give a concrete answer. Don't hedge excessively. Something like: "Yes, but with a condition." The condition is that the next test should be designed properly — random market assignment, enough markets for adequate statistical power, a clean pre-period. Reference that the current estimate is an ATT (effect on the tested markets) and national rollout generalizes to markets that may look different.]

---

## What We Tried and What We Learned Along the Way

[Brief paragraph — 3-4 sentences. Note that synthetic control was attempted but produced unreliable results due to the model collapsing onto a single donor market, a known limitation of the method with correlated control pools. Note that CausalImpact was attempted but skipped due to a Python library compatibility issue. Frame both as "here's what we learned" not "here's what went wrong."]

---

## Recommended Next Steps

[Bullet list, 3-4 items. Should include:
- Design the next geo test properly (random market assignment, matched pairs, adequate n)
- Rerun on a larger market set to estimate ATE, not just ATT
- Consider using this analysis as the baseline for a power calculation on the national test
- Note the post-promo hangover period in the data and whether that's worth capturing in the next test design]

---

*Analysis conducted using a Bayesian Difference-in-Differences model (CausalPy/PyMC). Results reflect posterior mean estimates with 94% credible intervals. Synthetic control and CausalImpact were attempted — limitations documented in the notebook and technical notes.*