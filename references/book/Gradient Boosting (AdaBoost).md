# Gradient Boosting (AdaBoost)

*Maksim Tcvetkov · Product Design and Security Lead*

Gradient boosting is used for classification and regression tasks and is similar to random forests. It is a complex topic, partly because the method is hard to interpret — that is, it is difficult to explain to a bank manager, in plain terms, what rules led to a given decision. In previous chapters on linear regression it was easy to see which feature carried which weight and how much it influenced the result. Gradient boosting applies to regression, classification, and any task with labeled data. One example is ranking the responses produced by a neural network inside voice assistants. The exception is computer vision and NLP — those are better handled by neural networks. In practice, easily interpretable algorithms are preferred, for instance in fintech. In marketing, however, complex algorithms like gradient boosting are entirely appropriate.

We will look at a specific variant of gradient boosting called **AdaBoost (adaptive boosting)**, which is very straightforward to implement and combines several different models at once — not only trees, but trees tend to give the best results. It has good computational complexity and strong predictive performance. It works on the principle of reweighting results. Decision trees are the building blocks, and an ensemble of them is gradient boosting.

The word "gradient" appears in the name because the problem is solved using gradient descent. Much like human learning, the AdaBoost algorithm learns from its mistakes, concentrating more on the difficult cases it encountered during the previous training iteration.

The algorithm is iterative, but parallelization is possible. In other words, we have tree 1, tree 2 is built on its results, tree 3 is built on those results, and so on. At each iteration the algorithms are assigned weights. Each new algorithm corrects the errors of the previous ones until a good result is reached.

![Two classes (square and circle): the first tree works by comparing against some threshold. Each new tree fixes the errors of the previous ones.](https://your-scorpion.ru/wp-content/uploads/2020/03/Group-317.png)
*Two classes (square and circle): the first tree works by comparing against some threshold. Each new tree fixes the errors of the previous ones.*

Let us look more closely at the core principle of AdaBoost: fitting weak "learners" (i.e., models that are only slightly better than random guessing) to repeatedly modified versions of the data. The next step combines predictions from all of them via voting to produce the final forecast. The data modifications at each so-called boosting iteration consist of applying N weights to each of the training samples. Initially all of these weights are set to 1/N, so the first step simply trains a weak learner on the original data. For each successive iteration the sample weights are individually modified and the learning algorithm is applied again to the re-weighted data. At this stage, training samples that were mis-predicted by the model in the previous step receive increased weights, while those that were predicted correctly receive decreased weights. As the iterations proceed, more and more priority flows toward the regions that are difficult to predict.

```python
from sklearn import model_selection
from sklearn.datasets import load_digits
plt.imshow(X[9].reshape(8,8))

X, y = load_digits(return_X_y=True)
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
X = scaler.fit_transform(X)
```

![](https://your-scorpion.ru/wp-content/uploads/2020/03/download.png)

```python
X_train, X_test, y_train, y_test = model_selection.train_test_split(X, y, test_size=0.32)
def get_error(pred, y):
    return sum(pred != y) / len(y)
```

Let us write a function that accepts the data and the number of tree algorithms we want to use.

We will use `accuracy` as the metric — it is not the best metric and works better for balanced classes, but you can swap it for whichever metric suits your needs (`roc_auc` / `f1` / `average precision`). `sample_size` is the size of the sample, and `classes_amount` will hold the classes. In my example the initial tree weights will all equal 1 — every tree carries the same weight — and they are stored in `models`.

```python
from sklearn.tree import DecisionTreeClassifier

def adaboost(X, y, N):
    sample_size = len(X)
    classes_amount = len(np.unique((y)))

    w = np.ones(sample_size, dtype=np.float32)
    w = np.ones(sample_size) / sample_size
    models = []

    for n in range(N):

        clf = DecisionTreeClassifier(max_depth=2, splitter='best', criterion='gini').fit(X, y, sample_weight=w)
        print('Accuracy using the default gini impurity criterion...', clf.score(X, y))

        predictions = clf.predict(X)
        e = get_error(predictions, y)
        if e == 0 or e >= 1 - 1 / classes_amount:
            break

        alpha = 0.5 * np.log((1 - e) / e) + np.log(classes_amount - 1)
        match = predictions == y

        w[~match] *= np.exp(alpha)

        w /= w.sum()
        models.append((alpha, clf))

    return models
```

A tree can be defined and trained using `DecisionTreeClassifier`. We will reject any tree whose accuracy error exceeds 0.5. In decision trees each step produces a binary output, so it is not straightforward to inspect the weight of each feature directly. However, with enough effort and when you are not working on the client side, an interpretation can still be found.

Let us train the algorithm with 500 trees. AdaBoost generates a sequence of weak classifiers; at each iteration the algorithm finds the best classifier based on the current sample weights. Correct classifications receive a lower weight, incorrect ones receive a higher weight, and this continues until the optimal model is found. The weight increase is achieved via `w[~match] *= np.exp(alpha)`. We are simply combining several weak learners into one strong learner. A weak learner is usually not a full tree but a stump — in boosting it is common practice to use stumps (depth ~3) rather than full trees.

```python
import numpy as np
N = 500

models = adaboost(X_train, y_train, N)

def predict(X, models):
    sample_size = len(X)
    y_pred = np.zeros((sample_size, 10))

    for alpha, clf in models:
        prediction = clf.predict(X)
        y_pred[range(sample_size), prediction] += alpha

    y_pred = np.argmax(y_pred, axis=1)
    return y_pred

print(f'Training set, algorithm accuracy: {(1 - get_error(predict(X_train, models), y_train)) * 100:.3f}')
print(f'Test set, algorithm accuracy: {(1 - get_error(predict(X_test, models), y_test)) * 100:.3f}')
```

```
Accuracy using the default gini impurity criterion... 0.26863226863226863
Accuracy using the default gini impurity criterion... 0.2334152334152334
Accuracy using the default gini impurity criterion... 0.28501228501228504
Accuracy using the default gini impurity criterion... 0.1294021294021294
Accuracy using the default gini impurity criterion... 0.24815724815724816
Accuracy using the default gini impurity criterion... 0.19737919737919737
Accuracy using the default gini impurity criterion... 0.1891891891891892
Training set, algorithm accuracy: 97.707
Test set, algorithm accuracy: 92.188
```

And let us visualize it:

```python
import matplotlib.pyplot as plt
train_errors = []
test_errors = []

for n in range(1, 26):
    mods = adaboost(X_train, y_train, n)
    train_errors.append(get_error(predict(X_train, mods), y_train))
    test_errors.append(get_error(predict(X_test, mods), y_test))

x = list(range(1, 26))

plt.xlim(0, 25)
plt.plot(x, train_errors, color='r', linestyle=":", lw=2, label='train errors')
plt.plot(x, test_errors, color='c', linestyle="--", lw=2, label='test errors')
plt.title('Classifier')
plt.xlabel('N')
plt.ylabel('Accuracy')
plt.legend(loc='upper right')
```

![](https://your-scorpion.ru/wp-content/uploads/2020/03/download-1.png)

From the chart it is clear that there are more errors on the test set than on the training set. However, once the number of trees reaches around 80 both curves level off into a plateau. This means there is no point in using fewer than 80 trees. In practice people use 1,000 or even 5,000 trees, but at those numbers there is usually no meaningful difference — the error has already reached its minimum.

If the example above feels too complex, here is the simplest gradient boosting implementation I can think of. It runs very slowly because it is a brute-force, greedy algorithm.

```python
import tensorflow as tf
import numpy as np
import sklearn
import matplotlib.pyplot as plt

(x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()

x_train = x_train.reshape(60000, 784).astype('float32') / 255
x_test = x_test.reshape(10000, 784).astype('float32') / 255

y_train = y_train.astype('float32')
y_test = y_test.astype('float32')

from sklearn.ensemble import GradientBoostingClassifier
clf = GradientBoostingClassifier(learning_rate=0.41, n_estimators=150, verbose=1, subsample=0.5)
clf.fit(x_train_flat, y_train)

from sklearn.metrics import accuracy_score
accuracy_score(y_test, clf.predict(x_val_flat))
```

Accuracy: 0.9238.

**Advantages of AdaBoost:**

- AdaBoost is easy to implement — all you need is a class of models and a count.
- It iteratively corrects the errors of a weak classifier and improves accuracy by combining weak learners.
- Many different base classifiers can be used with AdaBoost.
- AdaBoost is not prone to overfitting.

**Disadvantages:**

- AdaBoost is sensitive to noisy data.
- Training takes longer than with linear regression; classification is slower than with logistic regression.
- AdaBoost is strongly affected by outliers because it tries to fit every point perfectly.
- AdaBoost is slower and slightly worse than XGBoost, though it is easier to understand.

If you are reaching for a ready-made implementation, for serious projects on tabular data I would go with a modern gradient boosting library (CatBoost, XGBoost, LightGBM). For non-tabular data — neural networks. Or combinations of both: a voice assistant, for example, requires a dedicated network to detect a wake word ("Alice", "Siri") outside of a dialog context, plus separate models for speech recognition, intent detection, answer retrieval, and speech synthesis.

**Where does AdaBoost fit in a product designer's life?** In real-world tasks you often need to deliver a retention forecast to the business. One approach: define what customer churn means in terms of measurable values → model it as a binary classification problem using gradient boosting → drill down with features to make the result actionable → run an A/B test on the customers who appear likely to leave. Simpler regression tasks work just as well — estimating a car's resale value from its characteristics, for example.

More broadly, data-driven growth breaks down into several areas:

**New products.** Every product grows, reaches a plateau, and eventually dies — the S-curve. Knowing when that decline starts matters, and so does understanding how to make a product faster, more pleasant, or cheaper. Calculating loan-default risk or monitoring driver behaviour are classic examples: a safe driver means lower insured risk, which translates to a cheaper policy.

**Average order value.** It needs to grow. Per-customer price optimisation, personalised recommendations, and cross-sell suggestions all feed into this.

**Retention.** Churn analysis, targeted promotions, review monitoring, customer clustering, recommendations. A straightforward metric is the return rate: current active users ÷ total users × 100 (e.g., 800,922 / 1,203,211 × 100 = 66%). The inverse — churn rate — is the fraction of users who left.

**Conversion.** Omnichannel recommendations, scoring inbound leads for conversion potential, sales funnel analysis.

**Cost reduction.** Targeting, replacing manual steps with algorithms or RPA, anomaly detection, chatbots, inventory management, demand forecasting, process optimisation.

**Scaling.** Finding the right location for a new store.

---

## Extended example: segmenting users with LDA

Boosted classifiers require labeled data, but many real-world problems arrive without labels. Consider a local news portal that wants to understand what kinds of readers it has. The available data is just readers and articles — no explicit categories.

The goal is to represent each user's interests as a vector. If labeled data exists, multiclass classification is an option, but labeling is expensive and teams usually end up doing unsupervised clustering instead. A standard approach is **Latent Dirichlet Allocation (LDA)**.

LDA extracts latent topics from documents based on word co-occurrence. A cooking article will tend to contain words like "oven", "flour", "dough", "bowl"; a finance article will contain "rate", "market", "risk". LDA assigns each document a mixture of topics and each topic a distribution over words. The output looks something like:

- Sentences 2, 3, 4, 5, 6: 100% Topic A
- Sentences 1, 7, 8, 10: 100% Topic B
- Sentence 9: 60% Topic A, 40% Topic B

For each topic you can then inspect the top words: Topic A is 20% "oven", 10% "flour" — so Topic A is probably about food.

The algorithm refines this distribution by iterating over every word in every document. For each word it computes two probabilities — `p(theme|document)` (how likely is this topic in this document?) and `p(word|theme)` (how likely is this word given the topic?) — and multiplies them to get the updated probability that topic *t* generated word *w*. After many passes the distribution stabilizes.

In practice, start by loading the articles:

```python
import pandas as pd
news = pd.read_csv("data.csv")
print(news.shape)
news.head(10)
news.iloc[0]['title']
```

Do the same for the reading history. You will need user IDs and a list of articles each user read over a given period:

```python
users = pd.read_csv("users_articles.csv")
users.head(3)
```

The raw text will almost certainly need cleaning. Use either stemming (stripping word endings: "ran" → "run") or lemmatisation for full morphological normalisation (verbs to infinitive, nouns to nominative). Stemming is fast but crude; prefer lemmatisation via `pymorphy2`. Use `razdel` to tokenise Russian text into words and sentences:

```python
!pip install razdel pymorphy2
import re
import numpy as np
from gensim.corpora.dictionary import Dictionary
from razdel import tokenize
import pymorphy2
```

Remove stopwords (conjunctions, prepositions, and the like):

```python
import nltk
from nltk.corpus import stopwords
nltk.download('stopwords')
stopword_ru = stopwords.words('russian')
with open('stopwords.txt') as f:
    additional_stopwords = [w.strip() for w in f.readlines() if w]
stopword_ru += additional_stopwords
```

Text cleaning and lemmatisation:

```python
def clean_text(text):
    if not isinstance(text, str):
        text = str(text)
    text = text.lower()
    text = re.sub(r'[0-9]|[-—.,:;_%©«»?*!@#$^•·&()]|[+=\[\]/]', '', text)
    text = re.sub(r'\s+', ' ', text.strip())
    return text

cache = {}
morph = pymorphy2.MorphAnalyzer()

def lemmatization(text):
    if not isinstance(text, str):
        text = str(text)
    tokens = list(tokenize(text))
    words = [_.text for _ in tokens]
    words_lem = []
    for w in words:
        if w[0] == '-':
            w = w[1:]
        if len(w) > 1:
            if w in cache:
                words_lem.append(cache[w])
            else:
                temp_cach = cache[w] = morph.parse(w)[0].normal_form
                words_lem.append(temp_cach)
    return [i for i in words_lem if i not in stopword_ru]

news['title'] = news['title'].progress_apply(lemmatization)
```

Build the corpus and train the topic model:

```python
texts = list(news['title'].values)
common_dictionary = Dictionary(texts)
common_corpus = [common_dictionary.doc2bow(text) for text in texts]

N_topic = 20

%%time
from gensim.models import LdaModel
lda = LdaModel(common_corpus, num_topics=N_topic, id2word=common_dictionary)

from gensim.test.utils import datapath
temp_file = datapath("model.lda")
lda.save(temp_file)
lda = LdaModel.load(temp_file)
```

Inspect the top words per topic to label them:

```python
x = lda.show_topics(num_topics=N_topic, num_words=7, formatted=False)
topics_words = [(tp[0], [wd[0] for wd in tp[1]]) for tp in x]
for topic, words in topics_words:
    print(f"topic_{topic}: " + " ".join(words))
```

Represent each user as the mean topic vector of the articles they read:

```python
doc_dict = dict(zip(
    topic_matrix['doc_id'].values,
    topic_matrix[[f'topic_{i}' for i in range(N_topic)]].values
))

def get_user_embedding(user_articles_list, doc_dict):
    user_articles_list = eval(user_articles_list)
    user_vector = np.array([doc_dict[doc_id] for doc_id in user_articles_list])
    return np.mean(user_vector, axis=0)

%%time
user_embeddings = pd.DataFrame([
    get_user_embedding(row, doc_dict) for row in users['articles']
])
user_embeddings.columns = [f'topic_{i}' for i in range(N_topic)]
user_embeddings['uid'] = users['uid'].values
user_embeddings = user_embeddings[['uid'] + [f'topic_{i}' for i in range(N_topic)]]
user_embeddings.head(3)
```

The result is a compact numeric profile for every user — ready to feed into a clustering algorithm or a downstream recommender.
