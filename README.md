# Portfolio Exposure and Risk Model

The model has significantly simplified the operation for producing valuation, VaR and stress-testing results, and the approximations made in the implementation of the crawl out calculation in the model currently appear reasonable.

In the model, simulations take quarterly timesteps, and all surrenders are assumed to occur only on these dates. We will write Tn as the time representing the beginning of the n-th quarter, and will also abbreviate the fixed input discount factors as

 

to find the fair value at present (T0) of a cashflow paid at time Tn. The zero rates rn are related to this through semi-annual compounding:

 

The (annualized) stochastic yield of the underlying will be written as Rn, and follows a mean-reverting process.

The market value of the fund at the beginning of quarter n is denoted: MVn, and the book value: BVn. The market value grows so that (ignoring taxes):

 

where D is the duration of the fund, and the book value grows at the crediting rate:

 

with the result capped at the exposure limit: MVn/(1 − ExposureLimit) (that is, at the limit we have: ExposureLimit × BV = BV − MV).

Given an initial crediting rate C0, future crediting rates are determined through (ignoring adjustments):

 

the basic idea being that the crediting rate is chosen to cause the market value and book value of the fund to converge in D years.

The model makes some assumptions about surrender probabilities to generate quarterly surrender probabilities Pn, which indicate the probability of surrender between quarter n − 1 and quarter n, but assumed to happen at Tn. The survival probability Sn to quarter n is then given by:

 

The weekly-calculated VaR is the difference in model-calculated exposures between the present market value (MV) and a stressed value:

 

where _R is the upward shift in underlying yield (typically 20bp). Only this shift in initial market value is made; all other parameters (underlying yield process and initial value, the discount curve) have exactly the same parameters and starting values.

When performing a VaR calculation, it is not necessarily the case that a conservative assumption in the exposure calculation would necessarily lead to a conservative VaR. That is, assume that we have two approximate (i.e., model-produced) exposure results ˜E1 and ˜E2, resulting from a baseline and VaR or stress-test shift respectively. The ‘actual’ exposures E1 and E2 are not known, but if we have been conservative in our approximations, we know that:

 

So we can write ˜E1 = E1 + P1 and ˜E2 = E2 + P2, where P1, P2 _ 0 are the contributions to the exposure that were neglected in making the approximation.

In computing a VaR, we compute ˜E2 − ˜E1, which we would like to assume is a good representation of the ‘actual’ value E2 − E1. However, note that:

 

so we only end up with a conservative VaR result if we know that P2 _ P1, and an accurate result in any case if our approximation satisfies P2 − P1 _ (E2 − E1). What we are going to check is if a slightly different treatment of surrender delays and crawlout (below) will significantly affect the calculated VaR results.

The initial underlying yield process has both the starting value (and the initial crediting rate if the initial crediting rate defaults to equal the initial yield) and the long term rate increased, typically by _R0 = 1%, and the market value decreased.

The surrender probabilities (both initial and final) are increased by a factor of 3.

A combination of the above two shocks.

Once one is fairly confident that the results from the model are complete, the stress-test results follow.

If a contract specifies a non-zero surrender delay period, the fund manager will reallocate the underlying portfolio so as to be ‘immunized’–relatively low risk, liquid investments–with a maturity that will match the actual surrender date.

The model takes the simulated underlying yield at the trigger date: Rtr, and grows the market value of the fund as the corrected risk-free rate ˜R over the n quarters of the delay:

 

using the ‘risk-free’ rate defined as:

 

where Spread = 3.5% is a fixed spread between the risk-free rate and the underlying yield. At the same time, the book value BV is assumed to not grow (BVtr = BVtr+n, which will usually be the case when the market value is insufficient to ‘cover’ the book value) and the present value of the exposure at the end of the surrender delay is given by:

 

This estimate neglects the fact that the yield curve (see https://finpricing.com/lib/FxForwardCurve.html) will not give forward zero rates in exact agreement with (8), the possibility that the yield curve will have evolved between now and trigger, and the possibility that the book value will continue to grow even if the market value is less than the book value.
