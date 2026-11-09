# Price Elasticity Analysis: Findings & Pricing Recommendation
**To:** Mara Morales, VP Commercial Strategy, Kettlerock Brewing Co.
**From:** Data Science
**Re:** What happens to demand and revenue when we change prices

---

Hi Mara, please read through the findings below and send over your thoughts.

> Fill in: one or two lines. What this email covers and what you want back from her (e.g., which categories she is comfortable piloting a price change on).

## The Short Version

> Fill in: 3 to 4 sentences, the whole email in miniature. She may read only this section. Cover: which categories can take a price increase, which cannot, which we cannot call yet, and the one action you want her to take.

## What We Looked At

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

---

*Analysis conducted using a Hierarchical Bayesian demand model (PyMC), cross-checked against six other methods that largely agreed. Results reflect posterior means with 95% credible intervals. Based on two years of synthetic weekly data. A quarterly refresh and controlled price tests are recommended before large moves.*