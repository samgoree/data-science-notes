---
layout: post
title:  "Chapter 9: Statistical Inference"
number: 9

---

In Chapter {{chapter_statistics}}, we encountered summary statistics such as mean and correlation. These statistics all describe *samples*: sets of data that we have actually collected about a subset of a population. In this chapter, we will start discussing statistics that we can estimate about populations based on samples.

## Sampling

In statistics (and data science), we are generally interested in knowing things about populations. A *population* is a large group of individual people or things in the world. Something we may want to know is the average age of people in Europe. For that, the population is "people in Europe", the variable we are investigating is age and the statistic is the average. Alternatively, we may want to know the correlation in European cities between population and cost of housing. In that case, even though "population" is one of the variables, the statistical population is "cities in Europe," the variables we are investigating are population and cost of housing and the statistic is the correlation.

Generally, there are two types of datasets that we can collect about a population. A *census* is a complete dataset, which contains data about every individual in a population. A *sample* is a subset of that population, containing some, but not all, of the individuals. A *random sample* is a sample where each individual in the sample has the same chance of being chosen for the sample. A *nonrandom sample* is a sample where some individuals are more likely to be chosen for the sample than others.

In general, a census is extremely difficult (often impossible) to carry out. For example, the US census tries to measure a few characteristics about the entire US population. The government spent [$13.7 billion to carry out the 2020 census](https://www.gao.gov/products/gao-23-105819), and still can't successfully count every single American (though they get pretty close). In many cases, scientific experiments are conducted on experimental groups, which are each samples of some large population. For example, an experiment conducted on bean plants can only measure data about a sample of bean plants, not all bean plants everywhere.

Instead, the goal of inferential statistics is to *infer* statistics about an entire population based on data from a sample. Statisticians and data scientists carry out inference by *modeling* the population generating the data, and then *estimating* the values for specific numbers in the model, called *parameters*.

In addition to this kind of inferential statistical modeling, data scientists often carry out predictive modeling. The goal of a predictive model is not to infer details about a larger population, but to infer the value of some metric for an individual based on the values of other metrics about that individual. For example, we might use a predictive model to infer the best asking price of a house from its size and number of bedrooms/bathrooms. These predictive models are built in the same way, by estimating parameters based on a sample, so we will explore some simple inferential statistics here and leave predictive modeling to the next chapter.

If you are interested in learning more about the process of sampling and the different techniques that scientists have for collecting randomized data, you may want to take a research methods class.

## Modeling Random Events

Our mathematical model of randomness starts with counting all the possible outcomes of a random event. A random event is anything that happens without a predictable outcome. We write the fraction of outcomes which share a particular property A using a function: P(A). If half of outcomes have property A, we write P(A) = 0.5.

Probability follows two key rules: the probability of any outcome is non-negative, and the sum of the probabilities of every possible outcome of an event is 1. For example, let's say we roll two six-sided dice. There are 36 possible outcomes, and each has a probability of 1/36. We can define a property "at least one 6" and calculate P(at least one 6) by counting outcomes with at least one 6. It turns out 11 of the 36 outcomes have that property, so we know P(at least one 6) = 11/36. If we roll our pair of dice many times, approximately 11/36 of the trials will have at least one 6.

We can simulate this experiment using random number generators. A random number generator which picks a number between 1 and 6 uniformly at random behaves like a die roll. Since there are two dice, we will generate an array of shape `(n_trials, 2)` where `n_trials` is our number of trials.

```python
n_trials = 1000
rng = np.random.default_rng()
results = rng.integers(1,7, size=(n_trials, 2))
sixes = (results == 6)
at_least_one_six = sixes.any(axis=1)
fraction = at_least_one_six.mean()
fraction
```

Output: something around `0.305` (approximately 11/36)

This simulation captures the steps of the random event. We can show how the empirical probability (the actual fraction of events) approaches the theoretical probability over time by plotting the partial sums.

```python
rolling_mean = at_least_one_six.cumsum() / np.arange(n_trials)
plt.plot(rolling_mean)
```

![plot1](../../assets/images/die_rolls.png)

The value starts out jumping all over, but eventually settles to the theoretical value of 0.305.

### Random Variables

While this idea of random events and outcomes underlies probability theory, data scientists don't think about random events very often. It turns out that there are a ton of random events contributing towards any particular measurement, and it is kind of silly to try to model them all. Instead, we tend to model data using *random variables*.

A random variable, confusingly, is a type of function. It maps from random events to numerical values. For example, if we flip five coins and count the number of heads, we could write the number of heads as a random variable: X(Heads, Tails, Heads, Tails, Tails) = 2. For simplicity, we will just write X = 2. In data science, we often view columns of datasets as the value of a random variable across samples.

We can summarize any random variable based on the way it distributes probability over the number line. We often talk about these functions like physical objects, and treat probability like the weight or mass of that object. There are two ways to describe random variables in this way:

* Probability mass functions: These functions contain probability of specific values, but there are finitely many values that can come up. For example, the plot below shows a probability mass function which assigns probabilities only to the whole numbers 0-9. These discrete probabilities sum to 1. In our physical analogy, this is like a string of blocks of different weights, connected by a weightless wire.

  ![plot1](../../assets/images/probability_mass_function.png)

* Probability density functions: These functions contain probability of every value on the number line. There are infinitely many possible values that can come out of these random variables. For example, the plot below shows a probability density function which assigns probabilities to continuous values from -10 to 10. Since this function is continuous, we have to use the concept of an integral from calculus to sum over it. Its probabilities integrate to 1. In our physical analogy, this is a solid object which is denser in some places than others.

  ![plot1](../../assets/images/probability_density_function.png)

With a mathematical definition for a random variable, now we can define operations that we have already seen, like the mean, in terms of theoretical probability.

#### Expectation

The theoretical equivalent of the mean is the *expected value*, written with a "blackboard bold" E:
$$
\mathbb{E}[X] = \sum\limits_{x = -\infty}^{+\infty} xP(X=x)
$$
In case you missed it earlier, the large sigma (Σ) notates a sum over each value of x (like a for loop). In this case, it is an infinite sum, which practically means we sum over every value of x for which P(X=x) is nonzero. For example, let's say we have a random variable H that takes a coin flip and assigns 1 to a heads and 0 to a tails. The expected value of the coin flip is 
$$
\mathbb{E}[H] = 0 * P(X=0) + 1 * P(X=1) = 0 * 0.5 + 1 * 0.5 = 0.5
$$
Note that this equation is only in terms of the probability of each value the random variable, not anything related to the underlying random event.

A key property of the expected value is called *linearity of expectation*: that means that the expected value function obeys two key properties of linear functions:
$$
\mathbb{E}[X + Y] = \mathbb{E}[X] + \mathbb{E}[Y] \\
\mathbb{E}[aX] = a \mathbb{E}[X]
$$
Where X and Y are any two random variables and a is a constant.

#### Variance

The variance of a random variable is defined similarly to the variance of a sample: the expected squared difference from the expected value.
$$
\mathbb{V}[X] = \mathbb{E}[(X - \mathbb{E}[X])^2]
$$
As we also saw before, we can rearrange that definition using linearity of expectation to arrive at another definition for variance: the difference between the expectation of a variable squared and the expectation of the squared variable.
$$
\mathbb{V}[X] = \mathbb{E}[X^2 - 2X\mathbb{E}[X] + \mathbb{E}[X]^2] \\
= \mathbb{E}[X^2] - 2\mathbb{E}[X]\mathbb{E}[X] + \mathbb{E}[\mathbb{E}[X]^2] \\
= \mathbb{E}[X^2] - 2 \mathbb{E}[X]^2 + \mathbb{E}[X]^2 \\
= \mathbb{E}[X^2] - \mathbb{E}[X]^2
$$
This quantity becomes large when the random variable has a lot of high probability values far from the expected value and will become 0 when the random variable always has the same value. Note that random variables do not involve data -- these concepts are only defined in terms of probability.

### Example: Counting Invisible People

A common problem in statistical analysis of real-world populations is figuring out the size of a population without taking a census. This task is particularly important when estimating the size of populations of humans who might be reluctant to identify themselves, such as illegal drug users, or populations of animals that are difficult to observe, such as Siberian tigers.

However, we can use statistics to estimate the size of such a population using simple probability. Here is the procedure:

* First take a random sample of the population, call it A. These can be collected using methods like surveys for humans, or capture-tag-release studies for animals. Count the number of individuals that meet the inclusion criteria and call that number U.
* Next, take another random sample of the population, call it B. Count the number of individuals that meet the inclusion criteria and call that number V.
* Finally, count the number of individuals in common between the two samples, call that number W. The estimated total size of the population is UV/W.

This is a technique called the Lincoln-Peterson Estimator.

Why does it work? Well, consider the probabilities for an individual i:
$$
P(i\text{ in }A) = U / N
$$
P(i in A) is the probability that individual i is in sample A. Sample A contains U members of the target group, which is of unknown size N.
$$
P(i\text{ in }B) = V / N
$$
P(i in B) is the probability that individual i is in sample B. Sample B contains V members of the target group, which is of unknown size N.

So how many individuals are expected to overlap? Call the overlap W:
$$
P(i\text{ in both}) = P(i\text{ in }A)P(i\text{ in }B) \\
W = \#(\text{i in both})\\
\mathbb{E}[W] = \sum\limits_{i \text{ in }A} 1 * P(i\text{ in }B) = U P(i \text{ in } B) = UV/N
$$
In this context, the # symbol is used to mean the "number of" elements with a given property.

The expected value of W is equal to the probability that any individual from the population is in B, times U, which is the number of those individuals in A. You can think of this as going down the line of people in sample A: the probability that the first person is in sample B plus the probability that the second person is in sample B, plus the probability that the third person is in sample B etc. That expected value works out to UV / N. If we solve for our unknown, N, we arrive at our estimator UV / W.

## The Normal Distribution

One of the most useful probability density functions in data science is the normal, or Gaussian distribution. This distribution is commonly known as a "bell curve" because of its shape. It describes random numbers which tend to be located around a single mean value, but can vary up or down indefinitely. We saw the normal distribution in Chapter {{chapter_statistics}} in the Numpy function `rng.normal`.

![](../../assets/images/random2.png)

Normal distributions come up all over statistics because of a key result called the *central limit theorem*: the sum of many independent and identically distributed (iid) random variables is normally distributed. In practice what that means is that statisticians can usually assume that all sorts of continuous random variables are normally distributed, because their values are the sum of many iid random variables.

The actual functional form that produces this curve is a little complicated:
$$
\mathcal{N}(x;\mu, \sigma^2) = \frac{1}{\sqrt{2\pi\sigma^2}} e^{- \frac{(x - \mu)^2}{2\sigma^2}}
$$
The function contains many unusual symbols: 

* The script N is the name of our function. N(x) is the height of the curve at point x on the x-axis.
* The semicolon is just notation, specifying that two of the parameters of the function are shape parameters ("the normal distribution value for x with mean mu and standard deviation sigma is..."). 
* Mu (μ) is a parameter which specifies the center of the distribution.
* Sigma (σ) squared is a parameter that controls the width of the curve. We write it as "sigma squared" rather than giving it its own variable name for historical reasons.
* X is the actual input to the function.

Some things that you can prove about the normal distribution using calculus:

* The expected value is equal to the parameter μ

* The variance is equal to the parameter σ<sup>2</sup>

* 95% of the probability falls within the interval:
  $$
  \mu \pm 1.96 \sigma^2
  $$

### Example: Estimating Mean and Variance

At this point, we are very familiar with computing the mean and variance of a dataset. In statistics, simply computing the mean of a dataset without doing any more sophisticated analysis is often called a *population mean* since it assumes we have collected data for the entire population under study. If we want to treat our data as a sample and talk about a larger population, those statistics become the *sample mean* and *sample variance* instead.

Let's say we have a normally distributed random variable X with true population mean mu. We collect n samples from X and compute the mean of those samples (as seen before, statisticians use bars above variables to indicate sample means).
$$
\bar x = \frac{1}{n}\sum\limits_{i=1}^n x_i
$$
What is the expected value of the sample mean? We can compute that using linearity of expectation and the expected value of the normal distribution:
$$
\mathbb{E}[\bar x] = \mathbb{E}[\frac{1}{n}\sum\limits_{i=1}^n x_i] \\
= \frac{1}{n}\sum\limits_{i=1}^n \mathbb{E}[x_i] \\
= \frac{1}{n}\sum\limits_{i=1}^n \mu \\
= \mu
$$
Therefore, the sample mean is a good estimate for the population mean.

What is the expected value of the sample variance? 
$$
\mathbb{E}[Var] = \mathbb{E} [\frac{1}{n} \sum\limits_{i=1}^{n} (x_i - \bar x)^2 ] \\
= \mathbb{E}[\frac{1}{n} \sum\limits_{i=1}^{n} (x_i^2 - 2x_i\bar x + \bar x^2)] \\
= \frac{1}{n} \sum\limits_{i=1}^{n} (\mathbb{E}[x_i^2] - \mathbb{E}[2x_i\bar x] + \mathbb{E}[\bar x^2]) \\
= \frac{1}{n} \sum\limits_{i=1}^{n} (\mathbb{E}[x_i^2] - 2\mathbb{E}[\bar x^2] + \mathbb{E}[\bar x^2]) \\
= \frac{1}{n} \sum\limits_{i=1}^{n} (\mathbb{E}[x_i^2] - \mathbb{E}[\bar x^2]) \\
$$
Now that first term, the expectation of each variable squared, is tricky to deal with. It is not equal to the squared expectation of x, because squaring is not one of the operations allowed under linearity of expectation. But we can do a little trick involving the definition of variance:
$$
\mathbb{V}[x] = \mathbb{E}[x^2] - \mathbb{E}[x]^2 \\
\mathbb{E}[x^2] = \mathbb{V}[x] + \mathbb{E}[x]^2
$$
Substituting back into the sum:
$$
\frac{1}{n} \sum\limits_{i=1}^{n} (\mathbb{E}[x_i^2] - \bar x^2) \\
= \frac{1}{n} \sum\limits_{i=1}^{n} (\mathbb{V}[x_i] + \mathbb{E}[x_i]^2 - (\mathbb{V}[\bar x] + \mathbb{E}[\bar x]^2)) \\
$$
We know by the properties of the normal distribution what the expectation and variance of each x<sub>i</sub> should be, but the terms inside the parentheses are a little confusing. The variance of the mean of x and the expectation of the mean of x don't make a ton of intuitive sense, but we can use the definition of the mean to figure them out:
$$
\mathbb{V}[\bar x] = \mathbb{E}[(\bar x)^2] - \mathbb{E}[(\bar x)]^2 \\
= \mathbb{E}[\frac{1}{n^2}(\sum\limits_{i=1}^n x_i)^2] - \mathbb{E}[\frac{1}{n}(\sum\limits_{i=1}^n x_i)]^2 \\
= \frac{1}{n^2}\mathbb{E}[(\sum\limits_{i=1}^n x_i)^2] - \mathbb{E}[(\sum\limits_{i=1}^n x_i)]^2 \\
= \frac{1}{n^2}\sum\limits_{i=1}^n \mathbb{V}[x_i]
$$
And we know by the properties of the normal distribution that the variance of each x is equal to σ<sup>2</sup>. Therefore:
$$
\mathbb{V}[\bar x] = \frac{1}{n^2}\sum\limits_{i=1}^n \sigma^2 \\
= \frac{\sigma^2}{n}
$$
*Sidenote:* This is a very special quantity: the variance of the sample mean. If we take the square root, we get a statistic called the standard error of the mean:
$$
SEM = \frac{\sigma}{\sqrt{n}}
$$
Similarly, the expectation of the mean of each x is equal to μ. Substituting those values in, we get:
$$
\frac{1}{n} \sum\limits_{i=1}^{n} (\mathbb{V}[x_i] + \mathbb{E}[x_i]^2 - (\mathbb{V}[\bar x] + \mathbb{E}[\bar x]^2)) \\
= \sigma^2 + \mu^2 - (\sigma^2/n + \mu^2) \\
= \sigma^2(1-1/n)
$$
Which means that computing the variance on a sample doesn't estimate the true variance σ<sup>2</sup> of the population, it estimates σ<sup>2</sup>(1 - 1/n). In other words, when n is small it will systematically underestimate the right value. We can fix this problem by performing "Bessel's correction" and calculating the sample variance or standard deviation using the corrected formula:
$$
s^2 = \frac{n}{n-1} Var(x) \\
= \frac{1}{n-1} \sum\limits_{i=1}^{n} (x_i - \bar x)^2 \\
s = \sqrt{s^2}
$$
While you are not expected to do these sorts of mathematical proofs regularly as a data scientist, you are expected to understand and apply ideas like these developed and proved by statisticians. If you are interested in this kind of rigorous understanding of data analysis techniques, you may want to take a course in probability and statistics, such as Stonehill's MTH 395.

## Hypothesis Testing

While we can use these estimates of population mean and variance, they are only ever estimates. The exact true value of these statistics for the population is unknowable from only a sample. However, we can use probability theory and our assumptions about our sample to check the probability of different hypotheses, given our data.

A hypothesis is a claim about a population which can be either true or false. In hypothesis testing, generally we are interested in comparing two competing hypotheses:

* The alternative hypothesis is your claim. For example, "the population mean of group A is greater than the population mean of group B."
* The null hypothesis is the opposite, generally that there is no effect or no difference. For example, "the population mean of group A is equal to the population mean of group B." This means any visible difference in our results is the result of random chance, not a true effect.

In hypothesis testing, rather than try to come up with a statistical interpretation of the alternative hypothesis, which may be difficult to specify, we measure the probability of our data under the null hypothesis and try to show that it is unlikely, given our results.

### Example: Showing that a Mean is Greater Than Zero

Let's say we have data about student test scores before and after taking a class. We measure the change in each student's score. We hypothesize that the change in scores for the population of all students should be, on average, greater than zero (meaning that the students learned from their class). That is our alternative hypothesis. The corresponding null hypothesis is that the change in the mean of the scores is exactly zero.

We can quantify the probability of the null hypothesis by looking at the actual mean's difference from the null hypothesis, and dividing by the standard error of the mean to get a ratio: how far away is our mean from the null hypothesis, measured in units of standard error?
$$
t = \frac{\bar x - x_0}{SEM} = \frac{\bar x - x_0}{s / \sqrt{n}}
$$
This statistic, which is often called "Student's T" can give us the probability of the data under the null hypothesis through a table lookup. In practice, however, we can use a function from the library scipy to do this test.

```python
from scipy.stats import ttest_1samp
t, p, dof = ttest_1samp(X)
```

This function computes three values:

* t is the value of the t statistic, described above
* p is the probability of the observed data under the null hypothesis.
* dof is the "degrees of freedom" used for the calculation, typically n-1 based on the same correction described in the previous section.

### Example: Showing that One Mean is Greater than Another

Now, let's say we have two groups of students, A and B. Group A takes class A while group B takes class B, but both are assessed via the same tests. We similarly compute the change in each students test score. We hypothesize that group A will improve more than group B. That is our alternative hypothesis. The corresponding null hypothesis is that the two groups improved the same amount, i.e. the means of their changes in scores will be equal.

We can perform almost the same test on the difference between the two sample means using a slightly different t statistic:
$$
t = \frac{\bar x_a - \bar x_b}{\sqrt{s_{ab}^2 (1/n_a + 1/n_b)} } \\
s_{ab}^2 = \frac{(n_a - 1)s_a^2 + (n_b - 1)s_b^2}{n_1+n_2 -2}
$$
Here, the subscripts indicate which sample the mean or variance is from. The formulas are more complex because they take into account the difference in sample size between the two groups, as well as Bessel's correction.

Similarly, we can compute the values for this test using scipy:

```python
from scipy.stats import ttest_ind
t, p, dof = ttest_ind(X)
```

The interpretation of these values is similar to the one-sample T-test.

### Conclusion

This chapter has provided a brief introduction to the topic of statistical inference. The point of studying this topic in a data science class is to give you a taste of the kinds of questions that statisticians think about, not to give a full training in statistics. Hypothesis testing in particular is a complicated topic which is highly relevant to every scientific discipline. Future scientists should make sure to take a course (such as Stonehill's MTH 225) dedicated to understanding it.