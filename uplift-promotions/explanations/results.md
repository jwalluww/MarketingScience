# BlueWing Air — Promotional Targeting: Results
**To:** Alex Torres, Director of CRM & Loyalty Marketing, BlueWing Air
**From:** Justin Wall, Analytics Consultant
**Re:** Need help figuring out who our promo codes are actually working on

---

Hi Alex, please read through my findings below and send over your thoughts.

*9/23/2026*
*Justin Wall*

---

## The Ask

Your concern on the TakeOff Tuesday Flash Sale Discount Code is very real! After running some numbers, we found four distinct groups of customers: 1) Sure Things - customers who will purchase regardless of promo codes, 2) Lost Causes - customers who are not going to purchase regardless of promo codes, 3) Sleeping Dogs - customers who may purchase organically, but are tired of hearing from us, 4) Persuadables - customers who will only purchase if we send them a promo code. This final group is 10.9% of the population. Regardless of the size of these groups, they can all tell us something about their behavior around discounts. We should develop strategies for each of these customers segments and monitor them over time for behavior changes. Below I have the financials and recommendations moving forward. 

## TL;DR

*[Headline net revenue impact number, with confidence interval]*
The net-revenue by sending the campaign to the solely the Persuadables is $268k, which is down $6k from sending to everyone.

*[One-sentence recommendation]*
We recommend sending the TakeOff Tuesday Flash Sale Discount Code to solely the list of Persuadables to maximize revenue. 


---

## Background
Just to remind you...
The 50/50 campaign we ran for this promo was sent via email to 10,000 customers with a 10,000 customer holdout group. We balanced the groups on RFM metrics, email engagement, and loyalty tier to ensure those factors were not interfering with the effect of the promotion. The TakeOff Tuesday Flash Sale was a 15% discount on flight bookings where we saw 25.7% conversion from the discount group, and 20.9% converion from the control group. That's 4.8% absolute lift and 22.9% relative lift. The cost of the campaign from discounts was $79k and the incremental revenue was $96k. We analyzed this campaign and used the data from it to build a model to determine who actually needs a discount to purchase vs who would have purchased anyway.

---

## Key Findings

### 1. Are we giving away margin to people who'd book anyway?

*[$ given away to sure-things]*
Breaking things out, here are the percentages of our test group from each of the segments:
Lost Cause: 51%
Sure Thing: 37%
Persuadable: 10%
Sleeping Dog: 2%

Yes, we are giving away around $12k of discounts to the Sure Things - customers who would book anyway. Now that we have these segments, we can avoid sending the discount codes to those who we believe are going to purchase anyway or not purchase at all.

### 2. Are we training our best customers to expect discounts?

*[Finding — framed as a monitoring recommendation, not a fabricated number]*
To fully understand whether we are training our customers to expect discounts, we would have to measure their change in behavior over time. If we had a previous campaign, we would have a couple of data points and could see whether our sure-things are moving into persuadable territory. My recommendation is to keep a small holdout group for each of these flash sales so we can monitor our customers' statuses and whether they are migrating into other segments.

I thought once more about this problem and came up with a different definition. Who are our best customers, without LTV, I used the Gold Loyalty Tier as a proxy. The customers expecting discounts are persuadables, who are around 10% of our customer base. This number drops to less than 1% in our Gold Tier, so no, our best customers are not expecting discounts at the moment, but we will keep an eye on this down the road.

### 3. Who should actually get the discount?

*[Persuadable segment size, % of base]*


*[Policy tree visual]*


---

## Value Decomposition

*[Chart/table: where the net gain actually comes from — sure-things, sleeping dogs, persuadables]*


---

## Financial Impact Summary

*[Table: blanket-send revenue vs. policy revenue vs. discount $ saved]*


---

## Recommendation

*[Deploy the policy tree rule / targeting list]*


---

## Monitoring & Next Steps

*[Permanent holdout group]*


*[Re-score cadence before each future campaign]*


*[Watch-list follow-up]*


---

## Limitations

*[Campaign-specificity — this is calibrated to $5-via-email, not coupons generally]*


*[Segment boundaries are estimates, not hard truths]*
