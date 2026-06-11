**Company:** Arclight Cycling, a direct-to-consumer premium indoor cycling equipment brand (think Peloton-adjacent but smaller, ~$80M annual revenue). Sells high-end bikes, accessories, and a subscription app.

**Your role:** Senior Data Scientist, Growth Analytics

---

**From:** Dana Mercer, Chief Marketing Officer
**To:** Justin
**Subject:** Marketing spend, need some answers before Q4 planning

Justin,

Hope you had a good weekend. I'm heading into our Q4 budget planning session with the board in about six weeks and I need to come prepared with some real answers, not just gut feel.

We've been spending across a bunch of channels for the past two years: paid search, social (mostly Meta and TikTok), email to our existing list, display retargeting, and we brought TV back in Q3 last year as a brand push. Honestly, I'm not totally sure what's working. Our platform tells us paid search has incredible ROI, but I find it hard to believe it's *that* good, just feels like it's just taking credit for people who were already going to buy.

A few things I'd love to get clarity on before the board meeting:

1. **Which channels are actually driving new revenue?** Not what the ad platforms say but something more objective.
2. **Are we overspending anywhere?** I've always had a gut feeling we're pushing paid search past the point of diminishing returns, but I can't prove it.
3. **If we had to shift $500K from one channel to another to maximize revenue, where would it go?** We're not changing the total budget, just the mix.
4. **How long does our advertising actually "stick"?** Like, if we ran a big TV push in October, is that still influencing purchases in December, or does it disappear after a week?
5. **How confident should we be in any of this?** I've seen analyses before where a number gets presented like it's gospel and then falls apart under scrutiny. I'd rather know the range of plausible answers than get a single number that turns out to be wrong.

I know this is a big ask. Happy to talk through scope and timeline, just wanted to get this in writing so you have it. Let me know what you need from me.

Thanks,
Dana

***
***
***

# Marketing Mix Model — Findings & Budget Recommendation
**To:** Dana Mercer, CMO — Arclight Cycling
**From:** Senior Data Science
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