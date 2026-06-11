# Bayesian Marketing Mix Model
**Justin Wall | Marketing Science Portfolio**

## Overview
A Bayesian Marketing Mix Model built to answer a real business question: how do you know where to allocate marketing dollars? I created a fictional DTC company, built a realistic synthetic dataset, and used Bayesian modeling to answer five questions a CMO would actually ask before a board meeting.

## Business Context
The initial ask came from Dana Mercer, CMO of Arclight Cycling, ahead of Q4 budget planning. The full brief is in [explanations/email.md](explanations/email.md). Five questions, all answerable through MMM:

- Which channels are actually driving revenue?
- Where are we overspending and underspending?
- If we had to shift $500K, where would it go?
- How long does advertising spend stay effective?
- How confident should we be in any of this?

## Why MMM
As consumer privacy laws tighten and platform-reported attribution becomes less reliable, companies are turning to MMM because it only requires data you already own: spend and revenue. Given the size of the budgets involved, getting this right is one of the highest-leverage things a marketing data scientist can do.

## Key Findings
Full findings and the response to Dana are in [explanations/results.md](explanations/results.md). Summary:

- 40% of revenue is marketing-driven, 60% is organic baseline
- Paid Search drives the most attributed revenue (41% of marketing) but is showing signs of saturation
- Email has the highest ROAS of any channel and is significantly underfunded
- Display showed little evidence of effectiveness and was reduced to a learning budget
- TV spend is baked into baseline brand equity and should be trimmed carefully, not cut
- Projected annual revenue lift from recommended reallocation: **$166,218**

## Modeling Notes
The ideal saturation function for MMM is the Hill function, which is flexible and industry-standard. In practice, its three parameters per channel create a non-identifiability problem: the sampler finds multiple equally valid solutions and never converges. After diagnosing this through R-hat and trace plots, I switched to Logistic saturation, which has two parameters per channel, converges cleanly, and still answers every business question Dana asked. The full diagnosis and reasoning are documented in the notebook.

## Tech Stack
- Python, PyMC-Marketing v0.17.1, PyMC, ArviZ
- GeometricAdstock, LogisticSaturation
- SciPy for budget optimization
- pandas, numpy, matplotlib

## Repo Structure
```
explanations/
    email.md      -- The original ask from Dana
    results.md    -- Findings and budget recommendation
data/
    arclight_mmm_data.csv   -- Synthetic dataset (156 weeks, 2022-2024)
notebooks/
    marketing_mix_model_bayesian.ipynb  -- Full model, EDA, diagnostics, results
```