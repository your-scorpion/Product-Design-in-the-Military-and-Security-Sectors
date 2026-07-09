# Design on the Edge of UX. The Things People Rarely Write About.


Being a designer is not the easiest job in the world. Take, as an example, the process of getting a mockup approved. You know that feeling when a design slowly degrades under a hundred rounds of edits? Of course you do. So let's look at how to make design approval easier and faster. Start from this premise: the work has to be done quickly, to a high standard, and within the brief. If you're producing garbage, there's no point applying any of the methods below. But if you do quality work, then it pays to master the less obvious techniques for getting a mockup signed off — techniques that go beyond simply executing the work well.

Your relationship with the client should be very close, almost intimate. Bring the client into the development of the mockup from the very beginning; there's nothing to be afraid of in that. And never stop explaining that every decision you make carries a specific business rationale and is guaranteed to return the investment. I'd guess the workflow at your job looks something like this: the designer draws something, sends it to the studio's account manager, and the manager forwards it to the client with a note that says "a new creative concept." **That approach is guaranteed to produce dozens of revisions.**

Everyone involved in reviewing a mockup needs to understand their role in the discussion. The CEO has no business debating the line weight of an icon, and a junior account manager shouldn't be pretending to hold unimpeachable knowledge of business processes. Before you start, spend time planning the work — ideally in a form the client can see. When the client watches their edits push the deadline out, they become less nitpicky, and it also becomes much easier to bill for the extra requests. Personas and scenarios are enormously helpful for convincing people that the decisions being made are the right ones. To strengthen that persuasion further, there's a useful formula: **Trigger + Arguments + Benefit**. For instance: you need the best UX in banking; our people have launched three international neobanks, so you can be confident that every best practice in banking software will be applied here.

## Think about the business first

Business comes first. You test the hypothesis with questionnaires and prototypes — and the goal is not to have people merely *say* they'd pay, but to have them actually hand you real money for a product that doesn't exist yet. This is what finding your target audience really means: what problem you solve for them, and whether they'll pay for that solution right away and pay well. You need to know precisely how much money will be earned and how, on an eight-week horizon. If you want to make 400,000 rubles in two months, then profit in the first week can't be lower than 50,000. That's why goals are set week by week. Think early about where the traffic comes from, because whoever owns the traffic owns your business. If you can't be the source of traffic yourself, then you absolutely must go and talk to the decision-makers on the side of whoever does control it. At that point you'll be selling by solving someone else's business problem. A very common startup mistake is to build the product first and only then try to monetize it. By the time a couple of years have gone into the product, the business is still sitting at the zero mark.

### Reading your traffic, and spotting the fake kind

Once you've located your traffic source, the next discipline is analyzing it properly — and this is where a lot of mobile products quietly bleed money on traffic that was never real to begin with. For segmentation on iOS, the workhorse is Apple's Identifier for Advertising (IDFA), which you feed into a [look-alike](https://developers.facebook.com/docs/marketing-api/lookalike-audience-targeting/v2.5) audience. IDFA is a dedicated advertiser identifier that every iOS app can read; more sophisticated users can allow or deny its use (in which case it resets), or pay for Private Relay. When you have no access to IDFA, you fall back on building a fingerprint: IP address, locale, device model, screen resolution, light/dark theme, accessibility settings, font size. A look-alike is Facebook's system for finding an audience closely resembling one you provide; ideally you feed it your repeat buyers. If your orders aren't regular, an audience of people who placed an order in the last month (ideally weighted by LTV) can work, as can people who added something to cart or favourites, or simply spent a lot of time on the site.

RTB is the other lever: you buy display inventory at auction, and because RTB focuses specifically on impressions to your target visitors, the best advertiser bid is shown to the users most likely to care. Cross-referenced with IDFA, this tells you *when*, in *which geographies*, and inside *which niche products* the paying players surface — and you concentrate your traffic there.

Fighting fake traffic comes down to funnels, short-term signals (everything in a user's first 24 hours: finishing the tutorial, registering, in-game achievements), and long-term signals (retention, ARPU). Everyone starts with the ARPU accumulation curve; ROI is invaluable. The key is to analyze your *sources*, not just the aggregate traffic — ask your partners for SubIDs. On every analytics metric, an organic user almost always outperforms a paid user arriving from a banner. You also have to know your whales and subtract them from the analysis (for gamedev, a whale is anyone above $100 LTV). Together these let you read your traffic sources correctly and avoid reputational risk.

A concrete version of the problem looks like this: you're buying clicks for a new product from a contextual ad network, your click counter and your analytics both register the clicks, yet the bounce activity is uniformly red and not a single live user shows up. Where do you dig? That pattern is the signature of click fraud by site owners. In your analytics, build a report filtered to zero-duration visits and you'll see where the fraudulent clicks originate; look at the CTR of those placements — where the money was spent but no conversion happened — and exclude the bad ones via the "blocked placements" setting. Keep one definitional trap in mind: in Google Analytics a "bounce" means the user didn't navigate to a second page, even though they could in principle sit on a single page for an entire day; some other analytics platforms are stricter, treating any session with under fifteen seconds of interaction as a bounce.

### Giving your metrics a structure

People often ask how to lay out and structure their metrics — they know about the "metrics pyramid" but rightly sense it isn't universal. The simplest cut is to group metrics by department: marketing (CPV, CTR, Open Rate), business (GMV, LTV), and product (PR, CR, Churn Rate). You can also group by purpose of use: running the business, acquisition, PR, sales, retention. Or by stage of the user funnel, or by time horizon — leading metrics that speak about the future versus lagging ones that describe the past. There's no rigid boundary here; any metric can be a leading indicator for some other metric. Just bear in mind that some metrics are for working with and others are for presentations, and the latter can quietly become a department's targets.

## Develop the visuals gradually

Build the visuals up step by step, and if a visual doesn't solve the problem, throw it out without sentiment. You'll draw another one, this time paying closer attention to the client's actual tasks. It's important to understand the core mechanism behind how people receive criticism: it's a defensive reaction to a perceived threat against your property. You regard the results of your work as your property — and legally that's correct — but a pretty picture on its own doesn't sell, and a mockup has to be reworked as many times as it takes to reach a *sellable* result. Still, before you bin your work, answer one question honestly: is it really that bad? If you're a strong specialist, your solution ought to solve the client's problems. If it does, defend it. Over time clients will come to see you were right and will start listening to you — but if you can't forecast the results of your work in monetary terms, change profession. And don't forget that the client is duty-bound to curb any attempt by the designer at artistic self-expression at the expense of meaning.

There are, however, clinical cases where your arguments are useless. The client's life experience forces them to reject logic. The client asked for green text on a red background? The designer cries all night and tries to justify the logic behind one or another way of presenting the information, but the client overrides that logic because his own experience matters more to him. If you can't teach the client, you'll have to do what you're told. Account managers very often don't know how to teach a client and won't allow the designer to do it either, which leads to a miserable outcome: the designer gets accused of incompetence and unwillingness to do the job, and the client gets quietly reclassified as an a\*\*\*ole.

## You're working in a team

You're also, in all likelihood, not working alone but as part of a team. It's long been established that small, well-chosen groups handle a task best. The ability of the whole group is not the sum of its members' abilities; the multiplier on a group's capability is its cohesion and the social bonds within it. Groups with greater "social sensitivity" produced the best results, while groups dominated by a single individual are always less cohesive and less effective — a one-man show. Worth noting: the more effective teams were those that included women, because women tend to have far greater social sensitivity.

## Automation, or "just write a script for it"

I've automated a great deal: prep for print, [photo processing](https://your-scorpion.ru/script-photoshop/), slicing up graphics. And I came to understand that automation is not an end in itself. You should only try to automate something when it's justified — by reducing the designer's workload, by reducing the errors that creep in, by increasing the designer's throughput, or by adding genuinely new capabilities.

## Choosing the right image to sell with

We've covered the general shape of working well; now let's learn a bit more about how to make your clients richer. **How do you pick an image to sell with?** A good designer knows that emotions are, for the most part, personalized — for each person they attach to images that only that person fully understands. Not everyone associates red with sexuality or white with purity. There is no set of physiological methods that influences emotion through sight and works on 100% of people, but it's still essential to use the commonly accepted rules, because they at least cover a large portion of the audience.

Here it helps to remember our animal nature. Even though the average human being is on a couple of "harmless" medications and loves constructing chains of logic, emotion still rules over reason in us. *A human is an animal.* Over the course of evolution, our brain acquired "three layers" for dealing with the world: the first is logical thought; the second, the middle one, handles emotion; the third, the oldest, governs basic survival — food, reproduction, safety. That oldest, third layer takes priority over the rest. Recall Maslow's pyramid, which many people know from pickup-artist courses. The third layer of the brain is the base of that pyramid: food, sex, and danger. These are the strongest influences on a person; throughout the day people most often think, "Can I eat this?", "Can I have sex with this?", "Can this hurt me?" It happens so frequently that it has long since become automatic.

[![Maslow's pyramid of needs](https://your-scorpion.ru/wp-content/uploads/2014/06/Piramida_potrebnostey_Maslou1-300x245.jpg)](https://your-scorpion.ru/wp-content/uploads/2014/06/Piramida_potrebnostey_Maslou1.jpg)

The third layer was used widely by the acknowledged masters of marketing: Steve Jobs asked for designs to be made so delicious you'd want to lick them — which is exactly how the Aqua interface in Mac OS X turned out. Apple's design of that era really was very candy-like, a nod to food.

Among the most blatant ways to use the third layer is the formula "woman + your product." It works always and everywhere. It's very useful to feature tidy, attractive, well-proportioned people in photographs — people whose appearance promises excellent continuation of the species. And in any agitational poster you can always find at least one of these symbols: a bottle, a weapon, children, a hand, a bird, a woman, a president, a finger with a face on it.

Slightly more sophisticated are the patterns for forming a unique selling proposition:

1. **Creativity + product = a big budget.** M&M's, for example: melts in your mouth, not in your hand. A simple candy that builds an image and a story around itself through expensive ad campaigns.
2. **The best + product.** We make the best buns, or the best handymen-for-hire. The law is fairly strict about the word "best," but that's easy to work around.
3. **Fear + the absence of a competitor's product.** Lose weight without the gym, become a programmer in a week, tasty food without meat, English without rote memorization.
4. **Hidden procedures.** Three stages of glass tempering, extra inspections on a used car.
5. **Guarantee:** of employment, of quality, of results, of a low price.
6. **Value + product** = a cream with a vitamin complex, seedless tangerines, Italian husbands with no language barrier.
7. **For someone in particular.** Food for children, games for adults, a new life for the weary.
8. **Product + a property.** A clinic with the newest equipment, a phone with water protection.
9. **One of a kind.** Usually tied to geography.

A small experiment: food delivery in 40 minutes, free if it's late. Does that count as a USP? It's a value proposition, because delivery is a continuation of the user's case — the user's request is to *eat*, not to receive a delivery quickly. Or take the M&M's line, "melts in your mouth, not in your hand." The user wants to snack on something tasty, so this leans toward a USP — and, as a bonus, you won't get messy while eating. An advertising slogan is a USP; a value proposition is the actual solution to the problem. The USP is built on top of the value proposition. Value first, advertising second.

## Recognizing danger is the hard part

Things look a little less rosy when it comes to triggering the recognition of danger. Marketers learned long ago how to use food and sex to influence consumers, but to convey danger they stubbornly keep appealing to the newer layer of the brain. In most cases you end up having to decode what's even depicted on a hazard warning sign. And yet it's specifically the image of *someone else coming to harm* that draws attention to a potential threat. Aristotle already noted in *Rhetoric* that using fear implies the danger threatens *you*, and threatens you *now*.

The first layer of the brain should also be put to use, through cause and effect. First you create the precedent, then you resolve it. Steve Jobs first tells you that the problem with modern smartphones is the keyboard, useless most of the time — and then he presents a phone without one, the iPhone. You use this trick too, every time you go begging for a raise: you always explain *why* your salary should go up. If you don't, you'll only get the raise through a very lucky alignment of circumstances.

So: we've recalled Maslow's pyramid, chosen an image, written a short hook and some body copy. Your flyer is ready. What is the flyer's function? Either to inform or to persuade. Anything that doesn't serve one of those two goals can safely be called clutter and removed from the layout.

Let's expand the list of the most popular themes for selling: **sex, money, entertainment, gossip, freebies, a chance to show off one's erudition, violence, and the promise of fast money.**

## Higher needs and lower needs

We can go a step further and remember that Maslow split needs into lower and higher. Actions taken to win the respect of others are a *lower* need, which includes the desire for status, recognition, fame — to be famous, or at least to be the centre of attention. A *higher* need is self-respect, including the motivation to develop one's own physical strength, knowledge, competence, independence, and freedom. The lower need is always subordinate to the higher one — as in the Asian philosophy of the martial arts, where what was cultivated was not the desire to win, surpass, or achieve, but the struggle with oneself, through development. All of this is captured by one simple word: self-actualization.

But self-actualization isn't always about self-improvement. Two fine researchers, Taylan Urkmez and Ralf Wagner, studied the phenomenon of relieving stress through shopping. They found that the positive effect of shopping is extremely short-lived and comes with a sequel in the form of guilt. You can draw a direct analogy to the addiction created by purchases inside a mobile app.

## A few important fundamentals

**Fitts's law** states: "the time required to acquire a target is a function of the distance to the target and its size." More plainly: the bigger an object is and the closer it sits to the mouse cursor, the faster a person will click it. But Fitts's law holds little value without **Hick's law**, which states that the reaction time in choosing among some number of alternative signals depends on their count. More plainly: the more items there are to choose from, the longer the user takes to decide. This is why the standard advice — group your elements, no more than five menu items, and so on — is an incontestable rule of UX.

Fitts's law is captured by a clear formula. **MT = a + b · log₂(2A/W).** Where:

- **MT** = the movement time required (for instance, moving your hand to the screen)
- **a, b** = situational parameters that vary by case
- **A** = the distance of the movement from the start to the centre of the target
- **W** = the width of the target along the axis of movement

### Should you tell the user a gesture exists?

A fair UX question follows from these laws: should you explicitly signal to the user that a given control supports gestures — and is that needed for some gestures but not others? Start with the standard gesture vocabulary: tap, double-tap, drag, pinch, spread, press-and-hold, press-and-drag, and rotating the screen (yes, that's a control method too). Every one of these owes its existence to touchscreens, and their convenience comes from being as close as possible to a physical action; the tactile and visual feedback on screen should match accordingly. Because phone screens are small, gestures also help conserve precious space. These you don't need to teach — they're already baked into the user's experience.

Now picture typical user behaviour: a person skips the tutorial and then figures out the interface intuitively. So you *do* need to teach the less common gestures, but you do it through progressive disclosure — helpful tooltips with little arrows that appear during use. And the teaching takes real effort, because it has to travel: sensory memory → short-term memory → long-term memory, and it's that final step you have to concentrate on when teaching a new gesture. Show an example, let the user repeat it without a prompt, and correct them after some time has passed. If a person merely *scanned* the information, that's sensory memory; if they paid attention to it, that's short-term memory (about 20 seconds, no more than five elements).

## Cognitive resistance

Let's bring in another quote from the greats. The classic Alan Cooper introduced the concept of "cognitive resistance."

**Cognitive resistance is low** when a person, looking at an object, immediately grasps what function it can perform. You can pour water into a bottle, and you can do someone an injury with a brick.

**Cognitive resistance is medium** when a person understands reasonably well how an interface works. The plus button on an air conditioner is almost certainly going to raise the temperature, and that's easy to learn.

**Cognitive resistance is high** when a person genuinely struggles to make sense of the interface. You can't play chess without learning the rules first, just as you can't work out how a car battery charger operates on sight alone.

According to Cooper, almost all computer software carries an extraordinarily high level of this resistance. People react entirely naturally: they take only the minimum slice of a program's functionality they need and ignore the rest. It follows that chasing ever more features is not always justified. This, incidentally, is exactly the kind of insight that explains why people still cling to ancient browsers — and it points the way to giving them something more convenient instead: not more buttons, but a lower wall to climb.

## The reptilian brain

One more useful theory. Our brain divides into three parts, and the most ancient region is called the "reptilian brain." It governs the innate, automatic behavioural patterns responsible for the survival of the species through running, eating, mating, and fighting — the so-called rule of the four F's. To decide whether to run, mate, fight, or eat, the brain analyzes an object: if it's familiar, it's safe and can be engaged with — a fact many marketers exploit with great success. Any new, successful product should *feel* familiar; and if a product offers nothing new, then it should *feel* new.

## A small aside for clients: where do you find a designer like this?

Where do you find a designer who can create a unique product and possesses all the necessary knowledge? There's no single answer. On freelance marketplaces it's very hard to find even a competent layout artist; a loud name and years of experience tell you nothing; and large studios don't always have anyone on staff who is both sufficiently talented and not yet crushed by routine — they frequently outsource their creative projects, while the potentially brilliant designers get tossed into pass-through staffing. Not everyone who lists a six-figure salary on their résumé is actually worth even a courier's wage. Look at the portfolio and at the person — at their knowledge, at what they can make with their own hands — not at the track record and the number of likes. And once you've found such a person, hold on to them and create the conditions for them to work. If you don't make a mistake here, this one person will make you more money than an entire department of sales managers.

---

## Validating what you build: the statistics a designer actually needs

Everything above assumes you can *test* a hypothesis honestly. In practice, that means knowing whether your numbers are telling you the truth — and this is where most designers quietly fall over. So here's the practical core of it.

### Do you even have enough data?

The first thing to check is whether your funnel holds enough unique users to say anything at all, using the logic of a confidence interval.

Take a real example. On the homepage you have desktop visitors — 15,260 uniques, of whom 5,212 reach the end of the funnel. Mobile is similar: 14,230 uniques on the homepage converting into 5,040 requests.

To build a confidence-interval picture you need three things. First, the **confidence level** (your probability), usually 95%, but it can be 99% or 90%. The formula is P = 1 − α, so at P = 95% (0.95), alpha is 5%. Second, the **sample size (n)** — everyone who entered the funnel, i.e. 15,260 for desktop and 14,230 for mobile. From subjective experience, you ideally want no fewer than 25,000 visitors per group; the exact figure depends on how significant you expect the results to be. Third, the **conversion**: for desktop that's 5,212 / 15,260 = 34%, and for mobile 5,040 / 14,230 = 35%.

You can lean on an online calculator like [abtestguide.com](https://abtestguide.com/calc/). Its *Pre-test analysis* tab tells you whether you have enough data, taking the unique visitors expected per variation, the number of expected conversions for the control, and the expected uplift (the larger that uplift, the smaller the sample you need). Its *Test evaluation* tab finds the statistical significance between two samples — you enter visitor counts and conversions for version A and version B, where "Visitors" is the people who arrived and "Conversions" is the count of the action you've defined as your goal. With that you learn whether your data is statistically significant and whether the funnel holds enough of it. **Statistical significance** is the value at which the probability of this result (or one even more extreme) arising by chance is small.

### Computing the confidence interval by hand

A **confidence interval** tells you how stable a measured value is — how close the value will be if you repeat the measurement or experiment — which is why it should sit in the middle of the graph.

What you want is a *representative* sample: one that meets defined criteria. In our case the criterion is 95% significance — a 95% chance the data is correct — and that's the figure we carry forward. (Recall the conversion: 34% desktop, 35% mobile.)

The Z value chosen for building the interval is called the critical value of the distribution. To build a 95% interval you choose α = 5%. The less probable values sit at the edges, the most probable in the centre; the confidence probability is the area under the curve, shaded green, and it's also the P-value — the probability of the observed result arising by chance. The Z-score (the critical value) comes out at 3.1069 here, expressing how many standard deviations you are from the mean. For intuition: the slice of the normal curve between Z = −1.96 and Z = +1.96 contains 95% of all cases.

You can also compute the standard deviation yourself. Suppose you want the lower and upper bounds of the confidence interval for note-reading time, with a mean of 555 seconds and a standard deviation of 26 seconds. We've already settled on 95%. The formula is Z(α/2) · σ/√n. To get Z(α/2): divide 0.95 by 2 to get 0.475, and look that up in a Z-score table to find 1.96. Take the standard deviation (σ = 26) and divide by the square root of the sample size — √5,260 ≈ 123.5 — giving 26 / 123.5 = 0.210. Multiply 1.96 × 0.210 = 0.4116, which is your margin of error; but that's only half the interval's length, so double it: 0.4116 × 2 = 0.8232. So your reading time is 555 seconds ± 0.210, and you've worked out the standard deviation on your own.

### Drawing the conclusion

Comparing the two desktop funnels, variation B (35.87%) comes out 5.01% above variation A's conversion (34.15%). That lets you be 95% confident this is the result of your actions and not chance.

![Desktop A/B comparison](http://your-scorpion.ru/wp-content/uploads/2017/04/shot_170428_124747.png)

The mobile picture is the same: variation B (37.19%) is 5.00% above variation A (35.42%) — again, 95% confidence that this came from your actions rather than luck.

![Mobile A/B comparison](http://your-scorpion.ru/wp-content/uploads/2017/04/shot_170428_124838.png)

### Sizing the sample before you run

You don't always want to lean on a calculator, so it helps to size the sample yourself. When the sample size is equal across variants, with confidence at 95% and statistical power (1 − β) at 80%, I sometimes use:

**n = (16 · p · (1 − p)) / Δ²**

where Δ is the minimum detectable effect in %, p is the baseline conversion, and n is what we're solving for — the number of participants per group. For example, to detect a 1% effect at a 9% conversion:

```
n = (16 · 0.09 · (1 − 0.09)) / 0.01²
n = 14,560
```

The calculator route is the expensive one and gives you little room to manoeuvre — want to pick bootstrap over a t-test? You can't. Nor can you pass client data to a third party. So it's not a bad idea to build your own calculator on R → Shiny → Shinydashboard. A reasonable stack: dplyr, data.table, zoo, lubridate for data manipulation (if `floor_date` doesn't scare you); plotly and ggplot for visualization; DT and rpivottable for tables; and feather for fast work with data frames.

There's a companion formula for the confidence interval itself, where P is the baseline conversion, n the number of participants per group, and Z the z-score (use 2 for 95% accuracy, or 3 for 99.8%). A worked example: start with what's under the square root — 0.1 · (1 − 0.1) = 0.09; 0.09 / 3,889 = 2.314 × 10⁻⁵, whose square root is ≈ 0.004; then 0.004 × 2 = 0.008. So the confidence interval is 10% ± 0.8%.

### Which parameters the confidence interval actually rests on

A confidence interval tells you whether a value falls outside the band of acceptable values for a metric — in other words, whether you need to react to your A/B result at all. Four concepts do the work:

**Statistical power** — measured as a percentage, accounts for the difference between variant A and variant B; below 80%, don't trust it. **Sample length** — bigger is better. **Confidence interval** — stability: if you grow the sample, the result should stay put. **Statistical significance** — the probability that your test reported a difference between A and B that doesn't really exist. The optimal level is 95% (confidence probability 1 − α), at which the error probability (the P-value) is the remaining 5%. Statistical significance is precisely the probability of a Type I error — results can be statistically significant yet still untrustworthy if you picked the wrong statistical test.

A simple illustration: 18% of users subscribed to the newsletter; the calculator gives a confidence interval of ±1.7%, telling you that with 95% probability, under any scenario, between 16.3% and 19.7% of users will subscribe. Show a different group the changed design and 19% subscribe, with an interval of 17.7% to 20.7%. The smaller the overlap between the two intervals, the higher the reliability of the result; here the overlap works out to 2.7% against our 5% P-value, so the test can be trusted.

![Overlapping confidence intervals](http://your-scorpion.ru/wp-content/uploads/2018/02/ezgif-2-18ea2bd22f.gif)

And don't put 100% faith in any calculator — it won't choose the distribution shape or the test for you, and it won't clean or transform your data either.

### When the data is a survey: p-levels, distributions, and the t-test

Survey data deserves special care. Imagine you ran a SUS questionnaire across 20 respondents: last year SUS was 59 with a deviation of 2, this year it's 68 with a deviation of 2.7. Is the null hypothesis still valid?

SUS testing exists to compare two random variables — to prove the presence or absence of a difference between them. You presumably took `mean(2017)` and `mean(2018)` and got 59 and 68, but the mean alone, even with the deviations, doesn't tell you which result is *better*. Because the data is quantitative and non-binary, you use a **t-test**, the best fit for comparing two means with fewer than 30 observations. To check whether SUS really is 59 (the null hypothesis) you compute the **p-level of significance** — the smallest significance level at which, if p is below 0.05, you reject the null. The significance level is the probability of rejecting H₀ when it's actually true: a Type I error.

First, a homogeneity check: assume we ran Cochran's and Bartlett's tests and established that the data is homogeneous.

Then the **distribution**, where every study of a quantitative parameter begins. It's either bell-shaped and normal — Bernoulli (lots of data), Gauss, Student (little data), Bayes — or skewed, smoothed toward one side, which points to Poisson or Chi-square. If the distribution isn't normal, use the Wilcoxon test in place of Student's. Shapiro above 0.75 indicates a normal distribution. To see which you've got, plot a plain bar chart.

Since the raw data wasn't provided, we simulate a normal sample with `rnorm` — 20 observations, mean 59, standard deviation 2:

```r
d2017 = rnorm(20, 59, 2)
f2018 = rnorm(20, 68, 2.7)
```

Run `shapiro.test(d2017)` and `shapiro.test(f2018)` to get the t-statistic. A high p-value here would support the alternative hypothesis — that the distribution is *not* normal. We get W = 0.95 in both cases, so the distribution is normal. The confidence interval is admittedly on the low side, but the closer W is to 1 the more confidently you can call the data normal — and the crucial thing is that it's not below 0.75.

![Shapiro test result](https://your-scorpion.ru/wp-content/uploads/2018/06/mask.png)

The graphical route is better, especially for designers:

```r
hist(d2017,                                 # one-dimensional data
     breaks = "FD",                         # binning method
     col   = "gray",                        # histogram fill colour
     main  = "Data from the sample participants",  # plot title
     xlab  = "SUS, $",                      # X-axis label
     ylab  = "Frequency")                   # Y-axis label
```

![Histogram of SUS data](https://your-scorpion.ru/wp-content/uploads/2018/06/Android.png)

Or, better still for small samples:

```r
plot(density(d2017))
plot(density(f2018))
```

![Density plot](https://your-scorpion.ru/wp-content/uploads/2018/06/gik_ikt.png)

The sample does look normally distributed. But a caveat: the chance of a Type I error is high because the sample is so small — basing a decision on figures from just two studies is a dubious idea — so re-check once more:

```r
qqnorm(d2017, main = "")
qqline(d2017, col = 2)
```

![Q-Q plot](http://your-scorpion.ru/wp-content/uploads/2018/06/critical.png)

The line runs through the quartiles; if the points lie on it, the distribution is normal. Another criterion: if the data range fits within −3 to 3, then 0 is the centre and the standard deviation is 1. Age measured from 0 to 100 is often standardized to −3 to 3 with the centre at zero; once different datasets are brought to that same −3-to-3 range, you can compare observations across them.

On to the **confidence interval and standard deviation**. A confidence interval is the range within which a parameter's value (mean, median, and so on) may lie; building it requires the variance and the standard deviation. The standard deviation — computed in R with `sd(f2018)`, the square root of the variance — characterizes the spread; the larger it is, the more the mean can stray from the true value.

Now use the t-test — Student's criterion — which gives you the p-level of significance, the probability of error when rejecting the null (a Type I error):

```r
t.test(d2017, f2018)
```

Or, with `var.equal = TRUE` for homogeneous variances and `paired = FALSE` for independent samples:

```r
t.test(d2017, f2018, var.equal = TRUE, paired = FALSE)
```

We get a p-value well above 0.05 — no surprise, since the two samples are homogeneous. When p exceeds 0.05 you accept the null hypothesis, and that's the answer to the question. Had p been below 0.05, you'd reject the null in favour of the alternative.

![t-test result](https://your-scorpion.ru/wp-content/uploads/2018/06/Cup-2.png)

It's possible we used the wrong mathematical machinery — there's no guarantee we guessed and adapted the distribution shape correctly. Given how much had to be assumed, I wouldn't trust the result of an analysis like this and would decide based on my own expertise instead; the odds of a Type I or Type II error are high. A Type I error (5%) is rejecting the null when the alternative is in fact true, given that the null holds. A Type II error (20%) is when the alternative is true but you accept the null — there *was* an effect, and you failed to detect it. And, again, all of the above is only a surface-level t-test, fine when you've no time to dig deeper and you accept that survey data is very noisy. For money, the t-test won't do.

### A note on overlaying the curve onto the histogram

It might seem cleaner to lay the smoothed curve straight onto the histogram for a more visual read. In a histogram, each "brick" is normally placed so that the observation sits at the centre of its base; if one observation gets pushed hard to the right edge of a brick, it inflates that brick's height and you get irregular peaks. You can smooth this out with a kernel function, in which case the line over the histogram becomes a kernel density estimate, and in *that* form, yes, you can use them together. In R:

```r
d2017 = rnorm(15, 21, 2.3)
f2018 = rnorm(15, 23, 2.7)
x <- c(d2017, f2018)
d <- density(x)
plot(d)
```

Here `density(x)` computes the kernel probability densities for the values of x.

![Kernel density over data](http://your-scorpion.ru/wp-content/uploads/2018/08/shot_180817_104142.png)

But it turns out there's no point overlaying that curve onto the histogram — it's the same data in two representations.

### "Where's the sample size, then?"

People get tripped up here: they understand that expected uplift is the expected growth (the larger it is, the smaller the sample you need), and that the *Test evaluation* tab finds significance between two samples — but then ask where the actual required sample size is. Uplift is better thought of as the efficiency of the interaction. The bad-and-simple route is [abtestsize](https://abtestguide.com/abtestsize/). The proper way is to find the power of a t-test, the required sample size, or the minimum effect size in R with `power.t.test()`:

```r
power.t.test(n = 15, delta = 0.5, sig.level = 0.05)
```

```
Two-sample t test power calculation
          n = 15
      delta = 0.5
         sd = 1
  sig.level = 0.05
      power = 0.2619313
alternative = two.sided
```

That's a poor power of 0.25. Power should be at least 80% — it governs the probability of a Type II error, i.e. the probability of *not* being wrong. Grow the sample to 64 and confirm power rises to 80:

```r
power.t.test(n = 64, delta = 0.5, sig.level = 0.05)
```

Another example — what's the power of a one-sided t-test at significance 0.02, with 95 observations per group and an effect size of 0.75?

```r
install.packages("pwr", depend = TRUE)
library(pwr)
pwr.t.test(n = 95, d = 0.75, sig.level = .02, alternative = "greater")
```

Or: a two-sided test at significance 0.01, total sample 450 per group — what effect size can you detect at power 0.95?

```r
pwr.2p.test(n = 450, sig.level = 0.01, power = 0.95)
```

And if you have many samples and you're quite rightly using a one-way ANOVA, you can size each group's sample when you have 35 groups, aiming for the desired power of 0.92 at significance 0.05 with an effect size of 0.25:

```r
pwr.anova.test(k = 35, f = .25, sig.level = .05, power = .8)
```

Every answer returns n = *some number*, the volume to gather for each group. And when comparing two samples, choose Welch's t-test.

### Non-homogeneous samples, and moving to A/B/C

What if the samples aren't homogeneous, or next year a third variant joins and you end up with A/B/C testing? If a sample fails the normality test, normalization can help: look toward a logarithm, an exponential output, the Box-Cox correction, and a third option, bootstrap, for ratio metrics. Working with the logarithm, every value between 0 and 1 turns negative and large values get pulled down; the right tail of the graph flattens toward 0 while the left builds between 0 and 1. It suits transaction data well, with its long tail of large values — though note you should recenter the data to zero before taking the log.

Then the variant factor. Say you have variants A, B, C. Using Student's t-test to check each variant against one reference means multiple comparisons, which inflates the chance of a Type I or Type II error. To compensate, apply a multiple-comparison correction — Bonferroni reduces the error. You can also use analysis of variance (ANOVA) to find the significance of the difference between means, or Kruskal's rank-based methods. When there are many factors — and there always are — you want two metrics: the sum of squared residuals (the larger, the better that combination of factors describes the metric) and the transformed F-criterion read against a table of p-values (the smaller, the better). As always, homogeneity of variance matters. Or simply reach for the Mann–Whitney U test or the Kruskal–Wallis test, the two most popular non-parametric tests for non-normally distributed data; they'll tell you whether the difference in distribution is significant, just not *where* the difference lies.

A live version of this: you're running an A/B test on a small site's homepage to gauge a promotion, looking at four months of data. You already have a paying-user conversion with a median of 13.49% and a standard deviation of 1.9%. You expected a 20% lift to 16.18%, but got 15.44% — should you read that as a rejection of the null and the effect of seasonality or third-party updates? And in R, should you take `power.prop.test` for p1 = 0.1349, or account for the deviation and take 0.1539?

As an analyst, I'm no fan of means — but here you should *not* use the median; use the mean. If you take overall quarterly conversion, how will you assess the noise, by period rather than by users? That just lowers power. Start by taking a sample of the promo users specifically — the metric comes out less noisy, you test a specific change on specific users, and you get a result faster. Sensitivity is easier to measure on slices, like old versus new users. Then settle on your null hypothesis. If your metric is distributed exponentially, you don't want a parametric test; you want one that compares the distributions of the metric rather than the means — for instance the rank-based Mann–Whitney U. You reached for Student's t-test, but it needs independent samples and a normally distributed population, and you can't be sure of normality when you don't know the variance and mean of the population.

### Can ANOVA also find a feature's correlation with the target?

Given that ANOVA can size a sample, it's tempting to ask whether the same approach is good for detecting a feature's correlation with the target. But the p-value has nothing to do with improving your target metric. Generate two random samples that will almost certainly be statistically insignificant:

```r
x <- c(rnorm(2000))
y <- c(rnorm(2000))
anova(lm(y ~ x))
```

```
Response: y
            Df  Sum Sq Mean Sq F value Pr(>F)
x            1    0.08 0.08140  0.0849 0.7708
Residuals 1998 1916.23 0.95908
```

Now add 100 points with a linear relationship:

```r
x <- c(rnorm(2000), 1:20)
y <- c(rnorm(2000), 1:20)
anova(lm(y ~ x))
```

Only 1% of the data is non-random, yet the feature is already significant at p ≪ 0.001. For feature selection, BoostARoota (XGBoost) seems to perform well — much like Boruta.

---

As Richard Bandler, one of the founders of NLP, observed: *"disappointment requires adequate planning."* So remember — the fewer answers you have at the beginning, the more disappointment you'll have at the end.
