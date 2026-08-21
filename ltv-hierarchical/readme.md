# Customer Lifetime Value Modeling with Hierarchical Bayes
**Justin Wall | Marketing Science Portfolio**

## Overview
A Bayesian customer lifetime value model built to answer a real business question: which customers are worth investing in long-term, and how much should you spend to acquire or retain them? I created a fictional DTC company, built a realistic synthetic transaction dataset, and used BG/NBD + Gamma-Gamma to segment customers by projected 12-month value with full uncertainty quantification.

## Business Context
The initial ask came from Maya Torres, VP of Growth at Ridgeline Outfitters, ahead of a recurring budget fight over rising paid acquisition costs. The full brief is in [explanations/email.md](explanations/email.md). Three questions, all answerable through LTV modeling:

- How much is each customer (or segment) actually worth over the next 12 months?
- How confident should we be in that number?
- What should we be willing to spend to acquire or retain each segment?

## Why BG/NBD + Gamma-Gamma
Ridgeline is a non-contractual, non-subscription DTC business — customers buy whenever they want, with no visible cancellation event when they stop. That rules out survival/churn models built for subscriptions and calls for a model designed specifically for silent churn: BG/NBD estimates purchase frequency and dropout probability from transaction timing alone, and Gamma-Gamma layers in expected spend per transaction. Going Bayesian over an ML regression approach was driven by two things: Maya explicitly asked for confidence ranges, not point estimates, and the primary signal driving LTV here is RFM-based, which plays to BG/NBD's strengths rather than an ML model's.

## Key Findings
Full findings and the response to Maya are in [explanations/results.md](explanations/results.md). Summary:

- Customers split into four LTV tiers ranging from $3.97 to $17,978 in projected 12-month value — a roughly 400x spread between top and bottom quartile
- Top two tiers (50% of customers) justify the current $60 CAC with substantial headroom; bottom two tiers do not
- Elite tier (top 25%) is driven primarily by purchase frequency (10.6 repeat orders/year); High tier is more balanced between frequency and order value, with real room to grow purchase frequency through retention
- Holdout validation showed a modest optimistic bias (+0.095 purchases/customer) — conservative (lower-bound) LTV estimates are recommended for setting CAC thresholds
- LTV should be tracked as a long-term indicator of sustained marketing strategy, not a short-term campaign KPI

## Modeling Notes
The ideal path was `lifetimes`' MLE-based BetaGeoFitter — fast, simple syntax. In practice, the heavy one-time-purchaser population (46% of customers) created a degenerate likelihood surface: the optimizer collapsed the dropout parameters to zero regardless of penalizer or initial values, effectively claiming no one ever churns. After diagnosing this as structural rather than a tuning problem, I switched to PyMC-Marketing, where priors act as guardrails against parameter collapse. The Gamma-Gamma fit then hit its own non-identifiability issue — q and v blew up into the trillions at raw dollar scale — resolved by scaling monetary values and applying tighter, domain-informed priors. Both obstacles, the diagnosis, and the fixes are documented in the notebook and technical notes.

## Tech Stack
- Python, PyMC-Marketing, PyMC, ArviZ
- lifetimes (attempted, documented as unmaintained/non-convergent)
- BetaGeoModel, GammaGammaModel
- pandas, numpy, matplotlib

## Repo Structure
```
explanations/
    email.md      -- The original ask from Maya
    results.md    -- Findings and CAC/segmentation recommendation
    technical_notes.md  -- Technical notes on the model itself
data/
    ridgeline_outfitters_transactions.csv   -- Synthetic dataset (2022-2024)
notebooks/
    clv_hierarchical_bayes.ipynb  -- Full model, EDA, diagnostics, results
outputs/
    -- Any png or output from the model
model/
    -- The model itself
```