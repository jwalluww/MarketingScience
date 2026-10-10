# BlueWing Air — Promotional Targeting: Results
**To:** Alex Torres, Director of CRM & Loyalty Marketing, BlueWing Air
**From:** Justin Wall, Analytics Consultant
**Re:** Need help figuring out who our promo codes are actually working on

---

Hi Alex, please read through my findings below and send over your thoughts.

*10/10/2026*
*Justin Wall*

---

## The Ask

Your concern on the TakeOff Tuesday Flash Sale Discount Code is very real! After running some numbers, we found four distinct groups of customers: 

1) Sure Things - customers who are likely purchase regardless of promo
2) Lost Causes - customers who are not likely to purchase regardless of promo
3) Sleeping Dogs - customers who are less likely to purchase given a promo
4) Persuadables - customers who are more likely to purchase given a promo

This final group is the one we were looking for, but each group can tell us something about their behavior around discounts. We should develop strategies for each of these customers segments and monitor them over time for behavior changes.

## TL;DR
To fully maximize net revenue, we recommend sending the TakeOff Tuesday Flash Sale Discount Code to the list of customers generated from the model, which is around 94% of the population. The net revenue by sending the promotional discount to the model's recommended list of customers is $983,540, which is up $65,568 from sending to everyone.

---

## Background
Quick reminder on the original campaign:
The 50/50 campaign we ran for promo was sent via email to 10,000 customers with a 10,000 customer holdout group. We made sure the groups were equal on RFM metrics, email engagement, and loyalty tier so those factors would not interfere with the effect of the promotion. The TakeOff Tuesday Flash Sale was a 15% discount on flight bookings where we saw 25.7% conversion from the discount group, and 20.9% conversion from the control group. That's 22.9% increase in booking rate (4.8pp increase). The actual cost of the campaign from discounts was $78k and the incremental revenue was $21k, which accounts for the cost. These actual numbers will differ from the remainder of the numbers in this report as we only sent the promo to half of the customer list, and the model assumes we are including the other half as well (projecting to send to the full 20k).

---

## Key Findings

Let me address some of your key questions below:

### 1. Are we giving away margin to people who'd book anyway?

Before I answer this one, quick explanation on these segments. The "purchase with promo vs purchase organically" quadrant paints a really nice picture of the problem we are facing and the customer profiles that exist in our database. In reality, to maximize revenue given the price of the bookings & discount amount, we will send further into the model than just 10%. Those are our confirmed persuadables, but our model tells us that 94% of our customers is the proper depth to maximize net revenue. The segments are the diagnosis and the send list is our prescription.

Breaking things out, here are the percentages of our test group from each of the segments:
Lost Cause: 52%
Sure Thing: 37%
Persuadable: 10%
Sleeping Dog: 2%

Yes, we are giving away around $85k of discounts to the Sure Things - customers who would book anyway. That is our diagnosis talking - our prescription will have us saving a bit less in discounts, because some of these sure-thing customers still give us positive revenue according to our model, so we will end up saving around $14k in discounts, but netting $65k higher in total.

### 2. Are we training our best customers to expect discounts?

To fully understand whether we are training our best customers to expect discounts, we will have to measure their change in behavior over time. Once we have another campaign, we will see whether our sure-things are moving into persuadable territory. My recommendation is to keep a small holdout group (10%) for each of these flash sales so we can monitor our customers' statuses and whether they are migrating into other segments.

But I would like to answer your question with the data we have. Without LTV, Gold Loyalty Tier is a good proxy for best customers. The customers who respond well to discounts are persuadables, who are around 10% of our customer base. This number drops to less than 1% in our Gold Tier, so given this data, our best customers are not expecting discounts at the moment, but we will keep an eye on this down the road.

### 3. Who should actually get the discount?

This upcoming TakeOff Tuesday promo campaign should be sent to 94% of our population to maximize net revenue and avoiding giving away any discounts. Only 10% of our population are statistically confirmed as persuadables, but to maximize net revenue we need to go deeper into the model, as many more customers have smaller positive effects that pay off as a group. The list of customers is set up in the database for the ops team to ingest.

---

## Value Decomposition

This table is our value decomposition and our revenue per customer for each segment with sending to all vs sending to model-recommended. You can see the sleeping dogs will purchase less if we send discounts, they do not want to hear from us. The persuadables all want to hear from us.

| Segment      |   Customers | % of list   | Rev/customer, blanket   | Rev/customer, policy   | Net gain from policy   |
|:-------------|------------:|:------------|:------------------------|:-----------------------|:-----------------------|
| Lost Cause   |      10,343 | 52%         | $34.11                  | $34.41                 | $3,043                 |
| Persuadable  |       1,977 | 10%         | $31.44                  | $31.44                 | $0                     |
| Sleeping Dog |         327 | 2%          | $72.87                  | $138.18                | $21,334                |
| Sure Thing   |       7,353 | 37%         | $65.17                  | $70.77                 | $41,191                |

---

## Financial Impact Summary

This financial impact summary shows the most valuable table we have and shows the net revenue of sending to everyone, sending to no one, sending to persuadables, and sending to our model recommendation. There are two model recommendations at the bottom, and the value backtest wins since it's based on maximizing net revenue.

| Scenario                           |   Customers sent | % of list   | Gross revenue   | Discount cost   | Net revenue   | Net vs. blanket   |
|:-----------------------------------|-----------------:|:------------|:----------------|:----------------|:--------------|:------------------|
| Send to everyone (blanket)         |           20,000 | 100%        | $1,079,968      | $161,995        | $917,973      | +$0               |
| Send to no one                     |                0 | 0%          | $867,544        | $0              | $867,544      | -$50,429          |
| Persuadables only (CI + breakeven) |            1,977 | 10%         | $889,070        | $10,966         | $878,104      | -$39,869          |
| CF recommendation (top 82% by model confirmation)        |           16,310 | 82%         | $1,095,864      | $122,649        | $973,215      | +$55,243          |
| Value backtest (top 94% by backtest)    |           18,800 | 94%         | $1,131,490      | $147,949        | $983,540      | +$65,568          |


---

## Recommendation

Once again, I recommend you use our targeting list to send this upcoming TakeOff Tuesday promo email, which is about 94% of customers, just lopping off the back 6%. In future campaigns, we will continue to use a 10% holdout, and when the model performance begins to degrade, we can run another 50/50 campaign and build another one.

---

## Monitoring & Next Steps

Next steps, let's make sure that each campaign has a small 10% holdout group so we can continue to monitor our customers interactions with promotions and retrain this model when necessary. (Randomly send to half of the excluded 6% each campaign to check the cutoff.).

Before each campaign, we don't have to necessarily retrain the model, we can just rescore the customer population using our current model, until we see from the holdout group that the lift is declining, then we know the model is degrading and needs retraining.

We will check in to see if our customers are switching groups - e.g., sure-things becoming persuadables would indicate that our customers are beginning to expect these discounts.

---

## Limitations

This model specifically is calibrated to a 15% discount for flight bookings via email. It cannot guarantee the same type of performance from different levels nor different communication channels.

NOTE: Model validated on a random 6,000-customer holdout (3,000 sent, 3,000 not). Dollar figures are scaled to the full 20K list.