# Simple Recommendation Systems: ALS, AP@k, NDCG

Recommendation systems look deceptively simple from the outside: "show users what they might like." In practice, this is a high-load ML problem where business goals, ranking quality, latency, and data sparsity collide.

To build even a baseline recommender, you need:

- Linear algebra (matrix operations are central).
- Mathematical statistics (for evaluation and experiments).
- Practical Python and SQL.
- Data engineering tools (Spark/Hadoop, Airflow, Kafka).

At production scale, fast execution matters as much as model quality. That is why strong teams combine good modeling with distributed computation and careful prefiltering.

---

## Why Recommendations Are Hard

A canonical example is video, music, or news recommendation. The business objective is usually straightforward: keep users engaged after the current action.

If a user listens to rock every morning, the system should not revert to generic "popular" tracks next day. Timing and context matter.

Three persistent problems:

1. **Scale**: huge user-item matrices and expensive ranking.
2. **Cold start**: new users and new items with no history.
3. **Information bubbles**: over-recommending familiar content and missing exploration.

### Explicit vs implicit feedback

Recommendation pipelines rely on user actions.

- **Explicit feedback**: direct ratings, likes/dislikes, favorites.
- **Implicit feedback**: views, purchases, comments, searches, dwell events.

Implicit feedback dominates in real systems, but it is noisier:

- A comment can be negative.
- A purchase can be a gift (not personal preference).
- Add-to-cart does not always mean intent to buy.

You cannot avoid this ambiguity; you have to model it.

---

## Metrics

Metrics in recommenders split into two broad groups:

1. **Quality / relevance metrics**
2. **Ranking metrics**

And in real business settings, there is a third practical layer:

3. **Diversity / coverage metrics**

Examples: `coverage`, `surprisal`, `unexpectedness`, `novelty`, `serendipity`, `diversity`.

A useful warning: there is no single universal metric formula that everyone implements identically. Even standard names like `roc_auc_score` can differ across libraries.

### Hit Rate

The simplest metric: did the user interact with at least one recommended item?

- `hit_rate = 1` if at least one recommended item is relevant.
- `hit_rate = 0` otherwise.

`hit_rate@k` checks only top-k recommendations.

```python
import pandas as pd
import numpy as np

def hit_rate(recommended_list, bought_list):
    bought_list = np.array(bought_list)
    recommended_list = np.array(recommended_list)
    flags = np.isin(bought_list, recommended_list)
    return int(flags.sum() > 0)

def hit_rate_at_k(recommended_list, bought_list, k=5):
    recommended_list = np.array(recommended_list)[:k]
    bought_list = np.array(bought_list)
    flags = np.isin(bought_list, recommended_list)
    return int(flags.sum() > 0)

data = pd.DataFrame({
    "user_id": ["u1", "u2", "u3"],
    "recommended_list": [
        [143, 156, 1134, 991, 27],
        [1543, 3345, 533, 11, 43],
        [156, 3345, 10, 15, 1134],
    ],
    "bought_list": [[156], [611], [10, 24, 521]],
})

print(data.apply(lambda x: hit_rate(x["recommended_list"], x["bought_list"]), axis=1).mean())
```

If per-user hits are `[1, 0, 1]`, the average is `0.6667`.

Hit Rate is useful for expensive-item domains where one successful match can be enough.

### Precision and Precision@k

`Precision` measures the share of recommended items that were relevant.

- `Precision = relevant_recommended / recommended`
- `Precision@k = relevant_recommended@k / k`

This is usually more business-friendly than plain Hit Rate because it reflects recommendation list quality, not just binary success.

```python
import numpy as np

def precision(recommended_list, bought_list):
    recommended_list = np.array(recommended_list)
    bought_list = np.array(bought_list)
    flags = np.isin(recommended_list, bought_list)
    return flags.sum() / len(recommended_list)

def precision_at_k(recommended_list, bought_list, k=5):
    recommended_list = np.array(recommended_list)[:k]
    bought_list = np.array(bought_list)
    flags = np.isin(recommended_list, bought_list)
    return flags.sum() / len(recommended_list)
```

### Money Precision@k

Businesses often care about revenue-weighted relevance.

`Money Precision@k` weights hits by item prices.

```python
def money_precision_at_k(recommended_list, bought_list, prices_recommended, k=5):
    recommended_list = np.array(recommended_list)[:k]
    prices_recommended = np.array(prices_recommended)[:k]
    bought_list = np.array(bought_list)

    flags = np.isin(recommended_list, bought_list).astype(int)
    return (flags * prices_recommended).sum() / prices_recommended.sum()
```

Example:

- Recommended prices: `[33, 42, 35]`
- Bought flags: `[1, 0, 1]`

Unweighted precision: `2/3 = 0.6667`

Revenue-weighted precision:

`(1*33 + 0*42 + 1*35) / (33 + 42 + 35) = 68/110 = 0.6182`

### Recall

Recall asks: what share of truly relevant items did we recover?

```python
def recall(recommended_list, bought_list):
    recommended_list = np.array(recommended_list)
    bought_list = np.array(bought_list)
    flags = np.isin(bought_list, recommended_list)
    return flags.sum() / len(bought_list)
```

Recall is especially useful in first-stage candidate generation, where missing relevant items is costly for later ranking.

---

## Ranking Metrics

Relevance is not enough. Order matters.

In music/news products, bad ranking quality degrades user experience fast and can hurt sticky factor (`DAU/MAU`).

### AP@k

Average Precision at k captures ranking quality across the top-k list.

For each relevant position, compute precision up to that rank, sum, and normalize.

```python
import numpy as np

def precision_at_k(recommended_list, bought_list, k=5):
    recommended_list = np.array(recommended_list)[:k]
    bought_list = np.array(bought_list)
    return np.isin(recommended_list, bought_list).sum() / len(recommended_list)

def ap_k(recommended_list, bought_list, k=5):
    recommended_list = np.array(recommended_list)[:k]
    bought_list = np.array(bought_list)
    flags = np.isin(recommended_list, bought_list)

    if flags.sum() == 0:
        return 0.0

    score = 0.0
    for i in range(1, k + 1):
        if flags[i - 1]:
            score += precision_at_k(recommended_list, bought_list, k=i)

    return score / flags.sum()
```

### NDCG

`NDCG` (Normalized Discounted Cumulative Gain) rewards relevant items shown earlier in the ranking.

- `DCG` discounts gains by rank position.
- `IDCG` is the ideal DCG under perfect ordering.
- `NDCG = DCG / IDCG`.

A practical correction worth remembering:

If relevance is `[1, 0, 0, 1, 0]` (plus one relevant item outside top-5), then:

`DCG = 1/1 + 1/log2(5) = 1.4307`, not a value below 1.

This arithmetic is a common source of mistakes in hand calculations.

### MRR

`MRR` (Mean Reciprocal Rank) uses only the rank of the first relevant item.

It is simple and useful when "first good hit quickly" is the primary product goal.

---

## Loss Function and Algorithm Families

In any recommender, the objective is minimizing loss under product constraints.

A practical taxonomy:

1. **Heuristic recommenders**
2. **Collaborative filtering**
3. **Content-based recommenders**
4. **Hybrid systems**

### Heuristic baselines

Baselines are intentionally simple, fast to ship, and often surprisingly strong.

Common variants:

- Most popular recommender.
- Uniform random recommender.
- Popularity-weighted random recommender.
- Own-history recommender (recommend what user repeatedly buys/views).

Trade-off: these models often miss new items unless you explicitly blend novelty/seasonality/randomness.

---

## Baseline Pipeline with Time Split

Use a time-based split, not random split.

Why:

Random split leaks future popularity into train, giving over-optimistic offline metrics.

```python
import pandas as pd

data = pd.read_csv('retail_train.csv')

test_size_weeks = 3

data_train = data[data['week_no'] < data['week_no'].max() - test_size_weeks]
data_test = data[data['week_no'] >= data['week_no'].max() - test_size_weeks]

result = data_test.groupby('user_id')['item_id'].unique().reset_index()
result.columns = ['user_id', 'actual']
```

A simple popularity recommender:

```python
def popularity_recommendation(data, n=5):
    popular = data.groupby('item_id')['sales_value'].sum().reset_index()
    popular.sort_values('sales_value', ascending=False, inplace=True)
    return popular.head(n).item_id.tolist()

popular_recs = popularity_recommendation(data_train, n=5)
result['popular_recommendation'] = result['user_id'].apply(lambda _: popular_recs)
```

---

## Item-Item Collaborative Filtering with implicit

For weighted interaction matrices and nearest-neighbor item recommendation, `implicit` is a practical library.

```python
import pandas as pd
import numpy as np
from scipy.sparse import csr_matrix
from implicit.nearest_neighbours import ItemItemRecommender
```

Prefilter to top-N items for speed:

```python
popularity = data_train.groupby('item_id')['quantity'].sum().reset_index()
popularity.rename(columns={'quantity': 'n_sold'}, inplace=True)

top_1000 = popularity.sort_values('n_sold', ascending=False).head(1000).item_id.tolist()

data_train.loc[~data_train['item_id'].isin(top_1000), 'item_id'] = 999999
```

Build user-item matrix:

```python
user_item_matrix = pd.pivot_table(
    data_train,
    index='user_id',
    columns='item_id',
    values='quantity',
    aggfunc='count',
    fill_value=0
).astype(float)

sparse_user_item = csr_matrix(user_item_matrix).tocsr()
```

Fit and recommend:

```python
model = ItemItemRecommender(K=5, num_threads=4)
model.fit(sparse_user_item, show_progress=True)

recs = model.recommend(
    userid=2,
    user_items=sparse_user_item,
    N=5,
    filter_already_liked_items=False,
    filter_items=None,
    recalculate_user=True
)
```

Also useful for "similar products" blocks:

```python
model.similar_items(1, N=5)
```

---

## ALS Matrix Factorization

ALS remains one of the strongest practical baselines for large-scale recommendation.

Benefits:

- Fast enough for large sparse data.
- Parallelizable.
- Works well with weighting schemes (BM25, TF-IDF).

```python
from implicit.als import AlternatingLeastSquares
from implicit.nearest_neighbours import bm25_weight, tfidf_weight
```

Train/test by time, then fit ALS:

```python
model = AlternatingLeastSquares(
    factors=100,
    regularization=0.001,
    iterations=15,
    calculate_training_loss=True,
    num_threads=4
)

model.fit(csr_matrix(user_item_matrix).T.tocsr(), show_progress=True)
```

Generate recommendations with `recalculate_user=True` to refresh vectors for users not seen in the original fit pass.

Retrieve latent matrix shapes:

```python
print(model.item_factors.shape)
print(model.user_factors.shape)
```

For additional optimization, you can apply dimensionality reduction (for example, PCA) on latent factors.

---

## Two-Level Recommenders for Corporate Scale

At large scale, one model is rarely enough.

The common architecture is multi-stage:

1. **Stage 1 (fast candidate generation)**: high recall, broad candidate set.
2. **Stage 2 (heavy reranking)**: expensive model on small candidate list.

Typical pattern:

- Stage 1 ALS returns top-N candidates (for example, 400).
- Stage 2 model (for example, LightGBM) reranks and outputs top-k (for example, 16).

Why this works:

- Exhaustive ranking over all user-item pairs is infeasible.
- Stage 2 quality depends on Stage 1 recall.
- Too many candidates hurts latency; too few hurts quality.

### Data split for two levels

```python
val_lvl_1_size_weeks = 6
val_lvl_2_size_weeks = 3

data_train_lvl_1 = data[data['week_no'] < data['week_no'].max() - (val_lvl_1_size_weeks + val_lvl_2_size_weeks)]
data_val_lvl_1 = data[
    (data['week_no'] >= data['week_no'].max() - (val_lvl_1_size_weeks + val_lvl_2_size_weeks)) &
    (data['week_no'] < data['week_no'].max() - val_lvl_2_size_weeks)
]

data_train_lvl_2 = data_val_lvl_1.copy()
data_val_lvl_2 = data[data['week_no'] >= data['week_no'].max() - val_lvl_2_size_weeks]
```

Apply aggressive item prefiltering (for example, top-2000) before candidate generation.

### Candidate dataset for stage 2

Construct binary targets over generated candidates:

- `1`: user interacted with item.
- `0`: item is candidate but not interacted.

This teaches reranker to separate strong and weak candidates from stage 1 output.

### Stage 2 model

A common practical choice is `LGBMClassifier` with binary objective for a first production pass.

```python
from lightgbm import LGBMClassifier

lgb = LGBMClassifier(objective='binary', max_depth=7)
lgb.fit(X_train, y_train)
train_preds = lgb.predict(X_train)
```

Feature examples:

- User features: age, average check, weekday/weekend share, time-of-day behavior.
- Item features: department, category-level frequency, trend features.
- User-item pair features: category affinity, recency, first-level scores.

### Post-filtering

After scoring, filter out:

- Already-seen items (if product logic requires novelty).
- Banned categories.
- Business-restricted SKUs.

---

## Distributed Execution

At millions of users and items, one machine is rarely enough.

Spark is often used to scale candidate generation and feature pipelines:

```bash
pip install pyspark
```

When matrix dimensions explode, distributed computation is not optional.

---

## Practical Notes on Diversity and Business Alignment

High offline relevance can still underperform business goals.

Examples:

- Pure popularity boosts short-term clicks but shrinks catalog exposure.
- Own-history loops increase repetition and reduce discovery.
- Strong ranking with poor diversity can damage retention.

Balance recommendation quality with:

- Catalog coverage
- Novelty
- Revenue sensitivity
- User fatigue prevention

---

## Conclusion

A robust recommender stack is usually built in layers:

1. Fast baseline and quality metrics.
2. Candidate generation focused on recall.
3. Reranking focused on business value.
4. Post-filtering and operational constraints.
5. Distributed infrastructure for scale.

If you are starting from scratch, begin with time-based splits, strong baselines, and a transparent metric suite (`precision@k`, `recall@k`, `NDCG`, and revenue-aware metrics). Once that is stable, move to multi-stage ranking.

Relevant figures from the source article:

![Recommendation system diagram](https://your-scorpion.ru/wp-content/uploads/2023/03/Group-47391.png)

![Ranking and relevance visual](https://your-scorpion.ru/wp-content/uploads/2023/04/Group-47393.png)

![Modeling pipeline illustration](https://your-scorpion.ru/wp-content/uploads/2023/05/Group-47401.png)

![Additional formula reference](https://your-scorpion.ru/wp-content/uploads/2023/07/unnamed-file.png)
