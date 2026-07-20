# Scikit-learn: SVM, Linear Regression, and Gradient Descent

*Maksim Tcvetkov · Product Design and Security Lead*


---

First, the question that matters most: where would I, a designer, ever need linear regression? It's popular for applied problems like forecasting — targeted advertising above all, a single neuron in a classification layer, simple scoring systems for banks. In short, any problem where the relationships between variables are linear by nature: how age affects health, or how many operators at a bank hit their KPIs. Or whether there's a link between sales and ad spend, which channels move sales the most, and whether the dependence you're seeing is actually linear.

The ML world has three basic directions: regression, for predicting numeric values; classification, for sorting entities by some set of features (cats/dogs, will-quit/won't-quit); and clustering, for grouping similar objects better than a rule-based system could. Typically there's a test set for validating the model and a training set in which the target values are already known.

Linear regression lets you predict a variable Y from a variable X. Visualise that relationship on a chart and you get a straight line — often called the line of best fit. It's a genuinely simple idea: we predict Y given X. There's a target variable, and the set of values that target can take is effectively infinite. Predicting an apartment's price from the number of rooms and the distance to the metro, for instance, is a linear-regression problem. A metric model answers a metric hypothesis — the target variable depends linearly on the objects' features. A textbook case is forecasting a store's annual revenue, where the hypothesis is a dependence between the number of stores and the volume of revenue. In essence it's a model about money, which is why banks love it: credit has to be issued with a cool head, and every parameter feeds the decision (income, children, missed payments, tenure, health, default risk).

![Credit-scoring illustration](https://your-scorpion.ru/wp-content/uploads/2019/11/Frame-1-2.png)

Linear-regression models are used to demonstrate or predict the relationship between two variables or factors. The factor being predicted — the one the equation is solved for — is the dependent variable. The factors used to predict it are the independent variables. Linear regression analysis, then, predicts the value of one variable from the value of another.

Take the apartment price again. We clean the data, take a linear-regression model, and hand it a task: feed in a new apartment with no price, and the model predicts the price — and even tells us which parameters drive it.

Linear regression is a *building block* for more complex models, neural networks included. A neural net is, at heart, a chained series of logistic regressions, and to understand those you first need the linear kind. It's also highly interpretable, and for a great many problems it's simply enough on its own. There's a special case called simple (pairwise) regression, where only one feature is considered (k = 1). When there's more than one feature, the least-squares method can serve as an analytical solution.

The regression task is to minimise error. By mathematical convention, the two factors in a simple linear regression are written **x** and **y**, and the equation describing how **y** relates to **x** is the regression model. That model also carries an error term, written **Ε** — the Greek letter epsilon. The error term accounts for the variability in **y** that the linear relationship between **x** and **y** can't explain.

It's more interesting to work with categorical and real-valued variables, but for now, for simplicity, we'll stick to plain numbers. So: we have a design department of 18 designers, and we want to know which of them works most effectively. We'll use NumPy for the maths. A typical linear-regression task is to determine a *continuous* variable; if the dependent variable isn't continuous, it's a classification problem, not regression. I'll keep to simple linear models, since a plain weighted sum of feature values is usually enough.

The setup: as head of the design department, I want to understand which of my people is the most effective. Every designer's work is scored on a SUM metric, so I know exactly what quality each of them produces. Now I need to correctly relate years of experience to work performance.

In the language of machine learning we'll call our observations *features*. A feature is any characteristic of the data under study, expressed as a number. Let's start by listing the specialists' years of experience and looking at the shape of the data with `x.shape`. We have 18 observations (18 designers) — one set of features. To add a second feature set, we build a two-dimensional NumPy array. The second set is performance (SUM), an average score in the 0–100 range. The whole thing together is our *feature description*. Entered and plotted:

```python
import numpy as np
x = np.array([[1,3,4,5,11,0,8,6,3,7,16,0,2,3,2,4,21,4]])
print(x)
print(x.shape)
y = np.array([[32,54,54,35,86,12,74,67,35,75,94,12,56,54,40,35,87,47]])
print(y)
print(y.shape)

import matplotlib
matplotlib.use('TkAgg')
from matplotlib import pyplot as plt
plt.scatter(x, y)
```

![Scatter plot of experience versus SUM](https://your-scorpion.ru/wp-content/uploads/2019/10/giphy.gif)

Experience in years runs along the horizontal; the SUM result runs up the vertical. Even by eye there's a logic to it: less experience, worse results; as X grows, so does Y. Immediately you want to use these performance data to forecast — how much of a raise (or a cut) someone's due. Since the data don't start at 0/0, we need an intercept. There are two concepts here, the *intercept* (the shift) and the *slope* — sometimes called the coefficients or the parameters.

The intercept is itself a kind of feature, a shift in the data; we'll write it as x0. You could use a Bayesian approach here too — its behaviour is quite similar — but it would be out of place in this case (not to be confused with Bayes' theorem). Let's make life a little easier and pretend there's no saturation curve, since a seasoned specialist's early work is, by definition, weak — so we took SUM for the most recent year only. For someone with zero years of experience, we took the data over a few months.

Because this is linear regression, let's try to eyeball a weight for the feature and an intercept and draw a line through them. The intercept might be 15 — but what's the slope? The slope is hard to judge because the horizontal and vertical scales differ. Still, let's try 3, 4, 5 by eye:

```python
import numpy as np
import matplotlib
matplotlib.use('TkAgg')
from matplotlib import pyplot as plt

y = np.array([[32, 54, 54, 35, 86, 12, 74, 67, 35, 75, 94, 12, 56, 54, 40, 35, 87, 47]])
print(y)
print(y.shape)

X = np.array([[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],[1,3,4,5,11,0,8,6,3,7,16,0,2,3,2,4,21,4]])
plt.scatter(X[1], y)
plt.plot(X[1], 15*np.ones(18) + X[1]*4)
plt.show()
```

![Eyeballed regression line over the scatter](https://your-scorpion.ru/wp-content/uploads/2019/11/giphy.gif)

How to read it: a regression line can show a positive linear relationship, a negative one, or none at all. A flat line means no relationship between the two variables. A line sloping up is a positive linear relationship; a line sloping down, a negative one. Note that the line here climbs above the ceiling of 100 — you fix that by clipping. And it's right to clip negative values too. Trees can throw values out of the possible range as well, though that depends entirely on the algorithm.

Say I decide a slope of 5 is closest to reality. Now let's look at the errors, since the smaller the error, the better the model. Errors come in several kinds; for now we'll compute our predictions, take the error, and sum it across every observation. The approach is primitive, but it makes a good demonstration.

```python
y_pred1 = 15*np.ones(18) + X[1]*4
print(y_pred1)
err = np.sum(y - y_pred1)
print(err)
```

We get 179. That's a large value, which means we've under-predicted badly. A *negative* value would mean over-prediction — the more desirable outcome. Better to over-deliver than under-deliver; right now we risk erring on the side of under-prediction, and the business won't stand for that. There's another significant flaw: a *mean* error of zero. It's treacherous because you can have two observations that deviate in opposite directions by the same amount, so that in the sum they cancel out (−1 + 1 = 0). Two opposite objects with the same feature description would tell us the model is excellent — even though it might contain large deviations. So we bring every difference to a single sign, either with the absolute value (the MAE error) or by squaring everything, which also removes the minus sign. MAE is a method of estimating a model's parameters; its absolute values don't inflate the deviations counted as outliers, so the estimate is more robust and more median-like than MSE. There are more sophisticated metrics too — MAPE, RMSPE, and my personal favourite, RMSE.

So: regression gives us a target variable — a store's potential revenue, say — and we need to choose the right loss function. MSE is the quadratic loss; you compute the Hessian and confirm it satisfies the sufficient condition for a minimum. But for now let's look at MAE, since it's differentiable —

```python
def calc_mse(y, y_pred):
    err = np.mean((y - y_pred) ** 2)
    return err

print(calc_mse(y, y_pred1))

def calc_mae(y, y_pred):
    err = np.mean(np.abs(y - y_pred))
    return err

print(calc_mae(y, y_pred1))
```

![The X matrix and the y vector](https://your-scorpion.ru/wp-content/uploads/2019/11/Frame-2.png)

*X — the matrix of observations and features, sized rows × columns. y — the answer (the thing to predict).*

The functions and formula are above; MAE is computed like this: `np.sum(np.abs(y - y_pred1)) / 18`, giving 14.944444444444445. A little more detail here. MAE (mean absolute error) is the average absolute deviation, and that absolute value makes it robust to outliers. The catch is that the absolute-value function has no derivative at zero, which can make optimisation awkward. So to measure deviation you can simply take the squared difference instead.

And that carries us smoothly into MSE (mean squared error): `np.mean((y - y_pred1) ** 2)` — we take the mean rather than the sum, to keep the calculation simple. The idea is to minimise the sum of squared deviations of the actual values from the computed ones; we use it whenever we're solving a regression problem. Divide that sum by the number of observations and you have MSE, which surfaces errors more clearly than MAE. So: MSE = 326.1666666666667 and MAE = 14.944444444444445. A line is built through these points, which means this model — the line itself — will work better for the points on the chart. And that's exactly why the regression is *linear*: a straight line that most accurately describes the dependence in the data.

![Fitting a line through the points](https://your-scorpion.ru/wp-content/uploads/2019/11/Frame-3.png)

## Automating it: ordinary least squares

That was the by-eye method, but it's far nicer to automate. New SUM readings arrive every month, and we want to track the growth of the juniors and the continued relevance of the seniors. We need a formula that computes the two coefficients automatically — and here ordinary least squares (OLS) rides in. It needs a matrix whose height and width both equal the number of features and whose shape is square (18×18). If we have 18 columns and 18 rows, the two matrices can be multiplied. The resulting matrix is taken by the number of features, so 2×2.

Run `X.shape` and we learn our matrix is (2, 18); `X.T.shape` tells us the transpose is (18, 2). So the two can be multiplied. `np.dot(X, X.T)` gives us a 2×2 matrix, by the number of features: `[[18 100][100 1076]]`.

Worth flagging: working with matrices through OLS isn't especially stable. What I mean is this — if a matrix's determinant is close to zero, computing the inverse accurately is hard. Divide by a tiny value and you get an enormous result (10,000 / 0.003 = 3,333,333), so a minuscule change in the denominator drags a huge change into the result. Take 0.003 and 0.0000003: the gap between them is small, and both hug zero. But divide 1 by each and you get 333 and 3,333,333 respectively — an enormous range. That's why the inverse is unstable: a minimal error in the determinant produces a huge error in the inverse.

Next we find the inverse of the square matrix, `print(np.linalg.inv(np.dot(X, X.T)))`, which returns `[[0.11485909 -0.01067464] [-0.01067464 0.00192143]]`.

```python
W = np.linalg.inv(np.dot(X, X.T)) @ X @ y.T
print(W)
```

And the last step: multiply by the transposed matrix and by **y**, and out come our weights. Two numbers — the very ones we were trying to guess by eye, except this time the system guessed them: `[[32.48548249] [3.64261315]]`. We'd assumed an intercept of 35 and a slope of 5. In a real project I'd use partial derivatives to derive the formula, but for our example OLS will do.

In regression analysis the dependent variables sit on the vertical Y axis and the independent variables on the horizontal X axis. Together they form the equation for a line determined by least squares — worth understanding, because it's the basis for predicting how the dependent variables behave.

```python
X = np.array([[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],[1,3,4,5,11,0,8,6,3,7,16,0,2,3,2,4,21,4]])
y = np.array([[32,54,54,35,86,12,74,67,35,75,94,12,56,54,40,35,87,47]])

print(X.shape)
print(X.T.shape)
print(np.dot(X, X.T))
print(np.linalg.inv(np.dot(X, X.T)))
W = np.linalg.inv(np.dot(X, X.T)) @ X @ y.T
print(W)

plt.scatter(X[1], y)
plt.plot(X[1], 15*np.ones(18) + X[1]*4)
plt.plot(X[1], W[0] + W[1] * X[1])

plt.show()
```

![The eyeballed line and the machine-fitted line together](https://your-scorpion.ru/wp-content/uploads/2019/11/giphy-1.gif)

*The orange line is based on the values we computed using the weights.*

The example above makes it plain that the machine's line filters people more harshly than I did by eye. It's an elementary example of using machine learning for a business problem. Now let's compute the new MAE and MSE for the freshly found values:

```python
y_ml = W[0] + W[1] * X[1]
print(calc_mse(y, y_ml))
print(calc_mae(y, y_ml))
```

Before, MSE = 326.1666666666667 and MAE = 14.944444444444445; now MSE = 174.00144700635732 and MAE = 11.478128854730052. Much better already (remember, the closer to zero, the better). Beyond MSE and MAE there's also the logarithmic loss MSLE, and MAPE, the mean absolute percentage error.

In our particular department there are at least six designers working worse than colleagues with comparable experience. Broadly, though, the hypothesis held: the more experience a designer has, the better the result. But *we* know that as humans; the machine doesn't, and it will now predict a specialist's ultimate potential more precisely than we can. We can even index salaries to a specialist's growth and performance — the model lets us see how much a junior is growing relative to a senior's experience. A perfectly precise prediction is out of reach, of course, since a specialist's growth depends on the specialist.

## Gradient descent

So least squares computes well, and — crucially — fast. But you can't lean on it forever, and the alternative is gradient descent. A gradient is an iterative method: it points in the direction of the function's steepest *increase*. There's a mirror image, the anti-gradient, which points to the steepest *decrease*. OLS has a problem: it struggles at high dimensionality — more precisely, with a lot of data. Gradient descent gives up nothing to OLS on accuracy. The principle is simple: it finds a poor solution first, then a better one, then better still, and on down to the smallest error. Rule of thumb: lots of data = gradient descent. In the modern world the choice of algorithm is heavily tied to performance. OLS evaluates the whole dataset at once, whereas stochastic gradient descent lets you feed the data in batches — which saves RAM, of which there's never enough. (Both OLS and gradient descent will happily turn any piece of hardware into a space heater.) Stochastic gradient descent (SGD) is what you reach for when the data don't fit in memory and have to be split up. It reaches the global minimum faster, because not the whole dataset takes part in each iteration. The obvious downside is that it doesn't land on the true minimum point — but the accuracy is generally acceptable. It's all analogous to web analytics: there's sampling, and there are the problems that come with it.

Let's get our hands on **gradient descent**. It tries different options and moves toward the goal. Matrices let us run many operations at once. Calculus gives us a way to find partial derivatives by iteratively sweeping through the weights in MSE, and that's what we'll do. We have one variable, but we added an intercept, and that's a weight for a pseudo-feature — so effectively two features: the intercept and the weight on the experience feature. Classic gradient descent is used rarely; more often the choice is stochastic optimisation.

The process of minimising error in gradient descent plays out in a three-dimensional space formed by the two weight values (W0 = the intercept, and the slope *m* = experience). We can set the initial weights to −1 and 1. We'll find the derivative for each weight the vector way, where W[0] is both the intercept and a weight — the weight on the pseudo-feature 1.

```python
import numpy as np
X = np.array([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
              [1, 3, 4, 5, 11, 0, 8, 6, 3, 7, 16, 0, 2, 3, 2, 4, 21, 4]])
y = np.array([[32, 54, 54, 35, 86, 12, 74, 67, 35, 75, 94, 12, 56, 54, 40, 35, 87, 47]])
W = np.linalg.inv(np.dot(X, X.T)) @ X @ y.T

W = np.array([1, 0.75])
gradient_form_direct = 1/18 * 2 * np.sum(X[0] * W[0] - y[0])
print(gradient_form_direct)
```

We get −103.44, which isn't great. With a step that huge we could sail straight past the minimum we're after. To shorten the step we change alpha on each iteration — dividing by the iteration number, for instance. Let's add alpha as a learning rate, `alpha = 1e-5` (10 to the −5, or 1/10⁵). When you see a minus in the exponent, count the zeros backwards: −0.00001.

Values can differ wildly, but after standardisation or normalisation the weights become predictable — 1, 2, 10, but certainly not 1000. Again, W[0] is the intercept and a weight at once — the weight on the pseudo-feature 1.

```python
alpha = 1e-5
gradient_form_direct = alpha * (1/18 * 2 * np.sum(X[0] * W[0] - y[0]))
print(gradient_form_direct)
print(W[0] - gradient_form_direct)
```

We get 1.001 — something we can work with. Now we set a minimum value, and once the error gets close to it, we stop the algorithm. That's called reaching convergence. Or we set a fixed number of steps by hand, which is what we'll do here. One more wrinkle: with little data we'll do more computation than OLS would — but let's simulate a big-data situation on toy data:

```python
import numpy as np

X = np.array([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
              [1, 3, 4, 5, 11, 0, 8, 6, 3, 7, 16, 0, 2, 3, 2, 4, 21, 4]])
y = np.array([32, 54, 54, 35, 86, 12, 74, 67, 35, 75, 94, 12, 56, 54, 40, 35, 87, 47])
W = np.array([1, 0.5])

gradient_form_direct = 1e-4 * (1/18 * 2 * np.sum(X[0] * W[0] - y[0]))
print(gradient_form_direct)
print(W[0] - gradient_form_direct)

for i in range(2000):
    gradient_form = np.dot(W, X)
    W -= (1e-2 * (1/18 * 2 * np.dot((gradient_form - y), X.T)))
    if i % 200 == 0:
        print(i, W)
```

Note that gradient descent's solution is *approximate*. Each new iteration nudges the weights closer to where they should be, but noise has its say and the values will never be perfect. And if no analytical solution exists, gradient descent won't even reach satisfactory values.

![Gradient-descent weights converging](https://your-scorpion.ru/wp-content/uploads/2019/11/image-3-2.png)

We get 32.4854816 and 3.64261324. In practical work *nobody* usually sets out to prove that a global minimum exists. MSE and log loss are bounded below by zero, and the closer to zero, the more "fine, good enough" it is. Proving a global minimum exists is doable; *reaching* it is far harder. For the especially curious there's an alternative to gradient descent, K-FAC. Or bump up alpha so you need fewer iterations — `1e-2`, say, with more iterations — and you'll arrive at the same values OLS gave us. Often people run several gradient descents at once; they converge on different points and find different good starting approximations, which is how you land on a better minimum. Gradient descent has a linear rate of convergence, which is the more honest option; the stochastic version has a sub-linear rate, which spares you a long compute — it comes down to the horsepower on hand and the volume of data.

## Scaling features: normalization and standardization

Since we've touched on feature scaling, let's work through a couple of examples of normalization and standardization. Linear models learn effectively only from features that share the same scale, which makes feature scaling an inseparable part of preparing data for machine learning. Normalization suits measured features like height or salary; standardization is a better fit for models that rely on the distribution. Either way, standardization and normalization won't hurt — everything beyond that is very case-by-case.

Let's add a new factor — monthly salary.

```python
X = np.array([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1],
              [1, 3, 4, 5, 11, 0, 8, 6, 3, 7, 16, 0, 2, 3, 2, 4, 21, 4],
              [65000, 80000, 85000, 75000, 120000, 25000, 65000, 65000, 29000, 36650, 260000, 12000, 35000, 45600, 25000, 65000, 175000, 73000]])
```

Normalization is a 0-to-1 range that preserves linearity. The principle: subtract the feature's minimum value from each value, so the feature becomes zero at its minimum, while the maximum equals the spread between the maximum and minimum. Divide by that spread and you have a scaled feature. Other popular normalization methods are the square root and the logarithm.

The hypothesis is simple — the more we pay someone, the better the result we expect. All the features are on different scales. Coefficients depend on scale, so across different scales you can't read feature importance off the coefficients — and not every algorithm can even handle it. The data needs normalising (to the 0–1 interval); after that, the regression weights will tell you how important each feature is. The shape of the data won't change; only the mean and variance will. We find each feature's minimum and maximum: `print(X[1].min(), X[1].max())`, `print(X[2].min(), X[2].max())`, giving 0–21 and 12000–260000.

Normalization step by step: `(X[1].max() - X[1].min())` gives the spread — the maximum experience among the designers is 21 years. Look at the minimum with `(X[1] - X[1].min())`, giving `[1 3 4 5 11 0 8 6 3 7 16 0 2 3 2 4 21 4]`, minimum experience zero. Then bring everything into the range we want, `(X[1] - X[1].min()) / (X[1].max() - X[1].min())`, which returns something like `[0.04761905 0.14285714 0.19047619 0.23809524 0.52380952 0. 0.38095238 0.28571429 0.14285714 0.33333333 0.76190476 0. 0.0952381 0.14285714 0.0952381 0.19047619 1. 0.19047619]`. Now do it for both features:

```python
X_norm = X.copy()
X_norm = X_norm.astype(np.float64)
X_norm[1] = (X[1] - X[1].min()) / (X[1].max() - X[1].min())
X_norm[2] = (X[2] - X[2].min()) / (X[2].max() - X[2].min())
print(X_norm)
```

And now **standardization**: here we're trying to obtain the standard normal distribution — a range from −1 to 1 with 0 in the middle, most values bunching near zero. This brings every feature to one scale. It matters for linear models, which can't work with feature sets at wildly different scales at once — number of watermelons 100–600, number of seeds in tangerines 10000–30000. It's also critical for k-nearest neighbours (kNN). So it's better to bring the data to a state where the features sit at roughly one scale ahead of time.

Look at the charts:

```python
import matplotlib
matplotlib.use('TkAgg')
from matplotlib import pyplot as plt
plt.hist(X[1], alpha=0.6, color='g')

'''or'''
import matplotlib.pyplot as plt
plt.hist(X[1], alpha=0.6, color='g')
```

![Histogram of the experience feature](https://your-scorpion.ru/wp-content/uploads/2019/11/heart-icon2-1.png)

In principle it resembles the usual staffing of a design studio: a handful of experienced senior mentors and many juniors and mids. So the distribution is skewed — a typical situation for financial results too, since there's always a mass of everyday consumer goods and a tail stretching off to the right toward infinity, thanks to a small number of services with enormous price tags.

We find the mean of the first feature, `X1_mean = X[1].mean()`, giving 5.55 — the average tenure of a designer. Then the standard deviation, `X1_std = X[1].std()`:

```python
X1_mean = X[1].mean()
print(X1_mean)

X1_std = X[1].std()
print(X1_std)

X_standarted = X.copy().astype(np.float64)
X_standarted[1] = (X[1] - X1_mean) / X1_std
print(X_standarted)
```

![Standardized feature values](https://your-scorpion.ru/wp-content/uploads/2019/11/image-3-1.png)

Negative values in the result are what's below the mean. Ideally the mean will equal zero; let's compute it for the first feature:

```python
def calc_feature_std(x):
    result = (x - x.mean()) / x.std()
    return result

X_standarted[2] = calc_feature_std(X[2])
print(X_standarted[2])
```

```
[-0.15764177  0.09837795  0.18371785  0.01303804  0.78109718 -0.84036101
 -0.15764177 -0.15764177 -0.77208908 -0.64151903  3.17061451 -1.06224476
 -0.6696812  -0.4887606  -0.84036101 -0.15764177  1.71983613 -0.02109792]
```

The data are standardized. Algorithms find it easier to work with features in standardized form. In practice, people try both standardization and normalization and pick whichever scaling suits best — there's no axiom here.

## Doing it with scikit-learn: linear regression and SVM

There are plenty of useful technologies that help me plan the design department's work sensibly, and some of the analysis I've simply delegated to the computer. You can feed a neural network images and text, teach it computer vision — hand off, in short, the work of a matrix of pixels. Gradient boosting is built for working with features and has all the tooling for it: convolutions, pooling. Gradient boosting is very good with trees.

But today's subject is machine learning, which comes in two flavours: supervised and unsupervised. Supervised learning is about predicting some quantity. The "teacher" is a database of *object–answer* pairs, and it needs two datasets to forecast: training and test. In other words you need a dataset with the correct answer already attached, and from it you build a model that predicts the answer on similar data. We'll start simple — with **linear regression**. It's used to predict a continuous quantity; in our case, a forecast of spend on freelancers. Linear regression is essentially an algorithm that finds a certain kind of pattern in the data between an independent variable (X) and a dependent variable (y).

Given an object `x = (x1, …, xn)`, a linear-regression model predicts the target using a linear function. It builds the model on the training set and makes a prediction on the test set, returning the mean squared error. So: the model is `LinearRegression`, and there's a list of features.

We'll take scikit-learn, which has nearly every machine-learning algorithm you need. A note upfront: I'll deliberately show code that isn't textbook-perfect but is easier to follow and learn from. A designer — and this is a designer's blog — is perfectly entitled to write code that doesn't obey every canon of programming. What matters is that we solve the problem. So let's build our dataset:

```python
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import pandas as pd

def generate_dataset(n):
    x = []
    y = []
    random_x1 = np.random.rand()
    random_x2 = np.random.rand()
    random_x3 = np.random.rand()
    random_x4 = np.random.rand()
    random_x5 = np.random.rand()
    random_x6 = np.random.rand()
    random_x7 = np.random.rand()
    for i in range(n):
        x0 = i + np.random.rand() / 0.215 * 3.237
        x1 = i + np.random.rand() / 0.211 * 0.698
        x2 = i/2 + np.random.rand() - 1024 * 0.002 * 0.02 * 0.05
        x3 = i * 0.12 + np.random.rand() * 0.054
        x4 = i/1.42 + np.random.rand()
        x5 = i + 1 * 12 + np.random.rand() * 0.25
        x6 = i/4 + np.random.rand()
        x7 = i + np.random.rand()
        x.append([x0, x1, x2, x3, x4, x5, x6, x7])
        y.append(random_x1 * x1 + 1 + random_x2 * x2 + 1 + random_x3 * x3 + 1 + random_x4 * x4 + 1
                 + random_x5 * x5 + random_x6 * x6 + + random_x7 * x7)
    return np.array(x), np.array(y)

x, y = generate_dataset(3000)
X = pd.DataFrame(x)
y = pd.DataFrame(y)
X.columns = ['UI', 'UX', 'Payment', 'MRPPU', 'CPA', 'ARPPU', 'Tools', 'TSDB']
y.columns = ['Price_for_freelancers']
```

Above, we put the features into the `X` table and the target values into `y`: `Price_for_freelancers` is what we want to predict — the price of freelancers.

In real life, of course, you don't invent this data — you pull it out of the systems where it already lives, and getting the plumbing right is half the job. If, say, you need to gather price data across different ad campaigns, the paid, stable options are Supermetrics and Owox, or, at the pricier end, Fivetran and Stitch. Supermetrics can pull data from Yandex Metrica into Google Data Studio. There's a free route through Airbyte, but you won't escape a bit of Python plus cron to keep it running. Only once the numbers are flowing does the modelling below become worth doing.

Note the `test_size` parameter — it lets you plan the percentage of data set aside for the test. Pass a number between 0 and 1 (`test_size=0.2`) and that's the share of test objects as a percentage; this is the most common variant. But pass a number greater than 1 and it becomes a fixed count of objects in the test set.

As noted, building a model takes two samples: the training set, on which the model learns, and the test set, on which we check how well it works. The test is a simple comparison of the real prices against the predicted freelancer prices. To recap, our features live in the dataframe **X** and the target data in **y**, and the hypothesis is that there's a linear dependence between **X** and **y**.

```python
import seaborn as sns
new_df = pd.concat([y.reset_index(drop=True), X.reset_index(drop=True)], axis=1)
print(new_df)

sns.set_style('whitegrid')
sns.lmplot('Price_for_freelancers', 'CPA', new_df, palette='plasma', scatter_kws={'s': 0.4});
```

*Editor's note: recent seaborn requires keyword arguments — `sns.lmplot(x='Price_for_freelancers', y='CPA', data=new_df, …)`. The positional form above worked at the time of writing.*

![Seaborn regression plot of CPA against price](https://your-scorpion.ru/wp-content/uploads/2020/02/download.png)

The line clearly runs through a dependence between the `CPA` (cost per action) of the sites the designers built and `Price_for_freelancers`. The situation is artificial but very illustrative. Besides CPA, though, we have a whole set of other features. We'll use *train_test_split*, a very popular way to split data into training and test sets. We'll take a 0.25 share for the test and won't fix the randomness of the selection — it's more interesting that way. So the training set is 75% and the test set 25%. We leave `random_state` unset. Let's start building the model:

```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25)
from sklearn.linear_model import LinearRegression
lr = LinearRegression()
lr.fit(X_train, y_train)
y_pred = lr.predict(X_test)
print(y_pred)
```

Above we call linear regression, train on `X_train, y_train`, and predict on `X_test`. `lr.fit` returns `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None, normalize=False)`, and `predict` gives us the predicted values.

But surely we want to line the predicted values up against the actual ones?

```python
check_test = pd.DataFrame({
    "y_test": y_test["Price_for_freelancers"],
    "y_pred": y_pred.flatten(),
})

check_test.head(10)
```

|          | **y_test**   | **y_pred**   |
| -------- | ------------ | ------------ |
| **2830** | 8853.452885  | 8853.452885  |
| **2988** | 20299.645208 | 20299.645208 |
| **18**   | 38.872415    | 38.872415    |
| **1347** | 3825.888260  | 3825.888260  |
| **2838** | 8860.830740  | 8860.830740  |
| …        | …            | …            |
| **1571** | 5212.113755  | 5212.113755  |
| **718**  | 1117.374471  | 1117.374471  |
| **2283** | 7192.523583  | 7192.523583  |
| **2646** | 8947.234195  | 8947.234195  |

Test and prediction are identical. Visualise the data and it starts to make sense:

![Bar chart of the feature values](https://your-scorpion.ru/wp-content/uploads/2020/03/Untitled-1.png)

```python
strangeDataCheck = X.head(8)
strangeDataCheck.plot(kind='bar', figsize=(16, 10))
plt.grid(which='major', linestyle='-', linewidth='0.5', color='blue')
plt.grid(which='minor', linestyle=':', linewidth='0.5', color='red')
plt.show()
```

But let's run some checks and find out why the result is so strange. There's a metric that shows how strong the deviations are under the condition X = Y. Calling `r2_score` from the metrics class gives us the coefficient of determination. Not hard to guess that R² *is* the coefficient of determination (don't confuse RSS/TSS with ESS/TSS). The idea: the target variable is described by the sum of 10 random, equally weighted, independent factors. Suppose a cat might start meowing if you show it fish, meat, and eight other products — but we never know for sure whether it will. In that case R² = 0. But if showing it meat in the morning and fish in the evening *reliably* makes it meow, then that captured share pushes R² up — say to 0.2. The closer to 1, the better. One wrinkle: adding new variables with no relationship to the explained variable to the model still nudges R² upward. R²'s value doesn't depend on the scale of the predicted quantity and never exceeds 1 — unless by mistake we used RSS/TSS (model deviations in the numerator, data variance in the denominator), in which case a value above 1 tells us the model predicts rather poorly.

```python
from sklearn.metrics import r2_score
r2_score(y_test, y_pred)
```

In our case the coefficient of determination is 1 — practically perfect, and impossible with real data. On real data even R² = 0.9 is clear overfitting. Or, if both the training and validation sets show around 0.9, we've done very well. But in our case artificial data equals a strange result.

A brief aside on train/test sets. Never make the training and test sets the same. Never. Do that and the numbers from production will astonish you. So the convention is to split into training and test — but there's a problem here too: knowing all the answers from the test set, people start tuning the model to those answers. Hence a third set, the validation set, on which the final metrics are tested. Those numbers will be more modest. This is a simplified version of the cross-validation approach, where different models get different samples.

The above is a simple classification method. But if we want to add regression to the classification, we need a random forest. In the example below we'll look at a decision-tree algorithm called the **random forest**. For each tree a subset of features is chosen from the test set, and training proceeds. Each tree gives an answer, and if the largest number of trees votes for one answer, that answer is declared the winner.

We're looking for coefficients W for the features X, and we use `fit` to train, writing the result into a separate variable, `y_pred`. Then we create an object that will be our initial model.

```python
from sklearn.ensemble import RandomForestRegressor
model = RandomForestRegressor(n_estimators=2000, max_depth=18)
model.fit(X_train, y_train.values[:, 0])
y_pred_forest = model.predict(X_test)
r2_score(y_test, y_pred_forest)
```

The algorithm has run; with `predict` we can forecast freelancer prices and write them into `y_pred` as a plain array. We wrote the predicted value on `X_test` into `y_pred_forest`. Here the random forest does *worse* than linear regression, `0.714357` — and remember, I didn't fix `random_state` in the code.

## Reading the coefficients

And the sweetest part: let's look at the coefficients of the linear-regression model we built. The intercept — the free coefficient `W0` — lives in the `intercept_` attribute. Write `print(lr.intercept_)` and we get 3, the coefficient the model chose during training. The rest come from `print(lr.coef_)`. We can see clearly how much each parameter influences the result. But it's far nicer to judge feature weights visually.

```python
from matplotlib import pyplot as plt
%config InlineBackend.figure_format = 'svg'
%matplotlib inline
plt.barh(X_train.columns, lr.coef_.flatten())
print(X_train)
```

![Horizontal bar chart of feature weights](https://your-scorpion.ru/wp-content/uploads/2020/02/Frame-1-4.png)

## Overfitting and regularization

Now, overfitting: it's when a model works well on the data it was built on but predicts poorly on new data. With little data in the set, the odds of hitting this are high. We don't consider a model good until we've solved overfitting and selected good features. To cut the error we drop low-importance features, and for that we use regularization in the loss. In scikit-learn, linear regression with L1 regularization is the `Lasso` class, and L2 is `Ridge`. L1 works with the absolute value, L2 with the square. You tune the amount of regularization by changing alpha, and the rule is simple: the higher the alpha, the stronger the regularization and the more effectively you fight overfitting. Under regularization, feature weights shrink in absolute value, which removes one cause of overfitting — the uncontrolled growth of coefficients. The smaller alpha is in Lasso, the faster a feature's value zeroes out, which is a neat way to select features when there are very many. In Ridge the weights shrink in absolute value too, but more gently.

To assess a regression model's quality, we use metrics. The most popular ones:

Mean squared error — the average squared difference between the real and predicted quantity. This is MSE, the error function, computed simply: `mse1 = (check_test["error"] ** 2).mean()`.

The second is mean absolute error, which differs from MSE in reacting less to outliers in the data — the average not of the squared errors but of their absolute values. Again, computed simply: `(np.abs(check_test["error"])).mean()`.

Technically all of this could be done with OLS, but OLS is very resource-hungry at large data volumes. RMSLE, on the other hand, is well worth a look. This is all splendid — `Tools` is the most dominant feature — but why? The answer is in the numbers:

![Why Tools dominates: the numbers](https://your-scorpion.ru/wp-content/uploads/2020/02/Frame-1-2.png)

## When features live on different scales

Working with a linear model, we need the spread of values across different features to be of the same order of magnitude. You get there with a logarithmic transform plus standardization — a simple conversion of a value like 2237.037956 into 0.83846151, a necessary step in preparing data for correct feature selection. As a result, a standardized feature has a mean of 0 and a variance (squared deviation) of 1.

```python
sc = StandardScaler()
X_train_std = sc.fit_transform(X_train)
X_test_std = sc.transform(X_test)
```

In the example below we take a dataset whose features have different scales, so standardization is needed. From each feature value we subtract that feature's mean and divide by the standard deviation.

```python
import pandas as pd
import numpy as np
from sklearn.datasets import load_boston
from matplotlib import pyplot as plt
%config InlineBackend.figure_format = 'svg'
%matplotlib inline

boston = load_boston()
X = boston.data
y = boston.target
X = pd.DataFrame(X, columns=boston.feature_names)
feature_names = boston['feature_names']

from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25, random_state=42)
from sklearn.linear_model import LinearRegression
lr = LinearRegression()
lr.fit(X_train, y_train)
y_pred = lr.predict(X_test)

from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_train_scaled = pd.DataFrame(X_train_scaled, columns=feature_names)
X_test_scaled = scaler.fit_transform(X_test)
X_test_scaled = pd.DataFrame(X_test_scaled, columns=feature_names)
lr.fit(X_train_scaled, y_train)

plt.barh(X_train.columns, lr.coef_.flatten(), color="green")
plt.xlabel('Feature weight', fontsize=20)
plt.ylabel('Feature', fontsize=20)
plt.title("Feature scale")
```

*Editor's note: `load_boston` was removed from scikit-learn in version 1.2 over documented ethical concerns about the dataset. As a drop-in for the same demonstration, use `fetch_california_housing` or `fetch_openml(name="house_prices")`.*

![Feature weights on the Boston dataset](https://your-scorpion.ru/wp-content/uploads/2020/02/Frame-1.png)

`fit_transform` lets us compute the mean and standard deviation from the `X_train` dataframe and immediately compute the standardized value for each feature. Then, as we already know how, we take `lr.fit` and look at the new model built on now-standardized features — and drop the weights close to zero.

Back to our own example:

```python
feature_names = ['UI', 'UX', 'Payment', 'MRPPU', 'CPA', 'ARPPU', 'Tools', 'TSDB']
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_train_scaled = pd.DataFrame(X_train_scaled, columns=feature_names)
X_test_scaled = scaler.fit_transform(X_test)
X_test_scaled = pd.DataFrame(X_test_scaled, columns=feature_names)

lr.fit(X_train_scaled, y_train)

from matplotlib import pyplot as plt
%config InlineBackend.figure_format = 'svg'
%matplotlib inline
plt.barh(X_train_scaled.columns, lr.coef_.flatten())
plt.xlabel('feature weight')
plt.ylabel('feature')

print(X_train.describe())
print(X_train_scaled.describe())
```

![Feature weights after standardization](https://your-scorpion.ru/wp-content/uploads/2020/02/Frame-1-5.png)

![Describe output for raw and standardized features](https://your-scorpion.ru/wp-content/uploads/2020/02/Frame-1-6.png)

## Support Vector Machine

Another popular machine-learning method is the support vector machine (SVM). It's made of several algorithms and handles both classification and regression — classifying spam, say, or filtering out a certain kind of outlier. Visually it's a chart with a line, where the margin between the objects and the line is maximised and the error minimised; the dashed lines are the algorithm's degree of confidence.

![SVM margin and confidence bands](https://your-scorpion.ru/wp-content/uploads/2020/02/Frame-2.png)

You can take any datasets you like from the internet for experiments — [archive.ics.uci.edu](https://archive.ics.uci.edu/ml/datasets.php), for instance. I'll use my own dataset of designer data: [download](https://drive.google.com/file/d/1enlVxUU39Cm75o_Nljp5xBgV6Wj-H1tu/view?usp=sharing).

A few caveats, as always: SVM algorithms are demanding about their data, which has to be standardized and normalized. If you downloaded a dataset from the internet, scale the features without fail. I showed standardization earlier (subtract each feature's mean, divide by the standard deviation); you can also apply normalization, where each individual feature's minimum equals 0 and its maximum 1. My dataset has all of this handled. Let's begin:

```python
from sklearn.svm import SVC
import pandas as pd

data = pd.read_csv("export_dataframe_copy.csv", index_col="Good_UX")

data.head()

target = "Awards"

y = data[target]
X = data.drop(target, axis=1)

print(y)
print(X)
```

`MinMaxScaler` and `StandardScaler`. `StandardScaler` tends to show up more often in real problems, and it doesn't preserve sparsity, especially with many outliers — the mean becomes 0 and the standard deviation 1, which suits normally distributed data well. `MinMaxScaler` squeezes the data into the `[0, 1]` range. Use either one *before* `train_test_split` and you leak data, so watch that nothing from the validation/test set bleeds into training.

```python
from sklearn.model_selection import train_test_split
X_train, X_valid, y_train, y_valid = train_test_split(X, y, test_size=0.25, random_state=42)

from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
X_train = pd.DataFrame(scaler.fit_transform(X_train), columns=X_train.columns)
X_valid = pd.DataFrame(scaler.transform(X_valid), columns=X_valid.columns)
```

Logistic regression is about classification, so the Y variable should hold values of 1 or 0. Accuracy is a quality metric for classification tasks — the share of correct answers: Accuracy = (TN + TP) / (TN + TP + FN + FP).

```python
from sklearn.metrics import accuracy_score
clf = SVC(gamma=0.001, C=100., verbose=True)
import numpy as np

clf.fit(X_train, y_train)
```

```python
y_pred = clf.predict(X_valid)
y_pred_train = clf.predict(X_train)

accuracy_score(y_valid, y_pred)

from sklearn.linear_model import LogisticRegression
lr = LogisticRegression(solver="lbfgs", penalty='l2')
lr.fit(X_train, y_train)
y_pred_train = lr.predict(X_train)
accuracy_score(y_train, y_pred_train)
```

We got a less-than-satisfying accuracy of 0.6796407185628742 — let's improve it. The `SVC` model has a `C` parameter that penalises classification error; by default it's 1. Let's set several candidate values and see which are most advantageous:

```python
import numpy as np
c_values = np.logspace(-2, 5, 36)

accuracy_on_valid = []
accuracy_on_train = []

for i, value in enumerate(c_values):
    clf = SVC(C=value, kernel='rbf', gamma="auto")

    clf.fit(X_train, y_train)

    y_pred = clf.predict(X_valid)
    y_pred_train = clf.predict(X_train)

    acc_valid = accuracy_score(y_valid, y_pred)
    acc_train = accuracy_score(y_train, y_pred_train)

    if i % 5 == 0:
        print('C = {}'.format(value), '\tvalidation = {}'.format(acc_valid))
        print('C = {}'.format(value), '\ttraining = {}\n'.format(acc_train))

    accuracy_on_valid.append(acc_valid)
    accuracy_on_train.append(acc_train)
```

```
C = 0.01 	validation = 0.6905829596412556
C = 0.01 	training = 0.6796407185628742

C = 0.1 	validation = 0.6905829596412556
C = 0.1 	training = 0.6796407185628742

C = 1.0 	validation = 0.6905829596412556
C = 1.0 	training = 0.6796407185628742

C = 10.0 	validation = 0.6905829596412556
C = 10.0 	training = 0.6841317365269461

C = 100.0 	validation = 0.6905829596412556
C = 100.0 	training = 0.688622754491018

C = 1000.0 	validation = 0.6860986547085202
C = 1000.0 	training = 0.6961077844311377

C = 10000.0 	validation = 0.6771300448430493
C = 10000.0 	training = 0.7155688622754491

C = 100000.0 	validation = 0.695067264573991
C = 100000.0 	training = 0.7335329341317365
```

As you can see, accuracy on the validation data rises up to a point. From around 40,000 there's a visible dip on validation while accuracy on training keeps climbing — which means the model is starting to overfit.

```python
import matplotlib.pyplot as plt
plt.plot(c_values, accuracy_on_valid, label="valid", linewidth=3, alpha=0.4, color="g")
plt.plot(c_values, accuracy_on_train, label="train", linewidth=3, alpha=0.4, color="b")

plt.xlabel('C parameter value')
plt.ylabel('Accuracy')

plt.legend()
plt.grid()

plt.show()
```

![Accuracy on train and validation against C](https://your-scorpion.ru/wp-content/uploads/2020/03/Screen-Shot-2020-03-04-at-10.57.58-AM.png)

Besides accuracy, you have to weigh F1, precision, and recall. If accuracy = 86.43% while precision = 24.73%, that's unsatisfactory — the precision is low and the model can't be shipped. Always look at the confusion matrix. You can see plainly that validation accuracy at C = 40000 is already higher than SVM with no parameter tuning — but bear in mind that SVM isn't especially effective on noisy data.

## Beyond the basics

Everything above is the foundation; once it's comfortable, a few directions open up — the questions I'm asked most often after this material tend to cluster around the same handful of problems.

**Filtering the outliers, and what an anomaly even is.** From a business standpoint, an anomaly depends entirely on the business — but a dip in money is unambiguously one. For flagging them, a LightGBM quantile regression paired with a bidirectional LSTM (BiLSTM) works well: LightGBM averages nicely and produces fewer freak forecasts, so when the neural net predicts a severe anomaly, we don't believe it; when it falls inside a sensible window, we do. To cut the very strongest outliers out of clusters, DBSCAN does the job.

**Forecasting who converts to a paying user, from chains of events.** Start by aggregating the behavioural data. From there you can vectorise it and look toward an RNN or an LSTM that forecasts a time series several steps into the future; ARMA and its extensions also work well. It's worth weighing both fundamental analysis (a deep dive into the domain) and technical analysis (charts, candles), or the time-series family, ARMA and ARIMA — you feed those nothing but the shape of the curve and they read the regularities and forecast. And, of course, any flavour of regression: take every metric you've computed and pour it in. A time series itself is neatly described by trend, frequency, amplitude, and time.

**Finding a dependence between time and actions.** When a user installs the app and performs actions, each carrying a timestamp, and you want to understand the correlation between how those actions are distributed over time — I'd start with a G-test or a table-based chi-squared test, and then read up on the word *cointegration*.

**When the actual numbers drift from the forecast.** Say you have a chart of actual sales and a prediction model, and by eye they look close, but the real drop in sales runs much deeper than the forecast. To tell whether the drop is tied to specific factors or the model is simply off: localise the statistically significant drop using confidence intervals across different time windows of the prediction; build a model on the drop you've isolated; and then, with a model for during the drop and one for before it, compare their coefficients.

**Comparing two samples of unequal quality.** With one good probabilistic sample that's small, and a second that's a simple internet panel riddled with bias, I'd look toward Propensity Score Adjustment, borrowed from medicine: you can blend the samples with an algorithm and obtain the probability of belonging to the reference sample, working from socio-demographics, geography, and recorded activity.
