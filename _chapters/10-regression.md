---
layout: post
title:  "Chapter 10: Regression"
number: 10
---

In Chapter {{chapter_inference}}, we started to formalize the mathematics behind statistical analysis in order to make claims about populations based on  samples. In this chapter, we are going to explore a family of techniques for predictive modeling, which makes claims about new data based on relationships found in previous data, called regression analysis.

## Modeling a Linear Relationship

Predictive modeling generally means coming up with an equation that relates the value of one random variable to the value of one or more other random variables. Generally, we write these equations in terms of parameters, then estimate those parameters using estimation techniques based on a sample.

The simplest kind of relationship we can model using an equation is a linear one: where one variable consistently goes up or down when the other goes up. This model looks like a familiar linear equation:
$$
\hat y = mx + b
$$
Here, the hat indicates that y is a prediction of the true y. X is a random variable while m and b are parameters that we have to estimate based on a dataset consisting of n x-y pairs. Estimating these parameters based on data means asking: what is the line of best fit?

<img src="../assets/images/linear_regression1.png"/>  <img src="../assets/images/linear_regression2.png"/><img src="../assets/images/linear_regression3.png"/>    

Which of these do you think is the best fit for the data shown? If you said the first one, statisticians would agree with you.

We can formalize the idea of "goodness of fit" using a quantitative measure called a loss function. In this case, the measure statisticians use as their loss function is the sum of squared error between the prediction (the line) and the true values (the dots). We can draw those differences, called "residuals," as lines on the plot:

<img src="../assets/images/linear_regression4.png"/>  <img src="../assets/images/linear_regression5.png"/><img src="../assets/images/linear_regression6.png"/>    

Now it is clearest that the best line is the one that has the smallest length residuals. 

Generally, if we want to get the smallest lengths, we have two options: we can take the absolute value of the difference, or we can square the difference. Statisticians and data scientists generally prefer to square the difference, for reasons related to calculus. We can translate this idea into mathematics:
$$
\mathcal{L}(y, \hat y) = \sum\limits_{i=1}^n (y_i - \hat y_i)^2
$$
The first symbol there is a script L, which we use for "loss." You can see it is a function of y (the true y values) and y-hat (the estimates, the height of the line under each data point), and it goes point by point and measures the distance between the two, squared.

Now, we need to find the best possible values of the parameters to minimize the loss. We can do that using the concept of optimization from calculus. If you have studied calculus, the idea is to take the derivative of the loss with respect to b, set it equal to zero and solve. Then take the derivative of the loss with respect to m, set it equal to zero and solve. Ultimately, the solution ends up being:
$$
b = \bar y - m \bar x \\
m = \frac{\sum\limits_{i=1}^n (x_i - \bar x) (y_i - \bar y)}{\sum\limits_{i=1}^n (x_i - \bar x)^2}
$$
For practical purposes, this solution is implemented in a library called scipy.

```python
from scipy.stats import linregress
m,b,r,p,stderr = linregress(x,y)
```

This function takes a 1D array of X values and a 1D array of Y values and returns five things:

* m is the slope of the line
* b is the y-intercept of the line
* r is the correlation between x and y
* p is the probability that m = 0, based on a hypothesis test
* stderr is the standard error of m

The p value is a often-challenging topic for students of statistics. 

Crucially, these values are only valid if we make a certain set of assumptions about the data:

* We assume that the data actually has a **linear relationship**
  * If it doesn't (the values seem to lie near a curve, not a line), we may want to use a polynomial or other kind of regression instead.
* We assume that each data point's deviation from the line is due to **normally distributed** random error.
  * If it's not (the values aren't mostly near the line) you probably shouldn't trust the standard error.

* We assume that the random error has **variance that does not depend on the value of X**. This has a fancy statistical name: *homoscedasticity*.
  * If it's not (the values spread more towards one end of the line than the other), you probably can't use the standard error or p value. This is a common problem when working with financial data. Often, taking the log of the y values can make the data more homoscedastic.

## Including Multiple X Values

Often, the variance in data is not fully explained by a single X value. For example, when we are trying to 