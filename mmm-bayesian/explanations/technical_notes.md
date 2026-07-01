# Technical Notes: Arclight MMM Modeling Decisions
*Prepared for: Data Science Manager Review*

## 1. Objective
We built this model to determine how ad spend impacts revenue across five marketing channels, and we needed a tool that allows us to run a 'what-if' analysis for adding and moving revenue from one channel to another. We chose Bayesian methods due to its ability to calculate uncertainty inherent in MMM. Bayesian MMM allowed us to show the nuance around channels where we see lots of variance, avoiding any overconfidence when allocating a large marketing budget.

## 2. Data Summary
The data is at the weekly grain and spans across 3 years for five marketing channels including TV, Social, Paid Search, Display, and Email. The channels each show different trends based on their activity and there is seasonality and noise to the data as well. The only channel with $0 spend periods is TV, where the channel is not always-on, and we run intentional campaigns for certain events or periods. One note to make is that we could have used 2.5 years of data and validated with the final 6 months, but we chose to use all 3 years for training. Using all three years strengthens our ability to separate seasonal patterns from channel effects. Geo testing provides stronger causal validation than a holdout period alone. There could have been a campaign in December of the third year that did not occur in the prior two years which could have reduced our confidence in our model.

## 3. Modeling Approach
Want to start this out by saying we chose MMM over MTA because MTA is built for digital touchpoints only when you want to track individuals, but is getting harder to build with new privacy laws and data ownerships, whereas MMM is based on data we have access to. Incrementality testing is also an option, but is typically built as a support for an MMM model, and not a standalone determination of budget allocations.

For the MMM, we selected the Bayesian methodology due to it's ability to have credible intervals on the output, uncertainty quantifications, and we have prior information from industry standards and EDA to encode in the priors. I used PYMC-Marketing as the Python library due to the Bayesian methodology and it's inclusion of common aspects of an MMM such as adstock and saturation. It's also a well-maintained and documented library.

The features in the model are each of the channels, and the model accounts for seasonality, adstock, and saturation.

Adstock shows that these channels decay over time, e.g. customers do not forget a TV commercial immediately, it sticks with them for a couple of weeks; whereas an advertisement in an email or on social media may be a quick reminder of the brand, but will not make a long lasting effect. For adstock values in the model, we used industry standards that are known from other studies. We technically modeled them but effectively fixed them via tight priors. We set pretty tight priors and given more time we could loosen this band by decreasing the concentration from 100 to 15 or so and determine if our company works differently than the industry. Could be an interesting sensitivity analysis to do down the road. We can also run a pulse test by turning off a channel for a few weeks, then flipping it back on and determining how long it takes revenue to recover. For the time being, we were having convergence issues and needed to have less movement and more decisiveness in our model, so we sacraficed some information here in favor of learning about our channel attribution.

For saturation, each additional ad dollar does not produce the same amount of revenue in return, eventually ad dollars will become less effective in driving spend. We can model this diminishing returns aspect in our MMM by using specific functions. The Hill function allows us to interpret a parameter called the half-saturation, which shows us the weekly saturation value of each channel. In addition to the half saturation, we have the revenue ceiling which shows max contribution at full saturation and slope which shows gradual or steep diminishing returns. For the logistic function, the revenue ceiling parameter exists, but we collapse the half-saturation & slope parameters into one parameter called the saturation rate.

A next step for saturation that I left out was plotting the posterior saturation curves per channel with current spend overlaid. This provides confirmation of the saturation around ROAS. We would see channels on the steep portion of their curve which are underfunded, channels on the flat portion of their curve which are saturated.

One additional mention that we altered the final optimization numbers to move Display from the $0 recommended by the model to $4k as a learning buget, as there is the possibility our model has some confounding factors tied to display. In addition, there is enough uncertainty that Display may also have a small effect, so erring on the side of learning here and manually pushing it up to $4k, otherwise we'll never know.

## 4. The Convergence Problem
We initially determined the most comprehensive way of measuring saturation was using the hill function due to the separation of the half-saturation & slope parameters. When we ran the model, the sampler found multiple valid solutions and the chain never agreed on which solution was optimal. The parameters of the hill function trade off (high beta & high k produces same curve as low beta & low k) and this was causing our R-Hats to jump to 2.2 to 4.3 across all parameters (tightening priors did not improve it at all). The model also had an ess of around ~2.5, and the trace plots showed chains living in completely separate bands rather than overlapping, meaning we had a multi-modal model with non-identifiability and needed to adjust. 

I initially tried tightening priors, but eventually I changed over from the hill function to a logistic function which has one fewer parameter and reduces our flexibility but will actually give us a viable model, specifically it removes the parameter which causes the trade-off issue. Once it ran successfully the issue was verified. In a real life scenario, I would have spent more time working through the hill function before switching to the logistic function.

## 5. The Fix and Why It's Defensible
We chose logistic over hill due to 1 fewer parameter and less space the model would have for getting lost while searching. We gave up flexibility in favor of identifiability which is acceptable here because we needed to get a model to run and give us information about the channels themselves. The tradeoff is we can no longer independently estimate the half-saturation point and slope for each channel as these collapse into a single rate parameter, reducing interpretability slightly.

## 6. Fixed vs Estimated Parameters
For adstock parameters, I chose to set them at strict values with little to no wiggle room. This was initially part of our fix for the convergence problem, but we ultimately determined that adstock estimates in the results should reflect our prior assumptions rather than the data itself. A pulse test or loosened-prior sensitivity analysis would be needed to validate them.

## 7. Validation
To validate this model, I checked the following diagnostics:
R-Hat: Right around ~1
ESS: Greater than 400
Trace Plots: Fuzzy Caterpillar
Posterior Predictive Coverage: Posterior predictive covers observed revenue.

Just to reiterate some validation notes from earlier: I did not run any geo holdout tests nor did we validate against any real data, which indicates that our model is an estimate and requires additional rigor to give us more confidence. We could have trained our model on 2.5 years of data and validated against the final 6 months, generating a posterior predictive revenue for the holdout period using actual spend. Does the model's 90% predictive interval capture actual revenue 90% of the time? Having said that, I would take it with less weight than I would a geo test, which can validate these models more cleanly and clearly.

## 8. Known Limitations
The data in this problem is of course synthetic, and is set up to tell a story, whereas real life data is going to be messier and will not be a flashing sign of what to do. Email & Display had lower precision and wider ROAS bands which leads us to believe we need more data from these channels, more variation. More data, geo tests, and a holdout validation would give us more confidence and precision in our model. The optimizer's budget recommendation assumes the logistic saturation curves generalize beyond observed spend ranges -> this means that the recommendations for channel spend go beyond what we have seen in historical data. It's reasonable to recommend a more gradual increase if the business is uncomfortable with large fluctuations in ad spend.

## 9. If I Had More Time / Resources
If I had more time I would spend it on these aspects, in no particular order:
- Working through the Hill function convergence issue so we could interpet the half saturation & slope parameters
- Graphing the saturation curve for the business
- Loosening up Adstock parameter to determine if our business is similar to the industry by how it deviates from the prior
- Comparing the model vs a holdout dataset
- Running a geo test to measure causality of these channels
- Running a pulse test to determine actual adstock for our business.

## 10. Path to Production

Before this model goes into production, we should ensure we have a data pipeline creating the input data for the model. We will refresh it quarterly using the prior quarter's posterior as the next period's priors. We will create a dashboard for ROAS estimates, budget recommendations, and live optimization efforts for scenario testing.

## 11. Quick explanation of Geo Testing
We have discussed geo-testing quite a bit in this doc and I would like to elaborate. Geo testing is as close an RCT as we can get at the market level. To test whether these ad channels are effective, we cannot simply turn them off for individuals, as some are run at the market level. So we can flip these channels off for entire regions and watch the revenue difference between the test regions and a set of markets that are as close as possible in the aggregate. We measure movement in the test region vs the control region and determine the lift of the channel being isolated.