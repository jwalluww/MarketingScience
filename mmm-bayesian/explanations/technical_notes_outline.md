# Technical Notes: Arclight MMM Modeling Decisions
*Prepared for: Data Science Manager Review*

## 1. Objective
We built this model to determine how ad spend impacts revenue across five marketing channels, and we needed a tool that allows us to run a 'what-if' analysis for adding and moving revenue from one channel to another. We chose Bayesian methods due to it's ability to calculate uncertainty inherent in MMM. Bayesian MMM allowed us to show the nuance around channels where we see lots of variance, avoiding any overconfidence when allocating a large marketing budget.

## 2. Data Summary
- Time range, granularity, channels included
- Any known limitations of the dataset worth flagging upfront
The data is at the weekly grain and spans across 3 years for five marketing channels including TV, Social, Paid Search, Display, and Email. 

## 3. Modeling Approach
- Adstock: method used, why
- Saturation: method used, why
- High-level model structure (one short paragraph, not a full spec dump)
For the MMM, we selected the Bayesian methodology due to it's ability to have credible intervals on the output, uncertainty quantifications, and we have prior information from industry standards and EDA to encode in the priors. I used PYMC-Marketing as the Python library due to the Bayesian methodology and it's inclusion of common aspects of an MMM such as adstock and saturation. It's also a well-maintained library and the PYMC team are a really fun group of people.

Adstock is used for 

## 4. The Convergence Problem
- What went wrong (Hill saturation, R-hat/ESS symptoms)
- How you diagnosed it was non-identifiability and not something else
- What you tried that didn't work, briefly

## 5. The Fix and Why It's Defensible
- Why Logistic over Hill
- What you gave up by making this choice
- Why that tradeoff was acceptable here

## 6. Fixed vs Estimated Parameters
- What you fixed (adstock decay) and why
- What this means for how much to trust those specific estimates

## 7. Validation
- What you checked (R-hat, ESS, trace plots, posterior predictive coverage)
- What you did NOT validate against (geo holdout, real data) and why that matters

## 8. Known Limitations
- Synthetic data caveats
- Channels/parameters with low confidence (e.g. Display, Email)
- What would change your confidence level (more data, geo test, etc.)

## 9. If I Had More Time / Resources
- What you'd do differently with a real budget and timeline
- Next steps you'd recommend before this goes into production decision-making
With more time I would have attempted to fix the convergence problem without moving to logistic. In addition, I would have run some geo-tests (check my GitHub for this problem!) to determine whether the model is telling us the truth, and learn what channels actually cause spend.