# Chapter 5: Random Numbers and Summary Statistics

This page continues our study of Numpy through its random module which allows us to generate random numbers. Along the way, we also discuss a variety of summary statistics that are useful for understanding data.

## Pseudorandom Numbers

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
