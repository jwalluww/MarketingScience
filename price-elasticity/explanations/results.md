# Price Elasticity Analysis: Findings & Pricing Recommendation
**To:** Mara Morales, VP Commercial Strategy, Kettlerock Brewing Co.
**From:** Data Science
**Re:** Pricing Analysis

---

Hi Mara, please read through the findings below and send over your thoughts.

We have pricing recommendations, insights into our past year of pricing changes, and answers to your pricing questions. In addition, we have next steps with your input and approval. To start, here is a summary of questions from your previous email with some quick inline responses.

**TLDR;**

- **Are we priced right across our portfolio?** We are recommending testing some slight changes to several categories, but we're not too far off.
- **What happens to sales volume when we change the price of SKUs?** Sales volume will likely decrease if you increase the price of a SKU, but profit may increase depending on the margin.
- **What happened to Irongate IPA 6-pack last spring when we dropped the price $1.50?** Irongate IPA weekly units dropped by 17%, revenue dropped by 11%, Lager did not pick up the slack (in the Twin Cities market).
- **Are customers switching to another product or going to competitors when prices change?** We do not have insight into competitor sales, and did not see sales increase on one product given the price change from another.
- **How does demand vary by season and promotions?** Some SKUs are seasonal, like the Pumpkin Ale which excels in the fall; promotions are only useful for their price drop, the excitment of a promotion does not increase sales beyond the price drop.
- **Which products are sensitive to price changes and how sensitive are they?** Our most sensitive product categories are Amber, Wheat, IPA, Lager, and Mixed. Stout is somewhat sensitive. Sour & THC are not sensitive. Dropping the price by 5% could increase unit sales by 8%-10% on the sensitive products.

**Recommendations**

I am recommending price changes across the entire portfolio of SKUs with one caveat: these price changes are tested cleanly before official implementation. The pricing model I built shows us how prices associate with revenue, but to officially learn if price changes directly cause changes in demand, we need a pricing experiment.

Here is a our pricing recommendations across the portfolio of SKUs:

Test 5% price drop: Amber
Hold price steady: THC, Sour
Test 5% price increase: IPA, Lager, Mixed, Stout, Wheat

These recommendations vary considerably depending on the margin for each SKU.

**Analysis Details**

We analyzed two years of weekly sales across 22 of our products in all 3 distribution regions. During this project we essentially looked at the past two years, and how many fewer units were sold when we increased price (or vice versa). If we saw units drop quickly when price was increased, then we know not to touch the price.

> Fill in: 2 to 3 sentences. Two years of weekly sales, 22 products, 3 regions. The question is simple: when we change a price, how many fewer (or more) units do we sell, and does revenue go up or down? Do not use the word "elasticity" until the next section explains it.

> Fill in: one short paragraph plus one real example from your results. Plain-English definition: a 1% price increase leads to about X% fewer units. Then the one rule she needs: if volume drops faster than price rises (past -1), a price increase LOSES revenue; if it drops slower (between 0 and -1), a price increase GAINS revenue. Use one category as the worked example.

**Category Analysis**

| Category | How price-sensitive | If we raise price 5%: units | If we raise price 5%: revenue | If we raise price 5%: profit @40% | Confidence |
|---|---|---|---|---|
| IPA | Very Price Sensitive | -8% | -5% | +3.7% | Solid |
| Lager | Moderately Price Sensitive | -8% | -5% | +3.7% | Solid |
| Stout | Somewhat Price Sensitive | -8% | -5% | +3.7% | Solid |
| Wheat | Moderately Price Sensitive | -8% | -5% | +3.7% | Solid |
| Mixed | Moderately Price Sensitive | -8% | -5% | +3.7% | Solid |
| Sour | Not Price Sensitive | -8% | -5% | +3.7% | Low |
| Amber | Very Price Sensitive | -8% | -5% | +3.7% | Low |
| THC | Not Price Sensitive | -8% | -5% | +3.7% | Low |

With a 5% price increase, units and revenue are likely decreasing for every SKU on the board. Profit, on the other hand, may actually increase depending on what our margin is for each product. We should base our decision on the profit margin for each item.

> Fill in: the recommendation. Suggested shape: one short paragraph per bucket (raise / hold / test first), each with the reason in business terms. Be honest that in most categories a price increase costs more volume than it earns, so "raise" may be a short list. Stay inside the 5 to 10% range we actually observed.

**Promotions**

We split pricing promotions into two parts: the price cut and the promotion itself. In our data, the promoted SKUs sold better solely due to the price cut, and not the excitement of the promotion. Let's continue to run promotions and geo-test a promotion with a price-cut vs a promotion with no price cut to verify where this finding is true.

**Amber & THC Anomolies**

Over the past year we have been decreasing the price for Amber beer and the demand has continued to fall. The opposite has been true for THC, where we have seen increased demand with each price increase due to it's popularity. The pricing model I built has a hard time distinguishing price from trend, so we will need to run pricing tests specifically for these two before making any more permanent pricing adjustments. Amber may be moving to the point of diminishing profit, so we can also test pulling it from the shelves if the price gets too low.

**Price Simulator**

> Fill in: 2 to 3 scenarios she can picture, straight from the simulator. For example: +5% on IPA, +10% on Sour, -5% on Amber. For each, show units, revenue, and a plausible range.

To help you get a better picture of what revenue & units would look like with a change

**Next Steps**

Here are my recommended next steps:
1. Run controlled pricing test
Productionalize the model and check back in quarterly

Test in each market, test in mulitple seasons

> Fill in: which categories we are sure about, which we are not, and why. Then next steps: a small controlled price test on the uncertain categories, rerun the model next quarter with the new data, and (optional) check whether raising one category's price shifts sales to others.

Questions for you before taking next steps:
Which categories are you comfortable testing?


Thanks,
Justin
---