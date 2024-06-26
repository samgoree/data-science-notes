# Chapter 5: Random Numbers and Summary Statistics

This page continues our study of Numpy through its summary statistic functions and random number module.

## Summary Statistics

Imagine you're working in a science lab and collected data representing the heights of bean plants in inches. We enter it into a numpy array:

```python
data = np.array(
    [ 3.174, 17.66 , 15.531, 7.161, 9.449, 7.27, 19.099, 14.031, 14.534, 17.006, 15.542, 12.625, 6.113, 17.634, 11.902, 18.866, 14.264, 15.684, 12.615, 10.471, 14.853, 9.792, 10.204, 20.47, 10.678, 13.397, 24.104, 5.041, 10.467, 18.692, 16.722, 7.283, 5.909, 10.458, 20.511, 13.148, 13.946, 5.642, 12.778, 15.609, 14.553, 16.32, 16.709, 8.162,  5.917, 15.328, 14.09, 15.541, 19.338, 16.609, 14.26, 13.53, 4.889, 13.831, 19.539, 22.351, 12.885, 7.835, 18.078, 17.391, 16.591, 13.072, 12.516, 22.028, 7.276, 20.009,  7.48, 2.46, 18.017, 18.938,  7.135, 22.456, 12.247, 13.428, 22.245, 7.203, 14.993, 15.56, 14.696,  9.598, 9.69, 19.526, 16.333, 8.904, 12.372, 13.002, 7.857, 2.03, 13.767, 13.401, 9.123, 7.19, 16.523, 10.066, 11.733, 21.604, 13.691, 5.275, 9.158, 14.356])
```

If your supervisor asked you: "how are the bean plants growing?" You *could* just read out all of the numbers. But that would probably overload and confuse your supervisor. What might be a better answer?

* There are 100 of them (the *count* or *length*)
* The bean plants range from 2.03 to 24.104 inches (the *range*)
* Half of them are above 13.6105 inches, and half of them are below (the *median*)
* In total, we have grown 1321 inches of bean plant (the *sum*)
* Which means 13.21 inches per bean plant (the *mean*)
* The bean plants vary quite a bit from that average, often more than 5 inches higher or lower (the *standard deviation*)

We call these descriptions *summary statistics* because they summarize lots of data into simple, easy-to-interpret values.

<details><summary>A note on the word "statistic"</summary>While the academic discipline of statistics originated in the 19th century, its name and many of its key ideas go back further, to medieval Islamic and ancient Greek mathematicians. The word "statistics" comes from renaissance Italy, where the name literally means "the science of states" as in countries and their governments. It uses the same construction as other words ending in -ics, like ballistics (the "science of throwing") or cladistics (the "science of classification"). See <a href="https://www.ceeol.com/search/article-detail?id=286384">Walenty Ostasiewicz "The emergence of statistical science"</a> for more information.</details>

Numpy has functions to compute all of these summary statistics and more. For example: 

```python
np.mean(data), np.median(data), np.min(data), np.max(data)
```

Out: `(13.2104, 13.6105, 2.03, 24.104)`

Many of these functions can also be called as methods. For example:

```
data.mean(), data.min(), data.max()
```



In daily speech, we often refer to things as "random" because they are arbitrary or erratic (e.g. "I put on a random shirt today" or "you said something so random"). In data science, we do not use the word random like that. When we say something is random, we mean that it is literally chosen *at random* using a totally unpredictable process like a die roll or coin toss.

We can generate a random number using numpy's random module. For example, using `np.random.random`:

```python
import numpy as np
random_number = np.random.random()
random_number
```

When I run this code, it gives me the value `0.23318962558486067`, a randomly chosen float between 0 and 1. But if I run it again, it will give me a different value.

Computers can create random numbers using an algorithm called a pseudorandom number generator. These algorithms are only "pseudorandom" since computers are deterministic, meaning that they execute exactly the same code in exactly the same way every time. To make them seem truly random, we start them with a "random seed" which is a number chosen based on an unpredictable process. A common choice for random seed is the CPU temperature mod 1 degree or the current system time in nanoseconds mod 1 millisecond.

If we want to, we can actually set the random seed to a specific value by hand. If we do that, numpy will still seem like it's generating random numbers, but the numbers will all be exactly the same.

We can set the seed with a function `np.random.seed(seed_value)`. For example:

```python
np.random.seed(10)
not_so_random = np.random.random()
not_so_random
```

Out: `0.771320643266746`. If you run the same code, you will get the exact same output.

<details><summary>About Numpy's random number generator</summary>The specific random number generator that numpy.random uses is called the Mersenne Twister, which uses bitwise arithmetic to generate random numbers in a way which is guaranteed to be difficult to predict without the random seed. This property is important for security reasons, as random number generators are used frequently to come up with cryptographic keys for encrypting sensitive data.</details>

## Random Number Functions

There are several numpy functions 
