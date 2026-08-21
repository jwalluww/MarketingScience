# Customer Lifetime Value Modeling: Findings and Recommendation
**To:** Maya Torres, VP of Growth, Ridgeline Outfitters  
**From:** Data Science  
**Re:** 12-Month Customer LTV Segmentation and Acquisition Spend Framework

---

## The Short Version
Hi Maya,

Good news, we found the top 25% of customers are worth 400x more than the bottom 25% of customers at Ridgeline.

We built a customer lifetime value score to estimate how much a customer is worth over the next 12 months. The score comes with confidence ranges so you have some wiggle room when deciding how much to spend in acquiring the customer. You can now compare the cost to acquire or retain each customer against their worth to set thresholds and limits on ad spend.

---

## What Maya Asked For vs. What We Built
The initial request was to understand how much to spend acquiring and retaining each customer as one-time purchasers are not the same as repeat-purchasers. We built a customer lifetime value model which gives us how many transactions we can expect from that customer and how much each customer will likely spend, with confidence ranges built in. We can even take these values and bucket the customers into segments for better understanding of who we have as customers.


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
For CAC we are assuming a margin of 30% for simplicity. Our model slightly overstates retention and value, so it's actually telling you to spend more than you should to acquire these customers on the off-chance they hold some value, which is why we will use the lower bound of our confidence range.

The low tier represents around 25% of our customers and are mostly one-time purchasers. We actually found that 46% of our customers are one-time purchasers, so it's at least this group and part of the next. These low-value customers are only expected to spend a $3.61 each year and are not worth a large acquisition cost, around $1.08/customer, which does not meet the $60 threshold. The projected value of these low-tier customers is not justifying a large cost to acquire, but that does not mean they should be ignored. The strategy for these customers is to run some low-cost touches to lure them back. If we send a one-time customer an organic email, a post-purchase sequence, and a new product announcement, they may return as a more valuable customer. This is a group to continue exploring using other marketing techniques such as experimentation and uplift modeling to further refine our strategies.

The mid tier customers are partially one-time purchasers, but also have some valuable customers mixed in. The average spend is low, but we are expecting about $43.48/customer this year. The max cost to acquire each of these customers is optimistically $13.04/customer, which is still $46 under the $60 threshold. These customers should be treated similarly to the low-tier customers, not completely ignored, but also not given a large marketing budget.

The high tier customers are expected to each bring in about $1,769.85 this year. Given our 30% margin we should budget $530.95/customer for acquisition. These aren't our elite customers, but are bringing in enough value to justify the expenses.

The elite customers are expected to bring in a whopping $17,542.83 this year, and the budget for these customers should be around $5,262.85, plenty of room to spare around that $60 baseline. We shouldn't solely focus on these customers or spam them with too many messages, but they justify the paid search and social costs we have been debating lately.


---

## How Confident Are We?
The model is over-predicting by 0.095 purchases per customer on average. The bias for the model is overpredicting because the churn aspect isn't as confident - so the model says one-timers may return when they do not. This optimistic view of customers may place CAC numbers on the high side, but better to over-spend and actually gain the customers than under-spend and miss out on the customers. We are also using the conservative estimates in the models confidence ranges to counteract this over-prediction from our model.


---

## What would we do with more time
With more time we could build a machine learning model to improve the churn accuracy that accounts for various other features of our customers like loyalty program and browse activity, which may help us hone in on which customers are going to churn. For more precise and accurate segments, we can run some clustering across the final scores instead of splitting the customers into equal quarter buckets. For more nuanced and personalized marketing strategies, we can test these customers in marketing campaigns and build uplift models to determine who needs marketing campaigns vs who will show up without marketing campaigns. We can also build a MMM model which can help calibrate cost-per-acquisition more precisely.


---

## Recommended Next Steps
- Productionalize the model so we can begin tracking LTV over time.
- Develop a marketing strategy around each customer LTV segment.
- LTV should not be used as a short term marketing metric as it is , so avoid using it as a KPI for individual experiments. Instead, develop a marketing strategy and use LTV to determine the impact on customer value.
- We built simple segments to show the difference between customers, but a more precise method would be to cluster the customers into segments based on their projected value and retention.

---

*Analysis conducted using BG/NBD + Gamma-Gamma models (PyMC-Marketing). Results reflect posterior mean estimates with 95% credible intervals. Modeling documented in notebook and technical notes.*