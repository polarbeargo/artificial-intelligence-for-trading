# Multi-factor-Model
AI for Trading Nanodegree Program

[//]: # (Image References)

[image1]: ./images/BasisPointsPerDayperQuantile.png
[image2]: ./images/ObjectiveandConstraints.png
[image3]: ./images/QuantileAnalysisFactorReture.png
[image4]: ./images/TurnoverAnalysis.png
[image5]: ./images/factorReturns.png 
[image6]: ./images/pcaExplainedVarianceRatio.png  
[image7]: ./images/portfolioHoldingByStock.png
[image8]: ./images/portfolioNetFactorExposures.png
[image9]: ./images/riskModel.png
[image10]: ./images/OptimalHoldingsStrictFactor.png

# Statistical Risk Model
Creating a statistical risk model using PCA. So, the first thing is building the PCA model.
## Fit PCA  
![][image6]  
## Factor Returns
Implement `factor_returns` to get the factor returns from the PCA model using the returns data.
![][image5]  
## Quantile Analysis
### Factor Returns
View the factor returns over time. We should be seeing it generally move up and to the right.
![][image3]
### Basis Points Per Day per Quantile
It is not enough to look just at the factor weighted return. A good alpha is also monotonic in quantiles. Let's looks the basis points for the factor returns.  
![][image1]  
What do we observe?

- None of these alphas are **strictly monotonic**; this should lead you to question why this is? Further research and refinement of the alphas needs to be done. What is it about these alphas that leads to the highest ranking stocks in all alphas except MR 5D smoothed to *not* perform the best.
- The majority of the return is coming from the **short side** in all these alphas. The negative return in quintile 1 is very large in all alphas. This could also a cause for concern becuase when you short stocks, you need to locate the short; shorts can be expensive or not available at all.
- If you look at the magnitude of the return spread (i.e., Q1 minus Q5), we are working with daily returns in the 0.03%, i.e., **3 basis points**, neighborhood *before all transaction costs, shorting costs, etc.*. Assuming 252 days in a year, that's 7.56% return annualized. Transaction costs may cut this in half. As such, it should be clear that these alphas can only survive in an institutional setting and that leverage will likely need to be applied in order to achieve an attractive return.

## Turnover Analysis

Without doing a full and formal backtest, we can analyze how stable the alphas are over time. Stability in this sense means that from period to period, the alpha ranks do not change much. Since trading is costly, we always prefer, all other things being equal, that the ranks do not change significantly per period. We can measure this with the **factor rank autocorrelation (FRA)**.

[alphalens.performance.factor_rank_autocorrelation](https://quantopian.github.io/alphalens/alphalens.html?highlight=factor_rank_autocorrelation#alphalens.performance.factor_rank_autocorrelation)
![][image4]  
## Objective and Constraints
Using this class as a base class, you'll implement the `OptimalHoldings` class. There's two functions that need to be implemented in this class, the `_get_obj` and `_get_constraints` functions.
![][image2]
It put most of the weight in a few stocks.
## Optimize with a Regularization Parameter
In order to enforce diversification, we'll use regularization in the objective function. We'll create a new class called `OptimalHoldingsRegualization` which gets its constraints from the `OptimalHoldings` class to have Well diversified.
![][image7]
![][image8]
## Optimize with a Strict Factor Constraints and Target Weighting
Another common formulation is to take a predefined target weighting(e.g., a quantile portfolio), and solve to get as close to that portfolio while respecting portfolio-level constraints. 
![][image10]
![][image9]
