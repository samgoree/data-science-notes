---
layout: post
title:  "Chapter 11: Classification"
number: 11

---

In Chapter {{chapter_regression}}, we discussed predictive modeling in terms of predicting numerical quantities. In this chapter, we will extend predictive modeling to predicting categories, often called classification. We will then gather together many of the techniques we have discussed under a common framework for "machine learning models."

## What is Classification?

Sometimes, we want to make a prediction which is not a continuous value, but a discrete category. This comes up all the time, for example, when predicting discrete future things like the weather, when writing code to automate decision tasks like whether or not to interview a candidate for a job, or when organizing things into categories. These predictions require a different sort of model, called a classifier.

As a simple example problem, let's say we have four variables describing the weather, and want to predict another variable: whether it is a good day to play tennis. We have a dataset of 13 observations of these variables, and want to build a predictive model to predict whether it is a good day to play tennis in general based on the weather.

<img src="../../assets/images/tennis.png"/>

## Evaluating Classification

Just like we used metrics like the sum of squared errors and R-squared score to evaluate regression models, we need some metric to evaluate classification models.

The most obvious metric is *accuracy*:
$$
\text{Accuracy}= \frac{\#\text{correct}}{\#\text{total}}
$$
This metric measures the number of examples where we make the correct prediction, as a fraction of the total. Accuracy is often reported as a percent.

Sometimes, though, some errors are more important than others. When this is the case, we often describe the results of a classifier in terms of a *confusion matrix*:

|                    | **Predicted True**   | Predicted False      |
| ------------------ | -------------------- | -------------------- |
| **Actually True**  | True Positives (TP)  | False Negatives (FN) |
| **Actually False** | False Positives (FP) | True Negatives (TN)  |

(*sidenote:* in a statistics class, you may have encountered a similar table describing "type I" and "type II" errors. This is a related, but different concept involving the actual experimental process.)

We can express accuracy in terms of these four values:
$$
\text{Accuracy} = \frac{TP + FP}{TP + FP + TN + FN}
$$
For example, let's say we're trying to classify emails into spam and not spam. We may end up with a confusion matrix:

|                    | **Predicted True** | Predicted False |
| ------------------ | ------------------ | --------------- |
| **Actually True**  | 150                | 10              |
| **Actually False** | 100                | 1240            |

This means:

* There were 1500 total emails in the test set.
* 160 of them were actually spam.
* 1340 were not spam.
* Our spam filter filtered out 250 emails, 150 of which were actually spam and 100 of which were not.
* Our inbox ended up with 1250 emails, 10 of which were spam.
* The accuracy of the classifier was 92.7%

However, in spam classification, false positives are much worse than false negatives. Those are real emails that you'd like to read that are going to spam! On the other hand, having a few stray spam emails in your inbox isn't the end of the world. So we would like to use a different evaluation metric that better captures that aspect of performance.

It turns out there is a metric like that, called *precision*:
$$
\text{Precision} = \frac{TP}{TP + FP}
$$
This metric asks "of all the emails that we filtered out, how many actually were spam?"

For this spam example, the precision is 60%. That tells a very different story than the over-90% accuracy!

However, if we are in a different scenario, another metric may matter more. For example, let's say we are developing a model that determines whether a patient has a rare form of cancer. We find that our model has this confusion matrix:

|                    | **Predicted True** | Predicted False |
| ------------------ | ------------------ | --------------- |
| **Actually True**  | 5                  | 0               |
| **Actually False** | 995                | 9000            |

In this case, there are 10000 patients, and only 15 of them actually have the rare cancer. Our accuracy is just over 90%, but our precision is only 0.5 percent. Is that a problem?

Cancer tests are valuable because they give clues to doctors, and they don't have to work perfectly to do that. But a false negative, where the test says that a patient doesn't have the cancer when they actually do, is much more harmful than a false positive, since doctors can always do additional tests.

In this case, a better metric is *recall*:
$$
\text{Recall} = \frac{TP}{TP + FN}
$$
This metric asks "of all the patients who have the rare cancer, how many did we detect?" In this case, our recall is 100%, meaning this model is very good. However, you may notice that the *prevalence* rate of the condition (how many people actually have the cancer) is very low in our dataset, so we may need to gather more data before reaching any conclusions.

## Two Example Classifiers

### Decision Trees

Effectively, the thing that we want is a series of "if-then-else" statements about whether it is a good day to play tennis. For example, if it is overcast, it is a good day to play tennis. But if it's rainy, it is only a good day to play tennis if it is not windy. As humans, we can list a bunch of these decision rules just by looking at the data. However, if there were hundreds of rows or columns, it would be much more difficult, so we would like an automated algorithm to figure it out for us.

Computer scientists have developed such an algorithm. It produces groups of decision rules organized into a tree shape:

<img src="../../assets/images/tennis-tree.png"/>

In computer science, we call shapes like this "trees" since they form branches.

In this particular visualization, the top decision rule is the same one we came up with: if it is overcast, play tennis. We can write all the learned rules in English as follows:

* If the outlook is overcast, play tennis.
* Else:
  * If the humidity is normal:
    * If it is not windy play tennis
    * Else:
      * If it is cool, don't play tennis
      * Else play tennis
  * Else:
    * If it is sunny, don't play tennis
    * Else:
      * If it is windy, don't play tennis

The underlying algorithm for constructing these rules involves recursively splitting the data based on a measure of entropy. If you'd like to learn more about decision trees and other algorithms for learning from data, I recommend taking a machine learning class, such as Stonehill's CSC 322.

An implementation of this algorithm can be found in Scikit learn. For example, if we wanted to fit a decision tree to all-numerical data:

```python
from sklearn.tree import DecisionTreeClassifier

model = DecisionTreeClassifier()
# assuming X is numerical input data of shape (n,m) and y is categorical output data of shape (n,) 
model.fit(X_train, y_train)
print('Score:', np.score(X_test,y_test))
y_pred = model.predict(new_x_values)
```

For our tennis problem, though, we do not have numerical data for the weather variables. One way to solve this problem is by converting a column like "outlook" which has three values (sunny, rainy, overcast) into three columns with either 1 or 0.  Four rows that look like this:

| Outlook  |
| -------- |
| Sunny    |
| Sunny    |
| Overcast |
| Rainy    |

Would turn into this:

| Outlook_sunny | Outlook_rainy | Outlook_overcast |
| ------------- | ------------- | ---------------- |
| 1             | 0             | 0                |
| 1             | 0             | 0                |
| 0             | 0             | 1                |
| 0             | 1             | 0                |

This technique is called a "one-hot" encoding by computer scientists and "dummy variables" by statisticians. In Scikit-learn, we can use it like this:

```python
from sklearn.preprocessing import OneHotEncoder

encoder = OneHotEncoder()
X = encoder.fit_transform(X_with_strings).toarray()
```

Here, `X_with_strings` is a dataframe or array like the tennis data, which has categorical data stored as strings. X is the corresponding one-hot encoded numpy array. The `toarray()` at the end converts from a sparse representation to a numpy array, which is necessary if you want to look at the data.

If we want to encode new data later, we can keep the encoding consistent by using the same encoder object.

```python
new_X = encoder.transform(new_X_with_strings).toarray()
```

### Using Linear Regression for Classification (Logistic Regression)

It turns out we can also adapt linear regression to perform classification. The key idea is that instead of predicting a continuous target variable, we predict the probability of a given class. For simplicity, we will only consider problems with two possible output values, though regression can also be used for more sophisticated prediction problems.

What we need for this model is a function that turns the output of linear regression, which is a real number that can occur anywhere on the number line, into a probability which must occur between 0 and 1. It turns out that there is a function with that behavior: the logistic sigmoid function.
$$
\text{Sigmoid}(x) = \frac{1}{1+e^{-x}}
$$
![](../../../assets/images/sigmoid.png)

Notice this function starts at 0 for very large negative numbers and goes to 1 for very large positive numbers. The boundary between these two extremes happens smoothly, centered around 0. We can change the exponent in the denominator to vary the location and steepness of the boundary.
$$
\text{Sigmoid}(x; m,b) = \frac{1}{1 + e^{-mx + b}}
$$
Now, the contents of that exponent begin to look a lot like a linear regression model. We can include multiple X values as well like in multivariate regression to include more variables. It turns out we can fit this model to data from classification problems and it works, finding the optimal decision boundary between the two classes in the data. This model is called *logistic regression*. Even though it is called regression, it is a classification model.

The actual algorithm for fitting this model to data requires multivariable calculus, but we can just use the implementation from scikit-learn.

```python
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()
# assuming X is numerical input data of shape (n,m) and y is categorical output data of shape (n,) 
model.fit(X_train, y_train)
print('Score:', np.score(X_test,y_test))
y_pred = model.predict(new_x_values)
```

## Towards Machine Learning

The models we have explored in this and the previous chapter are a first look at the field of machine learning from computer science. Computer scientists have developed algorithms to fit these models to data and use them to make predictions, and so they are very valuable for predictive modeling in data science.

Machine learning and statistics are separate fields with very different languages, but they are ultimately two views of the same underlying world of mathematical modeling.
