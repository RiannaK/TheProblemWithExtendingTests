# On the problem of extending A/B tests


## Inspiration for the project

This analysis was motivated by Dominic Sando's [Medium article](https://dominicsando.medium.com/extending-a-b-tests-to-reach-significance-the-trap-even-the-smartest-people-fall-into-904f86a18f4b) which discusses the problems of extending tests to reach significance. The article references Aaron Defazio's [work](https://www.aarondefazio.com/tangentially/?p=83) showing the impact on false positive rates when tests are allowed to stop early.

Inspired by both of the above, this project analyses the impact on false positive rates when tests are extended in order to reach significance. While the exact impact is, of course, situational dependent, this analysis considers the scenario that Dominic describes in his article:

> 1.  If it was insignificant but close to 95% significance by its endpoint, they would extend it for more weeks to try and reach significance.

## Project setup

We shall consider situations where the null hypothesis is in fact true (i.e. we shall create two random processes with equal conversion rates) and look at the number of instances of in which we obtain a Type I error (i.e. we incorrectly accept the alternative hypothesis). We shall set a confidence of 95% and use a two-sided hypothesis test. To validate Dominic's arguments via Monte Carlo simulation, we first simulate 1,000,000 experiments over the course of 10 days. We shall look at the subset of experiments that were close to achieving significance (Type I error) but didn't quite get there. We shall then continue to simulate this subset of experiments for a further 5 days and calculate the observed false positive rate.
We will see that the false positive rate is much higher than 5% for experiments that were close to achieving significance at the 10 day point, and much lower than 5% for experiments that were very insignificant.

## Reproducing Defazio's results
To validate that the code works as expected, we shall first check that we can reproduce Defazio's analysis. We don't expect the exact same results as we are using 1,000,000 simulations as opposed to 100 but we should definitely agree with his conclusions. It's important that we can scale our simulations beyond 100 because, for our main analysis, we will need to cull a sizable fraction of the simulations.

|  |Run till end  |Early stopping  |
|--|--| -- |
| Count | 50,123 | 225,365 |
| Percentage | 5.0% | 22.5% |

![Early stopping](https://github.com/RiannaK/TheProblemWithExtendingTests/blob/main/images/EarlyStopping.png)

Our pictures tie up perfectly with Defazio's which is encouraging and the 5.0123% false positive rate is very reassuring that we are simulating this correctly. We get a false positive rate a little over 20% which seems reasonable. Yes - Defazio measures a false positive rate a little lower but he uses 10,000 times fewer simulations and it looks as though 6 breaches on day 1 were not counted in his analysis (I am not convinced that this was intentional on Defazio's part).

## Extending the Experiment to reach significance
To show the effect on false positive rates by extending our experiment, we could progress our simulations as before, but that will tell us nothing new; we already know that false positive rates will increase and the previous analysis shows that very clearly. Besides, extending tests that have achieved very little significance is not very realistic!
In Dominic's blog, he talks about extending tests where the result was "*insignificant **but close***".

To capture the spirit of that statement, let's look at the subset of experiments that achieved a high level of significance (by random chance) but didn't reach the 95% significance required for us to accept the alternative hypothesis incorrectly. We can then simulate those for a further five days and look at the impact on false positive rates. This is much more realistic and insightful because no experimenter will persevere if, after 10 days, their experiment has proved to be completely insignificant. In contrast, an experimenter who has found a result that is on the brink of significance, might be tempted to persevere. We'll consider the following three regimes:
 1. A significance level between 90-95% was achieved after 10 days.
 2. A significance level between 80-90% was achieved after 10 days.
 3. The test was found to be completely insignificant after 10 days.

With those regimes defined, let's see how the false positive rate changes over the next 5 days. For reference, a fair test should achieve a false positive rate of 5%

| Regime | Simulations | False Positives | FP Rate (%) |
|--|--|--|--|
|90-95%  | 24,630 | 4,826 | 20%|
|80-90%  | 50,508 | 4,608 |  9%|
|Not significant  | 49,671| 13 | ~0%|

![Extending Tests](https://github.com/RiannaK/TheProblemWithExtendingTests/blob/main/images/ExtendingTests.png)

## Closing remarks

These results are intuitive and further validate the arguments in Dominic's article. Where results are close but insignificant at the end of an experiment, the false positive rate can become materially higher by extending the test (c. 20%). As results become less significant, the false positive rate decreases and results that were found to be totally insignificant have very little chance of becoming significant in the near future. 

It can be done, but naively extending tests is a dangerous business...