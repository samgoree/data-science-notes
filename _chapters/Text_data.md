---
layout: post
title:  "Chapter 7: Text Analysis"
number: 7
---

One area where data science diverges from data analytics is its approach to text data. While data analysts typically view text as categorical, data scientists view text as sequence data. This chapter describes Python-based methods of text analysis with and without Pandas dataframes.

## Strings

Recall that in Python, we call text "strings" as they are strings of characters. We can index a string like a list to get specific characters out of it. For example:

```python
my_string = "hello!"
my_string[1]
```

Out: `e` 

If we use a negative number as an index, it wraps around:

```python
my_string = "hello!"
my_string[-1]
```

Out: `!` Notice that every character, including whitespace (' ', '\n' or '\t') and punctuation, counts as an index in the string.

We can also slice strings:

```python
my_string = "hello world!"
my_string[1:10]
```

Out: `ello worl`

Negative indices work here too:

```python
my_string = "hello world!"
my_string[1:-2]
```

Out: `ello worl`

If we omit the first index, it will go from the start of the string.

```python
my_string[:2]
```

Out: `he`

If we omit the second index, it will go to the end:

```python
my_string[-2:]
```

Out: `d!`

## Modifying Strings

Strings are immutable: if we try to edit their contents, we get an error:

```python
a = 'abc'
a[2] = 'd' # this doesn't work
```

Out: `TypeError: 'str' object does not support item assignment`

If we want to change something about a string, we have to create a new string:

```python
a = 'abc'
a = a[:2] 
a += 'd' # this does!
a
```

Out: `abd`

### String Methods

There are several methods which are useful for manipulating strings. 

For the following examples, assume we have the following string: `my_string = '    Hello world!    '`. Notice the space around the text.

* `.lower()`: convert the string to lowercase:

  ```python
  new_string = my_string.lower()
  new_string
  ```

  Out: `    hello world!    `

* `.upper()`: convert the string to UPPERCASE:

  ```python
  new_string = my_string.upper()
  new_string
  ```

  Out: `    HELLO WORLD!    `

* `.title()`: convert the string to Title Case:

  ```python
  new_string = my_string.title()
  new_string
  ```

  Out: `    Hello World!    `

* `strip(chars=None)`: by default strips whitespace from the ends of the string. If a string argument is provided, it will strip the characters in that string instead.

  ``` python
  new_string = my_string.strip()
  new_string
  ```

  Out: `Hello world!`

* `split(separator=None)`: by default, splits the string into a list of substrings around whitespace characters. If a string argument is provided, it will split around that string instead.

  ```python
  words = my_string.split()
  words
  ```

  Out: `['Hello', 'world!']`

  This is particularly useful in combination with the len()

* `count(substring)`: returns the number of times the given substring occurs in the string.

  ```python
  n_times = my_string.count('l')
  n_times
  ```

  Out: `3`, as the letter `l` occurs three times.

### Loading Data from Text Files

We can open any file and read its text as a string using the following syntax:

