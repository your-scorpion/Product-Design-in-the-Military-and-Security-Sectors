# ANOVA and Bootstrap: Validating UX in Python

*Maksim Tsvetkov*

There is a moment in every analyst's career when A/B testing stops being a party trick. Before that moment, it's the right answer to give in an interview, or a bogeyman to scare developers with. After it, you simply reach for a scatterplot, a violin plot, or a boxplot with the quiet understanding that you are running an ordinary statistical experiment — the same kind a sociologist or a clinician runs. You have found the magic "make it good" button: no endless re-checking, just two variables plotted against each other. And that's all there is to it?

Of course not. Real problems are messier, and there's an old joke that captures the gap exactly:

> — How do I check whether my test is statistically significant?
> — I can tell you.
> — I can tell you too. How do I *check* it?

This article is about closing that gap: about the tools that turn "I can tell you" into "here is the answer." We'll work through the analysis of variance (ANOVA) in its one- and two-way forms, its non-parametric cousins, and then the bootstrap — the resampling method that quietly holds up half of modern experimentation. Everything is grounded in Python and in the kinds of questions that actually land on a UX analyst's desk.

## When A/B testing stops being a party trick

Start with the constraints, because they shape every methodological choice that follows.

**Traffic is always the bottleneck.** If a product sees a hundred users a month at a 1% conversion rate, and the test design calls for ten thousand uniques across all arms, you are looking at a wait long enough to erase any benefit the test might have produced. Add up the cost of the people running it and the losses incurred while it runs, and the payoff can end up smaller than the price of validating the hypothesis. You'll often read online that "two weeks is enough and there's no downside." That number is borrowed from the *average purchase-decision time* in a handful of industries; in practice, the duration of a test is dictated not by the calendar but by traffic — specifically, by the minimum effect you can realistically hope to detect.

There is a standard playbook for the case where you're short on time and stuck with a weakly sensitive metric like conversion, which gives you a feeble effect size. You reduce variance with **CUPED** and post-stratification. But that isn't always appropriate — least of all when the entire market is doing it left and right and the edge has evaporated. So **proxy metrics** come to the rescue: a quantity correlated with your headline metric but far more frequent, and therefore far easier to move the needle on within a reasonable window.

And that's only half the trouble, because test results rarely arrive neatly normally distributed. A normal distribution is what you get under a particular statistical law (Pearson, Bayes) that spreads probability symmetrically around the mean; noise in the data smears that clean bell into something lopsided. When a distribution is skewed to the right, the median sits below the mean, and vice versa. Normality of the sample matters to us directly: no normal distribution means trouble choosing a method — and the browser-based calculators that promise to do your stats for you don't account for noise in the data at all.

**A quick but important caveat before we go further: not every "A versus B" is an A/B test.** Suppose you already have conversion data for a shoe store, and you decide to offer extra discounts to customers who have *already made a purchase*. You accumulate the new conversion figures under the promotion and reach for a two-sample comparison. Technically, this isn't an A/B test at all — A and B must be independent events, and here they aren't. What you're really asking is the probability of event B (a repeat purchase under the discount) *given* that event A (the first purchase) has occurred. That's conditional probability, P(B | A), and it belongs to Bayes, not to a two-independent-samples framework. Naming the design correctly is the first act of a good analysis.

## The trap of comparing raw before-and-after means

Here is the most common mistake I see. You cannot simply compare the growth in retail turnover or foot traffic to a brick-and-mortar store by taking averages across different time periods.

![Store metrics rising after a promotion launch](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32943.png)

Why not? The chart plainly shows the metrics climbing after the promotion launched. But — those numbers could have been pushed up by things that have nothing to do with your work: a one-off pension payout, the arrival of summer, another round of news provoking a run on buckwheat, a competitor down the street shutting its doors. The fix is straightforward: split the stores into two closely matched groups and draw your conclusions from the *difference* between them after launch, not from the before-and-after of a single group.

![Splitting stores into two matched groups](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32945.png)

Matching those groups well is itself a discipline, and it's worth spelling out the process end to end, because a bad split will quietly poison everything downstream.

First, clean the data of junk. Then choose your metric type deliberately — binary (0/1), continuous, or a ratio metric — because the type dictates the criterion. To decide which group is better, you apply statistical tests suited to that metric; for ratio metrics the workhorse is usually a non-parametric bootstrap, paired with CUPED to squeeze out extra sensitivity.

Before you trust any split, run an **A/A test** — an experiment in which no treatment is applied to either arm. If you find "statistically significant" differences in an A/A test, the significance is an artifact of your splitting, not of any real effect. You can seed this with a historical slice of data, then use a **Kolmogorov–Smirnov test** to check whether the distribution belongs to the family you assume it does. The A/A test is the smoke detector of experimentation: cheap, easy to forget, and the only thing standing between you and a confidently reported illusion.

This raises the question that quietly governs everything: **how do you choose the right criterion in the first place?** There are two honest paths. The academic one — reading the books and the papers — is real but treacherous, because books and papers routinely contradict one another. The one I actually use is empirical: I let simulated experiments tell me which criterion behaves. The loop is simple. Take the raw data; split the sample into two groups; run the data through a real test; then *simulate* that test two or three thousand times and watch the results, paying particular attention to the false-positive rate revealed by repeated A/A tests. A criterion that keeps its promised error rate under simulation is a criterion you can deploy. One that doesn't, isn't — no matter what the textbook says about it.

## Why you don't run twelve t-tests

Now the analysis lands on your desk: an A/B/C/D test. The analyst — well-read, possibly on articles of mine — starts comparing all twelve hypotheses pairwise with a t-test. This is a misuse of Student's criterion. Run enough pairwise comparisons and the odds of "finding" a difference where none exists climb until a false positive is nearly guaranteed. I understand that the market loves to apply Student's test even to data that only distantly resembles a normal distribution, but that habit is not quite right.

So what do we do when there are too many factors for a t-test? We run **ANOVA** to compare variances. ANOVA can stand in for Student's test wherever the latter would otherwise be stretched past its remit. Student's t-test is built for two samples drawn from normal distributions; the moment you have more than two samples, the classical one-way analysis of variance is the natural generalization of that same idea. And because the idea traces back to Student, the sample means it compares are assumed, under the null, to be equal.

That makes ANOVA a good fit for essentially any **multivariate test** — the kind where you're varying the font, shape, and color of a button all at once. Red background + white text + rounded corners; blue background + yellow text + no rounding; red background + yellow text + no rounding; and so on down the combinatorial tree. That's a job for ANOVA, with pairwise comparisons permitted in some cases. And when the test rejects the null and you need to crown a winner but you're drowning in pairs, [scikit-posthocs](https://github.com/maximtrp/scikit-posthocs) is what saves me — Tukey's HSD (Honestly Significant Difference), for instance.

The intuition behind such comparisons is geometric: the greater the distance between your data sets, the closer the *overall* median sits to the median of each individual set — and the more confidently you can say the groups are genuinely apart.

## The four sneaker shops

Make it concrete. Say you run four mobile apps, each selling branded sneakers of a different make. Marketing wants to know one thing: does the *average order value* differ across these shops? The null hypothesis under test is that the means across all four groups are equal. You have two independent variables in play — traffic, and the set of brands each shop carries — which makes this a genuinely tangled problem with twelve hypotheses hiding inside it (4×). You reject the null if even one of the micro-alternatives holds.

This is where a **one-way ANOVA** earns its keep. The null (H₀) is the equality of means: overall *m* = m₁ = m₂ = m₃ = m₄. If the result is *not* significant, you carry that into the business decision, having confirmed that average order value is effectively identical across shops. If instead you find a statistically significant difference (H₁), that's your cue to dig in and study how order value actually depends on the brand mix.

## Choosing the right flavor of ANOVA

There are two families to keep straight — one-way and two-way — and a small decision tree for when the assumptions bend or break.

A **one-way ANOVA** wants normally distributed data, two or more groups to compare, independent samples, and homogeneity of variance. Miss the normality assumption and you switch to a rank-based approach — a non-parametric ANOVA, comparable in effectiveness to Bayesian methods (though that's a debatable claim, and I'll leave it debatable). When results are discrete and the number of observations varies across many groups, you reach for the **χ² test** (chi-squared). For metrics like "satisfaction score" or "average task-completion rate," with unequal observation counts across cohorts, take **Tukey's ANOVA and/or post-hoc t-tests**. And if by some miracle every group has the *same* number of observations, a **repeated-measures ANOVA and/or post-hoc t-tests** is the cleaner choice.

The reason to be this careful is that the shape of your data isn't cosmetic — it dictates which test is even meaningful, and the wrong assumption can quietly invert your conclusion.

Consider skewed, heavy-tailed data. Suppose a histogram of your test results comes back **lognormal**. The tempting move is to log-transform it into something normal and proceed to ANOVA. But logging is not a free lunch. It doesn't always help — sometimes it even hurts a little — and, crucially, it changes the *interpretation* of your result. Not every distribution needs to be squeezed into [−1, 1]. Often the better play is to apply Student's test as-is (gaining statistical power) rather than lopping off part of your data and then agonizing over how to interpret relationships between the original variables while carrying an inflated Type I error risk. Simpler still: `wilcoxon.test` with `conf.int = TRUE` to get confidence intervals, or — simplest of all — the bootstrap.

Then there's data whose *geometry* is the whole story. Imagine a customer who books a car in the morning and again in the evening: two peaks in a 24-hour cycle. Or one who orders food four times a day at roughly fixed hours: four peaks. This repeats daily, which means the probability has to wrap smoothly around the seam between 23:59 and 00:00. A plain `t = mean/std` is useless here — you're estimating a probability density for **bimodal, circular data**, and the right tool is a mixture of **von Mises distributions**, treating the day as a closed circle rather than a line with two loose ends. The lesson generalizes: understand the geometry of your distribution before you pick a test, not after.

## Reading the F-statistic

The engine inside ANOVA is the **F-statistic**: the ratio of the variability *between* groups to the variability *within* each group. The picture to hold in your head is two pairs of intervals — A₁, A₂ and B₁, B₂. If they overlap, you cannot claim that one has a larger mean than the other; the between-group signal is drowned by the within-group noise. When the between-group spread dominates, F is large and the groups are pulling apart.

ANOVA — *Analysis of Variance* — asks a single question: is there any difference among all these observations? The null hypothesis says there is none. It is, at heart, a plain assessment of whether the sample means differ. You look at the F-ratio and the p-value, and as always you treat p ≤ 0.05 as evidence of a difference. To say it once more, because it's the whole point: ANOVA can substitute for Student's test when you have too many factors for a t-test. Run the ANOVA first; only if it shows a significant difference do you proceed to pairwise comparisons, and only then to pinpoint *where* the difference lives.

Both Student's t-test and ANOVA assess differences between sample means. The difference is that ANOVA imposes no ceiling on how many means you compare — and it goes further, letting you weigh more than one independent variable at once and estimate the effect of the interaction between two or more of them. (If you've read enough and want to play rather than read, there's an online calculator for the N−1 two-proportion test at <https://measuringu.com/ab-cal/>.)

## ANOVA by hand, then in Python

Theory sticks better once you've cranked the arithmetic yourself. Take this deliberately tiny data set:

| Group 1 | Group 2 | Group 3 |
| ------- | ------- | ------- |
| 4       | 8       | 2       |
| 5       | 5       | 4       |
| 7       | 3       | 6       |

The null hypothesis — the one a product manager should find unsatisfying — is that there's no difference between the means, that Group 1 = Group 2 = Group 3. The alternative says at least one pair of means differs meaningfully.

**Step one: the grand mean.** Sum every observation and divide by the count: 4 + 5 + 7 + 8 + 5 + 3 + 2 + 4 + 6 = 44, and 44 / 9 = 4.89. That's the mean of all observations, and it's trivially checked in code — `d = [4,5,7,8,5,3,2,4,6]` then `print(statistics.mean(d))`.

**Step two: the total sum of squares (SST)** — how much our data varies overall, ignoring the group structure. As with variance, we sum squared deviations from the grand mean (and remember, a minus times a minus is a plus):

```
(4 − 4.89)² + (5 − 4.89)² + (7 − 4.89)² =  0.79 + 0.01 + 4.45 =  5.26
(8 − 4.89)² + (5 − 4.89)² + (3 − 4.89)² =  9.67 + 0.01 + 3.57 = 13.25
(2 − 4.89)² + (4 − 4.89)² + (6 − 4.89)² =  8.35 + 0.79 + 1.23 = 10.37
```

Summing gives **SST ≈ 28.89** (I'm rounding lightly throughout). That's the total variability. Degrees of freedom for the total are always n − 1; with nine observations, df = 9 − 1 = 8.

**Step three: the within-group sum of squares (SSW).** First the group means: Group 1 is (4 + 5 + 7)/3 = 5.33, Group 2 is (8 + 5 + 3)/3 = 5.33, Group 3 is (2 + 4 + 6)/3 = 4.0.

Let's confirm those means in Python before trusting them:

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd
import researchpy as rp

landing_1 = [4, 5, 7]
landing_2 = [8, 5, 3]
landing_3 = [2, 4, 6]

result_df = list(zip(landing_1, landing_2, landing_3))
df = pd.DataFrame(data=result_df, index=None, columns=['set1', 'set2', 'set3'])
print(df)

print(rp.summary_cont(df['set1']))
print(rp.summary_cont(df['set2']))
print(rp.summary_cont(df['set3']))
```

The reported `Mean` comes back identical to our hand calculation. Now the deviations *within* each group — the SSW:

```
(4 − 5.33)² + (5 − 5.33)² + (7 − 5.33)² = 1.78 + 0.11 + 2.78 =  4.67
(8 − 5.33)² + (5 − 5.33)² + (3 − 5.33)² = 7.11 + 0.11 + 5.44 = 12.67
(2 − 4.0)²  + (4 − 4.0)²  + (6 − 4.0)²  = 4.00 + 0.00 + 4.00 =  8.00
```

Summed: 4.67 + 12.67 + 8.00 = **SSW ≈ 25.33**. Its degrees of freedom are the total observation count minus the number of groups: 9 − 3 = 6, or df = N − m.

> A field note, since Python on Windows occasionally wedges itself in a way only a reboot seems to fix: don't reboot. Keep a batch file handy instead —
> ```
> TASKKILL /F /IM pythonw.exe
> TASKKILL /F /IM python.exe
> ```

**Step four: the between-group sum of squares (SSB)** — how far the group means stray from the grand mean. For each group we take (group mean − grand mean)², weighted by the group's size:

```
Group 1:  3 × (5.33 − 4.89)² = 0.59
Group 2:  3 × (5.33 − 4.89)² = 0.59
Group 3:  3 × (4.00 − 4.89)² = 2.37
```

Summed: **SSB ≈ 3.56**, with df = m − 1 = 3 − 1 = 2.

Now the accounting closes: total variability 28.89, of which the within-group part is 25.33 (6 df) and the between-group part is 3.56 (2 df). The lion's share of the variability lives *inside* the groups — an early hint that the groups barely differ.

**Step five: the F-value.** F is the ratio of the between-group mean square to the within-group mean square — each sum of squares first divided by its own degrees of freedom. This is precisely where the original hand calculation is easy to get backwards, so let's be exact about which term goes on top. The numerator is the *between-group* mean square, SSB / df = 3.56 / 2 = **1.78**. The denominator is the *within-group* mean square, SSW / df = 25.33 / 6 = **4.22**. Therefore:

```
F = 1.78 / 4.22 = 0.42
```

Let's verify two ways, to be sure:

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd
from scipy import stats

landing_1 = [4, 5, 7]
landing_2 = [8, 5, 3]
landing_3 = [2, 4, 6]

result_df = list(zip(landing_1, landing_2, landing_3))
df = pd.DataFrame(data=result_df, index=None, columns=['set1', 'set2', 'set3'])
print(df)

print(stats.f_oneway(landing_1, landing_2, landing_3))
F, p = stats.f_oneway(df['set1'], df['set2'], df['set3'])
print(F, p)
```

Both calls agree: `F_onewayResult(statistic=0.4210526315789474, pvalue=0.6743486572598999)`. So what do we conclude? Read the p-value as the scale of the event that occurred. The usual rule is that p < 0.05 counts as statistically significant — a low p-value is good news, letting us reject the null and declare the groups different. Here p = 0.67, and since 0.67 > 0.05 we fail to reject the null. (Note the language: we *fail to reject*, we don't "accept" — the data may differ across samples, but not enough to matter.) The groups are, for practical purposes, the same.

Because a single worked example rarely makes the machinery click, here's a second, cleaner pass on a different data set — three systems whose response times we want to compare:

```
System A: {12.5, 10.2, 11.3, 13.1, 11.8}
System B: { 9.8, 10.5, 12.1, 11.6, 10.9}
System C: {11.7, 13.2, 12.3, 12.9, 11.5}
```

Name the mechanism honestly first. "Analysis of variance" tells you what it evaluates: variance *between* groups against variance *within* them. Each group needs an approximately normal distribution, the observations must be independent, and the groups should be homogeneous. Significance rides on the F-test, and the p-value flows from it: a large F-statistic means at least one group departs from the rest — though it won't tell you *which*, and for that you need post-hoc tests.

State the hypotheses: H₀, no difference; Hₐ, some difference; significance level 0.05. Then the means: A = (12.5 + 10.2 + 11.3 + 13.1 + 11.8)/5 = 11.78, B = 10.98, C = 12.32, and the grand mean = (11.78 + 10.98 + 12.32)/3 = 11.69. From there:

1. Compute the within-group sum of squares (SSW) — variability inside each group.
2. Compute the between-group sum of squares (SSB) — variability among the group means.
3. The total is their sum: SST = SSB + SSW.
4. Degrees of freedom for SSW: dfSSW = n − k = 15 − 3 = 12 (n = 15 observations, k = 3 groups).
5. Degrees of freedom for SSB: dfSSB = k − 1 = 2.

The F-value is `(SSB / dfSSB) / (SSW / dfSSW) = 2.616`. Look up the F(2, 12) distribution and the tail beyond 2.616 gives a p-value of **0.114**. (This is the p-value, not a critical value — a distinction worth keeping crisp, since it's exactly the sort of thing that gets mislabeled.) Because 0.114 > 0.05, we fail to reject the null and conclude there is no statistically significant difference in mean response time among Systems A, B, and C.

A word on **degrees of freedom**, since the term does a lot of quiet work above. Conceptually, imagine you have 100 contract developers and must assemble a team of ten. Each pick you're free to make reduces the choices still open to you; the count of values still free to vary is your degrees of freedom. More formally: degrees of freedom is the number of observations that can vary once something is held fixed. Take the sample 2, 3, 4, 5, whose mean is 3.5. If you insist the mean stay 3.5 while you change the numbers, the last value is no longer yours to choose — it's forced by the other three. So four numbers under a fixed mean give three free ones: n − 1. And when you draw many different samples from a population, their means themselves vary — which is exactly the variation the standard error of the mean describes.

## A one-way ANOVA on real landing pages

Now the way it actually goes on a UX problem. A one-way ANOVA tests the null that two or more groups share the same population mean; the alternative is that at least one mean differs. It applies to two or more sets of observations, and — importantly — the sample sizes are *allowed* to differ. A one-way ANOVA can only ever return a non-negative F. Here's some landing-page data:

```python
import matplotlib
matplotlib.use('TkAgg')
from scipy import stats

landing_1 = [0.1533, 0.1356, 0.1764, 0.3134, 0.1817, 0.1259, 0.1344, 0.0659, 0.1923, 0.1373, 0.0724]
landing_2 = [0.1745, 0.1662, 0.1672, 0.1819, 0.1749, 0.1649, 0.0835, 0.0043]
landing_3 = [0.1330, 0.1352, 0.1817, 0.1016, 0.1968, 0.1064, 0.1905]
landing_4 = [0.1033, 0.2741, 0.1433, 0.1677, 0.1697, 0.1636]
landing_5 = [0.1522, 0.1026, 0.1733, 0.1743, 0.1339, 0.1045, 0.1835]
print(stats.f_oneway(landing_1, landing_2, landing_3, landing_4, landing_5))
```

Out comes `F_onewayResult(statistic=0.2843605403769587, pvalue=0.886069813400433)`, and 0.886 > 0.05.

Had we landed at p ≤ 0.05 we'd reject the null of equal means, because a statistically significant difference would exist. Here p > 0.05, so the null stands and the differences among the means are not statistically significant. We do not reject H₀ in favor of the alternative: the group means are equal, the average order value across shops is the same. In plain terms — H₀: the landings are indistinguishable in quality; H₁: they differ; and our data doesn't budge H₀.

It really is that simple at the level of decision rules: H₀ says set1 = set2 = set3, no meaningful difference; p < 0.05 lets you reject it, p > 0.05 does not. The alternative (H₁) merely asserts that *at least one* mean stands apart, and when you're judging the falsity of H₀ it doesn't matter in the least whether the culprit is one pair of means or three — confirming H₁ just means the data deserve a closer look.

Let's pull everything into one DataFrame. Note that the naïve `zip` approach we used earlier would silently drop data, because the groups are uneven in length:

```python
result_df = list(zip(landing_1, landing_2, landing_3, landing_4, landing_5))
df = pd.DataFrame(data=result_df, index=None, columns=['set1', 'set2', 'set3', 'set4', 'set5'])
```

So we do it more carefully — and plot a boxplot straight away:

```python
df = pd.DataFrame({'landing_1': pd.Series(landing_1),
                   'landing_2': pd.Series(landing_2),
                   'landing_3': pd.Series(landing_3),
                   'landing_4': pd.Series(landing_4),
                   'landing_5': pd.Series(landing_5)})
print(df)
df.boxplot(column=['landing_1', 'landing_2', 'landing_3', 'landing_4', 'landing_5'], grid=False)
plt.show()
```

The boxplot shows serious outliers. A histogram makes it unmistakable that some landings are being dragged upward by a handful of extreme values:

```python
plt.hist(landing_1, alpha=0.5, label='landing_1')
plt.hist(landing_2, alpha=0.5, label='landing_2')
plt.hist(landing_3, alpha=0.5, label='landing_3')
plt.hist(landing_4, alpha=0.5, label='landing_4')
plt.hist(landing_5, alpha=0.5, label='landing_5')
plt.legend(df)
plt.show()
```

Eyeballing the means, you might swear one group is clearly better — the temptation here is to keep landing_1 and landing_2. And this is exactly the chart by which people wrongly crown a winner: the "winner" won on the strength of its outliers. This is the moment to switch off the engineer and switch on the designer-analyst, and to start interrogating the data rather than the plot. *Did we happen to concentrate the best price-to-quality brands in one shop? Did a seasonal promotion inflate the results? Could half the observations in one sample be purchases routed through an aggregator, a factor that swamped whatever the design did?* These aren't rhetorical asides — they're the confounders that a boxplot can't see and that a mean can't defend against.

Mechanically, the one-way ANOVA reduces to a single line, `print(stats.f_oneway(landing_1, landing_2, landing_3, landing_4, landing_5))`. If p > 0.05, you can say with good confidence that the sample means don't differially meaningfully, and you go on to explore the data by more familiar means.

Did that messy result sit uneasily with you? It should — the data has both outliers and wildly unequal group sizes, and I engineered it that way on purpose. In a situation like this you're better served by the **Kruskal–Wallis H-test** and **Welch's ANOVA**, the non-parametric analogs of ANOVA. They stand in for the parametric one-way ANOVA when its assumptions are seriously violated. Kruskal–Wallis assumes neither population normality nor homogeneity of variance; it asks only for an ordinal-scale dependent variable, and it's the right call when the breaches of normality and equal-variance are extreme. As a member of the non-parametric family, it doesn't touch the parameters of any probability distribution at all — it works from the *ranks* of the original values and their sums within the compared groups. Run it on the same data, remembering the null is still equality of the group centers:

```python
import matplotlib
matplotlib.use('TkAgg')
from scipy import stats
print(stats.kruskal(landing_1, landing_2, landing_3, landing_4, landing_5))
```

Result: `KruskalResult(statistic=0.2731503574200107, pvalue=0.9914808283052362)`. We wanted p ≤ 0.05 — "the differences between some of the medians are statistically significant" is what a manager is hoping to hear. But p = 0.991, and 0.991 > 0.05, so the rule bites: the differences between medians are not statistically significant. We cannot overturn the null that all group medians are equal. The centers are equal.

> Kruskal–Wallis gives 0.991 > 0.05; ANOVA gave 0.886 > 0.05. Both point the same way: the differences between the means are not statistically significant, and the null of equal means stands. We don't treat the test results as evidence of a winner — the variants are the same.

To restate the anchor: statistical significance is the measure that tells us whether a test result reflects a real pattern or mere chance, held to 95% confidence.

Now a different example, with a p-value at the other extreme:

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
from numpy.random import seed
import numpy as np
import random
from scipy import stats

seed(1234)
alpha = 0.05
true_cov = np.array([[.8, .0, .2, .0],
                     [.0, .4, .0, .0],
                     [.2, .0, .3, .1],
                     [.0, .0, .1, .7]])
random.shuffle(true_cov)

landing_1 = np.random.multivariate_normal(mean=[.8, .0, .2, .65], cov=true_cov * np.eye(4), size=140)
landing_2 = np.random.multivariate_normal(mean=[.4, .0, .2, .0],  cov=true_cov * np.eye(4), size=140)
landing_3 = np.random.multivariate_normal(mean=[.6, .0, .2, .21], cov=true_cov * np.eye(4), size=140)
landing_4 = np.random.multivariate_normal(mean=[.4, .1, .2, .0],  cov=true_cov * np.eye(4), size=140)
landing_5 = np.random.multivariate_normal(mean=[.8, .0, .2, .05], cov=true_cov * np.eye(4), size=140)

stat, p = stats.kruskal(landing_1, landing_2, landing_3, landing_4, landing_5)
print('Statistics=%.3f, p=%.3f' % (stat, p))

if p > alpha:
    print('Same distribution (fail to reject H0)')
else:
    print('Different distribution (reject H0)')
```

`Statistics=43287.107, p=0.000` — a p-value fit not merely for landing pages but for commissioning a nuclear reactor, a trading system, or an AI. Here the P < 0.05 rule fires: the differences among the centers are statistically significant, and we reject the null that all medians are equal. But be precise about what rejection buys you. Rejecting the null doesn't say *which* group differs. And when p rounds to 0.00, you have grounds to reject at the 5% level (0.00 < 0.05) but not necessarily at the 1% level — read the actual figure before you celebrate. Most importantly: we have grounds neither to *accept* the null nor to accept the alternative. The question was framed as "the probability of rejecting the null"; it said nothing about proving the alternative true. Rejection is a statement about H₀, not an endorsement of H₁.

## Two-way ANOVA: two factors at once

Return to the four sneaker shops. They're closely matched on assortment and on search ranking. But suppose you need to compare sales across shops that differ on their **Rambler rating** *and*, simultaneously, on **design** as a dominant factor — or you want to know whether an interactive chat on one of the sites gives conversion an extra push. That calls for a **two-way ANOVA**. This test examines the influence of one or more categories of independent variable — "factors" — on a dependent variable. Like every ANOVA, it assumes the observations are normally distributed. We want to gauge, through a two-way ANOVA, the effect of two variables (position in the Rambler ranking and differing design) on sales through the sites.

We'll use [pingouin](https://github.com/raphaelvallat/pingouin) — note it needs Python 3.5 or newer. It's an excellent library; just look at how much useful information it returns from a plain t-test:

```python
import matplotlib
matplotlib.use('TkAgg')
import numpy as np
import pingouin as pg

np.random.seed(44)
mean, cov, n = [0, 0], [(2, .6), (.92, 1)], 30
x, y = np.random.multivariate_normal(mean, cov, n).T
print(pg.ttest(x, y))
```

I prefer pingouin over pyvttbl, since the latter has all but stopped being maintained. On to the two-way test — first, prepare the data:

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd

data = pd.read_excel(r'E:\Python_2\Book1.xlsx')
df = pd.DataFrame(data)
print(df)
```

Then specify the arguments: `dv` is the column holding the dependent variable, and `between` lists the columns holding the between-group factors.

```python
import pingouin as pg
from statsmodels.graphics.factorplots import interaction_plot

fig = interaction_plot(df.Rambler, df.Design, df.Result, ms=8)
aov = pg.anova(dv='Result', between=['Rambler', 'Design'], data=data, detailed=True)
print(aov)
```

```
        Source            SS        DF     MS            F     p-unc    np2
0           Rambler  5.757415e+06   3.0  1919138.223  1.885  0.147555  0.124
1            Design  1.647162e+05   3.0    54905.413  0.054  0.983255  0.004
2  Rambler * Design  4.878005e+06   9.0   542000.585  0.532  0.842054  0.107
3          Residual  4.071443e+07  40.0  1017860.875    NaN       NaN    NaN
```

Read the p-value first; only if the null stands do you glance at the F-value. Rambler rating shows no significant influence on audience preference. Because every p-value sits well above the 0.05 threshold, the conclusion is that the three effects together — the two main effects and their interaction — exert no significant influence on sales from the landing pages. The interaction term, in particular, is not significant.

![Two-way ANOVA interaction plot](https://your-scorpion.ru/wp-content/uploads/2019/09/Figure_1.png)

Though I'll add an expert's aside that the numbers don't fully capture: landings sitting in the top 50 of Rambler's catalog performed markedly better than the same landings buried in the deeper positions. The model didn't find it significant; my eyes, across enough of these, are less sure. This is the honest tension between a test result and domain judgment, and a good analyst holds both.

There are, broadly, two schools of thought behind all of this: **Fisher** (ANOVA, p-values, the null hypothesis, a continuous measure) and **Neyman–Pearson** (PCA, the method of moments, chi-squared). Knowing which tradition a tool comes from tells you a great deal about what its numbers are entitled to claim.

## Bootstrap, intervals, and the A/B test

We've spent a while on the analysis of variance as a way to test equality of means. There are alternatives worth a glance — Welch's t-test, or Mann–Whitney–Wilcoxon. But are they always appropriate? No. Real problems throw up quantities with **heteroskedasticity** (often tamed by dividing y by x), and that alone can put the parametric tests on shaky ground. So we turn to a method that asks almost nothing of the data.

Back to basics for a moment. A statistic is a measurable function of a sample. The sample: X = {X₁, …, Xₙ}. The sample mean, for instance, is one such statistic.

![The sample-mean statistic](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32948.png)

Suppose we have a normal distribution (f₀), nicely differentiable, and we need to estimate its expectation. One route is **maximum likelihood**: write the likelihood function and maximize it over the parameter. You can take the logarithm — being monotonic, it leaves the location of the extremum unchanged — which usually makes the algebra kinder.

![Maximum likelihood, step one](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32950.png)
![Maximum likelihood, step two](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32953.png)

You'll find this example in nearly any textbook. But what if our density is a *mixture* of normals? Or what if we're asked for the *variance* of the estimate itself? Now point estimates start to fail, and the **empirical distribution function** comes to the rescue — the bootstrap in particular.

Say we have a pile of observations from an unknown distribution. We could peek at the distribution through its median. Is that a good approach? If all the data cluster near 50, the median will sit near 50 and all is well. But if half the observations huddle near 0 and the other half near 100, the median tells us almost nothing. So what do we do? We re-check, many times over. That is the bootstrap's whole philosophy applied to finding confidence intervals: if you'd rather not wrestle with Bayes and you just want sane results, use the bootstrap. It has one real drawback — less power than a parametric criterion — which we compensate for with a larger sample. And when the data are decidedly non-normal, the bootstrap is the single best way to find confidence intervals, precisely because it's non-parametric.

This is also the answer to a very common question: you've run an ordinary A/B test with two recommendation systems, and one has more purchases — a winner. To prove the result isn't a fluke, the bootstrap is exactly right. If you need an answer fast, the recipe is this: for each sample, draw customers with replacement 500, 1,000, or 2,000 times; on each draw compute the mean; trim the extreme 2.5% at each tail; and read off the 95% confidence interval. Then compare the intervals and check whether they overlap. If they don't, your winner is real. The heavier alternative is a t-test for normal data, or Wilcoxon with `conf.int = TRUE` for everything else.

The algorithm, laid out:

1. From a sample of size *n*, resample a new sample of the same size, many times (*B*). Call each new sample *x*.
2. Compute the values of interest on each.

   ![Bootstrap step 2](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32951.png)
3. Estimate the functional of interest over the bootstrap replicates, *T(F)*.

   ![Bootstrap step 3](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32952.png)
4. Compute the sample variance across the set of replicate values — and from there, work with that.

   ![Bootstrap step 4](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32951-1.png)
   ![Bootstrap step 4 continued](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32954.png)

One caveat up front: if you're sitting on an enormous data set — Yandex, Kaspersky, or Avito scale — the bootstrap isn't for you. It'll be too slow, and your choice is **linearization**. Everyone else can use the bootstrap. But — it also behaves poorly when a sample is riddled with anomalies or is simply too small. If the input data is distorted, the bootstrap output will be distorted right along with it. Garbage resampled is still garbage.

Take a **triangular distribution**, where the distributional law is not entirely obvious, and plot the theoretical, empirical, and bootstrap distribution functions together:

```python
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

def MakeDistrubition(x):
    if x < 0:
        return 0
    elif x < 1:
        return x**2 / 2
    elif x < 2:
        return 2 * x - x**2 / 2 - 1
    else:
        return 1

size = 400
X = np.random.uniform(0, 1, size=size) + np.random.uniform(0, 1, size=size)
Len = len(X)
B = 1
defacto = np.random.choice(X, (B, Len), replace=True)
defacto.shape
plt.figure(figsize=(20, 12))
for bootstrap_sample in defacto:
    plt.hist(bootstrap_sample, bins=Len, density=1, histtype='barstacked',
             cumulative=True, alpha=0.15, color='black', linewidth=2)
    plt.plot(np.linspace(0, 2, 400),
             list(map(MakeDistrubition, np.linspace(0, 2, 400))),
             color="magenta", label="True", linewidth=3)
    plt.hist(X, bins=Len, density=1, histtype='step', cumulative=True,
             label="Empirical", bottom=None, color='green', linewidth=3)
    plt.legend()
    plt.show()
```

The theoretical and empirical functions track each other closely, and you can see how a confidence interval has a downside and an upside — its lower and upper bounds. Enlarge the sample, and the empirical distribution function converges on the theoretical one.

A note on the two flavors. **Parametric bootstrap** works from some parameter of a distribution, drawing samples with moderate variance from it. **Non-parametric bootstrap** leans on no parameter at all — it simply optimizes the shift from the mean, working from a heuristic and assuming nothing.

The working principle: the bootstrap resamples the sample *with replacement*. It belongs to the Monte Carlo family — random-number generators, all the way down. We take *n* observations from the original data, compute some statistic on the drawn set (the mean, say), and put them back. That drawing-and-analyzing of a single sub-sample is *sampling*, and we repeat it many times over. Out of that repetition falls a mean with some characteristic spread around it. The quality criteria for a bootstrap are the shape of the boot-statistic's distribution — you want unimodality — and its bias: the smaller, the better.

Sampling itself splits into two camps, random and deterministic. Random is what it sounds like: pull values at random. Deterministic follows a system — take every second value out of every fifth, for instance. The bootstrap works mostly with random sampling, and it isn't shy about resampling: from the set 1, 2, 3, 4, 5 you might well draw 1, 3, 3, 3 — a chosen value is returned to the pool and can be picked again.

If your sample runs to a couple of billion values, constantly resampling with replacement becomes a punishing operation. When you're that lucky with data volume, use the **bucket method** instead: compute a mean per bucket, then compare the distribution of the two sets of means.

A confidence interval can be built many ways, and the bootstrap is just one — but it demands no normality and even tolerates some imprecision in the result, thanks to the central limit theorem. In the spirit of the three-sigma rule, you set aside the quantiles at the 2α and 1 − 2α levels, and those become the interval within which you can reject the null. One way to get a bootstrap interval is the *normal interval*, but it requires the CLT's conditions to hold; I'd point you instead toward the *central interval*, or the *percentile-based* interval.

Let's draw a vector of bootstrap values from a normal distribution:

```python
import numpy as np
np.random.seed(45)

X = np.random.normal(20, 2, 200)

def theta(input):
    return np.exp(np.mean(input))

def bootstrap(input, function, n_times=500000, random_state=15):
    np.random.seed(random_state)
    bts_statistics = np.empty(n_times)
    for i in range(n_times):
        input_b = np.random.choice(input, size=input.size)
        bts_statistics[i] = function(input_b)
    return bts_statistics

theta_estimate = theta(X)
theta_bootstrap_estimate = bootstrap(X, theta)
print(theta_bootstrap_estimate)
```

The output is a vector: `[4.47464000e+08 4.11481479e+08 4.13320475e+08 ... 4.68092497e+08 5.01441718e+08 5.58717285e+08]`.

For a bootstrap you want at least 10,000 observations gathered — 15,000 if your accuracy requirements are sensible — even though that will eat compute during the calculation. With only 100 observations, choose a more precise method for your confidence intervals. A Monte Carlo test is genuinely accurate where the bootstrap is not, and this is the point where you might be tempted to abandon the bootstrap and close the article. Don't — but understand the trade. The bootstrap is, after all, just a non-parametric method for estimating unknown features of a sampling distribution: variance, bias, percentiles. Monte Carlo buys its accuracy by making you commit to distributional assumptions. The bootstrap asks only for a large sample, and its reliability climbs with size, with the bonus that it applies across a broad range of hypotheses rather than a narrow H1-vs-H2-vs-H3. Just set the number of samples or buckets to a thousand or more, and accuracy converges by the law of large numbers. The idea is simple: bootstrap comes in parametric and non-parametric forms, and the non-parametric one runs on heuristics, free of assumptions.

But — and this deserves its own line — **this is not data normalization.** The bootstrap reshapes your uncertainty estimate; it does not reshape your data into a normal distribution.

So what *is* a confidence interval? It's a tolerated margin — a band inside which the true mean for the whole population lives. Do you see a p-value here? No, and you don't need one: a confidence interval can be read in place of a p-value. Concretely, if the system hands you an interval of 14.5 to 28.5, that's your range of plausible values. With n = 20, the narrower that interval, the better. For reference, 90% corresponds to a z of 1.645, 95% to 1.96, and 99% to 2.575. It's common to let a variable *n* stand for the range −1 to 1, *d* for −2 to 2, *y* for −3 to 3, and from there work out the confidence probability — those familiar numbers, 90, 95, 99, being the confidence levels themselves.

Let's build one by hand. SciPy has no bootstrap built in, so install `scikits.bootstrap`:

```python
import matplotlib
matplotlib.use('TkAgg')
from scipy import stats
import scipy
import scikits.bootstrap as bootstrap
from matplotlib import pyplot as plt

data = stats.poisson.rvs(33, size=15000)
results = bootstrap.ci(data=data, statfunction=scipy.mean)
print(results)

plt.plot(data, '.')
plt.waitforbuttonpress()
plt.show()
```

Alongside a tidy plot, what matters are the two interval bounds: 32.9208 to 33.1053 — the lower and upper edges of the range. The interval doesn't include 0, so we conclude the change in conversion is statistically significant. Now a new distribution:

```python
plt.figure(figsize=(14, 7))
x = np.concatenate([np.random.exponential(size=600), np.random.normal(size=600)])
plt.hist(x, 35, histtype="step", linewidth=1, density=True);
```

The brief: find the range in which the mean plausibly lies, and give a point estimate. The solution:

```python
n = len(x)
B = 100000
ranc = np.random.choice(x, (n, B))
mb = ranc.mean(axis=0)

mb.shape
intervals = np.percentile(mb, [1.5, 98.5])
print(intervals)
np.mean(x)
```

We get `[0.36265025 0.49580532]` and a point estimate of `0.42921685831404904`. The results rest on variance — that's important to keep in mind. The empirical distribution function is close to the theoretical one, and that closeness is the whole point of the exercise.

Now the question that actually pays the bills. We have two versions of a site's home page from an A/B test. The first version was seen by 3,700 users at a 4% purchase conversion; the second by 3,700 users at 6%. Assume the users are equivalent and no other factors intrude. Was there a real lift, or could a jump from 4% to 6% be chance? We compute a confidence interval for the *difference* of the two conversions and read off its bounds.

And here is where the arithmetic has to be honest, because the answer depends entirely on your traffic. At 3,700 users per arm, a 4%-to-6% lift is not noise: the 95% confidence interval for the difference lands around [0.010, 0.030], comfortably clear of zero, which means we *reject* the null and call the lift real (a two-proportion z of roughly 3.95, p < 0.0001). The same lift on a few hundred users per arm tells the opposite story — the interval widens to something like [−0.012, 0.052], straddling zero, and we can no longer rule out that the true difference is nil. That contrast *is* the lesson: the interpretation rule never changes — a difference interval that contains 0 means the change isn't statistically significant, and any apparent movement could be weather, a provider outage, or a shuffle in search rankings rather than your product decision — but whether you clear the bar is a question of sample size, i.e. of statistical power. Same effect, different traffic, opposite verdict.

Let's do it slightly differently: take two home-page versions and probe how significant the changes could be, by generating as many values as we like from the sample and computing a mean for each. This time I'll use a different library, `bootstrapped`.

After any group-comparison method (ANOVA, in this article), apply the bootstrap with a cap on the resampled size. Overdo the sample and chi-squared will *guarantee* you significant differences — on a truly huge sample I'd rather just look at the means and medians directly. The general rule of thumb: a t-test is for interval data, chi-squared for binomial or categorical data (less intuitive there, since mean and variance stop being meaningful).

```python
import numpy as np
import bootstrapped.bootstrap as bs
import bootstrapped.stats_functions as bs_stats

mean = 354
stdev = 20

population = np.random.normal(loc=mean, scale=stdev, size=15000)
samples = population[:2000]

print(bs.bootstrap(samples, stat_func=bs_stats.mean))
print(bs.bootstrap(samples, stat_func=bs_stats.std))
```

Out comes a center of 354.58 (353.71, 355.46) and a standard deviation of 19.74 (19.11, 20.36). Now we know how well the means from the loaded sub-sample match the overall mean; you'd read this as "the average time on site is roughly the same."

A word on **standard deviation**, since it's doing quiet work here — it's a value in the data's natural units, the spread per one sigma. Its minimum possible value is zero, reached only when there are no deviations at all: a data set of 24, 24, 24, 24, and a thousand more 24s. A healthy set looks like 1, 2, 3, 3, 4, 5, 6, 7 — mean 4, standard deviation ≈ 2. A worse one is 1, 2, 3, 4, 5, 6, 100 — mean 17, standard deviation ≈ 36. The median is 4 in both cases, but the standard deviation is wildly different, which is exactly why you don't judge spread by the median.

## From the bootstrap to the ensemble

Here's a connection worth making explicit, because it ties this whole article together. The bootstrap — resampling with replacement — isn't only a way to build confidence intervals. It's also the beating heart of one of the most useful models in applied machine learning, and following that thread takes us from hypothesis testing into classification.

Start with the simpler question the thread implies: how do you *classify*? The intuition is to split a data set into groups by feature, moving from broader categories to narrower ones — but the substance of that process is a **decision tree**. In classification and regression alike, decision trees do the work. For a binary tree — one node splitting into two — you travel down the left branch when a condition is true and the right when it's false. Features can be numeric or categorical.

Take a categorical example. You choose a splitting criterion — Shannon entropy, or the simpler **Gini index** — and that criterion is fixed when the tree (or the random forest) is created; you can even invent your own. You scan every feature, find where the Gini index is minimized, and split there. Some data:

```python
import numpy as np

X = np.array([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
              [1, 3, 4, 5, 11, 0, 8, 6, 3, 7, 16, 0, 2, 3, 2, 4, 21, 4],
              [57, 87, 34, 12, 43, 87, 46, 69, 141, 144, 45, 12, 94, 38, 91, 74, 121, 141],
              [1, 1, 2, 1, 2, 3, 2, 1, 2, 1, 2, 1, 2, 1, 1, 2, 1, 1]], dtype=np.float64)

y = np.array([1, 0, 0, 1, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1, 0, 0, 1])
x = X[2, :].copy()
print(x)
```

The split reads like this: values above a threshold go to the right subtree, those below to the left. Near the top of the tree you place the simplest conditions — typically a comparison of one feature *xⱼ* against a threshold *t*: *xⱼ* ≤ *t*.

```python
test_1 = x[x >= 32]
test_2 = x[x < 32]
print(test_1, test_2)

test1 = y[x >= 32]
test2 = y[x < 32]
print(test1, test2)
```

This is standard NumPy indexing: `>=` sends us right, `<` sends us left. To count how many values head into the right subtree, `len(y[x >= 33] == 1)` returns 16 — the values where x ≥ 33 and y = 1. I used 33 as an arbitrary threshold; in practice you'd sweep every candidate and, for each, compute the Gini index. That tells you where each value lands:

```python
x_right_side_zero = (y[x >= 33] == 0).sum()
print(x_right_side_zero)

x_right_side_one = (y[x >= 33] == 1).sum()
print(x_right_side_one)
```

Wrap it in a function that returns the Gini index. The function handles the specific case where a value above the threshold is assigned to class 1 and one below to class 0. One obvious pitfall: if either subtree comes back empty you'll divide by zero, so in this implementation both subtrees must be non-empty — and since there are only two classes, there's no sense splitting into three.

```python
def gini(x, y, border):
    qty_left = len(y[x < border])
    qty_right = len(y[x >= border])

    quantity1_left = ((y[x < border]) == 0).sum()
    quantity1_right = ((y[x >= border]) == 1).sum()

    probability_zero = quantity1_left / qty_left
    probability_one = quantity1_right / qty_right

    probability = np.array([probability_zero, probability_one])

    h = np.sum(probability * (1 - probability))
    return h

gini(x, y, 33)
```

That yields three numbers — 6, 10, and 0.234375, the last being the Gini index. In this data the third feature is salary, the second is years of experience, and the fourth is qualification level. Wherever the Gini index is smallest, you make the first split — finding the predicate that most cleanly cleaves the data in two — and then refine with further predicates. Swept in a loop:

```python
for border in x:
    print("Border:{}, gini={}".format(border, gini(x, y, border)))
```

```
Border:57.0,  gini=0.37038286388935737
Border:87.0,  gini=0.44437499999999996
Border:34.0,  gini=0.234375
Border:12.0,  gini=nan
Border:43.0,  gini=0.41709183673469385
Border:87.0,  gini=0.44437499999999996
Border:46.0,  gini=0.3819444444444444
Border:69.0,  gini=0.359375
Border:141.0, gini=0.4444444444444445
Border:144.0, gini=0.2283737024221453
Border:45.0,  gini=0.3966863905325444
Border:12.0,  gini=nan
Border:94.0,  gini=0.4530177514792899
Border:38.0,  gini=0.4444444444444445
Border:91.0,  gini=0.4444444444444445
Border:74.0,  gini=0.41975308641975306
Border:121.0, gini=0.45408163265306123
Border:141.0, gini=0.4444444444444445
```

A whole **forest** can be grown from trees like these — and this is exactly where the bootstrap re-enters, now wearing the name **bagging**. The mechanism is the one we already know: given observations [1, 2, 3, 4, 5], a bootstrap draw hands back a same-sized set. Two draws might give [1, 5, 3, 3, 5] and [2, 1, 4, 2, 2] — order doesn't matter, some observations are missing, and in the second the value 2 shows up three times. Make many such samples and grow a separate decision tree on each. Which tree do you believe? You put it to a vote. Feed in some data and one tree returns classes 0, 0, 1, 1; a second returns 0, 1, 1, 0; a third returns 0, 0, 1, 0. Where the trees agree, you take the majority — final result 0, 0, 1, 0 — and that, in essence, is how a random forest works.

A random forest inherits the trees' virtues: the data needn't be normalized, the tree is indifferent to feature *type*, and feature *scale* doesn't matter. Rescaling features barely nudges a random forest — whereas if you rescale or log-transform the *target* variable Y, a linear model breaks. Even if a couple of trees badly overfit, the rest smooth the result out. The forest's weakness is the flip side of its strength: the model is complex, and complexity means trouble justifying and interpreting it — it's a black box. Explaining to a business, and to that business's customers, that "we have depth 10 and 30 features, an enormous number of nodes, and *that's* why one applicant gets a loan and another doesn't" is genuinely hard.

A few practical notes on forests. Different libraries implement them differently, so watch that the whole forest actually uses all the features. When part of the data doesn't make it into a bootstrap sub-sample, it doesn't make it into training either — the errors measured on that held-out portion give you the **out-of-bag (OOB) error**, a free validation estimate. Because it's an ensemble, you can choose the number of trees (scikit-learn's default shifts from version to version) and the number of cores. Ideally, for anything performance-critical, you write the library in C and call it from Python.

## Taking it to production

A model that validates offline still has to survive contact with production, and the question of how you *monitor* it is its own discipline.

The classical answer is A/B tests plus a control group — but only after the metrics have cleared DS validation. You monitor whatever the model influences, and you add a **Population Stability Index (PSI)** on the features: compare the feature statistics over time, watch whether the mean or the maximum drifts, and inspect the distribution of the predictions themselves. Drift in the inputs is the early warning that your offline metric is about to stop describing reality.

On metrics, the **ROC curve** is best understood from its picture: true-positive rate on one axis, false-positive rate on the other. The diagonal is a coin flip — 50/50, a model that knows nothing — and any curve above the diagonal beats guessing. From it you read TNR and TPR, and the operating point you choose is a trade: if you want to auto-close 90% of tickets, you're accepting some percentage of errors to do it (say, 2%). The whole game is to push the area under the curve toward the top-left corner.

![ROC curve: TPR against FPR](http://your-scorpion.ru/wp-content/uploads/2020/12/Artboard-2.png)

Sometimes you *can't* run an A/B test — evaluating the impact of a marketing campaign, for instance, where there's no clean way to withhold treatment. Then you reach for causal-inference tooling. [CausalImpact](https://google.github.io/CausalImpact/CausalImpact.html) works when you have two correlated metrics and stationary data. [Prophet](https://pypi.org/project/fbprophet/) — Bayesian at heart — lets you model individual campaigns as regressors alongside other factors, with built-in support for holidays and seasonality. Beyond those sit ARIMA and survival/hazard models. And for a quick, no-code read, there's [GA Effect](https://app.iihnordic.dk/ga-effect/).

## Nota bene: the numbers won't tell you everything

I reach for the bootstrap when I meet mixed distributions, or when I need to simulate a sample mean — because the chance to work with 100% of the data comes around approximately never. What the bootstrap *can't* do is regression and approximation, and it yields to the parametric tests on raw power. It trades that power for a forgiving temperament: no normality required, a broad range of hypotheses covered, and honest uncertainty on data that would make a textbook test flinch.

But hold all of it lightly. Every test in this article answers a narrow, formal question, and none of them can tell you whether you asked the right one. The p-value can't see the aggregator traffic hiding in your sample; the confidence interval can't feel the seasonal promotion; the forest can't explain itself to the customer you just declined. The statistics are where the analysis becomes rigorous — they are not where it becomes wise. That part is still yours.
