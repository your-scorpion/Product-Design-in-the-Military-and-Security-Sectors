

# Validating A/B Test Results

A/B tests are how teams settle arguments about interfaces. They're also how those arguments get settled wrongly, because the most common analytical mistake is comparing two means without choosing a test, without considering the sample. Eyeballing GA reports — "the chart that's higher is the better version" — leads to incorrect conclusions and costs businesses serious money. If your toolkit so far has been "the calculator should show p < 0.05" and "normal distribution looks like a bell," this article is meant to widen it.

All examples are in R-Studio. The general flow of analysis: experimental design → raw data → processed data → choice of statistical model → summary statistics → p-value. Designers can manage the experimental design themselves; the rest is what we'll spend time on.

## Forming the hypothesis

The simplest framing: what are the business goals, how do clients achieve them, and how can you measure that achievement (single A/B test or multi A/B test). Equally important: how the data will be collected and validated, and how changes get rolled in after the test concludes. Assume the questions of representativeness and adequate sample size are settled — for this article, we'll treat the standard deviation (the square root of variance) as independent of sample size because the sample is representative.

In real life, the task often arrives as "our profit dropped, take a look." Or a product manager or game designer shows up with a feature already in production and asks you to determine its effectiveness — with no information about what the feature was meant to achieve, what data to investigate it on, or how to operationalise it. With loosely-defined tasks like these, you have to invent the hypotheses yourself and work out where to source the data. Data consolidation from multiple sources helps. But avoid hallucinations — the company should have a hypothesis map and a formed strategy for reaching its goals.

Or the hypothesis source is just a banal product-manager-vs-project-manager argument, hallway-poll results, the urge to optimise CRO. I don't enjoy testing features whose conversion uplift is obvious — that work is mostly pointless. Better to test features whose business return is unpredictable. Those are usually where growth in every key metric hides: CTR, conversion, CPA, ROAS, CPI. With small data quantities it's genuinely hard to evaluate non-binary metrics (average order value, revenue) — results get noisy. Garbage in, garbage out. A/B tests for binary tasks (done / not done) are straightforward. Either way, the first step is picking the target metric.

Then you settle the maths. Say we're frequentist, not Bayesian. Pick a test family. If the distribution is normal, parametric tests — Student's t-test and ANOVA for equality of means; F-test, Bartlett, Levene for equality of variances; proportion tests for binomial metrics; formal tests for distribution membership. They compute statistics from information about the original distribution function — meaning you have to specify some variables. If you're comparing means and don't have much data, bootstrap (in-app spending is a typical case). The last-resort family is nonparametric tests, which need only information from the sample. Sub-classes: randomness, symmetry, correlation, location-and-scale, location. Less powerful but universally applicable. Mann-Whitney and Kruskal-Wallis for shift; Friedman and Wilcoxon signed-rank for dependent samples; bootstrap for distribution-by-quantile comparisons; Pearson's chi-square for distribution-function changes.

A worthwhile habit for real left-skewed data: compare two samples with a nonparametric test, then compare the same samples after a log transform with a t-test. Double-check the result.

A good hypothesis respects induction and falsifiability. Induction is the negation of the hypothesis, the ability to formulate a negative judgement — were ancient Greek gods real? No. Falsifiability is about attempting to disprove the hypothesis; if you fail, you accept it. Prove ancient Greek gods didn't exist; until then, we assume they did.

When testing a hypothesis, remember that the null hypothesis is about the absence of differences — proving it requires the whole population, not a sample. The alternative hypothesis claims the differences are significant. All differences in samples are always the alternative hypothesis; the null is what's tested. A Type I error (α, false positive) is when you reject the null in favour of the alternative when in fact the null is true. H0 is considered correct until proven otherwise; if it's rejected, you go take action. This is the famous α = 0.05, which says one case in twenty will be a Type I error. A Type II error (false negative) is when the alternative is true but you accept the null. Said differently: you set the significance level when designing the test; it's the probability that the null is true. From the significance level you accept or reject the hypothesis using the resulting p-value, which is the probability of committing a Type I error. The probability of a Type II error is 1 − power — the chance of missing real changes. There's no point designing tests based on Type I alone. Lowering the significance level lowers the chance of Type I errors but raises the chance of Type II, so p-value gets picked based on how critical each error type is. You can't tolerate a Type II error in cases like a car alarm failing during an actual theft — that's catastrophic. Calculators like [evanmiller](https://www.evanmiller.org/ab-testing/sample-size.html), for confidence intervals on differences, don't account for Type II.

A way to picture it: p-value is the correspondence between the areas of two hypotheses with equal variances.

![p-value as area](https://your-scorpion.ru/wp-content/uploads/2020/12/Group-83.png)

Confidence intervals are always computed from test results and are far more informative than sample size and power. A confidence interval is a way of viewing the correspondence between real and theoretical data. The sample is from a normal distribution, and the sample mean can't be exactly equal to the population mean — so you need to know the interval the estimated parameter falls into.

Summary of error types: if a person is sick and we confirm it — true positive (TP). If not sick and we confirm it — true negative (TN). Type I error — we told a healthy person they're sick (FP). Type II — we told a sick person they're healthy (FN).

Errors will always be there; to gauge their significance, two metrics: recall and precision. Recall = TP/(TP+FN), Precision = TP/(TP+FP). They compete with each other, so you account for both via an integrated characteristic.

![Recall and precision](https://your-scorpion.ru/wp-content/uploads/2020/06/Group-62.png)

The trade-off between Type I and Type II matters in practice. Simple rule: low risk = aim for low FP. High risk = aim for low FN. Spam filters need few FPs — the cost of marking a real email as spam exceeds the cost of letting one phishing email through.

![Low-risk: minimise FP](http://your-scorpion.ru/wp-content/uploads/2021/01/2.png)

The reverse case: at a nuclear plant, FPs are fine — false alarms are cheap; missed alarms are catastrophic.

![High-risk: minimise FN](http://your-scorpion.ru/wp-content/uploads/2021/01/ada.png)

Back to business metrics — you can't forget them while you're deep in the technical side. You need the whole picture. Ignore LTV (lifetime value — how much a client brings in across their lifecycle) and ROI (return on investment — whether acquisition cost paid off), and short-term you can dramatically lift ARPU daily, ARPU monthly, and paying-user percentage — which is exactly how product managers job-hopping between companies build their resumes. Short-term ARPU lift just requires extra recurring promotions. Long-term it leads to financial problems because it cannibalises other monetisation mechanics, which becomes visible on six-month LTV. You can also hit a short-term KPI by uplifting one button's CTR while cannibalising other buttons' CTR. Or shut off mobile-app traffic and watch ROI rise on a very short horizon. Any temporary change to base unit economics leads to a temporary metric rise followed by a tragic dip.

The grocery-store promo trap is the cleaner version of the same dynamic: customers believe prices are rising and income is falling, so they spend less; sellers respond with price-cut promotions, which lifts revenue briefly; customers get used to promotions and start shopping deliberately for discounts, forcing sellers to cut again, and again. The connection to testing: after a one-shot promotion, business sees profit up, traffic up, more customers — and you, as the analyst, have to point out that on a three-month cut, margin and gross are down. The eternal dilemma: hit the plan and lose margin, or miss the plan, lift profit briefly, and lose revenue overall. Talk to business in terms of positive EBITDA, then they listen.

The directionality of a metric is also its reliability. You often track a proxy in lieu of the main metric, and the quality test is simple: a good proxy consistently mirrors the trajectory of the main metric. Result matches → +1. Doesn't match → −1. For very low-sensitivity metrics, look at the correlation of t-statistics instead. Pyramid metrics at the top — North Star and similar — are either hard to track, or you need to make them very specific: not "make the world better" but "number of new users."

> I'll use p-value = 0.05 because it's the convention. In real projects p-value is much lower.

## Running the test and collecting data

Either you go to a developer and somehow they handle everything for you — which is the main way A/B tests get run on mobile — or you use GTM or Google Optimize, where you split traffic, prepare the visual representation of the hypothesis, and set conditions for its display (segments and so on). You end up able to change not just colour and text on the page but also create new functional entities or evolve existing ones, and to segment hypotheses already at launch. That's a big advantage when working with the constrained technical and financial resources of a large business.

Be careful with Optimize — you can't lean on the automation. Suppose you have two variants but only the second goes through a redirect; the second will have higher latency and bounce rate, and the cohort will be smaller. On the higher-level question of when in the experiment a user should be assigned to their group, the obvious answer is "as early as possible." Tests can be exposed or non-exposed: in the first case you confront the user with the changes on the site, which reduces variance (in theory); in the second, you split into cohorts globally and extrapolate the effect to all traffic. A case from practice: we install a taxi widget only on the home page of our site and only for users with more than 20,000 accumulated points. That's an exposed test; we count A2C; we get a larger effect than when computing the metric globally.

Here's how most cases unfold: randomly split all new visitor traffic into test and control. From experience or theory, estimate the effect size and compute the sample from it. With results in hand, check for statistical significance. As an extra check, run an A/A/B test — that confirms the differences are at the user-behaviour level, not at the level of system bugs, weather changes, or other factors. The audience can be split by simple random, or by pre-stratification (CUPED). If almost all users are new, use CUPED with an additional covariate.

Ideally, you check in on the test after some time has passed and re-estimate the days needed for data collection. Don't rush data collection — the average cycle for opening a banking product is two weeks, so that's the minimum collection window. Data quality drives hypothesis-validation quality; verify data stability (absence of noise) against historical data.

You don't have to collect data yourself. The main sources:

1. Data from your own apps, sites, extensions (CRM, ERP, transactions, Yandex.Metrica, Amplitude).
2. Sniffing unencrypted traffic at major data-exchange nodes.
3. Buying third-party data — app and extension makers, ad/banner networks, malware, worms — both legitimate and not.
4. Open market data and agency studies (Rosstat, GFK).
5. Raw product events.
6. Buying traffic data from resources willing to sell it.
7. Hunting people, surveys, focus groups, research.

For the first category, pulling data straight from Google Analytics into R without going via CSV is cleaner than it sounds. Install the right packages — `install.packages("RGA")` and `install.packages("devtools")` — and load with `library(devtools)` and `library(RGA)`. Authorize with `authorize(username = "your_gmail")`; you'll be redirected to a browser and given a code to paste back into R. A hidden `.RGAEnv` environment gets created.

It's also worth wrapping data calls in existence checks:

```r
x <- NA
exists("x")
```

`list_profiles()` returns the profile ID you'll work with. Test things end-to-end with `get_ga()` — a Core Reporting API call that returns something like `users 333 / sessions 486 / pageviews 1505` for the first profileId found. `browseVignettes(package = "RGA")` gives a detailed reference.

A typical query:

```r
ga_data <- get_ga(
  profileId   = "ga:xxxxxxxx",
  start.date  = "2017-04-15",
  end.date    = "yesterday",
  metrics     = "ga:visits,ga:users",
  dimensions  = "ga:source",
  segment     = "",
  filter      = "ga:medium==organic"
)
```

The [API](https://developers.google.com/analytics/devguides/reporting/core/dimsmets) is good enough that queries can be as complex as you need. Sessions over time:

```r
ga_data <- get_ga(profileId   = "ga:xxxxxxxx",
                  start.date  = "2018-10-08",
                  end.date    = "yesterday",
                  metrics     = "ga:sessions",
                  dimensions  = "ga:date",
                  filters     = "ga:sessions > 0")
```

Filtering by keyword with regex:

```r
ga_data <- get_ga(profileId   = "ga:xxxxxxxx",
                  start.date  = "2016-01-01",
                  end.date    = "yesterday",
                  metrics     = "ga:sessions",
                  dimensions  = "ga:keyword",
                  filters     = "ga:keyword != (not provided)
                                 && ga:keyword != (not set)
                                 && ga:keyword != (other)",
                  sort        = "-ga:sessions")
```

The filter operators are: `=` exact match, `!=` not equal, `=@` contains, `!@` doesn't contain, `=~` matches regex, `!~` doesn't match regex.

Visualisation is then easy:

```r
install.packages("ggplot2")
library(ggplot2)
ggplot(ga_data, aes(date, sessions)) + geom_line()
```

More complex plotting too:

```r
ga_data <- get_ga(
  profileId   = "ga:GA_code",
  start.date  = "14daysAgo",
  end.date    = "yesterday",
  metrics     = "ga:visits,ga:users,ga:sessions,ga:bounces",
  dimensions  = "ga:date,ga:source,ga:medium",
  sort        = "-ga:date",
  segment     = ""
)

ggplot(ga_data, aes(medium, visits)) +
  geom_line() +
  geom_boxplot(fill = "green") +
  ylim(0, 10) +
  theme_dark(base_size = 14)
```

![GA data plotted in R](http://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-25-at-9.19.04-AM.png)

A common surprise when querying organic traffic: huge volumes of "not provided" results. That's an organic-traffic thing — paid Adwords traffic shows actual keywords in PPC. Pull the "not provided" rows with a filter to understand the source, and configure additional filters to see which pages those users land on.

![Filtering not provided](http://your-scorpion.ru/wp-content/uploads/2018/10/32.png)

Google Search Console has plenty of keyword data too, and you can upload Metrica data into it.

One more thing on running tests: when you experiment, verify no other source got cannibalised — that the behaviour of users outside the test group didn't shift in response. If you lowered prices for one group, did the rest of the customer base behave differently? This is the network effect, and it has a formal check: SUTVA (the stable-unit-treatment-value assumption). The current pattern for handling it is switchback testing — split traffic into multiple control and experiment groups, apply changes to the experiment groups, then after a short interval swap their roles. You get different metric values per group depending on whether the change was applied; comparing across those gives you the answer.

A quick aside on tooling. If you're trying to install R packages from a network behind a proxy, you'll bump into "server unreachable" errors fast. In Chrome, `chrome://net-internals/#proxy` exposes `script:http://wpad/wpad.dat`; opening it shows you the proxy. In the terminal:

```bash
export http_proxy=http://address.ru:8080
export HTTP_PROXY=http://address.ru:8080
# with credentials
export http_proxy='http://username:password@address.ru:port/'
# disable
export http_proxy=''
```

Check System Preferences → Network → LAN → Advanced → Proxies for the "Auto Proxy Detection" tick — the command line doesn't use that setting, but other software does. In RStudio: `Sys.setenv(http_proxy = "http://address.ru:8080")`, `Sys.unsetenv` to remove, `Sys.getenv("http_proxy")` to inspect. As a fallback, `brew install cntlm`. `netsh winhttp show proxy` shows the current Windows proxy.

## Characterising and normalising the data

Assume the data is ready — CSV or Excel. It might be a lot or a little; the probability density of the sample mean might be normal or not. The reason to check normality is to make the central limit theorem apply on small samples. With large samples and independent observations, the normality assumption isn't needed for the t-test (CLT does the work). On 1000 users you can't detect a 1–2% effect, but 20–30% is fine. Put differently: with a large sample, test results will be precise; with a small sample, the test may not be worth running.

When data is sparse, you need to check the distribution. You may not even be able to tell a uniform from a normal on a very small sample. In general: build a bar chart and look at its shape. A normal distribution looks like a bell with three sigmas on each side. The three-sigma rule: each sigma is one standard deviation; data is normally distributed, and 99.7% of the sample falls within three sigmas left and three right. If the sample is homogeneous and the distribution normal, the choice is Student's t-test. Power is hard to control with Student's, so the chance of a Type II error grows. If the chart shows left-skew, use Pearson's chi-square — a nonparametric criterion that's well suited to checking whether traffic is being split evenly, though it requires bucketing data into a contingency table. If the distribution is non-normal, the three-sigma rule is the wrong choice for degenerate cases. But on the chart below, the left distribution could well be log-normal and quantile-cut.

![Distributions](https://your-scorpion.ru/wp-content/uploads/2019/09/180.png)

You'll also see multimodal or bimodal distributions, more often after resampling. For mean estimation, with enough observations the data ends up normally distributed — that's the big payoff of working on a large product where there's always plenty of data: whatever the original distribution, the distribution of sample means tends toward normal. The result simply depends on power, i.e. on sample size. Perfectly normal data doesn't exist unless your idea of "real life" is the output of `rnorm()`. Our data is also discrete — a set of points. So on a large sample, Shapiro-Wilk will always be significant. On samples of 10–15 values it's always non-significant because of insufficient power. In statistics, power asymptotically approaches 1.

Power is the probability of correctly rejecting the null when it's false — that is, detecting a real effect when one exists.

Stratification (carving out a sub-group from the sample and dividing it randomly into two groups) helps with homogeneity. A stratum is a group of observations following a single rule. The stratified method is preferable when you first divide people by some feature (gender, say), and then sample equal proportions from each group. If people are distributed between two groups by pure random assignment with no other structure, the design produces conclusions only about that pool — without stratification, statistical significance still works mathematically, but you lose interpretive power across subgroups.

Segmentation matters. At least whales-vs-plankton, with random assignment, verified against an A/A test. Or by country, or by month of registration. If you don't have a parametric criterion, split the data into two equal groups, set α (significance level), n (sample size), mde (effect), shift one group by mde, and apply your criterion plus bootstrap.

The final percentage of null hypotheses accepted will be 1 − β. After the A/A test, the p-value distribution should be uniform. If it skews, there are strong dependencies in the data and you can't run the analysis. That same approach doesn't work for A/B.

Even with all that, samples can be unstable over time and imbalanced. Pick the right criterion for that situation. Resampling helps with the estimation, and so does mentally preparing yourself to re-run the test if a retrospective view of the data shows sudden jumps.

Outliers — look at a box plot and remove by hand, same with duplicates. With financial metrics you can't remove them, which means the spread of values increases and confidence intervals widen too. The three-sigma rule applies — remove all values beyond three standard deviations and see how the data changes. That can cost a lot of data, which may be critical. Another approach is the Box-Cox transformation. Don't drop outliers just to apply a particular criterion — wrong move. More pragmatically: start by looking at the slices where differences are already obvious.

![Box-plot generated](https://your-scorpion.ru/wp-content/uploads/2019/09/pofdk.gif)

Generate data with `usersExport <- data.frame(n = 3:90)` and plot `boxplot(usersExport)`. You get a near-perfect chart with quantiles.

Reading a box plot: the dot or line is the arithmetic mean; the box around it has a length corresponding to the precision of the population-parameter estimate; the whiskers correspond to one of the spread or precision measures. To build a box plot, write `boxplot(variable_name)`. As an experiment, punch holes in the data with `usersExport <- usersExport[-sample(3:90, 23), ]` and see how the plot shifts.

A box plot with outliers:

![Box-plot with outliers](https://your-scorpion.ru/wp-content/uploads/2019/09/loader.gif)

Normality can also be checked just by eyeballing `qqplot()`. On large samples, tests almost always show deviations from normal. So when the data is very non-normal — time-on-smartphone or financial metrics always have a floor of zero — you need normalisation or at least outlier removal. To the numbers: a 5% difference isn't very large. On a large sample it'll be very close to 0.05. And many tests are robust to moderate deviations from normality. Even small deviations become significant on large samples — that's true for any statistical test.

```r
qqplot(rt(a, df=3), x, main="t(3) Q-Q Plot")
abline(0, 1)
```

![Q-Q plot example](https://your-scorpion.ru/wp-content/uploads/2019/09/sber_logo.png)

R-Studio drew a Q-Q plot (left). This kind of plot shows data sorted, compared against quantiles from the standard normal. Apart from the outliers, points sit more or less on a line — but they skew. So the data is distorted. Another giveaway: points sit on the line in the middle but bend at the ends, which indicates higher values in the sample than expected from normal. The plot on the right is what normal looks like.

Another example with normal data: `x <- rnorm(100)`, line with `qqline(x)`, finish with `hist(x)`.

![Q-Q and histogram, normal data](https://your-scorpion.ru/wp-content/uploads/2019/09/edi_n.gif)

Non-normal: `y <- rgamma(100, 1)`, then `qqnorm(y); qqline(y)`, then `hist(y)`.

![Q-Q and histogram, non-normal data](https://your-scorpion.ru/wp-content/uploads/2019/09/Comp-1.gif)

A practical question that comes up: how to fit a dataset into a specific range. Generate non-normal data with `x <- rweibull(1000, 2, 66)` and look at `hist(x)` — clearly not normal. The simplest transformation is the log, which compresses big deviations and pulls outliers closer to the bulk:

```r
x <- x - 5
x <- log10(x)
x <- na.omit(x)
```

Check with `plot(density(x))`; the data has shifted and Box-Cox normalisation won't work any more. A more correct fix is standardisation:

```r
finalData <- (x - mean(x)) / sd(x)
```

Subtract the mean and divide by the standard deviation; the result has mean = 0 and sd = 1. Build the chart with `qqnorm(finalData)`. Range-scaling to [0, 1] is another common interpretation:

```r
x <- (x - min(x)) / (max(x) - min(x))
```

And outlier filtering without changing the underlying data: `x[!x %in% boxplot.stats(x)$out]`. A note on the temptations: don't use `runif(x)` thinking it normalises — that just generates uniform random data. In games, outliers are often cheaters and deserve separate attention; on product metrics, drawing three sigmas and chopping the rest is wrong, because anomalous behaviour ≠ no business value. Pick the transformation based on data type and the direction of the asymmetry.

On reading a Q-Q plot: the larger the area on the blue plot on the left, the straighter the red line on the right.

![Reading a Q-Q plot — first](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-32988.png)

Normally-distributed data shows up as roughly this:

![Reading a Q-Q plot — normal data](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-32993.png)

And for intuition, a histogram can have a long distribution tail (many values) or a short one (few).

![Reading a Q-Q plot — tail shapes](https://your-scorpion.ru/wp-content/uploads/2021/03/Group-32999.png)

Before moving on to choosing tests, the in-R workflow for actually checking distributions ties everything above together. Shapiro-Wilks is the most popular tool for it — more popular than chi-square:

```r
x <- rnorm(450)
shapiro.test(x)
```

W = 0.99518, p = 0.1758. p > 0.05 → normal. Below 0.05 → reject. `hist(x)` shows the bell. UX analysis takes 0.05 as the conventional Type I significance level. The normal distribution looks visually similar to the t-distribution, which has an extra `df` parameter for degrees of freedom (values free to vary). Low df → values that swing too high or too low; the boundary is around df ≈ 30, at which the t-distribution starts looking normal:

```r
x <- seq(-3, 3, length = 450)
plot(x, dnorm(x), type = "l", col = "blue")
lines(x, dt(x, df = 1),  col = "cyan")
lines(x, dt(x, df = 32), col = "red")
```

Blue (normal) and red (t with df = 32) overlap visually.

![Normal vs t-distribution](https://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-13-at-10.20.33-AM.png)

To compare against uniform data, `runif` (r for random, unif for uniform):

```r
y <- runif(450)
par(mfrow = c(1, 1))
boxplot(x, y, notch = TRUE, col = c("gold", "darkgreen"))
```

![Boxplot — normal vs uniform](http://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-13-at-9.33.54-AM.png)

Or histograms for clarity:

![Histograms — normal vs uniform](https://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-13-at-9.43.38-AM.png)

Real data won't sit neatly in [0, 1], so rescale to your actual range (here 15 to 100):

```r
xmin <- 15
xmax <- 100
y    <- runif(450) * (xmax - xmin) + xmin
```

R has the helper stats too: `sd(y)` standard deviation, `range(y)` range, `IQR(y)` interquartile range (useful with outliers), `quantile(y, c(0.25, 0.75))` quantiles, `mad(y)` median absolute deviation.

And there's an old pragmatic check: test the equality of means and the equality of medians; if both agree, the samples are the same.

One more word on outlier removal: on real tasks you can't just delete outliers, especially in eCommerce. The only acceptable removal is observations beyond 3 standard deviations from the mean, and only if you can afford to lose your whales. Or take the log via Box-Cox — at the cost of an unnatural metric. Otherwise: bootstrap, generate two distributions, compare based on quartile difference.

For pulling summary stats out of a fitted linear model — min, max, mean, median, quantiles, standard errors of parameters, significance levels — `summary()` is the function:

```r
x <- 1:30
y <- 0.5 + 0.75 * x + rnorm(x)
ourData <- lm(y ~ x)
summary(ourData)
```

![summary() output](http://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-17-at-9.45.36-AM.png)

And when you want to compare many distributions side by side with their density visible, the violin plot is the right reach — boxplot for position, plus the underlying probability density:

```r
library(ggplot2)
x <- runif(256, 0, 1) * 0.65 / 3
y <- runif(128, 0, 5)

xbin <- cut(x = x,
            breaks         = seq(0, 1, 0.1),
            include.lowest = TRUE,
            labels         = seq(0.05, 0.95, 0.1))

df <- data.frame(x = x, y = y, xbin = xbin)

ggplot(data = df, aes(x = x, y = y, colour = xbin)) +
  geom_violin(draw_quantiles = c(0.25, 0.5, 0.75)) +
  geom_point(alpha = 0.5)
```

![Violin plot](http://your-scorpion.ru/wp-content/uploads/2019/01/Screen-Shot-2019-01-22-at-1.42.21-PM.png)

## Choosing the winner

Depending on data quantity and normality, you pick different criteria for identifying the winner. If the data is normal, use a Bernoulli test, Gaussian calculations. Look at degrees of freedom; the variant either wins or it doesn't.

![Shapiro-Wilk normality test](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-269.png)
*Shapiro-Wilk normality test*

Start with Shapiro-Wilk for normality — very powerful. It's a W-criterion: if W = 1, the sample is precisely normal. Null hypothesis = the sample belongs to a normal distribution. A small p-value from Shapiro-Wilk means there are outliers. Anything above 0.75 can be treated as normal. The function is `shapiro.test(x)`, accepting samples between 3 and 5000 observations. Generate normal data with `x <- rnorm(4600)` and run `shapiro.test(x)`:

```
Shapiro-Wilk normality test
data: x
W = 0.99947, p-value = 0.222
```

p-value = 0.222, so we accept the null. W = 0.99947 is excellent — the sample was generated as normal. To reject the null, p-value should be no higher than α = 0.05 (max 0.1). On non-normal data: `y <- rgamma(100, 1); shapiro.test(y)`:

```
Shapiro-Wilk normality test
data: y
W = 0.9829, p-value < 2.2e-16
```

p-value far below 0.05, effectively zero (R only reports p-values above the threshold 2.2 × 10⁻¹⁶). We reject the null. First check whether p < 0.05, then compare means or medians. On asymmetric data, the median reflects central tendency much better than the mean. If one of the samples is markedly non-normal, take the median as the centre and reach for the Wilcoxon-Mann-Whitney test — nonparametric, a good replacement for Pearson's chi-square. If the data has a long positive tail (revenue) and a non-normal distribution: Mann-Whitney, permutation tests, or bootstrap. Permutation tests are about the hypothesis that two samples have similar distributions.

If all samples are normal, take the arithmetic mean and one of the Student's tests (a shift criterion).

The Shapiro-Wilk limitation is the 5000-observation cap. For larger samples, Shapiro-Francia w′ test. Or Anderson-Darling, which is easier to find ready-made and can check the data against any distribution. Kolmogorov-Smirnov (goodness-of-fit) also exists, but for real problems, better to avoid it.

---

A/B tests imply two datasets, so the test runs on both samples. Not necessarily Shapiro-Wilk — could be the nonparametric rank Wilcoxon-Mann-Whitney U-test. It checks the shift hypothesis, works on any kind of sample, but obviously both samples need to be roughly similarly distributed. The test is robust and operates on ranks. Shift isn't about checking the median; it's about checking the skew of the data relative to each other. If one sample is [2,3,4,5] and the other [5,6,7,8], that's obviously a shift.

For continuous data, in simple cases the WMW / Kruskal-Wallis tests work well — their null hypotheses are about distribution and median comparison. A 50/50 split implies the Bernoulli formula, possibly a Bayesian multi-armed bandit. If you used split-testing, reach for nonparametric ANOVA — the Kruskal-Wallis test. For comparing variances, Fligner-Killeen and Brown-Forsythe are solid choices.

A Bayesian approach is recommended when there's lots of data, as the alternative to frequentist. Frequentist is about the null hypothesis and the frequency of events; Bayesian is about accepting the null and lets you work with events and causes. You can't apply both simultaneously — that leads to the Lindy paradox.

Frequentist doesn't let you make an assumption ahead of time; you rely on the test sample's data. We repeat the experiment infinitely many times to get a histogram of objective uncertainty. The hypothesis itself is checked by classical p-value. Fix the sample size before the experiment — here are two formulas for it:

![Sample-size formula](https://your-scorpion.ru/wp-content/uploads/2020/10/Group-7.png)

1. The numerator is variance (α²); the denominator is *mde*.
2. *z* is the standard error — 1.96 at a 95% significance level.
3. σ is the standard deviation.

![Variable relationships](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32946.png)

Note that nearly all variables are coupled. The smaller the effect you want to detect, the larger the sample size.

Generalisation: take a homogeneous sample and variance is smaller, the probability density function is more concentrated than at 100% available data. But conclusions from a homogeneous sample are relevant to the sample, not to all the data. Translation: smaller variance = lower generalisability.

A concrete example. Find the sample size for a proportion test on categorical data. α = 95%, *mde* = 0.05, p = 70%, z = 1.96. Then n = 1.96² × 0.7 × (1 − 0.7) / 0.05² = 3.8416 × 0.7 × 0.3 / 0.0025 = 322.

The Bayesian idea: 500 heads out of 1000 coin flips computed by C(1000, 500) × 0.5^500 × 0.5^500, since the flips are independent. If we knew all the parameters of the coin, we could predict the outcome. Since we can't, we run many observations. Bayes lets you answer the inductive question of which variant is better and by how much. Bayes always has a prior, which means you have to bring your own priors on the parameters of the process you're studying. That's essentially how designers think anyway. With Bayes you don't have to compute power and significance, but you lose the explicit understanding of effect size. If Bayes doesn't suit you, use Student's or Bernoulli on large samples. If outcomes are 0/1, definitely Bernoulli and the binomial distribution — easy to work with, interpret, and visualise.

![Student's t-test](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-270.png)
*Student's t-test*

Student's. T-test, or computing Student's t-criterion, is the simplest way to check the accuracy of the mean for data with natural values. Samples need to be independent (not paired) and normally distributed — the more results, the closer to normal. That ensures density is concentrated around the mean, which lets you conclude things about the population from sample information alone. Running it is easy: `x = rnorm(1000000); y = rnorm(1000000); t.test(x, y)`:

```
data: x and y
t = -1.1696, df = 2e+06, p-value = 0.2422
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -0.004424298  0.001117388
sample estimates:
   mean of x      mean of y
-0.0007571144   0.0008963404
```

Sanity-check by hand:

```r
m = length(x); n = length(y)
t = (mean(x) - mean(y)) / sqrt(var(x)/m + var(y)/n); t
```

Both give t = −1.1696 and p-value = 0.2422. Since we always care whether p is greater or less than 0.05, with 0.2422 > 0.05 there's no reason to reject the null. p-value is the achieved significance level — the smallest significance level at which the null is rejected for a given statistic. Results are statistically significant when p drops below 0.05 (or 5%). If you can drive it down to 0.005, even better — false positives drop sharply. At 0.05, the convention is that one-third of conclusions are wrong. In an ideal world, even 0.005 should be a rough pointer to the final decision. Physics and genetics use 0.0000003. The lower p, the heavier the weight on the alternative.

In the example below I visualised how boundaries shift with input data; the moment data crosses the boundaries we accept H1 about the presence of differences. Differences may be at the level of the mean, median, or other parameters. Boundaries can be dynamic or static.

![Dynamic boundaries](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-268.png)

The null is always about no statistical significance — i.e. equality of the population parameter to the chosen criterion's value. It's called null because negation is zero and agreement is one. For example, "all users see the banner." The alternative says not all users see the banner — i.e. it doesn't assert equality of the population parameter to a pre-specified value.

The paired t-test has fewer degrees of freedom but removes individual variation. Use it for dependent samples; it's more powerful than the unpaired version. Dependent samples are when each element in the control group has a matching element in the second group — e.g. testing the same homepage in September 2018 vs September 2019, or designer skills before and after training. With dependent samples you care how changes in one group affect the other.

```r
leftGroup  <- c(12, 13, 11, 15, 19, 15, 17)
rightGroup <- c(14, 14, 16, 16, 18, 14, 15)
t.test(leftGroup, rightGroup, var.equal = TRUE)
t.test(leftGroup, rightGroup, paired    = TRUE)
```

p-value = 0.5648 unpaired vs 0.4539 paired. Discrimination is clearly stronger paired.

Second most popular: z-test of proportions. Parametric, checks equality of means. You see binomial distribution, you reach for the z-test — e.g. for ratio / conversion metrics. Independent samples required. For comparing variances, Levene's and Bartlett's tests — both parametric; Bartlett is more powerful but demands more normality than Levene.

Next, Wilcoxon-Mann-Whitney. It tests that results of random observations from one group can be higher than in another. Nonparametric, an alternative to t-test. The idea: t-test needs normal data and produces easily interpreted results but is sensitive to outliers. Without information about normality, use Mann-Whitney — keeping in mind it won't show fine differences between samples and is harder to interpret. The reason: the t-test works by comparing actual means, while Mann-Whitney compares ranks (the observation's position in the ordered sample), making it robust to outliers. But Mann-Whitney is itself quite sensitive to variance differences, which becomes very visible on large samples.

So in practice it's acceptable to use a t-test for mean estimation on a large sample, because normality is achieved through CLT-driven shrinking of extreme values (the more elements, the smaller the variance of the mean). But the further from normal, the worse the test compares samples. T-test isn't suitable for small samples from non-normal distributions, but is fine for large samples from non-normal.

T-test is sensitive to outliers. If there's a long tail in the data — and there almost always is in real financial data — that tail will hit the mean hard, and with high variance the test result is poor. Popular metrics like ARPU, sessions, time-anything, ARPPU, average order value aren't binomial. They often resist parametric tests; long tails mean most of the money comes from few users. The bucket technique helps, but again needs a large sample. With a small sample your choices are: U-criterion Mann-Whitney, compute p-value within days with a multiple-comparison correction (you lose power); cumulative p-value; bootstrap as the simplest fix, producing a distribution of the sample mean to look at the intersection of quantiles with the given significance level; CUPED transforms data toward normality with reduced deviation from the mean, then Welch-corrected t-test; or Mood's median test, which requires a contingency table.

`wilcox.test(mpg ~ am, data = mtcars)` returns "cannot compute exact p-value with ties." If that bothers you, switch to `wilcox.test(mpg ~ am, data = mtcars, exact = FALSE)` to tell R you don't need exact p-values. W = 42, p-value = 0.001871. p-value should be no higher than 0.05.

Two real observation sets:

```r
a = c(123, 105, 147, 142, 119, 129, 130,  87, 301,  92, 177, 141,
      137, 112, 138, 128, 114, 197, 198, 210, 101, 125, 134, 214,
      110, 100, 152, 122, 144, 148, 153, 212)

b = c(154, 512, 120, 131, 124, 118, 178, 140, 136,  68, 162, 127,
       78, 106, 133, 655, 155, 169, 199, 108, 143, 341, 121, 139,
      166, 174, 184,  98, 135, 132, 146, 209)

wilcox.test(a, b)
```

Output:

```
data: a and b
W = 455, p-value = 0.4507
alternative hypothesis: true location shift is not equal to 0
```

p-value far above 0.05. Accept the null. W is the Wilcoxon statistic — as the name says, the sum of ranks in one of the two groups.

> If *p* < 0.05, the null hypothesis of no difference is rejected.

Bernoulli is similar — takes two types of data (heads/tails, boy/girl, red/black). `rbinom(200, size = 1, p = 0.68)` where p is the chance of a 1. Roughly, `rbinom` tells you how many heads you'd get from N coin flips. The example shows 200 Bernoulli trials. Two Bernoulli trials to compare, since this looks a lot like a binary A/B test outcome:

```r
varA <- rbinom(200, 20, 0.5)
varB <- rbinom(200, 20, 0.25)
```

`hist(varB, probability = TRUE, col = gray(0.9))` for both. The plots show 200 binomially distributed random numbers for trial size = 20 with different probabilities of the event of interest (p = 0.25 and p = 0.5).

![Bernoulli histograms](https://your-scorpion.ru/wp-content/uploads/2019/09/32.png)

Binomial tests are proportion comparisons. They let you check hypotheses about app uninstall rates. Small sample (100), conversion to purchase 12% — binomial test is the right pick. Student's can be applied to a Bernoulli distribution, but the sample is small; really you want > 200. Bootstrap behaves similarly here.

For typical financial data — a peak of small payments plus a long tail — and where you're comfortable taking the log of something (but not zero), Box-Cox can transform the data toward normality. A simple log of a number, though it complicates interpretation. From there you use a familiar parametric test (Student's t), look at p-value, and judge significance for closing the experiment.

The process, end to end: detect outliers with boxplot, check normality with Shapiro-Wilk, characterise the distribution with `qqnorm`, pick the analysis method. Normal data → Bernoulli, Gaussian, Student's. Non-normal (smoothed on one side) → chi-square, Bayes, Poisson. Data that ranges from 0 to tens with outliers up to hundreds is usually Poisson — used in queueing theory. From there, estimate parameters, account for them when running a chi-square test of the sample against the Poisson distribution, and you get an answer about hypothesis correctness under Poisson.

The decision flow, condensed:

- Independent samples, A/B → Mann-Whitney. A/B/C+ → Kruskal-Wallis. About shift.
- Dependent samples, A/B → Wilcoxon signed-rank. A/B/C+ → Friedman.
- Contingency table with a null you can state → Pearson's chi-square.
- Confused? Bootstrap — it'll give you a distribution.
- Normal data, A/B → Student's or Welch's. A/B/C+ → ANOVA.
- Variance difference on normal data: Bartlett, Levene, Conover, picked by how far from normal you are.
- Binomial data: proportion test.

For a graphical hypothesis check, assume data is normal and the mean is an adequate measure of central tendency — then comparing two values reduces to computing their difference. For a multivariate A/B test, compute CTR per variant and plot. The plot you want: 95% confidence interval for the difference between means and medians (bootstrap works), and check whether the interval contains 0. CTR computation: 84,343 impressions, 21,384 conversions → 21,384 / 84,343 = 25.3%.

For testing the difference between two median values, t-test does the job. One-sample:

```r
x <- rnorm(10, 5, 1)
t.test(x, mu = 5)
```

Two-sample:

```r
d1 <- rnorm(12, 5.2, 1)
d2 <- rnorm(11, 5.1, 1)
t.test(d1, d2)
```

Note: R uses Welch's, not the "vanilla" t-test, so equal variance isn't required (the standard t-test cares). To check equal variance anyway, F-test:

```r
var.test(d1, d2)
```

If p < 0.05, variances differ — use Welch's. If p > 0.05, variances are approximately equal (not guaranteed) — you can use t-test with `var.equal = TRUE`:

```r
t.test(d1, d2, var.equal = TRUE)
```

To reduce variance, use stratification (splitting users into groups, clustering to isolate clusters), low-level metric trimming, or historical data (CUPED, originally from Microsoft).

A note about non-normal samples with non-trivial means: if A/B results have very close means but one sample is normal and the other isn't, the test you reach for depends on context. You can compare means mathematically — but ask yourself what you're comparing. Nicolas Cage's film roles vs. drowning deaths after falling in pools? Or actual ARPU values? Student's wants both samples normal. The shortcut is the nonparametric U-criterion Mann-Whitney for independent samples — the main downside being that the null is stated about the median rather than the mean. Or the consulting trick: rely on CLT with a large sample, draw multiple smaller subsamples from it, and their distribution will look normal.

When data isn't normal but you still need a result: first consider Box-Cox transformation, but it's a risky path. Otherwise, work with rank-based nonparametric tests — Wilcoxon's W or Mann-Whitney U. R covers both in `wilcox.test` for two samples; for more, `kruskal.test`.

Kruskal-Wallis, like any ANOVA, returns either p < 0.05 (groups differ) or p > 0.05 (no differences), without telling you which groups differ. Example matrix:

```r
x <- matrix(runif(12 * 100), ncol = 2)
wilcox.test(x, paired = FALSE, exact = FALSE)
```

Wilcoxon compares not values but their ranking. V = 720600, with the probability of randomly getting this value under H0 ≤ 0.05 (p-value = 2.2e-16, the smallest representable positive value). Reject the null. Caveat: rank-based methods compute exact p-values poorly when there are tied values. `wilcox_test` from the `coin` package handles this a bit better via normal-approximation:

```r
install.packages("coin")
library(coin)

testDataRnd <- data.frame(
  x = c(rnorm(n = 40, mean = 14, sd = 4),
        rnorm(n = 23, mean = 17, sd = 4)),
  g = c(rep("a", times = 40), rep("b", times = 23))
)

keepIt <- wilcox_test(testDataRnd[, 1] ~ testDataRnd[, 2])
keepIt
keepIt@statistic@teststatistic
```

The last line returns the z-value from Mann-Whitney.

To compare means specifically on non-normal data, with enough observations (400+), t-test still works thanks to CLT. Or just compare medians.

For Fisher's and chi-square tests on small two-observation matrices, generate one with `matrixData = matrix(1:9, 800, 2)` — a vector turned into a matrix with columns. Assuming independent observations, chi-square or Fisher's both work; numbers must be absolute and qualitative. Chi-square for normality check: `chisq.test(matrixData)`. RStudio warns about possible incorrectness because chi-square does poorly with small counts.

Another matrix: `matrixData = matrix(1:5, 6, 2)`. Fisher's: `fisher.test(matrixData)`. Fisher's is the F-test for comparing variances of two or more general normal small-volume datasets — no rule that p-value must be a fixed value for null acceptance. Or Pearson's chi-square with Yates' continuity correction: `chisq.test(matrixData, simulate.p.value = TRUE)`. The simulations find p-value more accurately without relying on the chi-square approximation. Here p = 0.6436 > 0.05 — the sample doesn't contradict the null.

To compare medians directly, Mood's median test exists — not very popular because of a high Type II error rate. R doesn't ship it out of the box, but you can simulate. Two samples of product usage in Northern and Western Europe:

```r
x1 <- 1:30
x2 <- 1:54
m  <- median(c(x1, x2))

set1  <- sum(x1 >  m)
set2  <- sum(x2 >  m)
set01 <- sum(x1 <= m)
set02 <- sum(x2 <= m)

finalData <- matrix(c(set1, set2, set01, set02), nrow = 2, ncol = 2)
chisq.test(finalData)
```

p-value = 0.01225 < α = 0.05 — median equality rejected. Usage differs. Better to verify with Wilcoxon-Mann-Whitney — doesn't check median difference, only ranks, but checks the probability of how often X < or > Y:

```r
wilcox.test(x1, x2, alternative = "two.sided", exact = TRUE, correct = FALSE)
```

p-value = 0.0007761, much smaller than α.

The main issue: you can't trust such small data quantities. Conclusions need hundreds or thousands of observations per column.

For visualising matrices, correlograms / heatmaps via `corrplot`:

```r
install.packages("corrplot")
m <- matrix(seq(-1, 1, length.out = 9), nrow = 3)
corrplot(m, method = "pie")

K <- matrix(runif(48), 2, 24)
corrplot(K, method = "pie")
```

![Correlogram via corrplot](http://your-scorpion.ru/wp-content/uploads/2018/12/Screen-Shot-2018-12-10-at-2.41.19-PM.png)

For ordering a range of values along an axis with a nice palette, the `RColorBrewer` library is what you want:

```r
setDataYears <- c("1860:2019")
newData      <- eval(parse(text = setDataYears))

plot(newData, edge.arrow.size = .4, vertex.label = NA)
plot(x = 1:160, y = 1:160, pch = 18, cex = 5, col = rainbow(newData))
```

What counts as "nice" is subjective. For better colours:

```r
install.packages("RColorBrewer")
library(RColorBrewer)
display.brewer.all()
```

Then with a chosen palette and a bit of order-randomisation:

```r
newData    <- sample(newData)
colorsSet  <- brewer.pal(12, "Paired")
plot(newData, pch = 19, cex = 6, col = colorsSet)
```

![Date range plotted with RColorBrewer](http://your-scorpion.ru/wp-content/uploads/2018/12/Screenshot_1.png)

`wilcox.test(mpg ~ am, data = mtcars)` we already covered above; here's the full set of guidelines, with the warning that the general rule for Shapiro-Wilk still applies:

- p > 0.05: data doesn't differ from normal.
- p < 0.05: data significantly differs from normal.
- `shapiro.test(data)` → W = 0.98134, p-value = 0.961: close to normal.

But if p-value ≤ α, data isn't normal. Plot it too — on large samples even tiny deviations from normality become significant.

```r
row1 <- c(29, 30, 34, 34, 45, 43, 33, 43, 48, 23, 27, 38, 32, 36, 35, 34, 33)
row2 <- c(34, 32, 31, 35, 55, 46, 43, 45, 30, 26, 27, 23, 47, 37, 29, 30, 60)

boxplot(data.frame(row1, row2))
summary(data.frame(row1, row2))
# plots
hist(row1)
boxplot(data.frame(row1, row2))
qqnorm(row1)
qqline(row1)
```

When inspecting Q-Q for normality, look at the points carefully — they should be on one line, and quantiles shouldn't escape the −2 to 2 range.

Left-side deviations are present, so for sample-independence use chi-square:

```r
chisq.test(data.frame(row1, row2))
```

The alternative hypothesis isn't always the negation of the null — rules from the alternative determine when to reject the null. Statistics should be more extreme under H1 than under H0. You compute the sample statistic and decide whether you're in the tail; if so, an unlikely event occurred and you reject H0. In the example, p = 0.1517 > 0.05. Accept the null: the two variables are independent. If we interpret these as utility-bill-debtor counts in Eastern vs Western Europe, the null says the average debtor count is unrelated across samples.

Comparing two independent samples, you could use a t-test — it compares two group means, or one mean against a hypothetical. t-test wants normal data, depends on df, and would in principle work: `t.test(row1, row2, paired = FALSE); lm(row1 - row2 ~ 1)`. But since we already concluded the data isn't normal, switch to nonparametric. Wilcoxon or Quade outperform Student's here:

```r
wilcox.test(row1, row2, paired = TRUE, exact = FALSE)
```

The test contrasts row1 against row2 as independent. V = 52.5, p = 0.4374 — V is the minimum rank sum. The test checks whether observations in one group are 50%+ likely to be larger than in the other. This isn't median comparison; it's mean comparison.

For a quick simple example of comparing samples — usually two landing pages:

```r
dat <- data.frame(
  landing1 = c(2141, 187,  54),
  landing2 = c(1875, 201,  65),
  row.names = c("showed", "bought", "installed")
)
dat_t <- data.frame(t(dat))
prop.test(dat_t$bought, dat_t$showed)
```

Output: `prop 1 = 0.0873, prop 2 = 0.1072`. Landing 2 has a higher click-through rate. p = 0.0383 < 0.05 — statistically significant.

For sample size, power is typically 0.8; for very large samples, 0.99 is fine:

```r
power.prop.test(p1 = 0.06, p2 = 0.08, power = 0.85)
```

Effect size isn't computed — it's picked from experience. Here .02 (the difference between .06 and .08). Smaller effect → larger sample needed.

The hard part is test design. Beyond A/B, there's A/A/B, A/B/n, factorial, multivariate. MVT is for when each change is individual, isolated from others, with all combinations tested:

- Change A: underline links.
- Change B: switch buttons from outlined to filled.
- Change C: increase line height.
- Change D: add skeleton loaders.

Outcomes might be:

- A: +3%
- B: +12%
- C: −20%
- D: +1%

A classic A/B would show 3 + 12 − 20 + 1 = −4%, so the combined variant doesn't ship. MVT should give A+B = +15%, A+D = +4%, A+B+D = +16%, and so on. But hitting statistical significance per outcome needs huge traffic.

A reasonable compromise is factorial design — test many combinations but not all:

- A: A = +3%
- B: A + B = +15%
- C: A + B + C = −17%
- D: A + B + C + D = −4%

On bootstrap p-value in multivariate tests: each variant compares against each, and you apply a multiple-comparison correction to the p-value (which makes it smaller, problematically so). You don't just decide a winner — you also have to identify who's most-winner. p = 0.05 is fine for A/B; at three variants it becomes 0.025, and the required sample is astronomical. Use corrections — Bonferroni, or better, Benjamini-Hochberg (which doesn't always apply).

More on multiple comparison. A standard A/B is two independent variants at some significance level, say 5% — five in 100 winners will be wrong (Type I error). In formulas: FPR = 1 − (1 − 0.05)¹ = 0.05, where FPR = 1 − (1 − α)ⁿ. α is significance, n the number of variants relative to the base.

For A/B/C and beyond, you hit the multiple-comparison problem and Type I error rate climbs fast. Testing 10 variants gets you a 40% Type I error rate. Fix with Bonferroni: 10 variants → α = 0.05 / 10 = 0.005. Or Holm's method.

CUPED and post-stratification do reduce variance, and not only for continuous metrics. Take conversion-to-purchase, where the user just arrived and you have no pre-experiment data on them. Introduce a new covariate — 1 or 0 — for whether the user has been on the site before. If not, set the covariate to any constant. That's an analogue of stratification and sampling, both of which reduce variance.

After the test, check whether all users actually completed the target action. If your service generates temporary email addresses and the average user lifecycle is five days, the sample shouldn't include anyone who started using the service in the last five days before the experiment ended.

No anomalous spikes during the experiment — they can introduce a statistically significant contribution.

For binomial metrics, watch power changes. If both observed groups dropped sharply, that hits power. Closer to the end of the experiment, recompute and see how different power is from the start. Significantly worse? Continue the experiment, wait for required power.

P-value also has its own confidence interval. Run 10,000 A/A tests at p = 0.05 and we expect 500 to register as significant. Even those 500 have confidence intervals — confidence intervals of confidence intervals. A probabilistic approach helps: Bayes' theorem.

P-value is a cumulative quantity, and in real projects you can only orient on it when it plateaus. Check p-value outliers per day after the test, or you'll hit the peeking problem — checking results mid-test and stopping early when significance appears. The fix: adaptive confidence intervals.

Convergence is the p-value going flat. If the planned data quantity matches what you have, and the statistic has been flat for 5-7 days, the experiment can close.

Watch the late-closing trap: at end-of-test with no significant effect, the temptation is to extend. The right move is to reduce variance (CUPED, predictor subtraction, linearisation). You can't change significance level or target metrics mid-test.

You might assume the t-test belongs exclusively to academic work and doesn't apply to business problems. Counter-example: take a ratio metric like CTR. Sum of clicks divided by sum of impressions — that ratio is the conversion to click. The data is dependent, so t-test doesn't apply here.

The other type is the per-user metric. Look at the mean — was the loot-box opened or not? Identically-distributed independent random variables — t-test applies. The trick: you can use linearisation to convert ratio metrics into per-user metrics. Sensitivity isn't lost, and the linearised metric moves in the same direction as the original.

You can't fit new hypotheses to the observed difference after the experiment ends, however tempting. That's p-hacking — collect data, test many hypotheses, publish the ones at p < 0.05. If you're in research and chasing grants, fine — on your conscience. In business it's not.

A concrete case to anchor everything above. A bank issues a lot of apartment-renovation loans, and the rate of early repayments suddenly jumps. Qualitatively — ask. Quantitatively — check the data for normality, then run a t-test if it's normal. Form two samples (early-close vs not) and query the credit bureau (BKI) for statistically-significant differences in loans opened outside your bank after they took one with you. That's the typical applied workflow when a metric shifts unexpectedly: don't start from p-values. Start from where the data is, what the comparison group should be, and what external signal could explain the move.

## Beyond R: Excel

Any analyst today — UX, business, dashboard-builder, researcher, developer — should be able to work in R and Python. But basic Excel still helps solve many tasks fast. Product work demands quick conclusions on popular metrics — DAU/MAU, conversion to registration — and metric values shift constantly. A common practice is using the mean for all primary KPIs when the distribution is normal — fast preliminary conclusions. You can identify distribution type using standard deviation. In Excel that's the **STDEV** function (`=сроткл` in Russian). The formula: STD = √[(Σ(x − x̄)²) / n] — root of the sum of squared deviations from the mean, divided by sample size.

![Standard deviation example in Excel](https://your-scorpion.ru/wp-content/uploads/2019/09/55-2.png)

In the example above, despite almost identical means, the second variant has huge swings around the mean. The first deserves more trust.

Excel has many other fine ways to visualise data — pivot tables especially. They work by virtually grouping rows with the same product names; the group gets summed. Convenient for aggregating data into a report.

---

What matters most in running tests is the ability to draw conclusions from numbers, think critically, draw conclusions from historical data, generate hypotheses, reason — to have a sense for what's rational and what isn't. Mathematical analysis only helps you avoid mistaking fiction for truth; the final business decision is still made by a person.
