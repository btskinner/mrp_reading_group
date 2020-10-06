---
title: BARP!
subtitle: Notes for MRP reading group
date: 27 May 2020
author: Benjamin Skinner
---

# Paper
Bisbee, J. (2019). "BARP: Improving Mister P Using Bayesian
Additive Regression Trees" _American Political Science Review_ 113:4,
1060–1065. [doi:10.1017/S0003055419000480](https://www.cambridge.org/core/journals/american-political-science-review/article/barp-improving-mister-p-using-bayesian-additive-regression-trees/630866EB47F9366EDB3C22CFD951BB6F)

## Data

- From five large surveys (following Buttice & Highton, 2013)
  - Three National Annenberg Election Studies
  - Two Cooperative Congressional Election Studies
- 89 items total
- Sample sizes ranging from 25,000 to more than 60,000 observations
- Treat disaggregated state averages as the "ground truth"
- Use covariate strata proportions to poststratify
- Individual-level covariates:
  - sex
  - race 
  - age 
  - education
- State-level covariates:
  - presidential vote 
  - religious conservatism
- Outcomes:

![_From Buttice and Highton, 2013, p. 465_](items.png)

## Method

- Randomly sample with replacement sample sizes:
	- small (1,500)
	- medium (3,000)
	- large (4,500)
- 200 replications
- To fit models:
  - MRP: [`lme4`](https://cran.r-project.org/package=lme4)
  - BARP: custom [BARP](https://github.com/jbisbee1/BARP) package (based on
  [`bartMachine`](https://cran.r-project.org/package=bartMachine)
  package)
  - Other (see below): [`SuperLearner`](https://cran.r-project.org/package=SuperLearner)
- Other comparison (from Section 7 of [supporting information](https://static.cambridge.org/content/id/urn:cambridge.org:id:article:S0003055419000480/resource/name/S0003055419000480sup001.pdf)):
  - Gradient Boosting Machines (GBM)
  - Kernel k-Nearest Neighbor (kNN)
  - Neural Networks (NNET)
  - Support Vector Machines (SVM)
  - Elastic-net regularization (LASSO)
- Evaluation metrics
  - mean absolute error
  - correlation between predicted state values and "true" values
  - Buttice & Highton (2013) use standardized versions of these
    values; Bisbee shows this version in Figure 1 of supporting
    information --- results are more strongly in favor of BARP over MRP

## Results

- Predictive accuracy (figure 1): 
  - MAE lower with BARP
  - ICC higher with BARP
- Sensitivity to misspecification (figure 2):
  - Drop state-level covariates from model and perform t-test on
    difference
  - BARP appears less sensitive to misspecification
- Sensitivity to within-state sample size (figure 3):
  - BARP less sensitive
- Comparison with other regularization methods (takeaway from supporting
  information, p. 18):
  
  > These comparisons produce three general conclusions.  
  > - First, MRP is competitive with more sophisticated methods in
  >    terms of mean absolute error in larger sample sizes.
  > - Second, BARP is among the best-in-class in terms of mean
  >   absolute error but is more in the middle of the pack in terms of
  >   interstate correlation.
  > - Third, the best methods evaluated in terms of mean absolute
  >   error are not the same as the best methods judged by interstate
  >   correlation. Across both metrics, LASSO regularization is
  >   competitive.

  
  
## Takeaway

- When conditions are good (strong 2nd-level covariates and more
  variation between groups than within), MRP and BARP are about the
  same.
- When less properly specified, BARP may be less sensitive to problems
  than MRP

## An aside to Buttice and Highton (2013)

Buttice, M., & Highton, B. (2013). How Does Multilevel Regression and
Poststratification Perform with Conventional National Surveys?
Political Analysis, 21(4), 449-467. [www.jstor.org/stable/24572674](https://www.jstor.org/stable/24572674)

> Given what researchers have learned about measurement error and
> reliability, few contemporary public opinion scholars would rely on
> national samples of 1500 to estimate state or congressional district
> opinion by simply computing the DM estimates (Erikson 2006). As a
> consequence, our focus is on how well MRP performs in an absolute
> sense. In the case of national surveys of typical size, because DM
> performs so poorly, the fact that MRP outperforms DM does not imply
> that MRP performs _well_ and that its estimates should be employed in
> substantive analyses. (Buttice & Highton, p. 453)

They expect MRP performance (public opinion across states) to be a
function of (p. 453--454):

1. Degree to which individual-level covariates account for opinion
1. Degree to which state-level covariates account for opinion
1. Degree to which true opinion varies across states relative to
   within states
   
![_From Buttice & Highton, 2013, p. 463. (**NOTE:** Meaning of ICC is
different from ICC in Bisbee_)](bh_table_2.png)
   
> Across the three measures, then, the Monte Carlo results show that MRP
> performance is neither uniformly strong nor uniformly weak. The
> quality of the estimates depends crucially on how well the state-level
> covariates account for opinion variation across the states along with
> how the interstate differences in opinion compare to the intrastate
> differences. (Buttice & Highton, p. 462)


# General Questions

- How much credence should we give to "horse race"-style comparisons
  using real data, particularly when the ground truth is just assumed
  from the data?  
  
  > Initially, it may seem problematic to treat a sample as the
  > population, but several factors suggest otherwise. First, consider
  > an item on abortion opinion for which there are ninety-five
  > Vermonters in a national sample of 30,000 respondents. We treat
  > those ninety-five as the Vermont population. If they happen to be
  > wildly unrepresentative, then some state-level covariates included
  > in the multilevel opinion model (like state presidential vote)
  > will not perform well, but this will be reflected in the measure
  > of state-level covariate strength. To the extent that state-level
  > covariate strength matters (and below we show that it is very
  > important), the problem will be addressed when we analyze the
  > relationship between the strength of the state-level covariates
  > and MRP performance. Second, in supplemental analyses one of the
  > state-level covariates we included in the multilevel opinion model
  > was state ideology, which was measured as the disaggregated state
  > mean ideology for the "population." If the ninety-five Vermonters
  > are not typical Vermonters and exhibit abortion opinion that is
  > not truly representative of Vermonters, then the same will likely
  > be true for their ideological preferences. So, by including "true"
  > ideological preferences as a state-level covariate in the MRP
  > models, we account for this. As it turns out, for the eighty-nine
  > items, overall MRP performance and the correlates of MRP
  > performance were nearly identical when we included state ideology
  > and when we did not. (Buttice & Highton, p. 455, footnote 14)
 
- Differences due to use of `lmer()` versus full Bayes via Stan?
