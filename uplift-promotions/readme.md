# Uplift Modeling for Promotional Targeting
**Justin Wall | Marketing Science Portfolio**

## Overview
An uplift modeling project built to answer a real business question: who should actually get a discount code, and who is just getting free margin? I created a fictional budget airline, built a realistic synthetic dataset from a 50/50 randomized promo test, and compared seven uplift methods to build a targeting list and estimate the net revenue impact versus blasting everyone.

## Business Context
The initial ask came from Alex Torres, Director of CRM & Loyalty Marketing at BlueWing Air, ahead of Q3 planning. The full brief is in [explanations/stakeholder_request_email.md](explanations/stakeholder_request_email.md). Alex's questions, all answerable through uplift modeling:

- Are we giving away margin to people who would book anyway?
- Are we training our best customers to expect discounts?
- Who should actually get the discount?
- What is the net revenue impact of sending to the right people instead of everyone?

## Why Uplift
A standard propensity model predicts who will buy. An uplift model predicts who will buy *because of* the offer. That difference splits customers into four groups:

- **Persuadables:** more likely to book if they get a code
- **Sure Things:** will book either way, so the discount is wasted margin
- **Lost Causes:** will not book either way
- **Sleeping Dogs:** less likely to book if they get a code

## Key Findings
Full findings and the response to Alex are in [explanations/results.md](explanations/results.md). Summary:

- The original test: 25.7% conversion with the code vs. 20.9% without (+4.8 percentage points, 22.9% relative lift)
- Segment mix: about 52% Lost Causes, 37% Sure Things, 10% statistically confirmed Persuadables, 2% Sleeping Dogs
- A blanket send gives away roughly $85K in discounts to Sure Things (projected to the full 20K list)
- Sending only to confirmed Persuadables does worse than the blanket send. Many more customers have smaller positive effects that pay off as a group
- Sending to the top 94% of customers by predicted effect projects to **$983,540** net revenue, **$65,568** above the blanket send
- One campaign cannot show whether discounts train customers to expect them. The recommendation is a permanent holdout, re-scoring before each campaign, and watching customers move between segments

## Modeling Notes
I compared S, T, X, R, and Doubly Robust learners, an uplift random forest, and a causal forest. With no ground truth for individual effects, I validated using an IPW-corrected value backtest, Qini scores, and a transformed-outcome proxy. The causal forest ranked in the top two on all three, gives honest confidence intervals, and defines the segments. The send list itself comes from the value backtest, not the segment labels.

Because the test was a true 50/50 randomization, the propensity is known (0.5), so no propensity model was fit.

Caveats: the 94% cutoff was chosen on the same holdout used to report results, so the lift is likely optimistic. Dollar figures are projections from a 6,000-customer holdout scaled to the full 20K list and carry sampling error. The dataset includes private ground-truth columns that were not used in modeling.

## Tech Stack
- Python, CausalML, EconML (CausalForestDML)
- XGBoost, scikit-learn
- statsmodels, SciPy
- pandas, numpy, matplotlib

## Repo Structure
```
explanations/
    stakeholder_request_email.md   # The original ask from Alex
    results.md                     # Findings and targeting recommendation
    technical_notes.md             # Technical notes on the models and validation
data/
    bluewing_air_promo_uplift.csv  # Synthetic dataset (20,000 customers)
    targeting_list.csv             # Final send list from the causal forest
notebooks/
    uplift_promotions_model.ipynb  # Full model, validation, segments, results
outputs/
    # Any png or output from the model
```