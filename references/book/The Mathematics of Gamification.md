# The Mathematics of Gamification

*Max Tsvetkov · originally published 29 February 2020. Translated and adapted from the Russian, with the author's follow-up answers from the original comment thread woven into the body as part of the argument. In-code labels have been translated to English and Python indentation restored; a couple of arithmetic slips in the source have been corrected and flagged.*

---

A good game, like any good product, is defined by sound balance and repeatable success. No repeatability = magic. Technically, it's that well-worn inequality, CPI < LTV. But when you're doing preliminary balancing there's no escaping day-1 and day-3 retention and the rest of the metrics. Look at a simple problem: on a level, over five minutes across three sessions a day, a player makes 30 taps, and each tap yields one unit of gold. 5 × 3 × 30 × 1 = 450 gold a day, with which the player can buy the content they need. But that's one player, one who plays every day, while others log in once a week. Some will end up with less money and fewer rare items, others with more — and many players will stop receiving their stream of enjoyment, so monetization brings in nothing. Let's smooth over the gulf between rich and poor. On entry you can grant +50 gold; on the first 20 taps give double gold; and after 80 taps, every second tap gives half gold. That narrows the financial gap between players. It's these simple but effective ways of working with gamification that this article is about.

> Gamification isn't about ratings and badges for achievements — it's about motivation. Octalysis.

Inflation can be financial, but it can also be inflation of *interest*. A highly levelled player finds the game less and less engaging, so we need to devalue their achievements and experience a little. Playing Lineage 2 a decade ago, people spent years levelling a character; now you can reach high levels in a couple of weeks. That's a deliberate move by the designers — to slightly devalue the veterans' supremacy and let new players catch up to the old hands fast. To pull it off you use a simple geometric progression, where prior experience is multiplied by some coefficient and handed to new participants. There are several kinds of progression, so let's cover the most popular. You can layer a significant-progress effect on top of it.

So: a new player starts the game as a naked character, able to kill a single monster. Kill a few and they earn enough gold to buy the most basic armour and sword. And then the loop closes — they kill stronger monsters, buy cooler items, kill stronger monsters still, and on it goes. You can implement that arc with a plain +10% progression, or make it more interesting using the Fibonacci numbers plus a 0.75% modifier.

Fibonacci is a sequence in which each new value equals the sum of the two before it (0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144). It doesn't grow as fast as a geometric progression, which gives the player more steps of growth.

Below is a simplified version of a Galton board. The numbers inside each hexagon are the probability with which an event can occur in that cell. The closer to the centre, the higher the probability — which is how a normal distribution of the data forms. But trace the lines from each number along the hexagons and you also get the perfect sequence 0, 1, 1, 2, 3, 5, 8, 13.

![A simplified Galton board hiding the Fibonacci sequence](https://your-scorpion.ru/wp-content/uploads/2020/02/Group-47389.png)

In an arithmetic progression each new value, from the second onward, equals the previous one plus a number d that's constant for the sequence. That d is called the common difference (0, 2, 4, 6, 8, 10; d = 2). It rises quickly at first, but with each step it falls further behind Fibonacci and the geometric progression in the size of its values. And an arithmetic progression is far too easy for players to work out.

In a geometric progression each subsequent number, from the second onward, is obtained from the previous one by multiplying by a fixed number q — the common ratio (1, 3, 9, 27; q = 3).

Why do we make the start of the game easy? Simple: early on the player is learning, and later, as skill grows, progress becomes harder and slower. To ramp up difficulty you can use a logarithm, a hyperbola, or power exponents below 1. We create difficulty, and the player is willing to pay money to simplify the process and save their time — the so-called paywall. But you have to watch yourself and not slide into pay-to-win. All of this is easy to learn if you deconstruct the mechanics of games.

## Why win?

Because people play with other people, in most cases they want to be better than the average player. That's the key idea of gamification: positive feedback on the player's actions. Player rankings serve this well. Rankings are about how people interact with one another — an easy way to give a group of people an interest in playing against each other. But it's not as simple as it looks: you can sort players by strength, by number of wins, by number of battles, by points earned for victories. One way to identify strong players and compare them is a tournament. Tournaments are time-bound, and the most logical format is a round-robin, where every participant plays every other once — but with 50 participants that's 50 × 49 / 2 = 1225 matches. Too many, so let's look at other systems.

The Olympic system (single elimination) is knockout play. Players are matched at random, the winner stays, the loser is out. The formula is simply n − 1. But the number of participants has to be even, and the output is only the winners — everyone else is simply a loser who dropped out at some stage.

The Swiss system pairs a player with an opponent close to their level. Players are sorted by some criterion, and after each match the ranking is re-ranked according to the points earned. It's easy to count: 300 participants and 20 rounds, 300 × 20 / 2 = 3000 matches.

Double elimination is a variation on the Olympic system — a knockout after two defeats. The difference is that players get two chances instead of one; a loser drops into the losers' bracket and plays other losers, 2n − 1. There's a chance two players meet twice. This system is heavily used in esports.

These systems share one weak spot: the draw. It's resolved by increasing the value of a win, or by switching to another game — moving to a penalty shootout in football, say. In the Swiss system the Buchholz coefficient is common: when players are level on points, you look at how the prize contenders' opponents did, and the sum of places tells you which player reached the lead while competing against weaker opponents. There's a variation called the Berger coefficient, and the Sonneborn coefficient (Solkoff), which also sums opponents' scores but drops the weakest and the strongest.

A ranking can be shaped not only by objective factors but by individual ones, which is why it matters to match players on connection quality, on skill, solo players against solo players and solid teams against solid teams. Let's look at one way to compute this, on the example of the Elo rating formulas for two players (originally developed for chess):

![The Elo expected-score formula](https://your-scorpion.ru/wp-content/uploads/2020/05/Group-319.png)

*Eα — a player's chance of winning, from 0 to 1. Rα and Rb — the players' current levels. 600 — the tunable Elo gap that corresponds to a tenfold chance of winning.*

The first stage: we compute player A's chances of beating player B. The second formula, just below, takes A's new and old ratings; Sα is the match result (0 for a loss, 0.5 for a draw, 1 for a win), and Eα is the previously computed chance of A winning. K — the key factor — is a regulating coefficient that controls how fast the rating changes; it's large at first, 90–100, which makes progress very fast early in the game, and later it's lowered to 5–10. As a bonus, the Elo rating makes information very easy to analyse, since the data comes out normally distributed. There are many ways besides Elo to compute a player's correct place in the rankings — auction theory and economic theory among them.

![Elo rating change over matches](https://your-scorpion.ru/wp-content/uploads/2020/05/Group-70-1.png)

Comparing *teams* means computing values for a team rating. Microsoft's TrueSkill system suits this, where the starting skill is a standard normal distribution N characterized by a mean, Mu, and a variance, sigma.

So — how to build a winners' ranking is clear. It's a very good instrument for raising competition among players, just as esports is simply an instrument for raising competitiveness in the gaming environment. Rankings are the most common social feature of all; for Microsoft's Xbox Live a leaderboard is a mandatory requirement, and it works even in single-player. Leaderboards primarily affect achievers and killers — the largest group of players — who want most of all to top the charts.

Now for the nuances. A player feels great and above average when they have a sense of closeness to the people they're competing against. You have to keep sustaining that vision — that the player either already is, or is tirelessly working toward being, better than average. A few examples: skill benchmarking, where the player gets a notification that in the progress of the "summon pets" skill they're in the top 50% of all players — and here's 20 gold for it. You mustn't merely inform; you have to reward. Social reward matters enormously — thousands of successful services were built on the humble like. And once a game grows an enormous audience, one big leaderboard has to be split into many small ones: by level, champion among friends, best on the server, best of the day in the city, strongest tank of the week, and so on.

## Probability theory

A casino is a simulation of choice. Every player wants to exert influence on what happens in the world, so games add different endings, different consequences for the choices a player makes. This is the domain of combinatorics, probability distributions, and mathematical statistics.

Mathematical statistics helps us make decisions. In any IT-adjacent profession it's important to know probability theory and statistics. Probability theory teaches us to reckon with the chance that some event will occur. The classic: roll a die and we know the probability of each of its six faces — we have 100% of the data. In statistics there's no complete data; we work with samples. And it's important to understand that no mathematical model gives a full picture of combat balance — they cover only simple problems — but they do sharply reduce uncertainty.

A few concepts. A *random variable* is a quantity (a number) that, as a result of a trial or experiment, takes on some value not known in advance. Examples: the temperature in a city, the number of steps you take.

It's conventional to distinguish at least two kinds. *Discrete* random variables take a finite or countable set of values (the natural or rational numbers, say) — a count of something, some whole number. The sum of pips from repeatedly rolling a die. The number of people who voted in an election. The number of people who come to a mall on a weekend. The second kind, *continuous* random variables, take an uncountable set of values (the reals, say). With a person's height you might have 180–181, but not 180.4 as a discrete step.

All of this scales neatly onto the types of metrics: either *proportions* (binary 1/0, yes/no), or *continuous* (time in seconds, minutes, money), or *ratios* (clicks per session, price per 1000 impressions) — a relationship between two random variables. Continuous values and ratio metrics are always the hardest to work with. Continuous metrics are finance and ARPV — receipts in a shop range from ₽100 to several million, which leads to a complicated distribution and large variance. A ratio metric is CTR (users, views, clicks); analytics systems almost always compute CTR per user and then average it, forgetting that different values carry their own variance.

Let's talk about discrete variables. Let X be a discrete random variable, like any number in a computer. The distribution law of that variable is the correspondence between the values it takes and the probabilities with which it takes them. For example, we have two dice. On a single throw the possible results run from 2 to 12. The number of possible outcomes is 36 — 6 on one die × 6 on the other. Yet the outcomes 2+1 and 1+2 both give the same result, 3. For a result of 4 there are more combinations: 1+3, 2+2, 3+1. And so the number of combinations grows up to 7, which can be made by 1+6, 2+5, 3+4, 4+3, 5+2, 6+1. Eight comes with fewer combinations, since if one die shows a 1 there's no way to reach 8. This is an example of determining the probability of each value. Note that the sum of the probabilities of a discrete random variable always equals 1 — one of these values will always be taken.

| 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    | 10   | 11   | 12   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 1/36 | 2/36 | 3/36 | 4/36 | 5/36 | 6/36 | 5/36 | 4/36 | 3/36 | 2/36 | 1/36 |

For two dice it all lays out simply and clearly. For three dice you can reuse the result of one die with a certain probability. Plot the probability distribution for two dice or for three and both give a bell-shaped curve. But — and this matters to us — the gap between the extreme values differs, and that difference is important.

![Probability distributions for two dice versus three](https://your-scorpion.ru/wp-content/uploads/2020/05/Group-317.png)

Now a Python example — let's simulate flipping a coin a hundred times. The function's result is how many times heads came up. We'll repeat the experiment 200 times, for 200 × 100 = 20,000 attempts in total. The result is a list like 50, 46, 53, 48 … 47, 53, 49.

```python
import numpy as np
# n — number of trials
# p — probability of the event occurring
np.random.binomial(n=100, p=0.5, size=200)
```

Another example: an urn holds 8 balls, 5 white and 3 black. We draw 3 at random and need to find the distribution law for the number of white balls in the sample. Drawing all the black balls can happen in only one way. The total number of cases is 56, since it's 8-choose-3: 8 × 7 × 6 / 6 = 56. So the probability that x = 0 is 1/56.

What can we do with random variables? We can sum them — simply Z = X + Y. Drawing one white and two black is interpreted as 1-of-5 and 2-of-3, or 15/56. Drawing two white and one black is 30/56, and drawing only white is 3-of-5, or 10/56. It all adds up cleanly: 1 + 15 + 30 + 10 = 56/56.

Another, more methodological example: ABC analysis, which works on the Pareto principle — 20% of all products generate 80% of turnover. Segment A holds the most frequent products; segment B, the products that give 15% of revenue; and segment C, the rest — an enormous pile of products with minimal revenue. Add a coefficient of variation for demand stability on top.

## Vectors: the basics

A vector in school geometry is a directed line with an arrowhead. In linear algebra it's an element of a vector space with a set of properties. In Python it's a numeric array (a NumPy array, say). Technically a **vector** is any set of numbers written in a definite order (in a column or a row). Let's spin up a one-dimensional NumPy array and check its dimension.

```python
import numpy as np
s = np.array([33, 54, 24, 76, 46, 44])
r = np.array([32, -22, 12, 0, 74, -3])
d = r * s
d
len(d)
```

Such a vector can be added, subtracted, and multiplied by scalars. For addition you have to define the vectors as arrays, not plain lists. Proportionality in mathematics is written with a special symbol, ‖. On to a problem: find the linear combination of three given vectors with three given coefficients — multiply each vector by its coefficient and add the results. Per the problem, the coefficients are 300, 200, and 2.

![A linear combination of three vectors](https://your-scorpion.ru/wp-content/uploads/2020/03/Frame-39.png)

We have an industrial enterprise with three asset-criticality statuses: *critical*, weighted 1000; *warning*, weighted 500; and *info*, weighted 200. Over a month, per asset, there were 10, 12, 15 critical events; 21, 34, 12 warnings; and 100, 200, 300 info events. Let's compute:

```python
critical = ([10, 12, 15])
warning = ([21, 34, 12])
info = ([100, 200, 300])
# find the linear combination of the three event vectors with their coefficients
critical = np.array([10, 12, 15])
warning = np.array([21, 34, 12])
info = np.array([100, 200, 300])
vd = 1000 * critical + 500 * warning + 200 * info
vd
```

The result is `array([40500, 69000, 81000])`.

## Data-oriented

Today's fashionable "data-driven" carries one simple idea: to know, at any moment, the answer to three questions — where am I now, where am I heading, and are we moving in the right direction. It's more accurate to call the approach *data-oriented*. First the goal, where we're heading: the product vision held by the game designer or product designer. Then the state: we look at the data (player logs) and the statistics. And the knowledge of where we're going — usually the hardest part. Behind that third point sits an understanding of how the numbers correlate with one another, where we're growing, where we're falling, which hypotheses win, and why.

Collecting data needs logging and exploratory analysis: a specialist decomposes the game into mechanics, each mechanic breaks into steps for events, and for each step you enumerate the list of player-behaviour parameters. Then you estimate what to pull from the player profile in general to understand the interaction with the mechanic. Say we have a daily quest to open chests. The quest has three events: open the window, see the quest, click the reward. Each event has parameters: reward size, time viewing, the player's gold balance, time of day, and so on. This matters — many analysts don't grasp that technical logs aren't the same as game events. You can't rely on server technical logs alone; they're divorced from the logic of the game.

A common example is ticket events. A player finished a level — but the ones who *couldn't* finish it matter to us too. A player spent 3 seconds on a level, and we send information to the server about what happened to them, which values and clicks contributed to their failure.

For big events you have to pull in a great many parameters — hero level, paying/non-paying player, platform, player age (different ages = different gameplay) — so build every global segmentation into every event.

Say we're preparing a promotion to launch, a typical lootbox sale. First we choose the right time: player numbers online on a Friday evening are the highest and the most stable. We look at the currency balance across levels, and demand for the item is estimated by the coefficient of variation. And we think ahead about how we'll measure the results: you have to wait a little, not start counting the moment the promo ends. Build a narrow confidence interval and wait some days for revenue to return to its pre-launch normal. Then draw conclusions — how much people bought, how much we lost afterwards (people now buy less, having stocked up on the promo), and subtract one from the other. Beyond that, you can analyse the behaviour of payers and non-payers separately after the promo ends, the behaviour of players who got the maximum reward, and soft-currency spending.

Here's how you might run advertising inside a game or product. We launch an A/B test of a banner and compute the CTR (click-through rate) for each. We assess the confidence intervals, and if they overlap we have no right to draw conclusions — there's no statistical significance. If they don't overlap, we take the z-test and check that the proportions aren't equal, then compute the z-statistic and the p-value. Don't forget random clicks; fraud has to be fought — either with heuristics, where you write rules that a click must come after the banner appeared on the page, or with deeper ML.

Another tool for working with perception is combinatorics. In a casino a person sees black come up several times running and concludes it's time to bet on red. That's called common sense, and it's a trap of our brain, because each new spin is still 50/50. If a game states a 25% chance to land a critical hit, that doesn't mean every fourth hit is a crit. Or a player has a 50% chance to pull a rare hero from a lootbox and two attempts to open it — a quarter of players won't get the rare hero in either attempt, will take offence, and will leave, even though common sense computes the odds as 50% + 50% = 100%. For this reason games very often don't use honest probability, to avoid the negativity.

Digging deeper into combinatorics, there are several kinds of events. A *random event*, in classical combinatorics, is one whose probability we don't influence (which card comes up). *Independent events* are those that happen independently of one another. *Dependent events*: draw one card from a deck, then another — dependent, because each time the number of cards falls. *Mutually exclusive events* are when one event rules out the appearance of a second — if we've already drawn the three of diamonds, there won't be a second one.

The Bernoulli formula describes the probability of event A occurring a certain number of times across several independent trials, sparing you an enormous amount of computation — you can easily work out the chance of getting a particular game item or discount. In Bernoulli the events are independent, and it's easy to implement in Google Sheets with COMBIN and POWER — an elegant solution and, as I like, fast to build. When the events *aren't* independent, we use the hypergeometric distribution: a player drew one card, 59 remain, so there are fewer — a direct dependence. It lets you compute the probability of drawing card M from a deck of N.

## Matrices

Working with data, you'll inevitably run into matrices. A **matrix** is a set of numbers arranged in rows and columns, like a table. A matrix of 3 rows and 5 columns is called **3 × 5**. The typical notation for one element's position is *a*₃₁, where the first index 3 is the row number and the second 1 is the column number.

Matrices differ by content:

![Types of matrices](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-304.png)

1. A square matrix of the second order
2. A column vector
3. A row vector
4. An upper-triangular square matrix
5. A symmetric square matrix

You can add and subtract only matrices of the same size, element by element. Multiplying a matrix by a scalar is like the vector operation and works element by element; numeric factors can also be taken out of a matrix. But that all falls within common sense and the part of maths with no letters in it. More interesting is **transposing a matrix**: transpose a column vector and you get a row vector, and vice versa.

![Transposing a matrix](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-308.png)

```python
import numpy as np
A = np.matrix("1,2,4,7;-3,43,0,3")
v = np.array([7, 5, 1])
print(A)
print(v)
print(A.T)
print(v.T)
```

The **inverse** of a number *a* is the number *a*⁻¹ that, multiplied by *a*, gives one. Every number has an inverse except zero — the inverse of 2 is 0.5, since 2 × 0.5 = 1.

A matrix's determinant is written **det(A)** — used, for example, to determine where a virtual camera is pointing, with world coordinates written as three vectors. Only square matrices have a determinant, and the closer the determinant is to zero, the harder the inverse is to compute.

```python
import numpy as np
A = np.matrix('2,7,4; 4,4,7; 2,5,7')
np.linalg.det(A)
```

The determinant of a product equals the product of the determinants and doesn't depend on the order of multiplication. The product of a determinant and its inverse equals one. The determinant of a diagonal matrix equals the product of the diagonal elements.

## Visualization

It's important to know how to visualize data. Tools: Excel (which can query external databases), Power BI, Tableau, Python/R, Google Data Studio, and much more. The tool doesn't really matter, of course — I've managed to build great dashboards in Grafana. The result is what counts, though the tools above make getting there far easier. Let's cover a few basic charts.

First, the line chart. It best describes the course of a process over time. Visually, lines are better drawn fairly thick, no more than four to a chart. Don't forget a legend for the colours, and label the axes and units. If the data is noisy, take a simple moving average and you get the trend. Colour can hint at a value's importance — yellow usually signals low severity, orange medium, red high, while green often indicates low load or the absence of threats.

![A line chart with a trend](https://your-scorpion.ru/wp-content/uploads/2056/04/Group-280.png)

Second, the bar chart. Classically stacking several quantities on top of one another lets you see the distribution; a stacked chart implies the most important value goes at the bottom. Don't confuse a bar chart with a histogram (whatever the Russian edition of Excel thinks). A histogram is used to show the frequency of a quantity's distribution across intervals and can only be vertical; a bar chart can also be horizontal. A histogram minimizes the interval between values. Error bars are usually laid over the columns to denote the standard deviation, the standard error, or confidence intervals.

![A bar chart](https://your-scorpion.ru/wp-content/uploads/2020/05/Subtract.png)

![Audience migrating between product versions](https://your-scorpion.ru/wp-content/uploads/2020/05/Group-70-2.png)

*The example shows how an audience flows from one version of a product to a newer one.*

Combine a line chart and a bar chart and you get a funnel. Funnels show cumulative churn well — you can see plainly how many people were lost (the churn rate).

![A funnel chart](https://your-scorpion.ru/wp-content/uploads/2020/05/Frame-1-2.png)

If each column stands for a particular date in time, that's a time-series plot: one axis shows time, and each data element's value is marked on the other — we could use one to show a city's population growth over time.

The histogram is a simple, clear chart that answers questions like "in how many moves does a player finish a level?" or "how many chests does a player open before a tank hero drops?" Technically it's a set of columns showing how often values appear in the dataset, each column representing values in a certain interval. Histograms are useful for checking the distribution of data and make dips easy to spot. Determining the optimal bin size can be tricky, since the optimum depends on the specific data. Histograms are usually used to visualize the shape, symmetry, and skew of the underlying distribution — and understanding the distribution is essential for further analysis, determining which statistical methods we can apply.

![A histogram](https://your-scorpion.ru/wp-content/uploads/2056/04/RE1Mu3b.png)

The 100% stacked combination chart is used to describe the structural process of a specific indicator — when you need to show the development of a process and its overall trend, how a quantity changes over time, like LTV changing with lifetime.

The scatter plot is for finding correlation between two variables — how much the number of wins affects a player's retention, say. Visually it's a mathematical diagram depicting the values of two variables as points on a plane. You can add further variables using colour (a scatter chart) or size (a bubble chart). Most often this is expressed as a player's result (win rate + damage dealt + gold earned) against two different numeric game parameters. More technically, a scatter plot shows the values of two variables laid along two axes. It's hard for beginners to interpret and takes training, but it can be very effective for experienced users, since the patterns let you grasp the essence of a problem at once.

![A scatter plot](https://your-scorpion.ru/wp-content/uploads/2020/05/download-4.png)

The parallel-coordinate plot can also be hard to read; it displays the metrics in a dataset. Suppose you have a database of metrics — each value of each metric is a point on its corresponding metric axis. Such a plot can show relationships in multidimensional datasets, but remember three key things: the order of the axes affects the interpretation; each metric must be normalized; and large datasets produce a great deal of visual noise.

Radar charts (spider charts) show multidimensional data using several axes arranged in a circle, with the axis length proportional to the data values, and the sectors between axes evenly spaced.

## Game theory

This is the applied mathematics of strategic play in society. In life, players by no means always make rational decisions, and game theory is precisely about interaction under such conditions. If you recall the film *A Beautiful Mind*, its protagonist is John Nash, famous for the Nash equilibrium. Broadly, game theory teaches us to set the rules of the game and to manipulate strategically. Social interaction is a mandatory condition, since a player's success depends directly on the other players. Choosing a product in a shop alone isn't a game — there's no second participant. But choosing a product with your wife, or a nuclear standoff between superpowers, is a game.

The most typical problem is the prisoner's dilemma. Two criminals committed a robbery and were caught, and the investigators need to prove they acted in collusion. The police put them in separate cells and offer each the same deal: give up your partner and reduce your prison term. If the first betrays the second, the second goes to prison for 10 years and the first goes free for helping the investigation. If both stay silent, both get a light sentence; if both betray each other, both get 5 years. So each prisoner has two options — stay silent or give up the partner.

![The prisoner's dilemma payoff matrix](https://your-scorpion.ru/wp-content/uploads/2020/05/RE1Mu3b.png)

First, the concept of a *dominant strategy* — the strategy that's best for a player regardless of what the others do. A rational player will choose exactly that. And the second kind, a *dominated strategy* — one that differs from the dominant and that the player will try to avoid. The first player has a dominant strategy to betray the second; the second, symmetrically, has a dominant strategy to betray the first.

The dilemma is that if both betray, both lose; if both stay silent, both notionally win. And no player can change their strategy without worsening the other's situation — a kind of Pareto-optimal frame. Betray/betray isn't Pareto-optimal, because it isn't optimal in this situation. The lesson is that cooperation brings gains, but mutual distrust spoils everything. And this is exactly why game theory doesn't predict behaviour well — real behaviour differs sharply from what the theory forecasts.

A second example is the game of Mafia. The players: a townsperson, a mafioso, and a maniac. The townsperson is honest, the maniac kills everyone, the mafioso is simply a criminal, and whoever's alive by morning wins. Night falls, the mafia wakes, and the contest is between the mafioso and the maniac. Both face the same choice — kill the mafia/maniac or a peaceful resident. If the maniac and the mafia kill each other, the peaceful resident wins; if the mafia kills the maniac while the maniac kills a resident, the mafia wins; if the maniac kills the mafia while the mafia kills a resident, the maniac wins; and if they just kill a resident, the mafia and maniac draw.

So the mafia's strategy doesn't depend on the maniac's: the mafia will want to kill the maniac, and then it wins. They could agree to kill a resident for a draw, but since no agreement between the sides exists, a good outcome for both is impossible — as is so often the case in real life.

Here's a similar case from life. It's not exactly game theory, but it's also about perceiving and interpreting external data. You've won a lottery and been promised $500,000 in three years, but the company offers to buy that promise out — to pay you here and now, since you're afraid it won't honour the obligation. How much should they pay? Present value = $500,000 / (1 + 0.1)⁵ = $500,000 / 1.61 = $310,559.

Losing almost $200,000 seems dreadful, but the money is needed now. And money can be put in a bank at interest: with k = 500,000, a rate of 10% over 5 years, today's $310,559 at 10% a year turns into exactly $500,000 over those five years. *(The example mixes a three-year promise with a five-year discount; keep the horizon consistent — the arithmetic shown is the five-year case.)*

## Fairy tales

Gamification isn't only about numbers. The numbers have to be interpreted onto people's cultural traits and patterns. First, the philosopher and historian Johan Huizinga, a contemporary of Carl Gustav Jung, who wrote the treatise *Homo Ludens*. It gathers the key foundational mythologies from many nations' cultures, in which a common structure was identified from the standpoint of play. And we don't treat a game only as a software product — sport, politics, and business relations are games too. Huizinga's marks of play:

- every game has participants;
- there's a beginning and an end;
- any game is outside objective reality and doesn't intersect with ordinary life;
- there's freedom of action within the game;
- the game is defined by a place, a location;
- the game is determined by time;
- the game has rules that all players observe, or they're out.

The second landmark figure in structuring behavioural patterns from folklore is Vladimir Propp and his *Morphology of the Folktale* — a powerful piece of research proving that fairy tales can be reduced to a mathematical formula. He found that across several hundred tales the base scenario is very similar, and that mythological elements are shared across different peoples. Every tale is a derivative of mythology. The archetypes and plotlines fit this structure:

- the protagonist is forced to leave their homeland;
- someone offers the hero trials;
- the hero receives a reward for the trials;
- fights the main boss;
- receives the main prize;
- returns home.

The third important author is Joseph Campbell and his *The Hero with a Thousand Faces*. The book's thesis is simple and akin to the above: all myths trace a single structure and cyclicity, which he called the *monomyth*. And it was on Campbell's work that the legendary George Lucas built the plot of the first Star Wars trilogy. The monomyth consists of three big steps: the separation stage → the liminal stage → the restorative stage.

The hero begins on the separation stage, parting with home, with the comfort zone, and crossing into a new, harsh, tense world. The hero gains a protector, a mentor, a helper — new friends, in short. Then comes the liminal stage, in which the hero must pass the point of no return that finally severs them from their familiar life. They pass through many trials, monsters, hard questions and decisions, and defeat the final boss. The boss needn't be a monster — it can be any embodiment of a change in the hero's character (was weak, became strong; was a coward, became brave; was a loser, became a winner). And the restorative stage, in which the hero returns to the original comfort zone and passes their new skills and achievements to their friends. In the end the onlooker experiences a true catharsis.

## The Monte Carlo method

Recall the example of black coming up several times running; against that fallacy you can set the Monte Carlo method. It's a method of approximate computation, very useful when the conditions of a problem change quickly and often. π ≈ 3.14 is an approximate value. Monte Carlo isn't a tightly defined group of methods for studying random processes; it describes any estimation approach based on random sampling. The model simulates many random signals with a given probability density, and the aim is to determine the outputs statistically.

The most common example is finding a geometric probability in Buffon's needle problem — dropping a needle to find the number π, based on Monte Carlo. A needle is dropped onto a surface marked with two parallel lines set some distance apart, and the problem reduces to finding an area.

![Buffon's needle](https://your-scorpion.ru/wp-content/uploads/2020/05/Frame-1.png)

How to use it in practice: two players attack each other in turn. Run many battles and take the average result. The number of trials needed depends on the distribution function (Laplace's coefficient will help). Start with two global variables, and expect to need many trials to reach sufficient accuracy.

## The metrics behind all of this

Everything above is the mechanics; underneath it sits a layer of measurement I'm asked about constantly, and it's worth setting down in one place.

**How LTV is calculated.** LTV combines many metrics, and planning LTV means forecasting revenue — no small task. The simplest, and least accurate, method is to take total revenue over the whole period and total unique users over the whole period and divide one by the other. There are more precise routes: lifetime (the average time from install to inactivity) plus ARPDAU (or ARPU). You'll need to pick an inactivity window — if a user hasn't returned in 14 days, count them as churned. A third variant is ARPDAU × lifetime, where lifetime is a retention interval — the integral of retention, or the whole span from install to inactivity; the ad networks will tell you your AD ARPDAU. ARPDAU isn't necessarily constant, and lifetime is information from the past. Another simple route is lifetime × ARPU; the complex one is the integral of the retention function × ARPU. A worked example, YouTube-style: paying users in one month, 10,000; revenue in the month, $325,584; ARPPU, $59.8; lifetime, 4 → user LTV = $59.8 × 4 = $239.2. As an indirect metric you can roughly estimate user lifespan: with, say, 200,000 monthly active users and a churn rate hovering around 4.3%, average lifespan under a known churn rate follows a geometric progression if users leave evenly — or, very roughly, 1 / 4.3% = 23 months, nearly two years. For non-contract users, LTV = average revenue per customer / churn rate, because churn is hard to define there: on a contract, churn is termination; without one, it's ceasing to visit the shop or app. The proper approach is a beta-geometric or non-binary distribution: all users split into a live stage and a dormant stage, and for the live ones the number of purchases should follow a Poisson distribution with a lambda transaction-rate coefficient. That's for live projects; if the project hasn't launched, use the analogue method — find similar projects and study the benchmarks.

**Is CLV a successor to LTV?** CLV, CLTV, and LTV are identical, so the answer above applies. One caveat: you'll very often see CLV computed as (1 / churn rate) × ARPA, which is incorrect. The metric is built from three quantities — average check, ARPU, and average customer lifetime — and the whole idea of CLV is forecasting revenue, i.e. simply multiplying by the number of users, without accounting for seasonality, growth, churn, or segment variance. LTV/CAC ratio is the key payback indicator: if LTV/CAC < 1 you're burning money, while LTV/CAC > 3 is profit and sustainable product growth. Example: CAC = $5, CLV = $14, 14/5 = 2.8, which is perfectly livable. Often it's simpler to use ARPU (average revenue across all users) for everyone and ARPPU (revenue from payers only) for the paying segment.

**Measuring how lost a person gets in a product.** Do our little improvements add extra wandering through the structure? Look toward the [keystroke-level model (KLM)](https://en.m.wikipedia.org/wiki/Keystroke-level_model), GOMS, or SEQ. As a first approximation, measure the number of clicks, the time, and the pages visited for the ideal path the designer intended versus the real one, and compare them while watching the error rate. What comes out is something like *lostness*, computed from three quantities: N, the number of unique pages visited during the task session; S, the number of visits to non-unique pages; and R, the number of pages the designer intended for the scenario.

![The lostness formula](http://your-scorpion.ru/wp-content/uploads/2020/07/Group-715.png)

## Knowing when significance is even reachable

Studies often report that statistical significance wasn't achieved — but is it always possible to get a significant result? Perhaps the sample was smaller than needed from the start. To answer that you look for *statistical power* — the probability of not missing an effect when one truly exists. In formula terms that's (power = 1 − β): we reject the null hypothesis, and it really is false. To compute power you need the sample size, the significance level, and the minimum effect size you expect.

Effect size deserves its own word — it's less a technical matter than one from psychology. We need to understand not just whether there'll be an effect, but how significant it must be; if power is low, we won't notice even a large effect. There's Cohen's rule of thumb for effect size: a small effect, r = 0.1, standard deviation d = 0.2; a medium effect, r = 0.3, d = 0.5; a large effect, r = 0.5, d = 0.8. The two simplest ways to measure effect are Poisson and Cohen. Cohen's *d* is the mean difference over the standard deviation, scaled by sample size, and you then compute from a t-test; a second form covers the case of standard errors rather than standard deviations, where t is the t-statistic, SE the standard error, and n the number of objects.

![Cohen's d](http://your-scorpion.ru/wp-content/uploads/2020/06/Group-52-1.png)

![Effect size from a t-test](http://your-scorpion.ru/wp-content/uploads/2020/06/Group-55.png)

Everyone knows the significance level is usually 0.05 — one error in 20. For the second kind of error the tolerable risk is higher: a type-I error is four times as dangerous as a type-II error, so the acceptable type-II risk is 1 in 5, or 0.2 — hence the conventional power of 1 − 0.2 = 0.8. The more dangerous the error could be (nuclear power, spacecraft), the smaller the probabilities you allow. The larger the sample, the higher the power. Alpha is the probability of a type-I error we're willing to accept (that 0.05 significance level again). As α rises, β falls, and power (1 − β) grows — a higher significance level means less chance of a significant result and lower power the other way. To compare different samples you use two-sided tests; for a one-sided test you halve the error parameter α (α = 0.20 one-sided against α = 0.10 two-sided).

**Sizing a paired test.** The sample-size formulas differ when the samples are paired — not independent. Paired samples consist of the same study objects examined at different moments: patients before and after treatment, the behaviour of a steady audience before and after a redesign. Finding a reliable difference between two paired samples uses a formula built from n (sample size), s (standard deviation), Zα (the type-I error coefficient), Zβ (the type-II error coefficient), and MDC — the minimum detectable change, the smallest effect we expect to find, given in absolute terms.

![Sample size for a paired test](http://your-scorpion.ru/wp-content/uploads/2020/06/Group-56.png)

To detect a 40% change in the sample mean against last year, where last year's mean was 200, MDC = 0.4 × 200 = 80. The table of standard normal deviates for Zα: 0.40 → 0.84, 0.20 → 1.28, 0.10 → 1.64, 0.05 → 1.96, 0.01 → 2.58. The standard error is taken as a z-score of, say, 1.64 (for 90% confidence) and inflates the error value: the larger the number, the higher the confidence. Standard error depends on the confidence level, the variance, and the sample size — larger variance means larger error, and a larger sample means less chance of error, so we strive for a big sample and small variance (from about 1000 up). For Zβ: β 0.40 → power 0.60 → 0.25; β 0.20 → power 0.80 → 0.84; β 0.10 → power 0.90 → 1.28; β 0.05 → power 0.95 → 1.64; β 0.01 → power 0.99 → 2.33. If you're tracking change over time within the same sample across a single year, there won't be a standard deviation between the paired samples.

**Sizing from aggregated data, when you only have the mean.** This is the problem of determining the sample size needed to estimate a mean at a given significance level. The initial sample size comes from a formula built on n (the preliminary estimate), Zα (the standard error coefficient), and s (standard deviation).

![Sample size for estimating a mean](http://your-scorpion.ru/wp-content/uploads/2020/06/Group-65.png)

B deserves detail: it's the desired precision, expressed as half the maximum acceptable confidence-interval width, in absolute terms. To get a confidence-interval width within 20% of the sample mean, where the mean is 60 apples on a branch, B = 0.2 × 60 = 12.0. The Zα table by significance level: 80% → α 0.2 → 1.28; 90% → α 0.1 → 1.64; 95% → α 0.05 → 1.96; 99% → α 0.01 → 2.58. The next step is correcting the sample size, and one more — an additional correction, since the formula above assumes the whole population is much larger than the sample we take. If the sample is more than 5% of the population, apply the finite-population correction (FPC).

## When A/B testing gets awkward

**Comparing two visuals.** Yes, A/B is the reflex — but there are other routes. In fact A/B is tricky here, because the traffic is almost certainly small, time is short, and the metric is conversion. Under those conditions you end up bootstrapping, post-stratifying, working with multi-armed bandits, or cutting the test's variance with CUPED, and speeding tests up through linearization on fewer samples. Go down that path and it's sequential testing via the frequentist approach; the Bayesian one lets you assign a cost to the loss from an error and keep within a set bound. Or run a plain survey: show two images, ask which the respondent prefers, add a −3 to 3 rating and an open question. Or the same but showing each respondent only one variant, so the contrast between results is smaller. I sometimes discard the answers of people who wrote nothing in the free-form field or typed something like "ttttt." Comments have to be read closely, separating fact from fluctuation: 5% of 6000 comments being negative about the service is a fact; 5 negative comments out of 6000 is a fluctuation.

**When A/B audiences overlap.** They shouldn't, but infrastructure and the business sometimes won't allow otherwise. It's a broad question, but the first tool is CUPED as a predictive algorithm. A recent example: an ad campaign on bus stops in Dubai, where we wanted to know how much the effect moved revenue in the specific districts with those stops. Every user could see the ad while moving around, so there's no control sample, and the objects are territories, not people. Predictive CUPED lets you forecast the target metric for the experimental period — you compute the expectation for the metric and compare it against the value actually observed — so in the end you're comparing a territory with the test against itself without it. The other trap is the interaction effect: one department launches a test on cart coupons while a second sends different mailings to different users. Each department's hypothesis could have won alone, but the combined effect turned negative. The fix is simple — don't run two tests at once, because in interaction tests you can't interpret the influence of the factors on their own.
