# Marketing Mix Model — Findings & Budget Recommendation
**To:** Dana Mercer, CMO, Arclight Cycling
**From:** Data Science
**Re:** MMM Results & Q1 Budget Reallocation

---

Hi Dana, please read through my findings below and send over your thoughts.

## Revenue Attribution

Around 40% of our annual revenue can be attributable to advertising spend, the remainder is organic (think word of mouth, returning customers, and residual marketing from years of spend — this will decay if we completely cut off marketing spend). The 40% is actively being created by our budget allocation decisions. Each channel is contributing to this 40%, with paid search contributing the most revenue at 41%. Social and Email contribute slightly less than paid search combined but are reasonable contributors, and TV and Display are lagging behind in contribution. This is purely revenue — it does not tell the full story, which includes advertising spend and return on advertising dollars, as you mentioned earlier.

## Channel Performance & ROAS

We are overspending in some places and underspending in others. TV and Display show a potentially negative ROAS, which means ad dollars can be pulled from those channels. Whereas Email and Paid Search are returning positively and could use an influx of ad dollars to contribute to their full potential, even if this means shaving down Social slightly.

Given the high spend in Paid Search and lower ROAS, we are beginning to see some saturation in that channel, but still believe it could benefit from additional spend. Compare this to Email, where there is lower spend and the potential for extremely high returns — not close to the point of saturation yet.

While I just advocated for a decrease in TV spend, given the history of MMM models, we believe that some of our past TV spend is baked into our baseline revenue and would shed off if we were to drop it too low.

## Budget Reallocation Recommendation

| Channel | Current Weekly Spend | Recommended Weekly Spend | Change |
|---|---|---|---|
| Paid Search | $23,819 | ~$42,000 | ▲ Increase |
| Social | $15,678 | ~$13,000 | ▼ Decrease |
| Email | $2,731 | ~$13,000 | ▲ Increase |
| Display | $10,428 | ~$4,000 | ▼ Decrease |
| TV | $23,666 | ~$13,000 | ▼ Decrease |

Display can be pulled down to $4K per week for now, as this channel showed very little evidence of effectiveness — we can continue to learn from this small budget. Trim Social and TV down to about $13K per week and bump Email up to $13K per week. Paid Search can have the remainder of the budget at approximately $42K per week.

**Quick note on Email:** the potential range for Email ROAS is wide. We could see an extremely large return, but could also see an underwhelming one. We are advocating for a moderate increase to build more spend history in this channel and improve our precision over time.

**Projected annual revenue lift from this reallocation: $166,218.**

## Channel Decay & Spend Sensitivity

Each channel reacts differently to reducing or increasing spend. TV, for example, would not show a large decrease in revenue immediately if we pulled spend — it would take a few weeks for us to see the revenue drop. Email is immediate. If we pull from Email, that share of revenue will leave us within a week.

## Confidence & Next Steps

Our confidence varies by channel. TV is our most precisely measured channel and it is consistently signaling over-investment. Paid Search and Social have solid evidence behind them. Email shows the highest potential return but needs more spend history before we can fully trust the magnitude. Display remains uncertain — the $4K learning budget is deliberate, not a write-off.

In terms of which channels we know can carry the load: Paid Search, Email, and Social are all showing positive ROAS and there is very little probability that additional ad dollars will go to waste.

When we adjust these values and observe our revenue over the next quarter, we can rerun the model for greater precision. We can also geo-test to determine whether any of these channels actually cause revenue to increase causally, rather than just correlate with it.

---

*Analysis conducted using a Bayesian Marketing Mix Model (PyMC-Marketing). Results reflect posterior mean estimates with 94% credible intervals. Recommendations should be validated against geo holdout tests before full budget commitment.*