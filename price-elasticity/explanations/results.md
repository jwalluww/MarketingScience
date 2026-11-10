# Price Elasticity Analysis: Findings & Pricing Recommendation
**To:** Mara Morales, VP Commercial Strategy, Kettlerock Brewing Co.
**From:** Data Science
**Re:** Pricing Analysis

---

Hi Mara, please read through the findings below and send over your thoughts.

We have pricing recommendations, insights into our past year of pricing changes, and answers to your questions. In addition, we need a little bit of information from you before proceeding with next steps. Here is a summary of questions from your previous email:

- Are we priced right across our portfolio?
- What happens to sales volume when we change the price of SKUs?
- What happened to Irongate IPA 6-pack last spring when we dropped the price $1.50?
- Are customers switching to another product or going to competitors when prices change?
- How does demand vary by season and promotions?
- Which products are sensitive to price changes and how sensitive are they?

I am recommending price changes across the entire portfolio of SKUs with one caveat: these price changes are tested cleanly before official implementation. The pricing model I built shows us how prices associate with revenue, but to officially learn if price changes directly cause changes in demand, we need a pricing experiment.

Here is a TLDR of pricing recommendations across our portfolio of SKUs:

5% price drop: 
Hold price steady: 
5% price increase:

These recommendations vary considerably depending on the margin for each SKU.

> Fill in: 3 to 4 sentences, the whole email in miniature. She may read only this section. Cover: which categories can take a price increase, which cannot, which we cannot call yet, and the one action you want her to take.

## What We Looked At

We analyzed two years of weekly sales across 22 of our products in all 3 distribution regions. 

> Fill in: 2 to 3 sentences. Two years of weekly sales, 22 products, 3 regions. The question is simple: when we change a price, how many fewer (or more) units do we sell, and does revenue go up or down? Do not use the word "elasticity" until the next section explains it.

## How to Read "Price Sensitivity"

> Fill in: one short paragraph plus one real example from your results. Plain-English definition: a 1% price increase leads to about X% fewer units. Then the one rule she needs: if volume drops faster than price rises (past -1), a price increase LOSES revenue; if it drops slower (between 0 and -1), a price increase GAINS revenue. Use one category as the worked example.

## Category by Category

| Category | How price-sensitive | If we raise price 5%: units | If we raise price 5%: revenue | Confidence |
|---|---|---|---|---|
| IPA | | | | |
| Lager | | | | |
| Stout | | | | |
| Wheat | | | | |
| Mixed | | | | |
| Sour | | | | |
| Amber | | | | |
| THC | | | | |

> Fill in: the table (round everything, show ranges where we are unsure), then 2 to 3 sentences on the pattern. Group categories into plain buckets: very sensitive, moderately sensitive, barely sensitive.

## Where to Raise, Hold, and Lower Prices

> Fill in: the recommendation. Suggested shape: one short paragraph per bucket (raise / hold / test first), each with the reason in business terms. Be honest that in most categories a price increase costs more volume than it earns, so "raise" may be a short list. Stay inside the 5 to 10% range we actually observed.

## What About Promotions?

> Fill in: in this data, promo weeks did not sell more beyond what the price cut itself explains. Translate that for her: what it means for how we use discounts, and what we would need to test (displays, features, ads) to find out if promos have any lift beyond the lower price. Careful with the wording, this is what the data shows, not a universal rule.

## Two Categories We Can't Call Yet: Amber and THC

> Fill in: Amber's price has trended down while its volume declined; THC's price and volume have both climbed. Price and trend move together, so the data cannot cleanly separate the two. Say that plainly, give our best range, and recommend a controlled price test instead of acting on the model.

## What-If Examples

> Fill in: 2 to 3 scenarios she can picture, straight from the simulator. For example: +5% on IPA, +10% on Sour, -5% on Amber. For each, show units, revenue, and a plausible range.

## Confidence & Next Steps

> Fill in: which categories we are sure about, which we are not, and why. Then next steps: a small controlled price test on the uncertain categories, rerun the model next quarter with the new data, and (optional) check whether raising one category's price shifts sales to others.

Questions for you before taking next steps:
Which categories are you comfortable testing?


Thanks,
Justin
---