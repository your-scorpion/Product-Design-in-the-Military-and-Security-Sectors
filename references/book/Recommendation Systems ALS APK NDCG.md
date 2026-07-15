# Simple Recommendation Systems: ALS, AP@k, NDCG
### A working guide from the simplest baselines to two-level models, with the code to run them

*Maksim Tcvetkov · 26 March 2023*

To build the simplest recommendation systems you'll need linear algebra, since ML leans on linear algorithms to work with matrices. An understanding of mathematical statistics won't go amiss either, for running experiments. Add basic Python and SQL, together with the standard DE menagerie of tools — Hadoop, Spark, Airflow, Kafka. Spark or Hadoop especially, since we plan to work with big data. It also helps to know how to build microservices and to be handy in some compiled language like Java (not Python), because with big data what matters is execution speed, not the speed of writing the code.

The canonical example of a recommendation is recommending videos or music, news, people on social networks. The business usually wants the user not to leave the service after watching the target video, but to stay. To that end we can recommend something similar and matching their interests. Solving a problem like this, we'll almost certainly run into thousands and millions of users — and recommendations on YouTube mean more than a billion. And all of it tied to time: if a user listens to rock in the morning, the next morning the service should suggest rock, not just popular tracks. If a user listens to podcasts from 9 to 10 and music from 10 to 18, we're obliged to account for it.

Above I mentioned the defining feature of working with recommendation systems: a great deal of data, which means a complex or inefficient algorithm will take a long time to run. Another problem is the cold start: new users arrive, or new objects appear. What do you recommend, and to whom, when there's no history of the new users interacting with the new objects? A third problem is the information bubble — a social network that keeps suggesting people from your former workplace and never anything new. The simplest solution to the cold start is to recommend nothing. Or to offer what's popular. A more engineering-minded approach is to use hybrid or content-based algorithms to generate the recommendations.

Recommendations are built on user actions. There's the notion of explicit feedback: when a user has explicitly rated an object or product — a dislike, a score from 1 to 10, adding a product to favorites — all of it clear in its meaning. Roughly speaking, the user has labeled the data for us. Rated low, we don't recommend; rated high, we do. In opposition to this stands implicit feedback, where the user isn't explicitly involved in rating the object: the mere fact of watching a film, or buying a product, a comment written or a query typed into a search box — all examples of implicit feedback. You can generate recommendations from implicit feedback too. It isn't always easy to work with, though: a comment might be negative, and we need to know in advance whether the text carries a negative tone. If a user leaves a negative comment on an article, then the content wasn't to their liking, and we don't recommend that article to other, similar users. Less obvious still is the feedback from a product bought as a gift for someone else rather than for personal use. Even adding something to the cart isn't always tied to a desire to buy. But the catch is that there's always more implicit feedback about, and you have to learn to work with it.

## Metrics

To beat the problems described above, there are metrics. They split into two subclasses. The first is **quality metrics**, which divide into accuracy metrics and ranking metrics. Which metric can beat the problem of showing nothing but the most popular items in the recommendations? A separate block of metrics answers for diversity here — `coverage`, for instance, reflects the number of objects that have been shown to all users. Other popular diversity metrics are Surprisal, Unexpectedness, Novelty, Serendipity, and Diversity.

![Taxonomy of recommendation-system metrics](https://your-scorpion.ru/wp-content/uploads/2023/05/Group-47401.png)

Not one metric has a settled formula; the formula for computing a metric is usually pure creativity, with an eye to what data you have, its quality, the business requirements, and the ways of securing investment. `roc_auc_score` is called the same thing everywhere, yet that single name hides different implementations across different libraries.

The first and simplest metric is `hit-rate`: was there a purchase of, or an interaction with, even one product from the recommended list? In other words, the metric describes whether at least one relevant product was among those shown in the recommendation block. Take `u1: recommended_list [124, 424, 356, 16, 43, 462], bought_list [424, 43]`, where products 424 and 43 were both shown and bought — that gives hit rate = 1. If nothing from the recommendations was bought, `bought_list []`, the metric is zero. Simple enough: you have the bought and the recommended products, you check whether a recommended product was bought, and the metric returns a binary 1 or 0. If even one recommended product was bought, it returns 1 (true). Working code:

```python
import pandas as pd
import numpy as np

recommended_list = [143, 156, 1134, 991, 27, 1543, 3345, 533, 11, 43, 156, 3345, 10, 15, 1134]  # id of items
final_bought_list = [156, 611, 10, 24, 521]  # bought

def hit_rate(recommended_list, final_bought_list):
    final_bought_list = np.array(final_bought_list)
    recommended_list = np.array(recommended_list)

    flags = np.isin(final_bought_list, recommended_list)
    hit_rate = (flags.sum() > 0) * 1
    return hit_rate

def hit_rate_at_k(recommended_list, final_bought_list, k=5):
    final_bought_list = np.array(final_bought_list)
    recommended_list = np.array(recommended_list)

    flags = np.isin(final_bought_list[:k], final_bought_list)
    hit_rate = int(flags.sum() > 0)
    return hit_rate

data = pd.DataFrame({"user_id": ["u1", "u2", "u3"],
                     "recommended_list": [[143, 156, 1134, 991, 27],
                                          [1543, 3345, 533, 11, 43],
                                          [156, 3345, 10, 15, 1134]],
                     "final_bought_list": [[156],
                                           [611],
                                           [10, 24, 521]]}); data

# data.apply(lambda x: hit_rate_at_k(x[1], x[2], 50), 1)
data.apply(lambda x: hit_rate(x[1], x[2]), 1).mean()
```

Since the result is `[1, 0, 1]`, the answer will be 0.66666666. In the example above, `Hit rate@k` is whether there was at least 1 relevant product among the top-k recommended — and this is the most commonly used version. Put another way, with Hit rate@5 and 20 products recommended, the metric counts only the first 5. We simply take a slice of the recommended list — 5 products, in this case. Bear in mind that the list is ranked, even if that isn't visible in the code. In reality, for every user and every product there will be some value — relevance, say — and the lists should be ranked by it.

Also, if a user was offered 20 recommendations, it doesn't matter whether they bought all 20 or only 1: in both cases hit rate = 1, because the metric is aimed at *at least one* relevant product among the recommendations. Hit rate does well as a metric when the goods being sold are fairly expensive, like home appliances.

Some may feel Hit rate is too simple, being binary. The second metric comes to the rescue: `Precision`. Computed per user, it's the metric closest to the business. It shows whether the user interacted with a recommended object. Where `Hit rate` returned 1 or 0 per user, `Precision` shows the share of relevant products among those recommended — in other words, what percentage of the recommended products the user bought. It's a very popular metric, which means it's simple to implement. The formulas:

- Precision = (# of recommended items that are relevant) / (# of recommended items)
- Precision@k = (# of recommended items @k that are relevant) / (# of recommended items @k)

`precision@k` is your call. If you showed the user only 10 films on the page as recommendations but the API returns 100, the metric still has to be computed over the 10 — because all 100 won't be shown, only 10 will. Often the `k` in `precision@k` is fairly small, no more than 20, and is set by business logic: 5 product recommendations in the cart, 20 answers on Google's first page, and so on.

But the version the business loves most is `Money Precision@k` = (revenue of recommended items @k that are relevant) / (revenue of recommended items @k). A concrete example. You showed the user these products in the recommendation block:

- Milk — 33₺
- Candy — 42₺
- Hummus — 35₺

Build an array `prices_recommended = [33, 42, 35]` and the purchase fact `flags = [1, 0, 1]` — that is, only the milk and the hummus were bought. `(1 + 0 + 1) / (1 + 1 + 1) = 0.66`. But if we want to please the business, we need to add money. At the formula level, you multiply the boolean values by the product prices: `(1 * 33 + 0 * 42 + 1 * 35) / (1 * 33 + 1 * 42 + 1 * 35) = 68 / 110 = 0.61` — 61% of the maximum possible revenue given the assortment shown to the user in the recommendation block. As code:

```python
def precision(recommended_list, final_bought_list):
    final_bought_list = np.array(final_bought_list)
    recommended_list = np.array(recommended_list)

    flags = np.isin(recommended_list, final_bought_list)
    precision = flags.sum() / len(recommended_list)
    return precision

def precision_at_k(recommended_list, final_bought_list, k=5):
    final_bought_list = np.array(final_bought_list)
    recommended_list = np.array(recommended_list)

    final_bought_list = final_bought_list
    recommended_list = recommended_list[:k]

    flags = np.isin(recommended_list, final_bought_list)
    precision = flags.sum() / len(recommended_list)
    return precision

def money_precision_at_k(recommended_list, final_bought_list, prices_recommended, k=5):
    final_bought_list = np.array(final_bought_list)
    recommended_list = np.array(recommended_list)
    prices_recommended = np.array(prices_recommended)

    final_bought_list = final_bought_list
    recommended_list = recommended_list[:k]
    prices_recommended = prices_recommended[:k]

    flags = np.isin(final_bought_list, recommended_list)
    precision = (flags * prices_recommended).sum() / prices_recommended.sum()
    return precision

import pandas as pd
import numpy as np

recommended_list = [143, 156, 1134, 991, 27, 1543, 3345, 533, 11, 43, 156, 3345, 10, 15, 1134]
final_bought_list = [156, 611, 10, 24, 521]
prices_recommended = [256, 634, 53, 324, 343, 123, 514, 512, 874, 571, 43, 55, 243, 634, 511]

data = pd.DataFrame({"user_id": ["u1", "u2", "u3"],
                     "recommended_list": [[143, 156, 1134, 991, 27],
                                          [1543, 3345, 533, 11, 43],
                                          [156, 3345, 10, 15, 1134]],
                     "final_bought_list": [[156],
                                           [611],
                                           [10, 24, 521]],
                     "prices_recommended": [[256, 634, 53, 324, 343],
                                            [123, 514, 512, 874, 571],
                                            [43, 55, 243, 634, 511]]}); data

money_precision_at_k(recommended_list, final_bought_list, prices_recommended, k=5)
precision(recommended_list, final_bought_list)
precision_at_k(recommended_list, final_bought_list, k=5)
```

The code returns `0.2`. Always pass the list of recommended products first and the bought list second, never the other way around — we'll need this for computing our third metric.

So, the third metric: **recall**. It's similar to precision. The metric answers for the number of products relevant to the user, and is usually used for pre-filtering models (removing the products we definitely won't recommend). As code:

```python
def recall(recommended_list, bought_list):
    bought_list = np.array(bought_list)
    recommended_list = np.array(recommended_list)

    flags = np.isin(bought_list, recommended_list)
    recall = flags.sum() / len(bought_list)
    return recall

recall(recommended_list, final_bought_list)
```

The result will be `0.4`. All these metrics are the classic quality assessment for a classifier — ROC-AUC curves, recall, accuracy, precision, f1-score, MCC.

## Ranking metrics

These metrics answer for the order of the recommendations, and that's a harder thing. Order matters a great deal. In a playlist, any ranking error can annoy the user; the wrong order in news recommendations can sharply reduce the Sticky factor (DAU/MAU). The better the song fits the feed, the likelier the user is to linger in the service. The more interesting the news, the more engaged they'll be.

The first metric is `AP@k`, a sum of terms. `@k`, again, is how many objects we recommended — `@k = @6` means we showed each user 6 objects. Look at any single term and you get plain `precision (true/false)`: if song *g* was played, the metric = 1; if song *f* wasn't, = 0. Each term is multiplied by an indicator function; then we sum and divide by the number of recommendations. By example: we recommended 6 songs in order `[1, 2, 3, 4, 5, 6]`, of which only positions 2, 3, and 4 — `[0, 1, 1, 1, 0, 0]` — matched the user's interests. For the first song `precision` = 0, for the second = 1/2, for the third 1/3, for the fourth 2/4, then 2/5 and 2/6. By the formula: `AP@6 = 1/2 (0 * 0 + 0.5 * 1 + 0.33 * 1 + 0.5 * 1 + 0.4 * 0 + 0.33 * 0) = 1/2 * 1.83 = 0.915`. As code, which returns `0.4448717948`:

```python
import pandas as pd
import numpy as np

recommended_list = [143, 156, 1134, 991, 27, 1543, 3345, 533, 10, 43, 156, 3345, 10, 24, 1134]
final_bought_list = [143, 156, 1134, 991, 27, 1543, 3345, 3345, 10, 24, 1134]

def ap_k(recommended_list, bought_list, k=5):
    bought_list = np.array(bought_list)
    recommended_list = np.array(recommended_list)

    flags = np.isin(recommended_list, bought_list)

    if sum(flags) == 0:
        return 0

    sum_ = 0
    for i in range(1, k + 1):
        if flags[i] == True:
            p_k = precision_at_k(recommended_list, bought_list, k=i)
            sum_ += p_k

    result = sum_ / sum(flags)
    return result

def precision_at_k(recommended_list, bought_list, k=5):
    bought_list = np.array(bought_list)
    recommended_list = np.array(recommended_list)

    bought_list = bought_list
    recommended_list = recommended_list[:k]

    flags = np.isin(bought_list, recommended_list)
    precision = flags.sum() / len(recommended_list)
    return precision

ap_k(recommended_list, final_bought_list, k=5)
```

AUC@k is computed differently everywhere; I compute it over the top-k observations, so it comes out roughly like the most popular AUC@k implementations. It's used to demonstrate correctly ranked products, but… it's poorly suited to the purpose. `NDCG` is better, so let's go straight to it.

NDCG (Normalized Discounted Cumulative Gain) is simple enough: the higher up the list a correctly guessed item sits, the smaller the denominator. That explanation won't do for the business, so let's unpack it. Start with DCG: a sum whose numerator records whether a shown, recommended product was bought, and whose denominator is the logarithm of the product's position among the recommendations. Still opaque to the business. Onward: of 5 shown products, the user clicked the first and the fourth — `[1 0 0 1 0]` — and also clicked a sixth that wasn't among our recommendations. A quick sanity check pays off here: since that first hit alone contributes 1/1 = 1, the total has to land above 1. So DCG = `1/1 + 0 + 0 + 1/log_2(5)` = `1.4307`. The formula:

![DCG formula](https://your-scorpion.ru/wp-content/uploads/2023/04/34242.png)

From there we do reach NDCG, which is simply this metric averaged across all users.

![NDCG formula](https://your-scorpion.ru/wp-content/uploads/2023/03/designer-2023-03-29-at-3.05.11-PM.png)

The denominator holds a quantity called IDCG. This one stands for the ideal world — it answers what the value would be if the top of the list held only the products the user actually bought. NDCG, then, is DCG divided by IDCG. To compute IDCG, go back to the example — `[1 0 0 1 0]` plus the one external click, that's the fact of it. Ideally we'd want to see `[1 1 1 0 0]`, the most relevant products shown first. IDCG = `1/1 + 1/log_2(3) + 1/log_2(4) + 0` = `2.13`. All told, NDCG = `1.4307` / `2.13` ≈ `0.6717`.

And the last metric within this article is MRR (Mean Reciprocal Rank) — we recommended some products and took the first one bought; that product's rank is the value of the MRR metric.

![MRR formula](https://your-scorpion.ru/wp-content/uploads/2023/03/designer-2023-03-29-at-2.55.39-PM.png)

## The loss function

Every algorithm has a loss function, and the algorithm should try to minimize the loss. Let's take the simplest possible example — classifying RecSys algorithms. Netflix once invited developers to experiment with recommendation algorithms in a contest with a large prize fund. Over its course a great many algorithms were invented, and once there were enough of them, an understanding emerged of how they might be classified. Very loosely, all the existing algorithms can be divided as follows:

![Classification of recommendation algorithms](https://your-scorpion.ru/wp-content/uploads/2023/03/Group-47391.png)

Heuristic algorithms are the simplest; we'll come back to them. Collaborative algorithms, such as matrix factorization, are used to predict the user–object interaction matrix. We hold the history of a user's interactions with objects, and we form recommendations on that basis. Say we're handed tables of users and objects — we obtain the product of two matrices.

Content-based algorithms are built on information about the content, that is, on features. A woman in the 30–40 range buys cosmetics; her age, sex, and purchased products are features, and they can be clustered. What similar users bought doesn't matter to us — only the classification does. Content-based and collaborative algorithms can and should be combined into hybrid ones.

The simplest algorithm is recommending the popular: we suggest our store's popular products to everyone. What counts as popular? You could say popular products are the ones with the largest share of clicks per impression (CTR). Such an algorithm is simple, not personalized, and many products will never make it into the recommendations. Simpler still is the random algorithm, which suggests random products to everyone; its quality is the lowest, since users' needs aren't taken into account.

A third option is the Random Popular weighted recommender, which recommends random products with a probability proportional to a product's popularity. Thanks to CTR we often know what we showed the user and what they clicked. If a product gets clicked, it's assigned some value between 0 and 1. The algorithm above simply leans on the click count, whereas in my proposed version we temper that share with the parameter that the product was shown. Another option is to recommend the products the user bought most themselves. Bought domestic vitamins — we'll recommend the same vitamins. This is a very popular algorithm, but it lacks any recommendation of new products. It's a heuristic one, and considered quite effective. Since it offers the user nothing new, we can dilute the recommendations with novelties, seasonal offers, or simply random products.

On to the code: we'll take the `retail-hero` dataset — 2,500 users, 90,000 products, 2,500,000 interactions.

We build the training set. We'll carve off three weeks for the test and send the rest into training. There's one potential problem. We recommend films to the user, and new films are released periodically. A film comes out early in the year, and with a random split into training and validation, an already-popular film lands in training — and makes it into recommendations, even though in real life the film might still be unpopular. So a random split isn't recommended; better to stick to other splits. For instance, take a date and say that everything before `xx/xx/xxxx:xx:xx` is the training set and everything after is validation. That's exactly what we'll do — a train-test split by time. As an alternative, pick *n* films per user, set them aside for the test, and put everything else into training.

Let's prepare the data for our baseline by splitting it into train and test:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

data = pd.read_csv('retail_train.csv')
data.head(2)
data['week_no'].nunique()

users, items, interactions = data.user_id.nunique(), data.item_id.nunique(), data.shape[0]

print('# users: ', users)
print('# items: ', items)
print('# interactions: ', interactions)

popularity = data.groupby('item_id')['sales_value'].sum().reset_index()
popularity.describe()

test_size_weeks = 3

data_train = data[data['week_no'] < data['week_no'].max() - test_size_weeks]
data_test = data[data['week_no'] >= data['week_no'].max() - test_size_weeks]
data_train.shape[0], data_test.shape[0]
```

The structure of `retail_train.csv`:

| user_id | basket_id | day | item_id | quantity | sales_value | store_id | retail_disc | trans_time | week_no | co_disc |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 2321 | 2.7E+10 | 1 | 1454845 | 1 | 1.23 | 54 | -0.5 | 1654 | 1 | 0 |
| 2321 | 2.7E+10 | 2 | 7636782 | 1 | 1.73 | 54 | -0.5 | 1654 | 1 | 0 |
| 35 | 2.7E+10 | 3 | 2473243 | 1 | 3.783 | 5332 | -0.5 | 3233 | 1 | 0 |
| 3278 | 2.7E+10 | 4 | 2425244 | 1 | 2.62 | 5332 | -0.5 | 3233 | 2 | 0 |

In my file the results are these:

```
# users:  1451
# items:  14795
# interactions:  74112
```

The code that follows is a baseline — a simple algorithm you can build complications onto. Intuitively, it's something simple you can implement quickly that gives decent quality. In our case the baseline is the heuristic algorithms.

Let's create a dataframe, dropping duplicate rows, of the users' purchases on the test set (the last 3 weeks) — list 1 and list 2:

```python
result = data_test.groupby('user_id')['item_id'].unique().reset_index()
result.columns = ['user_id', 'actual']
result.head(2)
```

![Test-set purchases grouped per user](https://your-scorpion.ru/wp-content/uploads/2023/05/Untitled-1.gif)

In the test we split the data by user and form a list of the films the user watched. The number of purchases or views doesn't matter — only the fact of the purchase does. And we get a table with the list of bought products. We have a dataset, we split it in two; we set some products aside in training, and from the training set we try to predict products for the test set.

Let's check that the users don't overlap across the two datasets:

```python
test_users = result.shape[0]
new_test_users = len(set(data_test['user_id']) - set(data_train['user_id']))

print('The test dataset has {} users'.format(test_users))
print('The test dataset has {} new users'.format(new_test_users))
```

Let's try building recommendations from a product's popularity *n*. Start by grouping all the data: take the training data, group it by ID, and find the sum. Sort by that value and take the top 5. The recommendations don't depend on the user, which gives us a little freedom.

```python
def popularity_recommendation(data, n=5):
    popular = data.groupby('item_id')['sales_value'].sum().reset_index()
    popular.sort_values('sales_value', ascending=False, inplace=True)

    recs = popular.head(n).item_id
    return recs.tolist()

popular_recs = popularity_recommendation(data_train, n=5)

result['popular_recommendation'] = result['user_id'].apply(lambda x: popular_recs)
result.head(2)
```

If you did everything right, the recommendations will be identical, since we're showing everyone the same thing.

![Identical popularity recommendations for every user](https://your-scorpion.ru/wp-content/uploads/2023/05/46464.gif)

If the business sends down a requirement to show diverse products, that means using random. A touch more interesting is recommending not random products but weighted ones. A logarithm is applied to temper a product's score and popularity. If total sales run from 0 to *n*, you can take the logarithm as 1 + sales_sum. The upside of such simple recommenders — especially when it comes to popular-only products — is that you can filter with them: apply the more complex algorithms only to the 100,000 most popular products, say.

For example, we have a sample of 100,000 popular products, and to each we add a weight. Without weights it works like this: one of 10 products is chosen at random, and for 1,000 users the selection algorithm runs 1,000 times, so each product is picked about 100 times. But if products 4, 6, and 9 carry a higher weight, the probability of picking them will be higher too.

**Complex algorithms.** First and foremost, the collaborative ones, which look at the object-interaction matrix. Such algorithms outstrip all the others in effectiveness. Collaborative algorithms work not with groups of users but only with ratings — we take similar users and recommend each other's content to them. A collaborative algorithm can be user-based, resting on the interests of similar users, or item-based, when we recommend products similar to what the user already bought: bought oranges → an orange is similar to a mandarin → recommend a mandarin.

To weight the interaction matrix and find similarity we use the implicit library, written in Cython — a C-like implementation of Python. It's better applied only to popular products, since after prediction the complexity can be cubic, quadratic, or worse — and that's slow to compute. Off we go, installing the library:

```python
import pandas as pd
import numpy as np
import implicit
import matplotlib.pyplot as plt
from scipy.sparse import csr_matrix, coo_matrix
from implicit.nearest_neighbours import ItemItemRecommender
from implicit.evaluation import (
    train_test_split,
    precision_at_k,
    mean_average_precision_at_k,
    AUC_at_k,
    ndcg_at_k
)
```

A caveat. If a user has viewed some amount of content, that amount is probably small. And if the user is new and has watched only one film — a rather rare film at that — there's nothing to recommend to them. Approach it from the content side, though, and a typical series has many views, on which you can layer a dot product. So we'll work through the item-based approach, since it's the more frequent one.

In the picture below, we need to recommend something to user number 4, given that they've already bought melon and tomato paste and rated them highly. The algorithm looks only at the ratings, so the tomato and the kiwi should end up in the recommendation. The picture below is an item–item interaction matrix — not a rating in the matrix, but a variation on explicit feedback.

![Item–item interaction matrix](https://your-scorpion.ru/wp-content/uploads/2023/04/Group-47393.png)

A list of 10,000 objects will be represented as a 10,000×10,000 item-similarity matrix. The cells hold some similarity parameter — kiwi is 100% similar to kiwi, and the cell where the kiwi column and kiwi row intersect holds a 0. Comparing kiwi with carrot, you can reach a better score with a dot product, cosine similarity, or weighting by TF-IDF or BM25.

```python
DATA_PATH = 'retail_train.csv'
retail_data = pd.read_csv(DATA_PATH)

print(retail_data.head(2))

TEST_SIZE_WEEKS = 3
```

But for the example we take popularity and work with popularity alone. Say we want to pick only 1,000 popular products from that same `retail_train.csv`. How to build `data_train` we've already laid out above.

```python
popularity = data_train.groupby('item_id')['quantity'].sum().reset_index()
popularity.rename(columns={'quantity': 'n_sold'}, inplace=True)

top_1000 = popularity.sort_values('n_sold', ascending=False).head(1000).item_id.tolist()
```

Next we turn the flat table into an interaction matrix, the type implicit needs. A little earlier we created a dummy `item_id`. We'll convert to a sparse matrix. The item with id 999999 stands for all the products that didn't make the top 1,000.

```python
data_train.loc[~data_train['item_id'].isin(top_1000), 'item_id'] = 999999

user_item_matrix = pd.pivot_table(data_train,
                                  index='user_id', columns='item_id',
                                  values='quantity',
                                  aggfunc='count',
                                  fill_value=0)

user_item_matrix = user_item_matrix.astype(float)

sparse_user_item = csr_matrix(user_item_matrix).tocsr()

user_item_matrix.head(3)
```

![The user–item matrix](https://your-scorpion.ru/wp-content/uploads/2023/05/jkf.gif)

We look at the matrix density — in my case 56.44131599277775: `user_item_matrix.sum().sum() / (user_item_matrix.shape[0] * user_item_matrix.shape[1]) * 100`.

Next we create special dictionaries mapping the real id to the row number — it's more convenient for numpy this way. If the matrix dimension is 2,500, the user ordering runs to 2,500, and the dictionary tells us which row corresponds to which real user.

```python
userids = user_item_matrix.index.values
itemids = user_item_matrix.columns.values

matrix_userids = np.arange(len(userids))
matrix_itemids = np.arange(len(itemids))

id_to_itemid = dict(zip(matrix_itemids, itemids))
id_to_userid = dict(zip(matrix_userids, userids))

itemid_to_id = dict(zip(itemids, matrix_itemids))
userid_to_id = dict(zip(userids, matrix_userids))
```

Time to build the recommender. We'll set the number of neighbors to 5, along with the recommendation method. We're interested in recommendations for just one user, so we pass user id 2, the matrix, and the number of recommendations `N=5`. And our recommender is ready — we've got the dot product.

```python
sparse_user_item = csr_matrix(user_item_matrix).astype('double').tocsr()
```

K=5 lets us shrink the matrix by working only with the most similar products — 5 being the number of neighbors that stay in the matrix. Then, for the user at row 2, we pull a certain number of recommendations and do *not* throw out the products the user has already seen. Into `filter_items` you can add the products you don't want to show — our DUMMY_ITEM_ID, 999999, for example.

```python
%%time
from scipy.sparse import csr_matrix, coo_matrix
from implicit.nearest_neighbours import ItemItemRecommender, CosineRecommender, TFIDFRecommender, BM25Recommender

model = ItemItemRecommender(K=5, num_threads=4)

model.fit(sparse_user_item,
          show_progress=True)

recs = model.recommend(userid=userid_to_id[2],
                       user_items=csr_matrix(user_item_matrix).tocsr(),
                       N=5,
                       filter_already_liked_items=False,
                       filter_items=None,
                       recalculate_user=True)
```

The output gives us row numbers and weights.

**Matrix decompositions.** Sometimes we need to decompose a matrix into separate matrices. One of the most popular methods is SVD — and it really is used, though it parallelizes. So: we have a matrix of numbers W, and we want to approximate it by matrices U, S, V. The numbers on the main diagonal are the singular values. The idea is simple: remove a row of zeros from the matrix and nothing changes. This is how we decompose a matrix — of the pixel count of a photograph, for instance — and find a new dimensionality. It's one way to optimize a photo's file size.

![SVD decomposition](https://your-scorpion.ru/wp-content/uploads/2023/05/Group-47398.png)

The picture above shows that we take k columns/rows and get an approximation of the original matrix. Suppose the matrix W is a photograph and the little squares are pixel brightness. Then the SVD decomposition lets us split one matrix into three and get a reduction in dimensionality.

So much for photographs — now to the business. In the matrix, users sit on the rows and products on the columns. We split the resulting matrix into a separate one for users and a separate one for products. And although we've reduced the dimensionality, our real goal is prediction — filling in the empty cells.

For proper recommendations we'll look at ALS. The idea is as simple as two matrices multiplied together to give a new one. ALS is fast, can be parallelized, and has regularization. You can apply various matrix weightings — TF-IDF, BM25 — and almost always it improves the quality a great deal.

Don't forget to run `pip install implicit`. For the matrices we'll use `csr_matrix`, and for the factorization `AlternatingLeastSquares`.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import implicit
from scipy.sparse import csr_matrix

from implicit.als import AlternatingLeastSquares
from implicit.nearest_neighbours import bm25_weight, tfidf_weight

import os, sys

module_path = os.path.abspath(os.path.join(os.pardir))
if module_path not in sys.path:
    sys.path.append(module_path)
```

Next we load the data: again set the last three weeks aside for the test and use the earlier weeks to train the model.

```python
data = pd.read_csv('export.csv')
data.columns = [col.lower() for col in data.columns]
data.rename(columns={'household_key': 'user_id',
                     'product_id': 'item_id'},
            inplace=True)

test_size_weeks = 3

data_train = data[data['week_no'] < data['week_no'].max() - test_size_weeks]
data_test = data[data['week_no'] >= data['week_no'].max() - test_size_weeks]

data_train.head(2)
```

![Loaded and split data](https://your-scorpion.ru/wp-content/uploads/2023/05/Untitled-2.gif)

Next, the feature information: we pull useful features out of the products table. Typing `item_features.department.unique()` shows some useful columns — department, for one. The contents of `product.csv`:

| product_id | brand | department | curr_size_of_product |
| --- | --- | --- | --- |
| 23243 | Private | GROCERY | 22 LB |
| 89433 | Private | MISC. TRANS. | 22 LB |
| 43263 | National | GROCERY | 22 LB |
| 52243 | National | MISC. TRANS. | 22 LB |

```python
item_features = pd.read_csv('product.csv')
item_features.columns = [col.lower() for col in item_features.columns]
item_features.rename(columns={'product_id': 'item_id'}, inplace=True)

item_features.head(2)
```

Into `actual` we set aside the test products for the user — we'll need this later to compute the metrics.

```python
result = data_test.groupby('user_id')['item_id'].unique().reset_index()
result.columns = ['user_id', 'actual']
result.head(2)
```

![The actual test products per user](https://your-scorpion.ru/wp-content/uploads/2023/05/etetett.gif)

We create a matrix of predictions over the popular products only. For each user we already have the `actual` column with the products set aside from the test. Now we cut down to the 1,000 most popular products.

```python
popularity = data_train.groupby('item_id')['quantity'].sum().reset_index()
popularity.rename(columns={'quantity': 'n_sold'}, inplace=True)

top_1000 = popularity.sort_values('n_sold', ascending=False).head(1000).item_id.tolist()
```

And again we shunt the products that didn't make the top 1,000 onto id 999999. We build a sparse matrix, as in the example above. I'll skip the dictionary step here, since that code appears earlier in the article.

```python
data_train.loc[~data_train['item_id'].isin(top_1000), 'item_id'] = 999999

user_item_matrix = pd.pivot_table(data_train,
                                  index='user_id', columns='item_id',
                                  values='quantity',
                                  aggfunc='count',
                                  fill_value=0)

user_item_matrix = user_item_matrix.astype(float)

sparse_user_item = csr_matrix(user_item_matrix).tocsr()

user_item_matrix.head(3)
```

Let's apply ALS. The most important parameter is `recalculate_user=True`, meaning that when information about a user is passed in, the vector gets recomputed. In business terms: suppose that at training time a user `n+1` didn't exist, and then they appeared — with some purchase history, no less. The first, intuitive move is to retrain the model, but that's slow. Since we already have the matrix, we can recompute for the user in half a step and get their vector. We'll set the regularization coefficient to 0.001, and after running the code, user id 2 was offered the following items.

```python
%%time
model = AlternatingLeastSquares(factors=100,
                                regularization=0.001,
                                iterations=15,
                                calculate_training_loss=True,
                                num_threads=4)

model.fit(csr_matrix(user_item_matrix).T.tocsr(),
          show_progress=True)

recs = model.recommend(userid=userid_to_id[2],
                       user_items=csr_matrix(user_item_matrix).tocsr(),
                       N=5,
                       filter_already_liked_items=False,
                       filter_items=None,
                       recalculate_user=True)
```

If the design has a "see also / similar products" block, `model.similar_items(1, N=5)` will fill it with content in short order.

![Similar-items recommendations](https://your-scorpion.ru/wp-content/uploads/2023/05/лофроажа.gif)

Hurray, we have a trained model. It lives in latent space. If we need the separate matrices, that's just as easy: `model.item_factors.shape` and `model.user_factors.shape`. Multiply those matrices together and you get a new one. On the trained model you can and should apply dimensionality-reduction methods — PCA, for example. Instead of 100 components we'd then keep just 2 — or rather, keep only the most useful part, which speeds the algorithm up. And to improve the quality of the ALS decomposition, you can weight the matrix with TF-IDF at little cost — an algorithm we've already mentioned. Here, though, TF-IDF is no longer a recommender but a matrix weighting; it returns a square matrix of product similarity. Usually this trick raises the metrics substantially. It's better to predict on the unweighted part of the matrix, and here's why: popular products can end up with small values and rare products with high ones, which isn't quite right. Weighting the matrix helps neutralize the problem.

The second recommendation is optimization: change the regularization, the number of iterations (more iterations = longer runtime), and play with the weighting functions.

## And now we've landed in a corporation

We've talked about recommendation systems for small services, but what to do when we land in a corporation with a vast audience? We'll have to wring the maximum out of our little recommenders, and for that we reach for two-level — or multi-level — recommendation models. Solutions based on two-level recommendations usually take the top places in competitions, so there's no doubting their effectiveness.

All recommendation models, collaborative or content-based, have their own problems, which hybrid algorithms solve. The very first problem to sort out is always runtime. With a vast audience and a vast number of items, even with infinite compute we couldn't score recommendations for every possible pair. The quick solution: the product of all items by all users, keeping only the good recommendations for ourselves. On the first level we work with a large number of candidates (an order of magnitude more than we need to predict), and a second algorithm — a more complex model — ranks the first model's output. The redundancy on the first level is necessary so that the second-level model has good material to re-rank. But! Feed in too many candidates and performance leaves much to be desired. You have to find the balance.

So the idea is quite simple: two levels of models or more, each level more complex than the last and therefore able to handle less and less data in an adequate time. More products = a simpler model. In slightly more technical terms, we select the top-N (400) candidates with a fast ALS model, then re-rank them with a complex model such as LightGBM and pick the top-k (16). Bear in mind that recommendation models (ALS, LightFM) often lose out to classic classification models like LightGBM — but when there's a lot of data and predictions, `# items * # users`, LightGBM can't cope with the volume.

The first level is candidate selection at high coverage, and can be implemented with ALS and item-item recommendations. On the first level I always use recall, and the higher its value the better. I've often seen the first-level model's quality assessed by all sorts of different metrics, but in my practice the task was always to guess as many of the products users clicked as possible — hence my advice to use recall. Guess most of the products, and you'll be able to rank them more precisely with the second-level model later.

Implementing the two-level model. We start with the first level and import the libraries. A reminder: implicit is used for regression — predicting the score.

```python
import os
import sys

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from scipy.sparse import csr_matrix
from lightgbm import LGBMClassifier

try:
    import implicit
except ImportError:
    !pip install implicit
    import implicit

module_path = os.path.abspath(os.path.join(os.pardir))
if module_path not in sys.path:
    sys.path.append(module_path)
```

Importing our own functions, whose implementations you'll find above:

```python
from metrics import precision_at_k, recall_at_k
from utils import prefilter_items
from recommenders import MainRecommender
```

```python
item_features = pd.read_csv('../content/sample_data/product.csv')
user_features = pd.read_csv('../content/sample_data/hh_demographic.csv')
data = pd.read_csv('../content/retail_test1.csv')
```

For the second-level model to work correctly, we need to validate. We split the dataset into several parts, as below. Alongside the interactions, we add features and split into train/test.

```python
item_features.columns = [col.lower() for col in item_features.columns]
user_features.columns = [col.lower() for col in user_features.columns]

# Rename columns for consistency between dataframes
item_features.rename(columns={'product_id': 'item_id'}, inplace=True)
user_features.rename(columns={'household_key': 'user_id'}, inplace=True)

# Define the size of training and validation datasets
val_lvl_1_size_weeks = 6  # Size of the validation dataset for level 1 model (in weeks)
val_lvl_2_size_weeks = 3  # Size of the validation dataset for level 2 model (in weeks)

# Create training and validation datasets for level 1 model
data_train_lvl_1 = data[data['week_no'] < data['week_no'].max() - (val_lvl_1_size_weeks + val_lvl_2_size_weeks)]
data_val_lvl_1 = data[
    (data['week_no'] >= data['week_no'].max() - (val_lvl_1_size_weeks + val_lvl_2_size_weeks)) &
    (data['week_no'] < data['week_no'].max() - val_lvl_2_size_weeks)
]

# Create training and validation datasets for level 2 model
data_train_lvl_2 = data_val_lvl_1.copy()  # For clarity. Further modifications will be made to this dataset.
data_val_lvl_2 = data[data['week_no'] >= data['week_no'].max() - val_lvl_2_size_weeks]
data_train_lvl_1.head(2)
```

Predicting in Spark isn't very convenient, so we use approximate-search methods. We keep 2,000 objects, so the runtime should suit us.

```python
# Get the number of unique items in the training dataset before applying item prefiltering
n_items_before = data_train_lvl_1['item_id'].nunique()
data_train_lvl_1 = prefilter_items(data_train_lvl_1, item_features=item_features, take_n_popular=2000)

# Get the number of unique items in the training dataset after applying item prefiltering
n_items_after = data_train_lvl_1['item_id'].nunique()

print('Number of unique items decreased from {} to {}'.format(n_items_before, n_items_after))
```

And the most enjoyable stage: we train the first-level model with `main recommender`, which holds ALS + an item-item recommender.

```python
recommender = MainRecommender(data_train_lvl_1)
recommender.get_als_recommendations(2375, N=5)
recommender.get_own_recommendations(2375, N=5)
recommender.get_similar_items_recommendation(2375, N=5)
recommender.get_similar_users_recommendation(2375, N=5)
```

Now for the second level, whose data we've already prepared. On the second level it's customary to use neural nets or boosting. Training happens on the chosen candidates: the first model gave us a result, we got predictions, and we passed them into the second model — and it's on those predictions that we train. Once the boosting model is trained and applied, it often turns out that there are good candidates and not-so-good ones, which leads to errors. So the second-level model should train only on the first model's output.

Practice. We form a dataset of `0`s and `1`s, which lets us see the average target. `1` is what the user viewed; `0` is what landed among the candidates. As for the target, it's most likely a business metric tied to money — clicks, adds-to-cart, or purchases, all of which can be optimized. In the example below we'll train on `data_train_lvl_2` and only on the chosen candidates. And if the user bought fewer than 70 products, `get_own_recommendations` will top the recommendations up with the most popular.

```python
# Create a DataFrame containing unique user IDs from the training dataset level 2
users_lvl_2 = pd.DataFrame(data_train_lvl_2['user_id'].unique())
users_lvl_2.columns = ['user_id']

# Filter out users in users_lvl_2 that are not present in the training dataset level 1 (warm start)
train_users = data_train_lvl_1['user_id'].unique()
users_lvl_2 = users_lvl_2[users_lvl_2['user_id'].isin(train_users)]

users_lvl_2['candidates'] = users_lvl_2['user_id'].apply(lambda x: recommender.get_own_recommendations(x, N=70))
```

```python
s = users_lvl_2.apply(lambda x: pd.Series(x['candidates']), axis=1).stack().reset_index(level=1, drop=True)
s.name = 'item_id'

users_lvl_2 = users_lvl_2.drop('candidates', axis=1).join(s)
users_lvl_2['flag'] = 1

users_lvl_2.head(4)
```

```python
users_lvl_2.shape[0]
users_lvl_2['user_id'].nunique()
```

```python
targets_lvl_2 = data_train_lvl_2[['user_id', 'item_id']].copy()
targets_lvl_2['target'] = 1  # purchases only here

targets_lvl_2 = users_lvl_2.merge(targets_lvl_2, on=['user_id', 'item_id'], how='left')

targets_lvl_2['target'].fillna(0, inplace=True)
targets_lvl_2.drop('flag', axis=1, inplace=True)
targets_lvl_2.head(2)
```

For content recommendations one usually leans on boosting — the best known being LightGBM, XGBoost, CatBoost. You can pick others; the main thing is not to use linear algorithms, because we need ranking. LightGBM is a perfectly good choice for the second level and gives classification out of the box. On features: it's enough to take the candidates and pull features from the dataset — the average purchase check, for instance. But don't forget common sense: if the check is 1,000 rubles, don't recommend a product for 10,000. Or the average purchase amount per product in each category, or purchase frequency. The algorithm has to support some kind of ranking. I'll take LightGBM with a plain binary loss as the simplest option to demonstrate. For a full-fledged system, though, use multi-class models. This is an example without feature generation, beginning with `item_features.head(2)`:

![LightGBM feature table](https://your-scorpion.ru/wp-content/uploads/2023/07/unnamed-file.png)

Next we can take user features such as age and add an average-check parameter, plus some item features. What else can we use as features: categorical features from the dataset, or features generated relative to the user, the item, or the pair. A common product feature, for instance, is how often it's bought per week. Take alcoholic drinks — they aren't bought all that often by children, and that's a good example of user/item data. Features can also be data from the first level.

```python
targets_lvl_2 = targets_lvl_2.merge(item_features, on='item_id', how='left')
targets_lvl_2 = targets_lvl_2.merge(user_features, on='user_id', how='left')

targets_lvl_2.head(2)
```

In my experience the most popular feature for users is the average check. Also: the average purchase amount per product in each category, the number of purchases in each category, monthly purchase frequency, the share of weekend purchases, the share of purchases in the morning/afternoon/evening.

At the data-prep stage don't forget pre-filtering. This step is needed to remove old products (with news, say), or rare items, or whole categories of products nobody buys. There's little use in them, and they'll eat compute.

```python
X_train = targets_lvl_2.drop('target', axis=1)
y_train = targets_lvl_2[['target']]
```

```python
# Get the names of categorical features in the DataFrame 'X_train'
cat_feats = X_train.columns[2:].tolist()

# Convert the selected categorical features to the 'category' data type
# This helps optimize memory usage and can improve performance for categorical data
X_train[cat_feats] = X_train[cat_feats].astype('category')
cat_feats
```

And, finally, the prediction itself. Now you can assess a ranking metric such as NDCG. That, in essence, is the whole prediction process with a two-level model.

```python
lgb = LGBMClassifier(objective='binary', max_depth=7, categorical_column=cat_feats)
lgb.fit(X_train, y_train)

train_preds = lgb.predict(X_train)
```

Then post-filtering. Having created recommendations for a user, it's advisable to filter the items after prediction — usually the ones the user has already seen. You can do this inside the model itself or at the post-filtering stage: a simple cutting-away of products. You can cut users too — removing from the marketing-email base, for example, anyone who hasn't opened a single one of our emails in a year.

And of course, systems this complex and multi-level aren't run on a home computer. I use Spark to distribute the computation; it installs with a simple `pip install pyspark`. Why Spark? Because a dataset usually runs to millions of items and even more users, and at that volume one machine's resources won't be enough — and you can't speed an algorithm up forever — so we move to distributed computing on Spark.
