# Price Elasticity Analysis: Findings & Pricing Recommendation
**To:** Mara Morales, VP Commercial Strategy, Kettlerock Brewing Co.
**From:** Data Science
**Re:** Pricing Analysis

---

Hi Mara, please read through the findings below and send over your thoughts.

We have pricing recommendations, insights into our past year of pricing changes, and answers to your pricing questions. In addition, we have next steps with your permission and input. Here is a summary of questions from your previous email:

- **Are we priced right across our portfolio?** TLDR; We are recommending testing some slight changes to several categories, but we're not too far off.
- **What happens to sales volume when we change the price of SKUs?** TLDR; Sales volume will likely decrease if you increase the price of a SKU, but profit may increase depending on the margin.
- **What happened to Irongate IPA 6-pack last spring when we dropped the price $1.50?** TLDR; Irongate IPA weekly units dropped by 17%, revenue dropped by 11%, Lager did not pick up the slack (in the Twin Cities market).
- **Are customers switching to another product or going to competitors when prices change?** TLDR; We do not have insight into competitor sales, and did not see sales increase on one product given the price change from another.
- **How does demand vary by season and promotions?** TLDR; Some SKUs are seasonal, like the Pumpkin Ale which excels in the fall; promotions are only useful for their price drop, the excitment of a promotion does not increase sales beyond the price drop.
- **Which products are sensitive to price changes and how sensitive are they?** TLDR; Our most sensitive product categories are Amber, Wheat, IPA, Lager, and Mixed. Stout is somewhat sensitive. Sour & THC are not sensitive. Dropping the price by 5% could increase unit sales by 8%-10% on the sensitive products. 

Amber   -2.160
IPA     -1.679
Lager   -1.635
Mixed   -1.574
Sour    -0.826
Stout   -1.116
THC     -0.734
Wheat   -1.860
dtype: float64

I am recommending price changes across the entire portfolio of SKUs with one caveat: these price changes are tested cleanly before official implementation. The pricing model I built shows us how prices associate with revenue, but to officially learn if price changes directly cause changes in demand, we need a pricing experiment.

Here is a TLDR of pricing recommendations across our portfolio of SKUs:

5% price drop: 
Hold price steady: 
5% price increase:

These recommendations vary considerably depending on the margin for each SKU.

> Fill in: 3 to 4 sentences, the whole email in miniature. She may read only this section. Cover: which categories can take a price increase, which cannot, which we cannot call yet, and the one action you want her to take.



Details of our analysis below:

We analyzed two years of weekly sales across 22 of our products in all 3 distribution regions. During this project we essentially looked at the past two years, and how many fewer units were sold when we increased price (or vice versa). If we saw units drop quickly when price was increased, then we know not to touch the price.

> Fill in: 2 to 3 sentences. Two years of weekly sales, 22 products, 3 regions. The question is simple: when we change a price, how many fewer (or more) units do we sell, and does revenue go up or down? Do not use the word "elasticity" until the next section explains it.

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

We split pricing promotions into two parts: the price cut and the promotion itself. In our data, the promoted SKUs sold better solely due to the price cut, and not the excitement of the promotion. Let's continue to run promotions and geo-test a promotion with a price-cut vs a promotion with no price cut to verify where this finding is true.

## Two Categories We Can't Call Yet: Amber and THC

Over the past year we have been decreasing the price for Amber beer and the demand has continued to fall. The opposite has been true for THC, where we have seen increased demand with each price increase due to it's popularity. The pricing model I built has a hard time distinguishing price from trend, so we will need to run pricing tests specifically for these two before making any more permanent pricing adjustments. Amber may be moving to the point of diminishing profit, so we can also test pulling it from the shelves if the price gets too low.

## What-If Examples

> Fill in: 2 to 3 scenarios she can picture, straight from the simulator. For example: +5% on IPA, +10% on Sour, -5% on Amber. For each, show units, revenue, and a plausible range.

## Confidence & Next Steps

Here are my recommended next steps:
1. Run controlled pricing test
Productionalize the model and check back in quarterly

> Fill in: which categories we are sure about, which we are not, and why. Then next steps: a small controlled price test on the uncertain categories, rerun the model next quarter with the new data, and (optional) check whether raising one category's price shifts sales to others.

Questions for you before taking next steps:
Which categories are you comfortable testing?


Thanks,
Justin
---