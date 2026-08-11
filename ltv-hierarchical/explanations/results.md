# Customer Lifetime Value Modeling: Findings & Recommendation
**To:** Maya Torres, VP of Growth, Ridgeline Outfitters  
**From:** Data Science  
**Re:** 12-Month Customer LTV Segmentation & Acquisition Spend Framework

---

## The Short Version
Hi Maya,

We built a customer lifetime value score to estimate how much a customer is worth over the next 12 months. The score comes with confidence ranges so you have some wiggle room when deciding how much to spend in acquiring the customer. You can now compare the cost to acquire or retain each customer against their worth to set thresholds and limits on ad spend.

---

## What Maya Asked For vs. What We Built
The initial request was to understand how much to spend acquiring and retaining each customer as one-time purchasers are not the same as repeat-purchasers. We built a cusotmer lifetime value model which over the next 12 months predicts whether a customer will return, how many transactions we can expect from that customer, and how much each customer will spend, with confidence ranges built in. We can even take these values and bucket the customers into segments for better understanding of who we have as customers.


---

## The Four Customer Segments

| Segment | Customers | Avg Frequency | Avg Order Value | 12-Month LTV | LTV Range (95% CI) |
|---|---|---|---|---|---|
| Tier 4: Low | 254 | 0.00 | $1.03 | $3.97 | $3.61 - $4.34 |
| Tier 3: Mid | 246 | 0.23 | $2.55 | $45.24 | $43.48 - $46.62 |
| Tier 2: High | 250 | 2.80 | $48.94 | $1,819.86 | $1,769.85 - $1,852.14 |
| Tier 1: Elite | 250 | 10.61 | $135.95 | $17,978.37 | $17,542.83 - $18,281.36 |

---

## What Should We Spend to Acquire or Retain Each Segment?
For CAC we are assuming a margin of 30% for simplicity.

The low tier represents around 25% of our customers and are mostly one-time purchasers. We actually found that 46% of our customers are one-time purchasers, so it's at least this group and part of the next (next steps are to more precisely bucket these customers based on their projectec value & retention). These low-value customers are only expected to spend a few dollars each year and are not worth a large acquisition cost, optimisically around $1.30/customer, which does not meet the $60 threshold. If you are spending more than a few dollars here, it's too much. Our model slightly overstates retention & value, so it's actually telling you to spend more than you should to aquire these customers on the off-chance they hold some value. The projected value of these customers is not justifying a large cost to acquire, but that does not mean they should be ignored. The strategy for these customers is to run some low-cost touches to lure them back. If we send a one-time customer an organic email, a post-purchase sequence, and a new product announcement, they may return as a more valuable customer. This is a group to continue exploring using other marketing techniques such as experimentation & uplift modeling to further refine our strategies.

The mid tier customers are partially one-time purchasers, but also have some valuable customers mixed in. The average spend is low, but we are expecting about $45/customer this year. The max cost to acquire each of these customers is optimiscially $13.99/customer, which is still $46 over the $60 threshold. These customers should be treated similarly to the low-tier cusotmers, not completely ignored, but also not given a large marketing budget.

The high tier customers are expected to each bring in about $1,819 this year. Given our 30% margin we should budget $555/customer for acquisition. These aren't our elite customers, but are brining in enough value to justify the expenses.


---

## How Confident Are We?
The model is over-predicting by 0.095 purchases per customer on average. The bias for the model is overpredicting because the churn aspect isn't as confident - so the model says one-timers may return when they do not. This optimistic view of customers may place CAC numbers on the high side, but better to over-spend and actually gain the customers than under-spend and miss out on the customers.


---

## What would we do with more time



---

## Recommended Next Steps
- Productionalize the model so we can begin tracking LTV over time.
- Develop a marketing strategy around each customer LTV segment.
- LTV should not be used as a short term marketing metric as it is , so avoid using it as a KPI for individual experiments. Instead, develop a marketing strategy and use LTV to determine the impact on customer value.
- We built simple segments to show the difference between customers, but a more precise method would be to cluster the customers into segments based on their projected value & retention.
- Forecast???

---

*Analysis conducted using BG/NBD + Gamma-Gamma models (PyMC-Marketing). Results reflect posterior mean estimates with 90% credible intervals. lifetimes MLE attempted; convergence failure documented in notebook and technical notes.*