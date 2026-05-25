# Evaluating A/B Test Results

A/B testing is the primary mechanism for resolving interface disputes within a team. Yet those disputes are often resolved incorrectly, because the most common mistake in analysing A/B results is comparing two means without selecting an appropriate test or evaluating the sample size. A quick visual scan of Google Analytics reports — "whichever line is higher wins" — leads to wrong conclusions that cost a business real money. If up to now you have been relying on the knowledge that an online calculator should show `p < 0.05` and that "a normal distribution looks like a bell", this chapter aims to widen that picture.

All examples are demonstrated in R Studio. The general analysis pipeline: experimental design → raw data → processed data → statistical model selection → summary statistics → p-value. Designers typically handle experimental design well; the remaining steps are what this chapter explores.

---

## Forming a Hypothesis

When framing a hypothesis, it helps to anchor on simple concepts: business objectives, how customers achieve those objectives, and how that can be measured (single A/B test or multi-variant). Equally important is understanding how data will be collected and validated, and how changes will be implemented once results are in. The chapter assumes that the representativeness and sample-size problem has been solved — that is, the standard deviation (the square root of variance) is treated as independent of sample size because the sample is representative.

In practice, the brief is rarely that clean. More often it arrives as: "Revenue dropped, go figure out why." Or a product manager shows up, describes a feature already live in production, and asks you to assess its effectiveness — without specifying what it was meant to improve, which data to use, or how to operationalise success. These loosely defined tasks require you to invent your own hypotheses and figure out where to get the data that supports them. Consolidating data from multiple sources helps, but do not let that turn into hallucinations. Every company should have a hypothesis map and a formulated strategy for reaching its goals.

Other common sources of hypotheses include: a disagreement between product and project managers, a corridor survey, a desire to optimise CRO. It is not worth testing features whose conversion lift is obvious — that is largely pointless work. Better to test features whose business payoff is unpredictable. Those tend to drive growth across all key metrics: CTR, conversion, CPA, ROAS, CPI. Worth noting: with a small dataset it is very hard to evaluate non-binary metrics (average order value, revenue) because results are extremely noisy — garbage in, garbage out. Binary A/B tests (done / not done) are much simpler to run. Either way, the very first step is to agree on the target metric.

Next comes the mathematical framework. For a frequentist approach (rather than Bayesian), pick a family of criteria. If the distribution is normal: parametric criteria — Student's t-test or ANOVA for comparing means; F-test, Bartlett, or Levene for equality of variances; a proportion test for binomial metrics; and formal distribution-fit tests. These compute their statistic from knowledge of the original distribution function, so you must supply certain parameters. If you are comparing means with limited data, use bootstrap (e.g., in-app spending). Last resort: nonparametric tests, which only require information from the sample itself. Sub-families include tests of randomness, symmetry, correlation, shift, and scale. They are less powerful but universally applicable — Mann-Whitney and Kruskal-Wallis for shift, Friedman and Wilcoxon signed-rank for dependent samples, bootstrap for quantile comparison, and Pearson's chi-square for changes in distribution shape.

A useful practice for real-world left-skewed data: run a nonparametric test on both samples, then run a t-test on their logarithms and compare the two conclusions — a double-check on the result.

A good hypothesis respects the principles of induction and falsifiability. Induction is the negation of a hypothesis — the ability to formulate a negative judgement: "Were the ancient Greek gods real? No, they were not." Falsifiability is about attempting to disprove a hypothesis; if every attempt fails, the hypothesis can be accepted. Prove there were no ancient Greek gods — until you can, we treat them as real.

When testing a hypothesis: remember that the null hypothesis asserts the absence of a difference and requires the full population to prove it — a sample is not enough. The alternative hypothesis asserts the significance of a difference. All differences observed in samples are always the alternative hypothesis; it is the null that is being tested. A type I error (α, false positive) occurs when we reject the null hypothesis in favour of the alternative, even though the null is true. H₀ is assumed correct until proven otherwise; if it is rejected, that is the signal to act. The familiar alpha of 0.05 means that one time in twenty there will be a type I error. A type II error (false negative) occurs when the alternative hypothesis is true but the null is accepted instead. During test design we set a significance level that expresses the probability of the null being correct. Based on that significance level we accept or reject the null using the obtained p-value — the probability of committing a type I error. The probability of a type II error is 1 − power, the chance of missing a real effect. There is no point designing tests based solely on the type I error rate: reducing significance reduces the chance of a type I error but increases the chance of a type II error. Set p-value thresholds according to how critical each type of mistake is in context. For instance, a type II error in the following scenario is catastrophic: a car alarm failing to trigger when the car is being stolen. When working with calculators such as [evanmiller](https://www.evanmiller.org/ab-testing/sample-size.html) to compute confidence intervals for differences, note that the type II error is not accounted for.

For some people, a visual framing helps: p-value measures how well the areas under two hypothetical distributions match when their variances are equal.

![p-value as an area comparison](https://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-25-at-9.19.04-AM.png)

Confidence intervals are always computed from the test results and are far more informative than sample size and power alone. A confidence interval is a way of looking at the correspondence between real and theoretical data. A sample drawn from a normal distribution cannot have its sample mean exactly equal the population mean — so we need to know the interval that contains the estimated parameter with some stated probability.

To summarise with a medical analogy: if a person is ill and we confirm it — true positive (TP). If they are healthy and we confirm it — true negative (TN). Type I error: we tell a healthy person they are ill (FP). Type II error: we tell a sick person they are healthy (FN). Errors are unavoidable; two metrics quantify their severity — recall and precision: Recall = TP/(TP+FN), Precision = TP/(TP+FP). The two compete with each other, so both must be tracked via their combined F-score.

![Recall and precision diagram](https://your-scorpion.ru/wp-content/uploads/2018/10/32.png)

Do not lose sight of business metrics while digging into the technical side — keep the whole picture in view. Ignoring LTV (lifetime value: total revenue generated by a customer over their lifecycle) and ROI (return on investment: whether the customer acquisition cost paid off) allows you to push ARPU (daily and monthly) and the percentage of paying users in the short term — a trick frequently exploited by product managers bouncing between companies. Short-term ARPU is easy to inflate by introducing recurring promotions, but in the long run this cannibalises other revenue mechanics and the damage shows up in 6-month LTV. A single button's CTR can be "uplifted" at the expense of every other button's CTR. Mobile app traffic can be paused to make ROI look good temporarily. In every case, any artificial change to the underlying economics produces a temporary metric spike followed by a lasting collapse. Consider the supermarket promo trap: shoppers, feeling squeezed, cut spending; retailers cut prices in promotions; shoppers get conditioned to wait for sales; retailers have to cut further. Any analytics work that misses the fact that a short-term traffic surge accompanied a gross-margin decline for three months is doing the business a disservice. The eternal dilemma: hit the plan and compress margins, or miss the plan but protect margin per unit. Talk to the business about positive EBITDA — that is the language that will get you heard.

> In all examples below I will use p-value = 0.05, as that is the conventional threshold. In production projects p-value is set considerably lower.

---

## Launching an A/B Test and Collecting Data

There are two main approaches. Either you work with a developer who handles everything infrastructure-side — which is the standard route for mobile apps. Or you use GTM or Google Optimize, splitting traffic, preparing a visual variant of your hypothesis, and setting conditions for its display (segments and so on). The latter lets you change not just colours and copy but also build entirely new functional elements, and you can segment hypotheses at the point of launch. This is a significant advantage under the constrained technical and financial resources of a large business.

Be careful with Optimize: do not trust the automatics. If you have two page variants but only the second goes through a redirect, the second variant will have higher latency and bounce rates, which means a smaller cohort. On the broader question of "when should a user be assigned to their test group" — the answer is obvious: as early as possible. Tests can be exposed or non-exposed: in the exposed variant the user is confronted with the change, which tends to reduce variance (though not always); in the non-exposed variant you split globally into cohorts and extrapolate the effect to all traffic. A practical example: if you install a taxi widget only on your homepage and only for users with 20,000 or more loyalty points, use an exposed test, measure A2C (assignment-to-conversion), and you get a larger effect than if you measured the metric globally.

In the overwhelming majority of cases here is what happens: all new traffic is randomly split into test and control. Based on experience or theory you derive the expected effect size and calculate sample size from it. Once results are in, you check for statistical significance. Optionally, run an A/A/B test to verify that any differences arise from user behaviour rather than system bugs, weather, or other external factors. Pre-test randomisation can be done by simple random assignment or by pre-stratification (CUPED). If almost all users are new, use CUPED with an additional covariate.

Ideally, once a test is live, it is reviewed at a set time and the required number of collection days is re-estimated. Do not rush data collection: the average decision cycle for a banking product is two weeks, which is the minimum data collection window. Data quality drives hypothesis validation quality — always verify data stability (absence of noise) against historical data.

Data does not have to be self-collected. Common sources include:

1. Data from your own apps, websites, and extensions (CRM, ERP, transactions, analytics, Amplitude).
2. Sniffing unencrypted traffic on major data exchange nodes.
3. Purchasing data from third-party providers — app developers, ad networks, browser extensions, and others, through both legitimate and less legitimate channels.
4. Public market data and agency research (e.g., national statistics bureaus, GfK).
5. Raw events from the product.
6. Purchasing site-traffic data from providers willing to sell it.
7. Recruiting participants, surveys, focus groups.

Connecting R directly to Google Analytics skips the CSV export entirely. Install the packages, grant OAuth access, and query the Core Reporting API with as much complexity as the API supports:

```r
install.packages("RGA")
install.packages("devtools")
library(devtools)
library(RGA)

# OAuth flow — a browser tab will open and generate a code to paste back into R
authorize(username = "your.gmail@example.com")
# An .RGAEnv environment is created, invisible to regular users

# Discover profile IDs
list_profiles()

# Quick sanity-check query
get_ga()   # returns users/sessions/pageviews for the first profileId found

# Structured query: sessions by date with an organic filter
ga_data <- get_ga(
  profileId  = "ga:xxxxxxxx",
  start.date = "2017-04-15",
  end.date   = "yesterday",
  metrics    = "ga:visits,ga:users",
  dimensions = "ga:source",
  segment    = "",
  filter     = "ga:medium==organic"
)

# Sessions over time, filtering out noise
ga_data <- get_ga(
  profileId  = "ga:xxxxxxxx",
  start.date = "2018-10-08",
  end.date   = "yesterday",
  metrics    = "ga:sessions",
  dimensions = "ga:date",
  filters    = "ga:sessions > 0"
)
```

Filter operators for constructing queries: `=` (exact match), `!=` (not equal), `=@` (contains substring), `!@` (does not contain), `=~` (matches regex), `!~` (does not match regex). Once data is retrieved, visualisation is straightforward:

```r
install.packages("ggplot2")
library(ggplot2)
ggplot(ga_data, aes(date, sessions)) + geom_line()

# Multi-metric boxplot by medium
ga_data <- get_ga(
  profileId  = "ga:код_GA",
  start.date = "14daysAgo",
  end.date   = "yesterday",
  metrics    = "ga:visits,ga:users,ga:sessions,ga:bounces",
  dimensions = "ga:date,ga:source,ga:medium",
  sort       = "-ga:date",
  segment    = ""
)

ggplot(ga_data, aes(medium, visits)) +
  geom_line() +
  geom_boxplot(fill = "green") +
  ylim(0, 10) +
  theme_dark(base_size = 14)
```

A common query artefact worth knowing about: a large proportion of `(not provided)` results appears when organic traffic is high — because organic keyword data is hidden by default. Paid AdWords traffic does surface keywords. To investigate the not-provided traffic, build a filter that extracts all `(not provided)` entries to find which pages those sessions landed on. Google Search Console also carries substantial keyword data and can supplement what Analytics withholds.

To pull keyword-level organic data and exclude the noise rows:

```r
ga_data <- get_ga(
  profileId  = "ga:xxxxxxxx",
  start.date = "2016-01-01",
  end.date   = "yesterday",
  metrics    = "ga:sessions",
  dimensions = "ga:keyword",
  filters    = "ga:keyword != (not provided) && ga:keyword != (not set) && ga:keyword != (other)",
  sort       = "-ga:sessions"
)
```

Full help is available via `browseVignettes(package = "RGA")`.

---

## Data Characterisation and Normalisation

Suppose the data is ready — CSV or Excel, large or small, with a potentially normal or non-normal probability density. Testing for normality is needed to ensure the Central Limit Theorem holds on small samples. On large, independent samples the normality assumption for Student's t-test is not required (the CLT handles it). With 1,000 users you will not detect a 1–2% change, but a 20–30% change is visible. In short: large sample → reliable results; small sample → it may not be worth running an A/B test at all.

With a small sample you need to check the distribution type. On a very small sample you may not even be able to tell a uniform distribution from a normal one. In general, plot a histogram and look at its shape. A normal distribution looks like a bell with three standard deviations on each side (the three-sigma rule: 99.7% of the sample falls within ±3σ). If samples are homogeneous and normally distributed, Student's t-test is the right choice, though it is hard to control power with it, raising the risk of a type II error. If the histogram shows left-skew, use Pearson's chi-square — a nonparametric criterion, good for verifying uniform traffic allocation but requiring data to be grouped into buckets (a contingency table). If the distribution deviates from normal, the three-sigma rule is suboptimal for degenerate distributions, though a left-skewed distribution in the chart below could well be log-normal and can be trimmed by quantiles.

![Data distribution examples](https://your-scorpion.ru/wp-content/uploads/2018/12/Screenshot_1.png)

Multimodal or bimodal distributions appear occasionally, most often after resampling. When evaluating means with a sufficient number of observations the data will approach a normal distribution regardless of the original shape — a major advantage of working on large products where data is abundant. Results depend purely on power, i.e., sample size. Perfectly normal data does not exist in the real world unless it was generated by `rnorm()`. Real data is discrete and consists of point clouds. On large samples the Shapiro-Wilk test is almost always significant. On samples of 10–15 it is almost always non-significant due to insufficient power. Statistically, power converges asymptotically to 1.

Power is the probability of committing a type I error — rejecting the null hypothesis when it is true.

Stratification (splitting a subgroup out of the sample, then randomly dividing it into two groups) works well for homogeneity. A stratum is a group of observations following a single rule. The stratified method is preferable when you first partition people by a characteristic (e.g., sex) and then draw from each group in equal proportions. If people are assigned entirely at random with no stratification, statistical significance cannot be meaningfully claimed.

Segmentation is necessary — at minimum into "whales vs plankton" with random assignment, validated against an A/A test. Or segment by country, registration month. Without a parametric criterion: split data into two equal groups, set α (significance level), n (sample size), mde (effect size), shift one group by mde, apply the criterion, and bootstrap. The final null-hypothesis acceptance rate is 1 − β. After an A/A test, check the p-value distribution: it should be uniform. A skew signals strong dependencies in the data, making analysis unreliable.

Even well-stratified samples can suffer from time instability and imbalance. Choosing an appropriate criterion for such samples matters. Resampling helps with estimation; so does mentally preparing for a relaunch if the retrospective data shows sharp discontinuities.

Outliers should be identified on a boxplot and removed manually, along with duplicates. When working with financial metrics where removing outliers is not feasible, the spread of values increases and so do confidence intervals. The three-sigma rule (remove all values beyond ±3σ and observe the change) causes significant data loss, which may be critical. An alternative is Box-Cox transformation, but be aware: removing outliers solely to satisfy a particular test is not the right approach. Better to first look at data slices where differences will be visually apparent.

Let us generate some data and build a boxplot:

```r
usersExport <- data.frame(n = 3:90)
boxplot(usersExport)
```

How to read a boxplot: a dot or line represents the mean; the surrounding box's length corresponds to the precision of the population parameter estimate; the whiskers represent a measure of spread or precision. To create a boxplot: `boxplot(variable_name)`. For an experiment, introduce gaps in the data with `usersExport <- usersExport[-sample(3:90, 23), ]` and observe how the boxplot changes.

![Boxplot example with outliers](https://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-10-at-2.41.19-PM.png)

Check data for normality visually with `qqplot()`. On large samples, tests will almost always detect deviations from normality. If the data is highly non-normal — for instance, time spent on a smartphone or financial figures that have a natural floor — normalisation or at least outlier removal is necessary. A 5% deviation is not enormous; on a large sample it will be very close to 0.05. Many tests are robust to moderate deviations from normality, though even tiny deviations become significant on large samples — and this is true of all statistical tests.

```r
qqplot(rt(a, df=3), x, main="t(3) Q-Q Plot")
abline(0, 1)
```

The Q-Q plot shows data in sorted order compared with quantiles from a standard normal distribution. If — aside from a few outliers — points roughly follow the diagonal, the data has some distortion. Points tracking the centre of the line but curving away at the ends indicate values higher than expected under a normal distribution. A normal distribution appears as a clean straight line on the Q-Q plot.

To generate and inspect clearly normal data:

```r
x <- rnorm(100)
qqline(x)
hist(x)
```

And non-normal data for comparison:

```r
y <- rgamma(100, 1)
qqnorm(y); qqline(y)
hist(y)
```

### Fitting data to a range

A common need is to bring data into a specific numeric range. The simplest normalisation is a logarithm — it compresses large deviations and pulls outliers closer to the bulk of the distribution. Alternatively, use nonparametric methods, or trim outliers — though with care: in gaming, outliers are often power users (or cheaters) deserving special attention, and on product metrics cutting everything outside three sigma destroys high-value data.

Begin by generating skewed data:

```r
x <- rweibull(1000, 2, 66)
hist(x)   # not particularly normal
```

If you need to make the situation harder by shifting and log-transforming:

```r
x <- x - 5
x <- log10(x)
x <- na.omit(x)
plot(density(x))   # shifted; Box-Cox will no longer work
```

Avoid `g <- runif(x)` — that generates random data, not a normalised copy. Standard score (z-score) is the correct way to achieve mean = 0 and sd = 1:

```r
finalData <- (x - mean(x)) / sd(x)
qqnorm(finalData)
```

To map values to a [0, 1] interval (min-max normalisation):

```r
x <- (x - min(x)) / (max(x) - min(x))
```

A quick way to filter outliers without altering the data:

```r
x[!x %in% boxplot.stats(x)$out]
```

The full helper statistics — standard deviation, range, IQR, quantiles, median absolute deviation — are all one-liners:

```r
sd(y)                      # standard deviation
range(y)                   # max minus min
IQR(y)                     # interquartile range (robust to outliers)
quantile(y, c(0.25, 0.75)) # quartiles
mad(y)                     # median absolute deviation
```

When in doubt about whether data is uniform or normal, generate a comparable uniform sample and overlay boxplots or histograms:

```r
x <- rnorm(450)
y <- runif(450)
par(mfrow = c(1, 1))
boxplot(x, y, notch = TRUE, col = c("gold", "darkgreen"))
```

If real data does not naturally fall in [0, 1], scale the uniform distribution to match the actual range (e.g., 15 to 100):

```r
xmin <- 15; xmax <- 100
y <- runif(450) * (xmax - xmin) + xmin
```

A pragmatic double-check: test equality of means and equality of medians separately; if both conclusions agree, the samples are effectively the same.

---

## Choosing a Winner

The choice of criterion depends on the volume and distribution of the data. Normally distributed data calls for Bernoulli-style tests and Gaussian calculations — degrees of freedom, variance comparisons. Non-normal data demands a different toolkit.

### Shapiro-Wilk normality test

![Shapiro-Wilk normality test](https://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-13-at-9.33.54-AM.png)

The Shapiro-Wilk test is a powerful W-criterion for normality. If W = 1, the sample is perfectly normally distributed. Null hypothesis: the sample belongs to a normal distribution. A small p-value indicates outliers. Everything above 0.75 can be considered normal. The function `shapiro.test(x)` accepts a sample of 3 to 5,000 observations:

```r
x <- rnorm(4600)
shapiro.test(x)
# Shapiro-Wilk normality test
# data: x
# W = 0.99947, p-value = 0.222
```

p-value = 0.222 → accept the null hypothesis. The W value 0.99947 is excellent because the data was generated normally. To reject the null hypothesis, p-value must be below α = 0.05 (at most 0.1). Test on non-normal data:

```r
y <- rgamma(100, 1)
shapiro.test(y)
# W = 0.9829, p-value < 2.2e-16
```

p-value < 2.2e-16, far below 0.05 (R only reports values above the threshold 2.2×10⁻¹⁶). Reject the null hypothesis.

The decision rule is the same regardless of whether you are looking at W or p-value:
- p > 0.05 → data does not differ from a normal distribution.
- p < 0.05 → data differs significantly from a normal distribution.
- `shapiro.test(data)` returning W = 0.98134, p-value = 0.961 indicates approximate normality.

Always plot the data alongside the test — on large samples even tiny deviations become significant for all statistical tests, and a graph clarifies whether the distribution is symmetric or skewed left or right.

The limitation: 5,000 observations maximum. For larger samples, use the Shapiro-Francia test or the Anderson-Darling test (easier to find in ready-made form and can test against any distribution). Kolmogorov-Smirnov is also an option, though less suited to real-world tasks.

For a concrete A/B dataset, start by characterising both samples:

```r
row1 <- c(29,30,34,34,45,43,33,43,48,23,27,38,32,36,35,34,33)
row2 <- c(34,32,31,35,55,46,43,45,30,26,27,23,47,37,29,30,60)

boxplot(data.frame(row1, row2))
summary(data.frame(row1, row2))

# Visual checks
hist(row1)
qqnorm(row1); qqline(row1)
```

When reading a Q-Q plot for normality: points should lie along a single line, and quantile values should not stray beyond the ±2 range. A leftward skew is present in the data above, so chi-square is used to test for independence:

```r
chisq.test(data.frame(row1, row2))
```

The alternative hypothesis is not always the negation of the null — it is the rule by which we reject or accept the null. Choose test statistics that are more extreme under the alternative than under the null. Compute the statistic for the specific sample and judge whether it falls in the distribution's tail. If p-value = 0.1517 > 0.05, accept the null hypothesis: interpreting the two samples as debtor counts in Eastern and Western Europe, we conclude H₀ — the average number of debtors in the two samples is unrelated.

### Student's t-test

![Student's t-test](https://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-17-at-9.45.36-AM.png)

The t-test is the simplest way to check the accuracy of a mean for data with natural values. Samples must be independent (not paired) and normally distributed — the larger the sample, the closer to normal. This guarantees concentration of probability density around the mean, enabling population-level inferences from a sample alone:

```r
x <- rnorm(1000000)
y <- rnorm(1000000)
t.test(x, y)
# t = -1.1696, df = 2e+06, p-value = 0.2422
# 95% confidence interval: -0.004424298  0.001117388
# mean of x: -0.0007571144   mean of y: 0.0008963404
```

Manual verification:

```r
m <- length(x); n <- length(y)
t <- (mean(x) - mean(y)) / sqrt(var(x)/m + var(y)/n)
t   # -1.1696
```

Since 0.2422 > 0.05, there is no reason to reject the null hypothesis. P-value is the achieved significance level — the smallest significance level at which the null hypothesis would be rejected for this particular test statistic. The smaller the p-value, the less likely the result is random. Results are "statistically significant" when p < 0.05 (5%). Getting to 0.005 is even better, greatly reducing the false-positive rate. At 0.05, roughly 1 in 3 positive conclusions are wrong. In physics or genomics, thresholds of 0.0000003 are standard. The lower the p-value, the stronger the evidence for the alternative hypothesis.

The visualisation below illustrates how boundaries shift depending on input data; crossing the boundary triggers acceptance of H₁ (that a difference exists). Boundaries need not be dynamic — static boundaries work too.

![Dynamic boundary visualisation](https://your-scorpion.ru/wp-content/uploads/2019/01/Screen-Shot-2019-01-22-at-1.42.21-PM.png)

The null hypothesis is always about the absence of statistical significance — equality of the population value against a chosen criterion. It is called "null" because negation is zero and agreement is one. The alternative hypothesis asserts that not all users see the banner, i.e., no claim of prior equality.

The **paired t-test** has fewer degrees of freedom but eliminates the influence of individual differences. Use it for dependent samples — when each element in the control group has a corresponding element in the second group. Examples: testing the same home page in September 2018 vs September 2019, or measuring designers' skills before and after a course:

```r
leftGroup  <- c(12, 13, 11, 15, 19, 15, 17)
rightGroup <- c(14, 14, 16, 16, 18, 14, 15)
t.test(leftGroup, rightGroup, var.equal = TRUE)  # p-value = 0.5648
t.test(leftGroup, rightGroup, paired = TRUE)     # p-value = 0.4539
```

The paired test is more discriminating. To compare medians with a t-test — one-sample (is the mean equal to 5?) or two-sample:

```r
# One-sample
x <- rnorm(10, 5, 1)
t.test(x, mu = 5)

# Two-sample
d1 <- rnorm(12, 5.2, 1)
d2 <- rnorm(11, 5.1, 1)
t.test(d1, d2)
```

Note that R's default is Welch's t-test, which does not require equal variances. First run an F-test to compare variances:

```r
var.test(d1, d2)
```

If p < 0.05, variances differ — use Welch's test. If p > 0.05, variances are approximately equal and classical t-test with `var.equal = TRUE` applies. To reduce variance, use stratification (user clustering, CUPED from Microsoft), low-level metrics trimming, or historical covariate adjustment.

**Z-test of proportions** is the second most popular choice: a parametric test for equality of means when you see a binomial distribution (conversion, ratio metrics). It tests a binary random variable (heads/tails, 1/0, yes/no). Use it for independent samples. For testing equality of variances, use Levene or Bartlett — both parametric, with Bartlett being more powerful but demanding a more normal distribution.

### Wilcoxon – Mann-Whitney

This nonparametric criterion tests whether random observations from one group tend to be higher than in another — an alternative to the t-test. Where t-test needs normally distributed data and is sensitive to outliers, Mann-Whitney needs no normality assumption, though it will not detect subtle differences and is harder to interpret. The reason: the t-test compares actual observed values (means), while Mann-Whitney compares ranks (positions in an ordered sample), making it outlier-resistant. However, Mann-Whitney is highly sensitive to differences in variance, which becomes very noticeable on large samples.

Therefore, using the t-test on large samples for evaluating means is acceptable in practice, because normality is ensured by the CLT compressing extreme values. Just remember: the more the distribution deviates from normal, the worse the t-test performs in sample comparison. T-test works for large samples from non-normal distributions; it does not work for small samples from non-normal distributions.

T-test is sensitive to outliers. When data has a long tail — almost always present in real financial data — the tail heavily skews the mean, and large variance produces poor results. Common metrics like ARPU, session count, time-based metrics, ARPPU, average order value are not binomial distributions, and parametric tests often cannot handle them due to long tails. Solutions: bucket technique (needs large samples), Mann-Whitney with per-day p-values and multiple-comparison corrections (losing power — avoid corrections if at all possible), cumulative p-value, bootstrap (simplest — get the distribution of the sample mean and check quantile intersection at the significance level), or CUPED (normalises data by reducing deviations from the mean) plus Welch's t-test.

```r
wilcox.test(mpg ~ am, data = mtcars)
# Warning: cannot compute exact p-value with ties
wilcox.test(mpg ~ am, data = mtcars, exact = FALSE)
# W = 42, p-value = 0.001871
```

Testing two observation sets:

```r
a <- c(123,105,147,142,119,129,130,87,301,92,177,141,137,112,138,128,114,197,198,210,101,125,134,214,110,100,152,122,144,148,153,212)
b <- c(154,512,120,131,124,118,178,140,136,68,162,127,78,106,133,655,155,169,199,108,143,341,121,139,166,174,184,98,135,132,146,209)
wilcox.test(a, b)
# W = 455, p-value = 0.4507
# alternative hypothesis: true location shift is not equal to 0
```

p-value = 0.4507 >> 0.05 → accept the null hypothesis. W is the Wilcoxon test statistic — the sum of ranks in one of the two groups.

> If p < 0.05, the null hypothesis of no difference is rejected.

For non-normal data when a result is required regardless:

- First, consider Box-Cox data transformation — risky but sometimes viable.
- Alternatively, use rank-based nonparametric criteria: `wilcox.test` for two samples, `kruskal.test` for more:

```r
x <- matrix(runif(12*100), ncol = 2)
wilcox.test(x, paired = FALSE, exact = FALSE)
# V = 720600, p-value = 2.2e-16  → reject H0
```

Wilcoxon compares ranks, not values. Note: rank-based methods compute exact p-values poorly when there are ties. The `wilcox_test()` from the `coin` package handles this better by approximating the Wilcoxon distribution with a normal distribution:

```r
install.packages("coin")
library(coin)
testDataRnd <- data.frame(
  x = c(rnorm(n=40, mean=14, sd=4), rnorm(n=23, mean=17, sd=4)),
  g = c(rep("a", 40), rep("b", 23))
)
keepIt <- wilcox_test(testDataRnd[,1] ~ testDataRnd[,2])
keepIt@statistic@teststatistic   # returns z-value from Mann-Whitney
```

For comparing means under a non-normal distribution with at least 400 observations, the t-test is still usable (CLT kicks in). Otherwise, simply compare medians.

**Summary of criterion selection:**

Independent samples:
- A/B → Mann-Whitney
- A/B/C+ → Kruskal-Wallis
- Normal distribution, A/B → Student's t-test or Welch's t-test
- Normal distribution, A/B/C+ → ANOVA
- Variance differences (normal): Bartlett, Levene, Conover — by degree of departure from normality
- Binomial data → proportion test
- Contingency table with a formulated null hypothesis → Pearson's chi-square

Dependent samples:
- A/B → Wilcoxon signed-rank
- A/B/C+ → Friedman test

No idea what you're dealing with? Bootstrap — it will give you a distribution to work from.

### Chi-square and Fisher's exact test for survey data

For two-observation matrices, the choice between Fisher and chi-square depends on data volume. Both require absolute, categorical (qualitative) counts — not percentages. Assuming observations are independent:

```r
matrixData <- matrix(1:9, 800, 2)
chisq.test(matrixData)
# RStudio will warn about possible inaccuracy — chi-square performs poorly with small cell counts
```

With a smaller matrix, use Fisher's exact test:

```r
matrixData <- matrix(1:5, 6, 2)
fisher.test(matrixData)
# F-test for variance comparison of two or more normally-distributed small samples
# Does not require a fixed p-value for null-hypothesis acceptance

chisq.test(matrixData, simulate.p.value = TRUE)
# Simulation finds p-value more precisely by using a random-number generator
# p-value = 0.6436  >>  0.05 → sample does not contradict the null hypothesis
```

For comparing medians, Mood's median test exists but has large type II error rates. A simulation in R:

```r
x1 <- 1:30; x2 <- 1:54
m    <- median(c(x1, x2))
set1 <- sum(x1 > m);  set2 <- sum(x2 > m)
set01 <- sum(x1 <= m); set02 <- sum(x2 <= m)
finalData <- matrix(c(set1, set2, set01, set02), nrow=2, ncol=2)
chisq.test(finalData)
# p-value = 0.01225 < 0.05 → reject equality of medians
```

Confirm with Mann-Whitney (which tests rank ordering, not medians directly):

```r
wilcox.test(x1, x2, alternative = "two.sided", exact = TRUE, correct = FALSE)
# p-value = 0.0007761  <<  0.05
```

The fundamental constraint: trust these conclusions only when each column contains hundreds or thousands of observations. With tiny samples, results are unreliable regardless of the test.

For visualisation, correlogram / heatmaps work well:

```r
install.packages("corrplot")
m <- matrix(seq(-1, 1, length.out=9), nrow=3)
corrplot(m, method = "pie")

# Or a richer random example
K <- matrix(runif(48), 2, 24)
corrplot(K, method = "pie")
```

### Bernoulli / binomial tests

The Bernoulli test handles two categories of data (heads/tails, boy/girl, red/black):

```r
rbinom(200, size=1, p=0.68)   # p = probability of getting 1
```

`rbinom` tells you how many "heads" you get in a fixed number of coin flips. Comparing two Bernoulli trials — closely resembling binary A/B results:

```r
varA <- rbinom(200, 20, 0.5)
varB <- rbinom(200, 20, 0.25)
hist(varB, probability=TRUE, col=gray(0.9))
```

![Binomial distributions with different p values](https://your-scorpion.ru/wp-content/uploads/2021/01/2.png)

The plot shows 200 binomially distributed random numbers for trials of size 20 with probabilities 0.25 and 0.50. Binomial tests are comparisons of proportions. They are ideal for testing, say, an app uninstall rate. If the sample is small (100) and conversion is around 12%, the binomial test is the best choice. Student's t-test can be applied to a Bernoulli distribution but the sample must exceed 200; bootstrap behaves similarly.

For typical financial data with a spike at small payments and a long tail, and where working with a logarithm is acceptable (but not of zero), Box-Cox transformation can normalise the data. After transformation, apply a standard parametric test (t-test), read p-value, and evaluate significance for closing the experiment.

The full decision process: identify outliers via boxplot → test for normality with Shapiro-Wilk → characterise the distribution with `qqnorm` → select the analysis method. Normal data: Bernoulli, Gaussian, Student's. Non-normal (flattened on one side): chi-square, Bayes, Poisson. A range of 0 to tens with a cluster of outliers in the hundreds is typically a Poisson distribution (used in queuing theory). Apply chi-square to the sample against the Poisson distribution and get an answer on the hypothesis.

### Graphical hypothesis checking

Suppose data is normally distributed and the mean is an adequate measure of central tendency. The comparison then simply requires computing the difference between the two means. For a multivariate A/B test, compute CTR for each variant and plot confidence intervals. Example CTR calculation: 84,343 impressions, 21,384 clicks → 21,384 / 84,343 = **25.3%**.

For graphical comparison: compute the 95% confidence interval for the difference of means and medians (bootstrap works well here) and check whether it captures zero. If it does, no significant difference is observed.

### The Bayesian alternative

The Bayesian approach is recommended when data is abundant — it is an alternative to the frequentist framework. Frequentism is about null hypotheses and event frequencies; Bayes is about accepting the null hypothesis and enables reasoning about events and their causes. Do not mix the two approaches — that leads to "Lindy's paradox."

The frequentist approach does not allow prior assumptions; it relies entirely on the test sample and imagines repeating the experiment infinitely many times to build a histogram of objective uncertainty. The hypothesis is checked via classical p-value. Fix the sample size before the experiment. Two useful formulas:

$$n = \frac{z^2 \cdot \sigma^2}{\text{mde}^2}$$

- Numerator: variance (α²); denominator: mde.
- *z* = standard error; 1.96 for 95% significance level.
- σ = standard deviation.

Note that virtually all variables are interrelated: the smaller the effect you want to detect, the larger the required sample size. With a homogeneous sample, variance will be lower and the probability density function more concentrated than with 100% of available data — but conclusions will apply only to the sample, not all data. Less variance = less generalisability.

Example: find sample size for a proportion test on categorical data. α = 95%, mde = 0.05, p = 70%, z = 1.96:

$$n = \frac{1.96^2 \times 0.7 \times 0.3}{0.05^2} = \frac{3.8416 \times 0.21}{0.0025} = 322$$

The Bayesian idea: 500 heads in 1,000 coin tosses, computed as C⁵⁰⁰₁₀₀₀ × 0.5⁵⁰⁰ × 0.5⁵⁰⁰ (flips are independent). If we knew all possible parameters of the coin, we could predict the outcome. Since we cannot, we run many observations. Bayes answers the induction problem: which variant is better and by how much? Bayes always needs a prior distribution — you must have a prior belief about the parameters of the process being studied. This is actually the default mode of thinking for a designer. With Bayes you do not need to calculate power and significance, but you lose a clean understanding of effect size. If Bayes does not appeal, Student's or Bernoulli for large samples works fine. Binary outcomes (0 and 1) are definitively Bernoulli and binomial distribution — easy to work with, interpret, and visualise.

---

## A Worked End-to-End Example

The most complex part of a test is its design — target audience, timing, duration, choice of metrics, sample volume, and whether the hypothesis is even testable. The simplest thing is comparing proportions. Imagine two landing pages:

```r
dat <- data.frame(
  landing1 = c(2141, 187, 54),
  landing2 = c(1875, 201, 65),
  row.names = c("showed", "bought", "installed")
)
dat_t <- data.frame(t(dat))
prop.test(dat_t$bought, dat_t$showed)
# prop 1          prop 2
# 0.08734236     0.10720000
# p-value = 0.0383
```

Conclusion: landing page 2 has a higher click conversion. p-value = 0.0383 < 0.05 → statistically significant.

For sample-size planning, power is typically set at 0.8; use 0.99 for very large samples:

```r
power.prop.test(p1 = .06, p2 = .08, power = .85)
```

Effect size is not calculated — it is chosen from experience. Here it is 0.02 (the difference between 0.06 and 0.08). The smaller the effect size, the larger the sample required.

### A/A/B and multivariate tests

Beyond A/B exist A/A/B, A/B/n, factorial, multivariate (MVT), and many other designs. An MVT test isolates each change from all others, cycling through every possible combination:

- Change A: make links underlined.
- Change B: switch from outline buttons to filled buttons.
- Change C: increase line-height.
- Change D: add skeleton screens.

### Avoiding the peeking problem

P-value is a cumulative quantity. On real projects, treat it as a guide only when it reaches a plateau. Check p-value outliers day-by-day after the test, otherwise you face the peeking problem: looking at interim results mid-test and stopping early upon seeing nominal significance. Solution: adaptive confidence intervals. Convergence means the p-value has reached a plateau. If the pre-specified sample size matches what was actually collected, and the statistic has been flat for 5–7 days, the experiment can be closed.

A late-closing trap also exists: if no significant effect is found at test end, the temptation is to extend. To fix this, reduce variance (CUPED, predictor subtraction, linearisation). Never change the significance level or target metrics mid-test. 

If the p-value for a binomial metric shows both observed groups crashing simultaneously, check how power at end compares with power at start. Power significantly worse? Continue the experiment until the required power is reached.

P-value itself has a confidence interval. Running 10,000 A/A tests at p-value = 0.05 will flag 500 by chance alone — those 500 have their own confidence interval on top. A probabilistic approach helps here: Bayes' theorem.

For command-level help: `help(t.test)` and `help(wilcox.test)`.

---

## Beyond R Studio

Any analyst today — UX, business, dashboard, researcher, developer — should be comfortable in both R and Python. But basic Excel skills still help resolve many problems quickly. Working on a product often requires rapid answers on DAU/MAU, registration conversion, and frequently changing metric values. A common practice is to use the mean for all primary KPIs when the distribution is normal, enabling fast *primary* conclusions. Distribution type can be understood via standard deviation, computed in Excel with `=STDEV()`. The formula is:

$$\text{STD} = \sqrt{\frac{\sum(x - \bar{x})^2}{n}}$$

— the square root of the sum of squared differences between sample elements and their mean, divided by the count.

![Standard deviation comparison chart](https://your-scorpion.ru/wp-content/uploads/2021/01/ada.png)

In the example above: despite nearly identical means, the second variant shows enormous oscillation around its mean. The first variant deserves more confidence.

Excel also offers excellent data visualisation, with pivot tables particularly useful. They work on the principle of virtually grouping rows with identical values and computing aggregates — sums, counts — making them ideal for data aggregation and reporting.

---

## Conclusion

The most important skill in running tests is the ability to draw conclusions from numbers: critical thinking, evidence-based inference from historical data, hypothesis generation, rational and irrational intuition. Statistical analysis only prevents mistaking fiction for fact. The final business decision is still made by a person.

Arranging date ranges in R for visualisation is easy — generate a sequence, then style it with `RColorBrewer` for clean, production-quality palettes:

```r
setDataYears <- c("1860:2019")
newData <- eval(parse(text = setDataYears))
plot(newData, edge.arrow.size=.4, vertex.label=NA)

install.packages("RColorBrewer")
library(RColorBrewer)
display.brewer.all()

# Shuffle and colour
newData <- sample(newData)
colorsSet <- brewer.pal(12, "Paired")
plot(newData, pch=19, cex=6, col=colorsSet)
```

When you need to compare the shapes of many distributions simultaneously — understanding whether values cluster around the median or bunch at the extremes — a **violin plot** is a strong alternative to a boxplot. It shows the same quartile markers but adds the probability density envelope:

```r
library(ggplot2)
x <- runif(256, 0, 1) * 0.65 / 3
y <- runif(128, 0, 5)
xbin <- cut(x, breaks=seq(0,1,0.1), include.lowest=TRUE, labels=seq(0.05,0.95,0.1))
df <- data.frame(x=x, y=y, xbin=xbin)

ggplot(df, aes(x=x, y=y, colour=xbin)) +
  geom_violin(draw_quantiles = c(0.25, 0.5, 0.75)) +
  geom_point(alpha=0.5)
```

Use `summary(lm(y ~ x))` on any linear model to get min, max, mean, median, quantiles, standard errors, and significance levels in a single call.
