# Python in the Analysis of Experiments

*Maxim Tsvetkov — 18 January 2019*

Making decisions without data is a game of Russian roulette: maybe it works out, maybe it doesn't. That is why data should be accumulated from the first day a business draws breath. Data is the raw material a company runs on, and in the early days it will help you make decisions at almost no cost. Once the volume pushes past a terabyte, though, it becomes genuinely hard to keep squeezing useful features out of it on a regular cadence. Visualization is what rescues you. With a working grasp of the underlying mathematics and the libraries Python puts within reach — matplotlib, seaborn, plotly — you can cover the overwhelming majority of what leadership needs to see in order to decide.

There is no shortage of tools for the job: R, Python, JavaScript, MATLAB, Scala, Java. R is, at heart, a language for researchers and students, which is precisely why it currently carries a richer library of visualization packages than Python does. Python's advantage shows up later, when the analysis has to be folded into a wider engineering effort.

It helps to ground all of this in a domain where the stakes are written in plain numbers, and the one I know best is fintech. Banking is a profit-and-loss business, so every task ultimately traces back to the P&L line. You have to separate the players first — neobanks, peer-to-peer platforms, personal-finance managers — but the shared backdrop is the same: a lightly regulated market, modest household incomes, low financial literacy. Out of that backdrop fall the recurring themes of big data, biometrics, and omnichannel delivery.

Fintech is awash in data, but the data is always filthy and full of holes. You can partner with other banks to exchange it (within the bounds of data-protection law) and with telecoms, which buys you some genuinely useful signals — the older a customer's SIM card, for instance, the higher the odds you should extend them credit. The business always sets the analytical task, and the framing tends to be anything but simple, ranging from the obvious wish to predict retail churn (in Russia a bank earns handsomely from a customer who merely holds a debit product) all the way to psychographic profiling. There will almost certainly be work on reducing one-size-fits-all treatment and personalizing the offer to each customer, because even inside a single segment the offer-to-expectation fit is lopsided for customers at opposite ends of the distribution. So there is perpetual demand for up-sell and cross-sell models built from the ratio of "negative" spending (rent, utilities, fines) to "positive" spending (a new wardrobe, the cinema, the circus, the mountains).

Then there is the classic credit decision. A bank wants to know whether a prospective borrower will actually repay, and that is textbook statistics with a default action: if they won't pay, don't lend (the conservative bank), or, inverted, lend to everyone by default (micro-lending). If your null hypothesis is "we do not lend," then a Type I error is to lend and acquire a customer who never repays. A second cluster of tasks lives in online marketing. Pushing ads at every user is wasteful — the leads are low quality, the bank racks up loan rejections and irrelevant offers, and the spend never earns back — so targeting gets enormous attention. This is recommendation by point of interest (filling stations, malls, airports, universities), which means accumulating events as JSON, batching them, and shipping them over HTTPS to your web servers. From there you take latitude and longitude, correct for the polar distortion of the Mercator projection and for time offsets against Greenwich, "draw" a hexagon or a square to fix the user's location, identify where they are and what surrounds them — via KLADR, FIAS, ISO 3166, or GeoNames — and only then does the recommender engine take over. And because compute is usually finite, the forests and similar models are tuned to isolate the confident answers cheaply: a scoring rule might drop every applicant with more than three delinquencies over the past three years into a separate branch and spend no further resource on them, while those with fewer get a closer look at salary, the number of banks they've been served by, and so on. FICO's banking scorecard is worth studying as the canonical example. Those are the tasks that come first; the toolchain that follows is R, Zeppelin, Presto, Python.

Back to the mechanics. The pipeline I follow looks roughly like this:

- check the data for normality;
- test for a difference using a statistical test;
- estimate the magnitude with a confidence interval (the mean under normality, the median when the data are not normal);
- compare against how the two groups of users behaved beforehand.

On large projects, where a positive change of even one percent matters, analytics is not optional. At a minimum you have to check not only the result of the A/B test itself but also how the two user groups behaved *before* the experiment, and then strip out the influence of other experiments, past and present.

You need a great deal of data for distributed analysis in Python to mean anything at all; without volume there is no point. The flip side is that with enough data you will almost always land a small p-value, and what remains is to work through normality, independence, and the rest. Sizing the sample in the first place calls for power tests. On small samples a statistical test can be less reliable than an expert's judgment; on large data even the most minute departure from normality becomes visible. And on a small sample you simply cannot lean on the central limit theorem.

The foundational library here is Matplotlib. I will say plainly that, popular as it is, Matplotlib is an old and awkward thing, and for comfortable work you are better off with an API built on top of it — Seaborn, say, which brings its own, frequently more elegant, ways of drawing. A single `sns.set()` is enough to feel the difference:

```python
import matplotlib
matplotlib.use('TkAgg')
import numpy as np

from matplotlib import pyplot as plt
import seaborn as sns; sns.set()
norm_data = np.random.normal(size = 1000, loc = 0, scale = 1)
plt.hist(norm_data)
plt.show()
```

![Histogram rendered by Matplotlib and by Seaborn](https://your-scorpion.ru/wp-content/uploads/2019/01/Frame-40.png)

*On the left, the histogram as Matplotlib draws it; on the right, the same in Seaborn.*

So we have our first Python chart. The temptation now is to leap straight from fiddling with lists to working with arrays of values — but first a short refresher on the theory of mathematical statistics.

The null hypothesis is always the conservative one (it is easier to test), and the alternative, in the normality setting, is that the distribution is not normal. Normality is the first thing you check, for a structural reason: for a given mean and standard deviation there is exactly one normal distribution, whereas the number of non-normal distributions is unbounded. You can test for it with the Shapiro–Wilk test, which examines the null hypothesis that the data were drawn from a normal distribution. There is also the notion of a mixture of distributions, and once you are in that territory you find yourself reaching for Markov chain Monte Carlo (MCMC).

Validating an A/B test is a special case of testing a statistical hypothesis. A statistical hypothesis is an assumption about the form of a distribution and the properties of a random variable — something you can confirm or refute. By default you set out to test H0, the null hypothesis. It holds until the contrary is shown; if the contrary *is* shown, the alternative H1 prevails.

The conventional notation:

| **Quantity**            | **Population** | **Sample** |
| ----------------------- | -------------- | ---------- |
| Variance                | σ²             | *s*²       |
| Sample size             | *N*            | *n*        |
| Correlation coefficient | *p*            | *r*        |

Population and sample — what is the difference? The sample is the set of data you happen to hold. The population is the totality of data that could, in principle, enter the calculation. The homely version: you have a cake, you cut a slice, you taste it. If the slice is good, you generalise from the sample (the slice) to the whole population (the cake).

![The population](https://your-scorpion.ru/wp-content/uploads/2020/04/Frame-40-2.png)

*The population*

Suppose we have a sample from a normal distribution with an unknown parameter α and a known parameter σ = 1. Our H0 is that α equals some specific value — and crucially the hypothesis is framed *before* the data, not derived from them. To test H0 we compute a quantity from the sample and compare it against the theoretical one. We allow for deviation from the theoretical value, because the arithmetic mean is never exactly equal to the expectation; it will always drift a little. The whole craft lies in deciding which deviation between the observed and theoretical values we are prepared to call admissible (not significant), and which cannot be written off to chance (significant). If the deviation is significant, H0 is rejected; if not, H0 stands.

A word on the correlation coefficient. Correlation is the interdependence of two or more random variables, and it comes in many shades — strong to varying degrees, or negative. The internet loves to set two utterly unrelated quantities side by side — the number of Zoom meetings in Chita against the number of knives sold in Alabama — and marvel at the resemblance. There is no causal link whatsoever; there can simply be a similarity in how the indicators move.

To get to work we need to:

- settle on H0 and H1;
- assume the null is true and define a statistic — a function of the sample — which we will call T. For a normally distributed random variable with unknown variance, the statistic we use is this:

![The t-statistic](https://your-scorpion.ru/wp-content/uploads/2020/03/Frame-37.png)

*X — the sample; α — the H0 value of the expectation; n — the sample size; X with a bar — the sample mean; σx — the unbiased estimate of the standard deviation.*

- Under the formula above the statistic follows Student's distribution (the t-distribution) with df = n − 1, where *n* is the sample size. If X in that formula is normally distributed, then the statistic is t-distributed — we take this as established. For the standard normal, σ = 1 and a = 0.
- If the expectation is known but the variance is not, and you want to test a hypothesis about the variance, you take quantiles of the chi-square distribution — a different distribution altogether. For most other distributions no such statistic exists. The sample mean of a uniform distribution, for example, is fairly awkward to reckon with, so we fall back on the CLT (as the number of objects in the sample tends to infinity, the distribution approaches normal). This is exactly what large companies exploit, since they sit on heaps of traffic. If traffic is scarce, you turn instead to optimization — and you can speed an A/B test up by managing its sensitivity: optimising variance, swapping the descriptive statistics, transforming the data, reducing its dimensionality (trim the tails and the variance shrinks).
- Next, the significance level α — the admissible probability of a Type I error (the famous 0.05), with the exact value varying by task.
- Then the critical region Ω, which you can picture as an interval telling you that the T from the steps above has landed inside it.
- Finally, run the test: compute T for the sample, and if it falls in Ω, conclude that the data contradict H0 and accept H1.

It is reasonable to balk at that 0.05. An error in five cases out of a hundred can still mean millions of dollars, or five dead patients — does everyone really lean on a five-percent error budget with nothing else behind it? In practice, no. A value below 0.05 leads us to reject H0 and call the difference statistically significant, but the p-value is heavily swayed by outliers, which is why it is so often estimated by bootstrap. The bootstrap can throw up a situation where the raw p-value sits well above 0.05, yet once you bootstrap it and read off its 50th percentile (the median), that median can come in below 0.05, and you reject the null after all. Experiments are validated this way all the time. Alternatively, adopt 0.01 rather than 0.05 as your rule — a stricter bar for "reject the main hypothesis." When you are designing critical infrastructure, a p-value below 10⁻⁵ is entirely plausible. The p-value, in the end, hangs on the number of observations, the size of the deviation, and your own subjective confidence: the more elements in the sample, the sharper the conclusion; the smaller the coefficient of variation, the sharper still.

The standard deviation is the square root of the variance — put another way, the mean of the squared differences between the values in a dataset and their mean. If the data are normal, mean and variance are all you need; to test hypotheses about the variance of a normal distribution you use quantiles of the chi-square distribution. Most other distributions, by and large, have no corresponding statistic, which is why the central limit theorem gets pressed into service with some margin of error. The charts above and below show what a normal distribution looks like once visualised.

![A box plot fused with the density of a normal distribution](https://your-scorpion.ru/wp-content/uploads/2019/01/Frame-39.png)

Mean, median, and mode are measures of the centre of a distribution. Its spread — the variability, the volatility of the sample — is captured by the variance. And this is where the standard deviation enters, the variance's offspring, telling you how far on average the sample's elements stray from the mean. If the average literate Chinese reader knows three thousand characters, the standard deviation is around four hundred. If your mean is a hundred applications and the standard deviation is three hundred, the data swing wildly — and that ratio has a name, the coefficient of variation: divide the standard deviation by the mean, read the result as a percentage, and you have a proportional measure of dispersion.

![Standard deviation around the mean](https://your-scorpion.ru/wp-content/uploads/2020/05/Group-70.png)

Variance is not a constant; it is something you can work on. There is, for one, the decile method. You take all the data from test and control, and you carve the distribution into some number of quartiles, deciles, percentiles. Each micro-bucket carries its own internal spread, and that is the lens through which you assess the experiment — the first decile holding the smallest values, the tenth the largest. The approach lowers variance by lowering the range within each slice. With multiple comparisons you have to apply corrections — the classic, conservative Bonferroni; the Holm method; Benjamini–Yekutieli; Benjamini–Hochberg — or else simply tighten the p-value.

And if you would rather not wrestle with the normal distribution at all, you reach for bootstrapping.

The central line in the figure is the median — sort the sample from smallest to largest and take the element in the middle. Beyond removing outliers, sizing the sample, and clustering users into groups, what everyone ultimately wants is a sound measure of central tendency, and that can be expressed three ways:

- **The arithmetic mean.** The idea is that any typical value drawn from the dataset should resemble the mean. It is not the most dependable measure — it is badly affected by extreme highs and lows. (2 + 4 + 6 + 26) / 4 = 9.5, the favourite trick of unscrupulous media.
- **The median.** Unlike the mean, every value is sorted in ascending order and the one that falls in the middle is taken as the centre. It is regarded as the sturdier choice because it is more robust — robustness being resistance to large and small values alike. With an even count, you average the two values at the centre of the sorted set.
- **The mode.** The value that turns up more often than any other. It is less a "central" value than the other two; it is more the heaviest weight pulling on the dataset's average.

![Mean, median, and mode](https://your-scorpion.ru/wp-content/uploads/2019/01/Frame-41.png)

Let us work an example. We choose a statistic; our hypothesis concerns the expectation of a normally distributed random variable with *known* variance, so we take a slightly different statistic from the one used above. If H0 is true, the statistic T follows the standard normal distribution.

![The statistic for a known variance](https://your-scorpion.ru/wp-content/uploads/2020/03/Frame-38.png)

Our null hypothesis is that a = 6. We then draw a sample within the experiment and set out to test it.

```python
import numpy as np
from scipy import stats
data = np.random.normal(10,1,size = 200)
data
```

On to checking statistical significance. Say we have pulled a month of transaction data and want to know whether there were any departures from normal behaviour. We set α = 0.05 and feed the numbers into the formula:

![The numbers substituted into the formula](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-291.png)

Now the critical region. Which region we choose depends on the alternative hypothesis — it may be two-sided, left-sided, or right-sided, according to the task. In our case the value could shift either way, left or right, so we choose a two-sided critical region. In choosing a two-sided test we have fixed the condition under which the hypothesis is judged: a two-sided test does not presume we know in advance which value will be larger and which smaller. It is the more conservative and more general choice. We simply invoke the two-sigma rule to obtain the interval, or consult a table of quantiles. Quantiles and quartiles are straightforward: line the values up in order — a hundred of them, say, 1, 2, 3 … 99, 100 — and the fifth quantile is 5. A quartile is just a special case of a quantile; the most common are the 25th, 50th, and 75th.

Finding a quantile is easy enough: the two-sigma rule gives the interval we fall inside with 95% probability, and outside with 5%, so the quantiles are 2 and −2 — that is, α/2 and 1 − α/2, the quantiles of the standard distribution. The 2 is precisely the quantile of order 1 − α/2 = 0.975. Now we can take a sample, compute the statistic T from it, and reject H0 if T lands in the critical region. To keep the sigmas fresh: three sigma is 99.73%, two sigma 95.45%, one sigma 68.27%.

Computing a quantile directly, incidentally, is a one-liner. Suppose a right-sided region running from the quantile out to infinity; take the function for the standard normal at 1 − α:

```python
from scipy import stats
t = stats.norm.ppf(1 - 0.025)
t
```

That returns a critical region from 1.959963984 to infinity.

We compute the expectation, `mean = data.mean()`, and get 9.9296. Then the statistic, `T = 10 * (mean - 6)`, which gives 39.2962. Our critical region runs from 2 to infinity (or from −2 to minus infinity), and the value has landed inside it. We reject the hypothesis. The thing to hold onto is that the smaller α is, the wider the critical region grows — which is the dial by which we govern the test's precision and our willingness to reject. As a quick touchstone for reading these numbers: given two t-tests returning p-values of 0.435 and 0.0169, the first leaves the equality-of-means hypothesis comfortably standing, while the second topples it.

Now a harder example:

```python
import matplotlib
matplotlib.use('TkAgg')
import scipy.stats as stats
from matplotlib import pyplot

x = stats.norm.rvs(loc=5, scale=3, size=543)
print (stats.shapiro(x))
pyplot.hist(x)
pyplot.show()
```

I deliberately avoid setting a `seed`, because the more your own results vary from run to run, the better. The call returns two numbers — the test statistic and its associated p-value — and in my run the p-value came out to 0.011658577248454094.

Here it is worth slowing down, because the interpretation is exactly where people trip, and the trap is in which direction the verdict points. Shapiro–Wilk's null hypothesis is that the sample was drawn from a normal distribution — H0: X ~ N(μ, σ). So a p-value of 0.0116, sitting below the 0.05 threshold, does not vindicate normality; it *rejects* it. The sample, despite having been generated by `norm.rvs`, fails the normality test at this size — a small, almost paradoxical illustration of how these tests behave once you push on them. (It is an easy slip to read a sub-α p-value as a clean bill of health, and worth correcting wherever it appears.)

The chi-square statistic answers a different question entirely — not whether a sample is normal but whether the observations in two groups are independent of one another — so the two tests should never be conflated, even when they happen to surface in the same analysis. And the logic of the verdict is asymmetric. If p ≤ α, the data do not match the normal distribution and we reject H0. Any sufficiently low p-value gives grounds to reject the null and to treat the test and control groups as different. But if p > α we do not reject H0 — and we are *not* then entitled to conclude that the data are non-normal, nor, frustratingly, that they are normal. We have simply failed to dislodge the null.

There is, as the joke goes, a nuance. Different criteria test different hypotheses — equality of medians, of means, of distributions, of variances — on one and the same metric. Shapiro–Wilk tests the null that the sample came from a normally distributed population; but how *materially* a departure from normality bears on your use of parametric methods is usually decided by eye, from the charts and the data. On a small sample that nonetheless differs sharply from normal, you can afford a p-value above 0.05; on a large sample even a slight difference will register as significant at p < 0.05. The p-value itself does not depend on α — but whether you accept or reject H0 depends on α very much indeed. The Kolmogorov–Smirnov test makes the point even more pointed: toy samples that are obviously non-normal to the naked eye will sail through it, while on enormous samples the normality hypothesis gets rejected even where, on a B2C product, the small departures from normality are no problem at all. You can spend a whole evening playing with `ks.test(rnorm(n, mean = 0, sd = 1), pnorm)` and watching your scepticism toward these criteria grow. The deeper truth is that the larger the sample, the less the departures from normality matter in practice — yet normality tests work the other way round: more data means more power, and more power means p < 0.05 more often.

Before drawing any conclusion about the *absence* of a difference, though, we still have to establish whether the statistical test we used had enough power to detect one. And power comes down to sample size. You cannot compare laws that are close to one another on small samples.

Which raises the question that comes up more than any other: how many users do you actually need for a statistically significant test? The simplest route — and not the most correct one — is to eyeball the minimum effect you expect, take the conventional constants for power and significance, and let a [web service](https://www.evanmiller.org/ab-testing/sample-size.html) hand you the sample size for a 50/50 split. Say the baseline conversion (c1) is 4% and the lift you are after is 8%. Drop those into the [calculator](https://www.evanmiller.org/ab-testing/sample-size.html), and while you are at it compute the new conversion directly: 0.04 × (1 + 0.08) = 4.3%.

Here is the equivalent in R:

```r
size <- power.prop.test(
 n = NULL,
 p1 = A,
 p2 = B,
 sig.level = alpha,
 power = (1-beta),
 alternative = "one-sided"
)
```

And with the numbers filled in:

```r
power.prop.test(p1=.04, p2=.08, sig.level=.05, power=.8, alternative = "two.sided")
```

Each of these approaches approximates the binomial distribution, so the sample size differs a little from one method to the next — and the calculator simply lowballs the figure you really need.

The fuller treatment goes like this. You start from your current conversion (before the test begins) and an expected lift (an expert guess, say, that conversion ought to jump 20%), together with a p-value, the probability of error. In practice people almost always take a 5% p-value and 80% power; the two can be traded against each other. The significance level is the threshold against which we accept or reject the hypothesis given the p-value in hand. For the critical values themselves, Za depends on the test:

- **p-value 5%** — two-sided 1.96, one-sided 1.65
- **p-value 1%** — two-sided 2.58, one-sided 2.33
- **p-value 0.1%** — two-sided 3.29

And Z₁−β depends on the power: 80% (Z₁−β = 0.84), 85% (1.04), 90% (1.28), 95% (1.64).

Here is one of the formulas I use in practice, assuming equal samples. First the variance, with a current conversion of 50% and an expected 50% + (20% / 2) = 60%:

> 0.5(1 − 0.5) + 0.6(1 − 0.6) = 0.25 + 0.24 = 0.49.

Then, taking a 95% confidence level, which gives Za = 1.96:

1. 0.49 / 0.01 = 49 × 2 = 98
2. (1.96 + 0.8)² = 7.6176
3. Sample size = 98 × 7.6176 = 746 (split between control and test)

![The sample-size formula](http://your-scorpion.ru/wp-content/uploads/2020/05/535353.png)

The formula carries a constant of 16 in the numerator and the deviation you want to detect in the denominator. The larger the value in the denominator, the larger the numerator has to be: put 1% in the denominator — that is 1/100, squared to 1/10,000 — and you need ten thousand users to confirm a one-percent improvement. The calculators are interchangeable enough that, if Evan Miller's does not suit you, there are plenty of others — [socioline](https://socioline.ru/rv.php), [easycalculation](https://www.easycalculation.com/statistics/statistical-significance.php), [abtestguide](https://abtestguide.com/calc/), [vwo](https://vwo.com/tools/ab-test-siginficance-calculator/), and the thoroughly entry-level [neilpatel](https://neilpatel.com/ab-testing-calculator/) — or you can go through the joint distribution.

A common variant of the sizing problem is the one where you only need to be sure that variant B is no *worse* than A — the classic cost-optimisation case, where conversion must not fall. There you look for the sample size at which you are confident conversion has not dropped by some set amount, and the genre has a name: non-inferiority A/B tests. The alternative is the purely scientific route, where the delta tends to zero and the sample size therefore tends to infinity — so you settle instead on a workable delta, making an assumption about how the width of the confidence interval scales with sample size and deciding what precision is acceptable. As for the calculators, the "Difference" parameter people often misread is simply the minimum detectable effect; power, meanwhile, is the chance of erring — of committing a Type II error.

Returning to the harder example, we swap the Shapiro–Wilk test for Anderson's, `print (stats.anderson(x))`, and check once more that the sample is more or less normally distributed. The Anderson–Darling test is often followed by the Cramér–von Mises w², and the result can be confirmed by eye as well:

![A visual normality check](https://your-scorpion.ru/wp-content/uploads/2019/01/Figure_1-2.png)

Readers invariably write in asking after some more exotic normality criterion whose name they have half-remembered, and the honest answer is that the catalogue is effectively bottomless — Shapiro–Wilk, Anderson–Darling, Cramér–von Mises, Kolmogorov–Smirnov, and on it goes. Which one you reach for matters far less than understanding what any of them is actually doing, and, as the Kolmogorov–Smirnov digression above should make plain, none of them substitutes for looking at the chart.

Now an F-test, because the time has come to test the significance of *differences*. Suppose we need to compare the performance of salespeople in two cities. For this we use ANOVA — analysis of variance — from SciPy, via `stats.f_oneway`. ANOVA's null hypothesis is that the expectations coincide. Where Student's t-test compares the mean across two independent or dependent groups, the F-test asks whether there is any difference at all, and it extends to more than two samples. ANOVA is not, strictly, an F-test in full: it is a regression model, counted among the generalised linear models (GLM), and it serves to compare the mean of some attribute across independent groups.

```python
import matplotlib
matplotlib.use('TkAgg')
import scipy.stats as stats

a = [2,3,1,4,3,4,2,4,-1,32,12,53,2,2,3,2.3,2,4.2,3,32,1]
b = [3,4,-1,3,4,43,4,14,2.3,1,3,2.3,12,42,2.4,3,4,1,4,1,2]
print (stats.f_oneway(a,b))
```

We get `F_onewayResult(statistic=0.0386380063725391, pvalue=0.8451628704190369)`, which tells us whether the between-group variance exceeds the within-group variance, computing the probability of observing that variance ratio under the F-distribution. It is, of course, nowhere near enough for a scientific write-up — there are no degrees of freedom reported — but we have the coveted p-value, and since 0.845 sits well above 0.05 we have no grounds to reject the null that a = b on this data. And since the data plainly do not look normal, the F-test was not quite the right instrument here in any case; the honest reading is that the salespeople's performance clearly hinges on more than the numbers we happen to hold. Our null was a = b, the alternative a ≠ b. In truth, if you have no appetite for memorising a wardrobe of criteria, it is enough to master ANOVA and the bootstrap, since it all collapses into the general linear models.

Let us compare the means of two samples with a t-test. For that we need the sample mean, its size, and its deviation. We want to know whether two groups of data differ — let them be the results of an A/B test on tutorials in a mobile app. The procedure is to interpret the statistic in a two-sided test under an approximately normal distribution, which means the null may be rejected when the two sample means diverge too far. In R we used `t.test()` for a plain Student's t-test; in Python we take the more involved road. You can run either a one-sided or a two-sided t-test in Python. If your data are noisy, do not forget to handle the variance. Student's test for independent samples assumes equal variances. Frankly, the t-test is the most conservative of the lot: it wants full homogeneity of the samples (50/50) and strict normality, and it is no use at all for binary or continuous-metric tests.

It is fair to ask what the t-test is actually made of — what principle it rests on, and why you should trust its verdict. Take, again by example, a pair of dependent samples: A/B data from before and after a landing-page change. We take Student's t, we find the number of degrees of freedom df, and we take the critical value of Student's t (at 0.05) for that df as the basis for our decision. The null hypothesis is that the two sample means are equal; to reject it, the t-statistic must meet or exceed the critical value for the relevant df.

First we check the data for normality:

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd
import numpy as np

x = np.array([5, 15, 25, 35, 45, 55])
y = np.array([5, 20, 14, 32, 22, 38])
data = {'manager_data': x, 'with_video': y}
host_df = pd.DataFrame(data)

print (host_df[['manager_data', 'with_video']].describe())
```

The data can be treated as normal, since the mean and the 50% (median) sit very close together. Now we look at the difference of each pair of values, row by row:

```python
diff = host_df['manager_data'] - host_df['with_video']
print (diff)
```

We compute the mean of the differences:

```python
mean_diff = sum(diff) / len(host_df)
print (mean_diff)
```

The standard deviation of the differences from the mean:

```python
std_diff = diff.std()
print (std_diff)
```

And, at last, Student's t-statistic:

```python
tstud = mean_diff / (std_diff / np.sqrt(len(host_df)))
print (tstud)
```

We get a Student's t of 1.83 which, at df = 6 − 1 = 5 and a significance level of 0.05, we compare against the table value of 2.57. The 1.83 falls below the critical 2.57, so we accept the null hypothesis: there is no statistically significant difference before and after the test. And we verify the arithmetic:

```python
print (stats.ttest_rel(x,y))
```

If it is the bare theory you are after, suppose we want to compare two figures for respondents' answer times and decide whether they differ — exactly a case for the t-test. Our datasets are Resp A: {10.2, 8.9, 12.5, 9.8, 11.3} and Resp B: {12.7, 11.8, 13.2, 10.5, 12.1}. First we frame the hypotheses. The null (H0): there is no meaningful difference in the mean response time between Resp A and Resp B. The alternative (Ha): there is a meaningful difference. We take statistical significance at 0.05 (5%).

Then, step by step. The mean for Resp A: mA = (10.2 + 8.9 + 12.5 + 9.8 + 11.3) / 5 = 10.54. The mean for Resp B: mB = (12.7 + 11.8 + 13.2 + 10.5 + 12.1) / 5 = 12.06. So mA − mB = 10.54 − 12.06 = −1.520. The standard deviations: sA = 1.394, sB = 1.026. The sample sizes: nA = 5, nB = 5. The formula:

![Two-sample t-statistic](https://your-scorpion.ru/wp-content/uploads/2023/11/ова.png)

gives t = (10.54 − 12.06) / √((1.394² / 5) + (1.026² / 5)) = −1.9636. Next we determine the t and p values. We look up a table of critical values for Student's t. Here the number of degrees of freedom equals the sample size minus the number of parameters we had to estimate during the analysis — that is, nA + nB − 2 = 8. We agreed on a significance level of 0.05, and with df = 8 the t-value comes to roughly 2.306, with a two-sample p of 0.0852. If the computed t falls in the rejection region, or the p-value is below the significance level, we reject the null. Our computed t (−1.9636) does not exceed the critical value (2.306), and the p-value of 0.0852 is greater than 0.05. Which means the difference between the two means is not statistically significant: there is no difference between the values from Resp A and Resp B.

A two-sided test yields a p-value twice that of a one-sided one, which is why it sets a stricter bar for rejecting the null. You can, in principle, recover a one-sided p-value from a two-sided one, but a properly designed test should never make that necessary. In the two-sided case we split the 0.05 in half, dealing 0.025 to the positive tail and 0.025 to the negative; in the one-sided case the whole 0.05 sits in a single tail. Because the other tail is ignored, there is a risk of error: having built a new tutorial, you could test one-sidedly whether it beats the old one — but any information about whether it is *worse* than the old one is thrown away. If, on the other hand, the new tutorial is aimed at a different audience and you are certain it cannot be worse, then the one-sided test fits and buys you greater precision.

That distinction surfaces a question whenever people first set up a two-sided test: do you halve only the significance level, or the minimum detectable effect along with it — so that a relative change of 11% goes into the calculator as 5.5%? For two-sided t-tests you size the sample on an arbitrary quantity called the minimum detectable effect (MDE) and, as everywhere in statistics, it depends on a great many things. Officially, you do not halve it. An example: H0 that the mean = 20, H1 that the mean ≠ 20 (which is what we want to prove), rejecting when Z ≤ −Z₂.₅ and Z ≥ Z₂.₅ (the 5% split in two). If the sample mean is 22 with a standard deviation of 2, then Z = (22 − 20) / (2 / √100) = 10, which lies well outside the −1.96 to 1.96 band — and the division happened only once. In the calculators, the "difference" parameter is what carries the power, and for power you take the one-sided probability.

The intuition behind the MDE is that the larger the sample, the smaller its variance, and so the smaller the effect you can detect; the parameter is therefore something you can move around fairly freely. Conversely, on a small sample you can only catch a comparatively large effect, all else equal. This is precisely what makes the two-sided test so serviceable when you lack a deep grasp of statistics — which, candidly, almost nobody has. I always compute two-sided probabilities, with the sole exception of strictly positive values; the power is lower, but the CLT works beautifully and the statistic comes out Student-distributed. You can test one-sided hypotheses too, halving the significance level, which behaves as a kind of analogue of two-sided testing.

![A normal distribution with 2.5% in each tail marking rejection of the null](http://your-scorpion.ru/wp-content/uploads/2020/09/Group-1.png)

Look at the figure. The lower line is the probability — the area under the standard normal that lies either below −2.40 or above +2.40. The probability works out to 0.009 + 0.009 = 0.018, that is, 0.009 in each of the lower (left) and upper (right) tails. On that basis we could conclude, for instance, that under-ripe watermelons carry a lower concentration of vitamin A than the general population. Any frequentist analysis comes down to four interlocking quantities — α the significance level, β the power, n the sample size, and the MDE the effect — all of them reckoned in advance, before the experiment, and patched up with corrections afterwards. One-sided and two-sided criteria differ only in the power, α or α/2; and power, the chance of committing a Type II error, resembles the significance level but is bound far more tightly to the size of the effect. So the practical answer runs: if power is low, apply the corrections — it will not inflate the Type II error rate, and the result is something you can steer by. And it is always worth making the effect size larger, because a small effect may not repay the work it costs.

The inputs for the next example: a null hypothesis that the expectations for the two groups are equal, with equal variances. The data represent the financial results of two different online stores in a similar line of business.

```python
import matplotlib
matplotlib.use('TkAgg')
from scipy import stats

a = [742,148,423,424,122,432,-1,232,243,332,213]
b = [-1,3,4,2,1,3,2,4,1,2]
print (stats.ttest_ind(a,b))
```

The result runs into the Behrens–Fisher problem — no exact solution exists — but the probability still lets us draw a conclusion. Once you allow for the fact that a small, ungrouped sample carries a large variance (check it with `print(np.var(a))`), the quality of the data becomes questionable. If the data are not normally distributed, you need the Mann–Whitney test, also known as the Wilcoxon test. Our sample is small, so Mann–Whitney suits it well — the ranks will not overlap much. In running it we convert the data to ranks, build two distributions of those ranks, and try to find the difference between the 50th quartiles of the two rank distributions.

Mann–Whitney's general assumption is that all observations from both groups are independent and continuous. There is a wrinkle on continuity: a little repetition of values within the samples is permitted. H0 states that if we draw one random element from each of the two distributions, the element from the first has a probability ≠ 1/2 of exceeding the element from the second. To begin, we define a U-statistic whose distribution we know on the assumption that the null is true:

![The U-statistic](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32957.png)

*Tx — the largest of the rank sums, corresponding to the sample of size nx.*

Take two samples, 1, 3, 4, 6 and 2, 7, 8. We form a single series from smallest to largest — 1, 2, 3, 4, 6, 7, 8 — and assign ranks, the smaller the value the smaller the rank, splitting the series out like so:

| **Value in the series** | **Rank** |
| ----------------------- | -------- |
| 1                       | 1        |
| 2                       | 2        |
| 3                       | 3        |
| 4                       | 4        |
| 6                       | 5        |
| 7                       | 6        |
| 8                       | 7        |

We sum the ranks: T1 = 1 + 3 + 4 + 5 and T2 = 2 + 6 + 7. The larger sum, Tx, is 15 (T2). Nx — the number of elements in the group holding the largest rank — is 3. Then we put the values into the formula and compute the statistic: U = 4 × 3 + ((2 × (2 + 1)) / 2) − 15 = 12 + 3 − 15 = 0. If the sample is small, you open a reference and read off the critical values.

```python
import matplotlib
matplotlib.use('TkAgg')
from scipy import stats

a = [742, 148, 423, 424, 122, 432, -1, 232, 243, 332, 213]
b = [-1, 3, 4, 2, 1, 3, 2, 4, 1, 2]

u, p_value = stats.mannwhitneyu(a, b)
print("two-sample wilcoxon-test", p_value)
```

The p-value came out to 0.0007438622219910575. Samples can also be compared by eye:

```python
import matplotlib
matplotlib.use('TkAgg')
import numpy as np
from scipy.stats import ttest_ind
import matplotlib.pyplot as plt
a = np.random.normal(loc=0,scale=24,size=4454)
b = np.random.normal(loc=-1,scale=1,size=7643)

print(ttest_ind(a,b))

plt.hist(a, bins=24, color='g', alpha=0.75)
plt.hist(b, bins=24, color='y', alpha=0.55)
plt.show()
```

![Two overlaid sample histograms](https://your-scorpion.ru/wp-content/uploads/2019/01/Figure_1-3-2.png)

*We get statistic = 4.3337700320925885, pvalue = 1.4776127369400805e-05 — far below 0.05, so we reject Student's null of equal means. The two stores have data that are simply too different; segmentation is needed.*

Now consider a two-sided test for the null hypothesis that the expected mean of one sample of independent observations equals the mean of another. The "two-sided" aspect means we take account of both the upper and the lower bounds of the population's distribution. The standard deviations have to be equal.

```python
import matplotlib
matplotlib.use('TkAgg')
from scipy import stats

from scipy import stats
a = stats.norm.rvs(loc = 5,scale = 10,size = 23000)
b = stats.norm.rvs(loc = 5,scale = 10,size = 23425)
print stats.ttest_ind(a,b)
```

We get statistic = −0.7043486133916781, pvalue = 0.4812192321148787, far above 0.05. And since we treat p > 0.05 as the unlikely error and p ≤ 0.05 as the likely one, the two samples can be considered equal. For testing equality of means, the Mann–Whitney, Wilcoxon, and Kruskal–Wallis tests will serve just as well.

With the individual tests in hand, it is worth walking one A/B test end to end, the way it actually unfolds. We take [a dataset](https://drive.google.com/open?id=1Ov6-ogMXQ-CrT4nVgAPZ8KOEaqEAGleU) of conversion figures from before a designer joined the landing-page work and after — a paired sample. At first glance the instrument is a paired t-test, and it is enough to look at the difference. But the very first move is an outlier check by box plot:

![Box plot of the paired conversion data](http://your-scorpion.ru/wp-content/uploads/2019/02/Figure_1-4.png)

The box plot shows no substantial outliers. The next step is the distribution. For two dependent samples you have to test normality on the *difference* between the two sets, which you can do with a histogram, a Q–Q plot, or a statistical test.

![Histogram of the differences](http://your-scorpion.ru/wp-content/uploads/2019/02/Figure_1-5.png)

The distribution looks normal. For assurance we read off the Shapiro–Wilk result, `print (stats.shapiro(df['final_design']))`, and get W = 0.9910880327224731 with a p-value of 0.1491156369447708 — normal after all, the result being non-significant. Had the distribution been non-normal, we would have switched to the Wilcoxon test.

Then to the paired t-test itself:

```python
print (stats.ttest_rel(df['Manager_design'], df['Designers_design']))
```

Its result is statistic = −8.062787757694716, p-value = 3.5364610751401186e-14. The figures are statistically significant; we can reject the null in favour of the alternative. The designer's intervention in the landing-page work had an effect — in this case a positive one. Strict normality is usually unnecessary, and the normality tests are themselves fairly strict; and there are, it bears repeating, many different t-tests.

Pushed a little further, the harder version of this is comparing many cohorts at once. There you want ANOVA — a close cousin of the t-test, again sharing the GLM backbone — to check the data as a whole and to hunt for anomalous activity. To trust the result you need homogeneity of variance, normality of the data, an equal number of observations in each group, and independence of observations. If the group sizes differ substantially, you move to the Kruskal–Wallis H-test or Welch's ANOVA. Take [this data](https://drive.google.com/open?id=1iJk0Euej0DDD3e-1nd9oG9co-aRxnOK7): we want to compare the means of three-or-more samples via the F-distribution, using `stats.f_oneway()`.

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd
import scipy.stats as stats

df = pd.read_csv("/Downloads/testData2.csv")
iOS = df.iOS.dropna()
Android = df.Android.dropna()
Windows = df.Windows.dropna()
print (stats.f_oneway(iOS, Android, Windows))
```

We get `F_onewayResult(statistic=7.3488433965696975, pvalue=0.000674137267723654)`. As an alternative you could use the Fligner–Killeen test, which is better still. With p < 0.05 we can reject the null. The null (H0) is that there is no difference between the groups; the alternative (H1) is that there is. There is a difference, and the next task is to determine which groups differ from which. But before the test you really ought to have worked the data — the charts show outliers:

```python
sns.distplot(iOS, rug=True, hist=True)
sns.distplot(Android, rug=True, hist=True)
sns.distplot(Windows, rug=True, hist=True)
plt.show()

df2 = df.drop('user', 1)
plt.hist(df2, edgecolor='k', alpha=0.6)
plt.show()

plt.plot(df2)
plt.show()
```

![Distribution plots revealing outliers across the three groups](http://your-scorpion.ru/wp-content/uploads/2019/02/Frame-2.png)

If the data turn out non-normal, look toward Dunn's test or Kruskal–Wallis — I name them together because the Kruskal–Wallis criterion is identically equal to Mann–Whitney when you have only two groups to compare.

Before any of that, though, the imported table itself deserves a look. You find the number of rows with `print (len(objects))`, the number of columns with `print (objects.shape)`, and the data types with `print (objects.dtypes)`. Then come the descriptive statistics, `print (objects.describe(include="all"))`, which give you a description across the ordinal scale (data for comparison), the quantitative scale (numbers), and the nominal scale (numbers not meant for comparison).

![The output of describe()](http://your-scorpion.ru/wp-content/uploads/2019/03/Screen-Shot-2019-03-07-at-9.28.51-AM.png)

There, std is the standard deviation (the root of the variance), mean is the mean, the 50% (median) is a quantile, and the 25% and 50% are quartiles — a quantile being the fraction of cases below a given value. You scan it and look for anomalies. You also want to see the density of the data, and a histogram serves, here on the Windows column:

```python
matplotlib.style.use('ggplot')
objects['Windows'].hist();
plt.show()
```

![Histogram of the Windows column showing right-side outliers](http://your-scorpion.ru/wp-content/uploads/2019/03/Figure_1-5.png)

There are outliers on the right — and that is critical, as is the bell shape (since we have more than 150 observations). Departures from symmetry, by contrast, are not critical.

Not every metric is continuous, either. Picture a kind of card sort run on twenty admins: you give them a list of menu items and ask them to pick the ones they find clear and worth keeping in the menu. The output is binary, 1 or 0, with a binomial distribution, so what you are really measuring is agreement. I would begin with the Stuart–Maxwell test, then Cochran's Q, then the marginal-homogeneity test — but only on the condition that the samples are related (a repeated measurement). Certainly not Student's t. Cochran's Q has a null that the proportions of "successful" outcomes are equal across all groups, against an alternative that at least one group differs in its proportion; the null is rejected when the computed critical value Q exceeds the chi-square critical value, and on rejection you run pairwise Cochran's Q follow-up tests to locate the differences.

Depending on the data, you can also turn to Pearson's chi-square. A simple example: toss a coin 100 times and get 45 heads and 55 tails. The expected 50/50 is the null. Chi-square accepts or rejects by degrees of freedom (df), which you can read from ready-made tables — two sides to a coin, so 2 − 1 = 1 degree of freedom. Take 0.05 (95%) and the table gives 3.841; to reject the null you need a value above it. The arithmetic:

> (55 − 50)² / 50 = 25 / 50
> (45 − 50)² / 50 = 25 / 50
> 50 / 50 = 1

One degree of freedom, and the value 1 < 3.841, so we accept the null — or fail to reject it, whichever reading you prefer. Now let us try to reject it, with 100 tosses giving 34 heads and 66 tails:

> (34 − 50)² / 50 = 256 / 50
> (66 − 50)² / 50 = 256 / 50
> 512 / 50 = 10.24

Here 10.24 > 3.841, so we reject H0: the data are statistically significant. The natural extension is whether this carries over to values in the range 0 to 1 — whether a Cochran's Q result can tell you, with sufficient significance, that the variable in the population is distributed like the one in your sample. If the population size is known, you compare your sample against the available sample from the population, which is chi-square or Fisher's exact test, Fisher being good with small, unbalanced samples.

## Visualization

It matters that data be visualised — and not only because the people commissioning your work are visual creatures; there are more technical reasons too. Anscombe's quartet, for one. It is four sets of points, each with an X and a Y, numerically identical across the four. Visualise them, though, and you see that while the samples are equal by their summary figures, they are nothing alike in shape on the chart. Every dataset below shares the parameters mean = 7.50, std = 1.94, r = 0.82.

![Anscombe's quartet](https://your-scorpion.ru/wp-content/uploads/2020/05/download-2.png)

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np

sns.set()
data = np.random.multivariate_normal([0, 0], [[43, 2], [5, 1.2]],size=25000)
data = pd.DataFrame(data, columns=['x', 'y'])

for col in 'xy':
 sns.kdeplot(data[col], bw=.2, shade=False, label="MAU"),
plt.show()
```

![A kernel density estimate of two samples](https://your-scorpion.ru/wp-content/uploads/2019/01/Figure_1-2-1.png)

The chart above is splendid for comparing two samples by eye. The confidence interval lies between the 5th and 95th percentiles; the 90% confidence interval is the two-sided criterion running between the 5th and the 95th.

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np

plt.style.use('classic')
plt.style.use('seaborn-whitegrid')
data = np.random.multivariate_normal([0, 0], [[3.4, 1.87], [2.8, 1.44]],size=25000)
data = pd.DataFrame(data, columns=['x', 'y'])
sns.distplot(data['x'])
sns.distplot(data['y']);

plt.show()
```

![Two overlaid distribution plots](https://your-scorpion.ru/wp-content/uploads/2019/01/у-2.png)

The charts above are standard enough — you have built or at least seen them many times over. The next one, the jointplot, is rather more interesting. It combines the histograms of x and y and folds in a conventional scatter plot, giving you a sort of cube of histograms.

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np

plt.style.use('classic')
plt.style.use('seaborn-whitegrid')
data = np.random.multivariate_normal([0, 0], [[43, 2], [5, 1.2]],size=25000)
data = pd.DataFrame(data, columns=['x', 'y'])
with sns.axes_style('white'):
 sns.jointplot("x", "y", data, kind='kde');

plt.show()
```

![A jointplot](https://your-scorpion.ru/wp-content/uploads/2019/01/Figure_1-2-2.png)

There it is. Next, a scatter plot.

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
import numpy as np
a = np.random.rand(20)
b = [3, 4, 3.4, 6, 7, 8, 9, 10, 4, 0.3, 4.2, 4, 23, 3, 33, 3, 1, 4, 0.1, 4.2]
colors = np.random.rand(20)
plt.scatter(a, b, c=colors, s=100, alpha=0.65, marker=(5, 0))
plt.show()
```

![A scatter plot](https://your-scorpion.ru/wp-content/uploads/2019/01/Figure_1-3.png)

The scatter plot is interesting because the patterns of dispersion let you read off the different kinds of correlation. Reaching toward the upper-right corner is a good trend; lying flat and horizontal is a neutral one; reaching toward the upper-left corner is a negative one.

And the box plot, which there is no doing without. It works with a group of at least five numbers — minimum, first quartile, median, third quartile, maximum — with whiskers running from each quartile out to the minimum or maximum. You can always get the full reference on its parameters by typing `? sns.boxplot`.

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

b = [1, 2, 3, 4, 3.4, 6, 7, 8, 9, 8, 4, 0.3, 4.2, 14, 21, 1, -8]
df = pd.DataFrame(b)
sns.boxplot(data=df)
plt.show()
```

![A box plot](https://your-scorpion.ru/wp-content/uploads/2019/01/Figure_1-3-1.png)

That box-plot-over-density figure near the top of this piece is worth reconstructing, since the question of how it was built comes up often. I am not sure I made it entirely in Python — that I cannot reproduce it quickly off the top of my head suggests I leaned on other tools as well, and the slightly crooked left quantile rather confirms it. What comes to mind, in the pre-New-Year spirit:

```python
import numpy as np
import scipy
import pandas as pd
from scipy.stats import norm
import matplotlib.pyplot as plt

mu = 0
sigma = 1

n_bins = 54
x = np.random.normal(size=1000)
fig, axes = plt.subplots(nrows=2, ncols=1, sharex=False)
median, q1, q3 = np.percentile(x, 50), np.percentile(x, 25), np.percentile(x, 75)

n, bins, patches = axes[1].hist(x, n_bins, normed=True, alpha=.1, edgecolor='black')
pdf = 1/(sigma*np.sqrt(2*np.pi))*np.exp(-(bins-mu)**2/(2*sigma**2))
bins_1 = bins[(bins >= q1-1.5*(q3-q1)) & (bins <= q1)]
bins_2 = bins[(bins <= q3+1.5*(q3-q1)) & (bins >= q3)]
pdf_1 = pdf[:int(len(pdf)/2)]
pdf_2 = pdf[int(len(pdf)/2):]
pdf_1 = pdf_1[(pdf_1 >= norm(mu,sigma).pdf(q1-1.5*(q3-q1))) & (pdf_1 <= norm(mu,sigma).pdf(q1))]
pdf_2 = pdf_2[(pdf_2 >= norm(mu,sigma).pdf(q3+1.5*(q3-q1))) & (pdf_2 <= norm(mu,sigma).pdf(q3))]

# probability density function
axes[1].plot(bins, pdf, color='blue', alpha=.6)

axes[1].fill_between(bins_1, pdf_1, 0, alpha=.36, color='orange')
axes[1].fill_between(bins_2, pdf_2, 0, alpha=.36, color='green')
axes[1].set_ylabel('Probability Density')

# box and whiskers
axes[0].boxplot(x, 0, 'gD', vert=False)
axes[0].axvline(median, color='orange', alpha=.6, linewidth=.5)
axes[0].axis('off')

plt.show();
```

![Reconstructed box-over-density figure, first approach](https://your-scorpion.ru/wp-content/uploads/2019/12/Screen-Shot-2019-12-23-at-5.25.20-PM.png)

Or, not to labour it:

```python
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np
from scipy.stats import norm

x = np.random.normal(size=1000)

f, (ax_box, ax_hist) = plt.subplots(2, sharex=True, gridspec_kw={"height_ratios": (.15, .85)})

sns.boxplot(x, ax=ax_box)
sns.distplot(x, ax=ax_hist, hist = False)
print (np.percentile(x, [2.5, 97.5]) * 2*1000*1000)
```

![Reconstructed box-over-density figure, second approach](https://your-scorpion.ru/wp-content/uploads/2019/12/Screen-Shot-2019-12-23-at-5.25.14-PM.png)

If I were writing this today, I would use a box-and-whisker plot and/or a [distplot](https://plot.ly/python/distplot/).

Let us look at the quartiles in one more example.

```python
import seaborn as sns
sns.boxplot(data["x"])
data["x"].describe()
```

```
count    23.000000
 mean      0.112399
 std       0.071212
 min       0.007407
 25%       0.055556
 50%       0.103704
 75%       0.166667
 max       0.244444
 Name: x, dtype: float64
```

![A box plot with its describe() summary](https://your-scorpion.ru/wp-content/uploads/2020/01/sbr3_depth-2.png)

The median is the 50th percentile, the line at the centre. The data here are a dump of hits from user behaviour, so the 50th percentile is 0.10 clicks. The left edge of the large box is the 25th percentile, the value of the first quartile; the right is the third quartile, the 75th percentile. The vertical end lines mark the statistically significant sample.

If it is the shape of the distribution you are chasing, though, the box plot only carries you part of the way — the violin plot is the better instrument, making it plainer where the data cluster, around the median or out near the maximum and minimum.

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt

rs = np.random.RandomState(42)
data = rs.randn(1000, 3) * [2, 0.564, 1.5] + [0, 1, -1]
data[:, :32] * -5 + 10
df = pd.DataFrame(data)
print(df)

sns.violinplot(x='variable', y='value', data=pd.melt(df), scatter_kws={"s": 100}, bw=0.3, kind="violin", palette = "Blues")
plt.violinplot(df.T)
plt.xlabel('Day')
plt.ylabel('CPC')
plt.show()
```

Classically it carries a thick black bar at the centre, marking the interquartile range, which thins into a fine black line tracing the 95% confidence interval, with a white dot at the centre for the median.

![A violin plot](http://your-scorpion.ru/wp-content/uploads/2019/06/Figure_1-5.png)

The 92nd percentile sits off the left edge of the middle of the normal-distribution chart. A percentile is the percentage that falls below a threshold value, and you can get the probability in Excel as well: `=NORM.S.INV(0.92)` returns 1.40507156. The left side of the chart works the same way — `=NORM.S.INV(0.08)` gives −1.4050716.

And now a little 3D, which business is fond of:

```python
import matplotlib
matplotlib.use('TkAgg')
import matplotlib.pyplot as plt
import numpy as np
from matplotlib import cm
from mpl_toolkits.mplot3d import Axes3D

x = np.arange(-2, 5, 0.85)
xlen = len(x)
y = np.arange(-5, 2, 0.25)
ylen = len(y)
x, y = np.meshgrid(x, y)
r = np.sqrt(x**2 + y**2)
z = np.sin(r * 1.3)

ax = plt.figure(figsize=(8,6))
ax = ax.add_subplot(1,1,1, projection='3d')
ax.plot_surface(x, y, z, cmap=cm.coolwarm, edgecolor='black', linewidth=0.23, antialiased=True)

plt.show()
```

![A 3D surface plot](https://your-scorpion.ru/wp-content/uploads/2019/01/ezgif-5-c5643cc01074.gif)

## A last word on grouping users

A question I am asked almost as often as the t-test one: when you have a mass of observations spread across many different groups, on what principle are they sorted into cohorts? It is worth separating two ideas that are easy to conflate. Cohort analysis is less about a single verdict and more about the dynamics of change inside a product — whether a business model holds up over time, and how one slice of users compares against another. The natural comparison is between cohorts: the lifetime value of customers who arrived with a promo code against those who did not, for instance.

Suppose the observations themselves look broadly similar and the question is how they clump together. This is where hierarchical cluster analysis earns its place — it does precisely the work of breaking objects into groups. A cluster is nothing more exotic than a clump of objects that sit close together in their properties. How close is "close" is decided by the distance metric, and the two you reach for most often are Euclidean and block (Manhattan). The difference between them is more consequential than it first appears: Euclidean distance squares the differences before summing them, which turns a large gap into a gigantic one and lets outliers dominate the geometry. Block distance does no such squaring, so large differences stay proportionate and weigh less heavily on the result.

For seeing the structure, a dendrogram is the right picture. It shows exactly where the jump in inter-cluster distance occurs — the height at which the algorithm decided that two groups were too far apart to keep merging. I take normalised data as a table, import it into Python, and read the tree: each colour marks a distinct cluster, and the blue links are the joins the algorithm declined to make, the points where it stopped fusing things into ever-larger clusters.

```python
import matplotlib
matplotlib.use('TkAgg')
import pandas as pd
import matplotlib.pyplot as plt
import scipy.cluster.hierarchy as sch

objects = pd.read_csv('/Downloads/data3.csv', delimiter=',', index_col=False)
print (objects.head())

print (objects.shape)
dendrogram = sch.dendrogram(sch.linkage(objects, method='ward'))
plt.show()
```

![A dendrogram from hierarchical clustering](http://your-scorpion.ru/wp-content/uploads/2019/03/Figure_1-5-1.png)

Step back to a more abstract level and the discipline is always the same. You form the cohorts, you choose the metric you will track them by, and you fix the time split. The split might be the day, week, or month of first purchase; it might be the promo code used, the acquisition channel, or whether the customer holds a subscription. The metric might be GMV — the gross value of money that moved through — or Profit, or ARPU; it might be the average cheque or ARPPU, retention, or frequency of action per unit of time. And, just as importantly, you decide the granularity of time at which you will watch that metric move. None of this is statistical machinery for its own sake; it is the scaffolding that makes the tests above mean something about a living product rather than a frozen sample.

## Closing thought

For attractive, publication-ready statistical charts, Seaborn remains the best tool in the Python ecosystem — it gives you sophistication for almost no ceremony. If your goal instead is interactive charts that live on the web, then Bokeh, Pygal, and Plotly are where you should be looking. Learn Python and mathematical statistics together, and watch how much your product design grows as a result.
