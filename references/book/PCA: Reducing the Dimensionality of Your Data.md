# PCA: Reducing the Dimensionality of Your Data

*Maksim Tcvetkov · Product Design and Security Lead*


---

Reducing dimensionality is almost always a win. The model learns faster, overfits less, low-value features never make it in to spoil the quality — pure economy all round. And if the model is genuinely large (200+ features), any team lead will demand PCA + UMAP and a bit of robustness for good measure. What we'll look at, as the title gives away, is principal component analysis (PCA) and everything orbiting it. PCA is a simple, well-known way to reduce dimensionality, used when interpretability doesn't matter and you can afford to build linear combinations — in economics, for instance, to fold a heap of indicators into one dominant one. The method comes down to approximating the feature matrix with a matrix of lower rank, a so-called low-rank approximation. Here there's no target variable, there are many features, they're poorly interpretable, and data is scarce. That situation is exactly what PCA is for.

Let's first look at the other dimensionality-reduction methods and see why the choice fell on PCA. There are many ways to shrink a dataset, and the algorithms for reducing the feature space split into two groups: *feature selection* (dropping the least important) and *dimensionality reduction* by forming new features out of the old ones.

The most primitive method is univariate feature selection, which works from the correlation of each feature with the main variable and then keeps features either by a threshold or by a predefined count. Another route is L1-regularized regression: crank up the regularization and the feature weights shrink evenly — the weight value simply drops. Once it hits zero, that feature's weight zeroes out and the feature leaves the model, the least important ones first. Alternatively, when a random forest runs, features take part in the splits and that characterizes their importance. These methods are called *univariate* because they assess each feature in isolation, whereas in real life a *group* of features exerts influence together — which is why the method isn't popular.

An example: take the number of likes on Dribbble as the target variable, and build a non-linear dependence off the number of shots. The first shot lands in a category of its own and pulls in plenty of likes; then, as the quantity and quality of shots grow, likes rise in proportion. But past a certain threshold of shots and followers, a designer no longer sees the same explosive growth in new followers and likes as before, because the audience is finite. On top of that, trends can shift and the audience's attention can move to other designers. A linear model can't track behaviour like that. You can use non-linear models — gradient boosting or a random forest, anything with branching. Logistic regression is a linear feature, the construction of a hyperplane in space.

Understand, too, that filtering features by p-value is a very poor option, however beloved of beginners. Only at a great stretch can p-value have a positive effect on the target metric, especially for linear models. The results depend heavily on sample size — a very strong feature can show significant correlation with the target on a small sample. If you're going to go this route, permutation importance plus checking the selected features on cross-validation is the correct choice.

Since the method isn't ideal, that kind of feature selection won't always suit — there's no guarantee it preserves the maximum of useful information. The second approach is dimensionality reduction, where we create new features from the old. You might look toward agglomerative clustering, which does a decent job of merging small clusters into larger ones: at the start each object is its own cluster, and then they're combined into bigger ones. Or the reverse — a divisive algorithm begins with one large cluster and keeps splitting it into smaller pieces.

scikit-learn itself has a built-in feature importance that lets you see which features mattered. Or there's multidimensional scaling, or truncated SVD. Plenty of options.

Consider the non-greedy algorithm — a brute-force sweep of every combination of features, discretely scoring the model's quality. The formula is simple: 2ʸ, where *y* is the number of features. Say we have features 1, 2, 3, 4. First we assess each on its own, then we start combining every possible variant: 1+2, 1+3, 1+4, 2+3, 2+4, and so on. With only four features the algorithm is perfectly fine and stays efficient. But real work has more features than that, and you'll have to switch to a greedy algorithm.

There are two kinds of greedy algorithm. We can build the model up from a single feature and keep choosing the best one — the *forward* (bottom-up) variant. First we build a model on each feature, find the best, and add a second feature to it. That prunes the number of options: we tried variants 1, 2, 3, 4, realized 2 was best, and now try every combination with it; we saw that 2+3 works best of all, so next we look for a third feature. The *backward* (top-down) variant takes all features at once and eliminates them one at a time, step by step — a slower method we won't dwell on.

And then PCA. The principle of principal component analysis: take a pair of features — a smartphone's length and width. They'll almost certainly correlate. You obtain a covariance matrix and keep the vectors with the largest eigenvalues, which lets you "compress" many features into fewer. Clearly, in production you might have 100 features and want to squeeze them into 50, or 1000 into 50. The first component takes the bulk of the informativeness and it declines from there, so it's not a given that the new 50 features are the strongest. When the algorithm finishes you get simple linear combinations of the original features with different weights — the components. What sets them apart is that they don't correlate with one another; the algorithm hands you new features.

The example spoke of two features, but you can cluster on a single row of data too. Look at the illustration below — an inquisitive eye will spot at once that user 1 and user 2 resemble each other, while users 3, 4, and 5 clearly belong to a different cohort.

![One-dimensional clustering of users](https://your-scorpion.ru/wp-content/uploads/2019/12/Frame-3-2.png)

But add a second feature and we have to plot on two axes. Again, user 1 and user 2 fall into one cluster. Add a third dimension and you'd have to show it somehow — by the size of the dots on the chart, say — which is already less legible; add a fourth and it becomes a hard visualization problem. PCA visualizes quickly; t-SNE is fashionable and can even visualize a fourth dimension. But a designer will have to reach for PCA to bring the data down to two dimensions, because reports for management demand simple, legible visuals.

![Adding dimensions makes visualization harder](https://your-scorpion.ru/wp-content/uploads/2019/12/Frame-4.png)

On the left of the chart below is the principle of "scattering" the data across the plot: the red dot is the mean, a shared centre. It's used to move the centre of the dataset to the centre of the chart (on the right) and to find the position of a line at which the distance from every point to that red line is minimal. All of this is possible by maximizing the sum of the squared distances. The red line is called PC1.

![Centring the data and fitting PC1](https://your-scorpion.ru/wp-content/uploads/2019/12/Group-12.png)

But how is the line built? Here we arrive at the answer to why the method is called *least squares*: it's simply a way of finding the smallest distance from the line to a point on the chart. The distance from a point to the centre is fixed, so we can draw the dashed red line A (chart below). And now the Pythagorean theorem comes in — the one used the world over to find distances between points, a² = c² + b². The idea: as **c** grows, **b** shrinks, and vice versa. So we can use either **b** or **c** to find the smallest distance from a point to the line **a**. It's easier to compute **c**, minimizing the sum of the squared distances from the points to the origin. Do this for every point, sum the values, and you get the sum of squared distances = SS (sum of squares).

![Pythagoras and the sum of squares](https://your-scorpion.ru/wp-content/uploads/2019/12/Frame-5-1.png)

Then we compute the linear combination. PC1 is a linear combination of the elements; by Pythagoras, a² = c² + b² — in our case a² = 1² + 1.2² — and we take the square root √. The result is 1.56. The next step is scaling — we simply resize the resulting triangle: 1.56 / 1.56 = **1**, 1 / 1.56 = **0.64**, 1.2 / 1.56 = **0.75**. These are our new values, by which the triangle shrinks proportionally.

![Scaling the unit vector](https://your-scorpion.ru/wp-content/uploads/2019/12/Frame-6-2.png)

The Pythagorean theorem is also known here as the perpendicular lemma. To find the best linear approximation in a linear space for a vector, you drop a perpendicular from the vector (onto gene 2), and the projection is the best linear approximation. The element ŷ is the best linear approximation of Є in the linear space L.

![The perpendicular lemma and the projection](https://your-scorpion.ru/wp-content/uploads/2021/02/Group-32962.png)

We've computed a unit vector — the eigenvector for PC1. We're done with PC1, but there's still PC2, and it's simply a line perpendicular to PC1. That, broadly, is the general idea, and it's how PCA is carried out via singular value decomposition (SVD). You'll have noticed the resemblance between PCA and SVD? PCA is an analogue of singular value decomposition, but lighter — the minimal eigenvalues and their corresponding eigenvectors have been stripped out. The gist, I think, is clear: we look for a straight line relative to which the data spreads out best when projected.

![Projecting the data onto the principal axis](https://your-scorpion.ru/wp-content/uploads/2019/12/Group-23.png)

## Unsupervised learning and k-means

And here we start talking about unsupervised learning, which is where PCA and clustering are so often deployed together. Unsupervised learning helps you find patterns in a dataset. Commit this to memory: unsupervised learning is clustering. There's no target variable, though the number of clusters may be known — scattering a mobile app's users into groups from their purchase data, for example. Or take an algorithm like k-means. We want to assemble a sample of objects with features and understand how they relate. We aren't after an exact answer; there is no ready answer — the algorithm simply scatters objects into clusters of similar objects. It's called k-means, and without it PCA wouldn't be nearly as popular. A simple, uncontrolled clustering algorithm that groups features by the distance between them. Let's generate some data:

```python
from sklearn.cluster import KMeans
import numpy as np
import pandas as pd
from sklearn.preprocessing import MinMaxScaler

## all values from 0 to 1.
scaler_0and1 = MinMaxScaler()

def generate_dataset(n, seed):
    shift_matrix = np.array([[1, 2], [6, 9], [14, 2]])
    data = np.random.randn(3, 2, n) + shift_matrix.reshape((3, 2, 1)) * 0.101
    data = data.reshape((-3, 3))
    df = pd.DataFrame({'x': data[:, 1], 'y': data[:, 0]}, columns=['x', 'y'])
    df = df.astype(float)
    return df

train = generate_dataset(124, 432)
print(train)
```

```
       x         y
0    0.237691 -0.378895
1   -0.843438 -1.229108
2    0.732647 -0.513095
3   -0.145382  0.147770
4   -0.010221  0.274869
..        …       …
243  1.139096  0.789607
244 -0.407632  0.706096
245  0.580292  0.598956
246  0.260324 -1.176885
247 -1.233812  1.091857
```

Now let's train with `fit_predict` and see which cluster (denoted by numbers) each observation is assigned to. First we specify how many clusters we want, which is what the `n_clusters` parameter is for.

```python
from sklearn.cluster import KMeans
train_scaled = scaler_0and1.fit_transform(train)
kmeans = KMeans(n_clusters=3, random_state=0)
print(kmeans)

train_labels = kmeans.fit_predict(train_scaled)
print(train_labels)
```

```
[2 2 2 2 0 0 0 0 1 2 0 2 2 2 2 0 2 2 2 2 1 1 2 0 1 2 1 0 2 2 2 2 2 1 2 1 0
 2 2 0 0 0 0 0 0 2 0 2 2 0 1 0 1 0 2 2 1 2 0 0 0 1 2 2 1 0 1 2 0 1 1 2 2 0
 2 0 2 2 0 0 0 0 1 1 2 1 0 1 2 2 1 0 1 2 1 1 2 2 2 2 0 0 2 2 1 2 1 1 2 1 2
 1 2 2 0 1 1 1 1 1 0 0 0 2 0 1 1 1 1 0 1 0 1 1 2 1 1 1 1 1 1 1 1 1 1 1 1 1
 1 2 1 0 1 0 0 1 0 0 0 0 1 0 1 1 1 0 1 1 0 1 2 1 0 0 1 1 1 1 1 1 1 0 1 0 0
 0 0 0 0 1 0 1 0 0 0 1 1 0 0 1 0 1 2 1 1 0 1 0 2 0 1 2 0 0 2 2 1 0 2 2 2 2
 2 2 2 1 2 2 1 2 0 1 1 2 2 0 0 0 1 2 1 2 2 1 0 2 2 1]
```

Let's visualize it in colour. Say we had data on different user behaviours; they split into three clusters. First we take the `train` dataset and plot it. The red diamonds are the cluster centres, the so-called centroids.

```python
import matplotlib.pyplot as plt
centers = scaler_0and1.inverse_transform(kmeans.cluster_centers_)
plt.scatter(train['x'], train['y'], c=train_labels)
plt.scatter(centers[:, 0], centers[:, 1], marker='D', color='red')
plt.xlabel('money spent')
plt.ylabel('content viewed')
plt.title('Training data for the model')
```

![k-means clusters with centroids](https://your-scorpion.ru/wp-content/uploads/2019/12/sbr3_depth-3.png)

A fair question: after building a scatterplot it may turn out that all the points are spread evenly and can't be visually separated into clusters. In that case you can try adding more features.

Now let's look at the *inertia* — the sum of the squared distances from objects to the centres of their clusters. The smaller the better; ours is 8.18320090646764. If there are as many clusters as objects, inertia is zero, but we want a sensible number of clusters. Getting it is easy: `print(model.inertia_)`. We aim for a balance between the number of clusters we want and the size of the inertia.

```python
test = generate_dataset(443, 563)
test_scaled = scaler_0and1.transform(test)
test_scaled = pd.DataFrame(test_scaled, columns=['x', 'y'])
test_labels = model.predict(test_scaled)
print(test_labels)
```

We can see that the centroids from the test data align with the main data.

## Reducing dimensionality with PCA

Now to reducing dimensionality itself. We have a dataset of objects and we want to train a decision tree to predict the target variable — but the objects have too many features. Here PCA comes to the rescue. A covariance matrix is computed for the data, and for that matrix the eigenvectors and eigenvalues are found. In the production world it's conventional to split the data into numeric and categorical features and reduce each separately — which also makes it easier to build in interpretability by giving a principal component a proper name.

First, bring every feature to the right scale, either by standardization or normalization. Here's a dataset with two features: column zero is the smartphone's screen diagonal, and column one is the number of mis-taps on key buttons in the app. Remember that observations always sit in the rows and features in the columns. Visualize it and you can see the two features correlate strongly.

```python
import numpy as np
import matplotlib.pyplot as plt
phones = np.array([[5.5, 0.61],
                   [5.1, 0.55],
                   [1.4, 0.21],
                   [3.5, 0.34],
                   [7, 0.81],
                   [6.3, 0.78],
                   [7.2, 0.95]])
plt.scatter(phones[:, 0], phones[:, 1])
```

![Two strongly correlated features](https://your-scorpion.ru/wp-content/uploads/2019/12/sbr3_depth-2.png)

The data has lined up along a straight line, which means we can compress it to a single dimension — one principal component with minimal loss of information.

The next step is centring the data, done very simply with `mean_values = np.mean(phones, axis=0)`, after which we get the transformed feature values with `phones_centered = phones - mean_values`. Now each feature's mean equals zero. Then we find the covariance matrix for the centred feature values — the multidimensional analogue of variance. By the law of linear algebra the vectors sit in the columns, so we swap columns and rows, giving a transposed matrix: `covariance_matrix = np.cov(phones_centered.T)`.

Next we find the eigenvectors of the covariance matrix: `eigenvalues, eigenvectors = np.linalg.eig(covariance_matrix)`. The snag is that this is a matrix and we need a column. The fix is the `reshape` method, which gives us a column:

```python
result_pre = np.dot(phones_centered, eigenvectors[:, 0])
result = result_pre.reshape(-1, 1)
```

```
array([[ 0.35476857],
       [-0.04957755],
       [-3.76326252],
       [-1.66326203],
       [ 1.86798318],
       [ 1.16962746],
       [ 2.08372289]])
```

There we are — we've got the principal component. `eigenvectors` is responsible for the direction (90°, say), while `eigenvalues` is the magnitude of the variance in that direction. The larger the value, the more we like it.

```python
from sklearn.decomposition import PCA
pca = PCA(n_components=1)
mc = pca.fit_transform(phones)
mc
pca.explained_variance_ratio_
# array([0.99916695])
```

`n_components` is usually no more than 400. In our toy case the share we got is very close to one (0.99916695), meaning we'll lose a tiny percentage of information. In banking, even a 10% loss of information is considered fine. Now we put PCA to use. Within classification models the algorithm subtracts the mean from each feature value.

Let's tune the parameter k for PCA. Below we'll build a cumulative-sum chart and find how many components account for PCA's default energy of 98%. The cumulative share of explained variance is the share of variance explained by a principal component together with the components before it.

```python
from sklearn.decomposition import PCA
from sklearn import datasets
import numpy as np
import matplotlib.pyplot as plt

iris = datasets.load_iris()
dataset = iris.data
dataset.shape
dataset = dataset.astype(float)

pca = PCA().fit(dataset)
cumulative = np.cumsum(pca.explained_variance_ratio_)
plt.step([i for i in range(len(cumulative))], cumulative)
plt.show()
```

![Cumulative explained variance across components](https://your-scorpion.ru/wp-content/uploads/2019/12/sbr3_depth-1.png)

*Result: array([0.92461872, 0.97768521, 0.99478782, 1.]) — the cumulative share of variance by component. You can safely drop the last two components and keep the first two.*

The parameter k for PCA is chosen by the "elbow" on the scree plot. The principle is simple: the X axis carries the principal-component numbers and the Y axis their variances. Where the sharpest bend is, that's where we stop.

```python
pcamodel = PCA(n_components=5)
plt.plot(pca.explained_variance_ratio_)
plt.xlabel('number of components')
plt.ylabel('cumulative explained variance')
plt.show()
```

![Scree plot of explained variance](https://your-scorpion.ru/wp-content/uploads/2019/12/sbr3_depth-4.png)

You can see the numbers didn't deceive us: PC1 explains far more of the variance than the components that follow. So we've walked the whole chain: standardize the data → compute the covariance matrix → calculate the eigenvectors and eigenvalues → compute the principal components → move from a multidimensional space to a two-dimensional one.

## Adjacent problems this unlocks

Worth noting: this article is about working with *features*. For *objects* you use other algorithms — data distillation, say, or instance selection. And once PCA and k-means are in your toolkit, a few adjacent, practical problems become easy, the ones I get asked about most.

**Dropping values above or below a threshold.** This is really data transformation from one form into a fundamentally different one, but the everyday version is filtering by a cut-off. Take a dataset:

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
# to render plots inline in Jupyter
%matplotlib inline

np.random.seed(654)
raw_data_X = np.arange(35) / 135
raw_data_Y = np.exp(raw_data_X) / raw_data_X
raw_data_addit = np.random.rand(35) / 0.442
data = pd.DataFrame({'x': raw_data_X, 'y': raw_data_Y, 'valid': raw_data_addit})

data.shape
data.head()
```

Then use `drop` to remove every row where the `valid` column is below 1. `inplace` is there to save the table back over itself.

```python
plt.scatter(data['x'], data['y'])
index_outlier = data.loc[data['valid'] < 1].index
print(index_outlier)
data.drop(index_outlier, axis=0, inplace=True)
# axis=0, since we're deleting rows
data.head()
```

![Filtering rows by a threshold](http://your-scorpion.ru/wp-content/uploads/2020/01/sbr3_depth.png)

**Merging repeated elements of different types into a single event.** This is a matter of differing categorical data. Since you can't compare numbers with objects, you turn the data into dummy variables — the `object` dtype is mostly string data. The number of columns grows, some columns disappear, and this is called encoding (digitization). Keep in mind that too many features means trouble training models.

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

raw_df = pd.DataFrame({"id": [1] * 4 + [2] * 4,
                       "manager": [1, 2, 3, 4] * 2,
                       "CPA": np.random.rand(8) * 0.034})

df_for_math = pd.DataFrame({"manager": np.ceil(np.arange(12) / 2) - 2,
                            "ROI": np.arange(12) + 1 * 0.05})

LTV = 0.5 * 3**2 + 6 + 2 + np.random.randn(14, 1)
name_client = ['Katya', 'Vika', 'Nenasheva', 'Viktor', 'Anatoly', 'Nenasheva',
               'Viktor', 'Vika', 'Nenasheva', 'Elena', 'Vika',
               'Nenasheva', 'Nenasheva', 'Dmitry']

final_df = raw_df.merge(df_for_math, on='manager')
final_df['ROI'] = final_df.CPA * 412.
final_df['Client'] = name_client
final_df['LTV'] = LTV
# final_df['LTV'] = final_df['LTV'].astype(str)

final_df
pd.get_dummies(final_df)
```

![The dataframe after get_dummies encoding](http://your-scorpion.ru/wp-content/uploads/2020/01/sbr3_depth-3.png)

Or, as an alternative, aggregate instead of encoding: `final_df.groupby(['manager', 'ROI'])[['LTV']].mean()`.

![The grouped and averaged dataframe](http://your-scorpion.ru/wp-content/uploads/2020/01/sbr3_depth-4.png)

**Automatically weeding out junk survey responses.** With a pile of survey results where experience says roughly 10–15% is garbage, you can automate the cull: take the PCA method from this article, reduce the dimensionality, then run DBSCAN over the result. It should find clusters — and some of those clusters may well be the junk answers.

*Editor's note: since publication, pandas `get_dummies` now defaults to boolean output rather than 0/1 integers (use `dtype=int` for the old behaviour), and scikit-learn's `KMeans` changed its default `n_init` in version 1.4. The logic above is unchanged; only these defaults have shifted.*
