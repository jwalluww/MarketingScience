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

Your concern on the TakeOff Tuesday Flash Sale Discount Code is very real! After running some numbers, we found four distinct groups of customers: 1) Sure Things - customers who will purchase regardless of promo codes, 2) Lost Causes - customers who are not going to purchase regardless of promo codes, 3) Sleeping Dogs - customers who may purchase organically, but are tired of hearing from us, 4) Persuadables - customers who will only purchase if we send them a promo code. This final group is the one we were looking for, but each group can tell us something about their behavior around discounts. We should develop strategies for each of these customers segments and monitor them over time for behavior changes. Below I have the financials and recommendations moving forward.

## TL;DR

*[Headline net revenue impact number, with confidence interval]*
The net-revenue by sending the campaign to the solely the Persuadables is $268k, which is down $6k from sending to everyone.

*[One-sentence recommendation]*
We recommend sending the TakeOff Tuesday Flash Sale Discount Code to solely the list of of customers generated from our uplift model, which is around 90% of the population. 


---

## Background
Just to remind you...
The 50/50 campaign we ran for this promo was sent via email to 10,000 customers with a 10,000 customer holdout group. We made sure the groups were equal on RFM metrics, email engagement, and loyalty tier so those factors would not interfere with the effect of the promotion. The TakeOff Tuesday Flash Sale was a 15% discount on flight bookings where we saw 25.7% conversion from the discount group, and 20.9% converion from the control group. That's 4.8% absolute lift and 22.9% relative lift. The cost of the campaign from discounts was $78k and the incremental revenue was $21k, which accounts for the cost.

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
This upcoming TakeOff Tuesday promo campaign should be sent to 90% of our population, to maximize revenue. I understand that it appears that only 10% of our population are persuadables, but given our 


*[Policy tree visual]*
When we have the opportunity to build a model, we should build the model as the precision will be greater; having said that, I pasted a chart for you that gives a shortcut to who you can target for this type of campaign without needing a full model build. This is a set of policy rules from this model to use as shorthand for next campaign.


---

## Value Decomposition

*[Chart/table: where the net gain actually comes from — sure-things, sleeping dogs, persuadables]*


---

## Financial Impact Summary

*[Table: blanket-send revenue vs. policy revenue vs. discount $ saved]*


---

## Recommendation

*[Deploy the policy tree rule / targeting list]*
I recommend you use our targeting list to send this upcoming TakeOff Tuesday promo email, and 

---

## Monitoring & Next Steps

*[Permanent holdout group]*
Next steps, let's make sure that each campaign has a small 10% holdout group so we can continue to monitor our customers interactions with promotions and retrain this model when necessary. 

*[Re-score cadence before each future campaign]*
Before each campaign, we don't have to necessarily retrain the model, we can just rescore the customer population using our current model, until we see from the holdout group that the lift is declining, then we know the model is degredating and needs retraining.

*[Watch-list follow-up]*
We will check in to see if our customers are switching groups - e.g., sure-things becoming persuadables would indicate that our customers are beginning to expect these discounts.

---

## Limitations

*[Campaign-specificity — this is calibrated to $5-via-email, not coupons generally]*
This model specifically is calibrated to a 15% discount for flight bookings via email. It cannot guarantee the same type of performance from 

*[Segment boundaries are estimates, not hard truths]*
As stated earlier, the segments represent the general idea of uplift modeling, but in real life, we want to maximize our revenue and target a much broader audience of customers to avoid losing out on any sales.