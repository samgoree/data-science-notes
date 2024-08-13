# Chapter 7: Text Analysis

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

### String Arithmetic

Recall that we can use arithmetic on strings:

```python
a = 'hello'
b = 'world'
a + b
```

Out: `helloworld`

If we wanted a space in between the two words, we could use a space literal character in between them:

```python
a = 'hello'
b = 'world'
a + ' ' + b
```

Out: `hello world`

```python
a = 'hello'
a * 5
```

Out: `hellohellohellohellohello`

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

  **This is particularly useful in combination with the `len()` function to count words and/or lines!**

* `count(substring)`: returns the number of times the given substring occurs in the string.

  ```python
  n_times = my_string.count('l')
  n_times
  ```

  Out: `3`, as the letter `l` occurs three times.

* `combined_string = delimiter.join(list_of_strings)`: Join a list of strings into one string around a delimiter. For example:

  ```python
  list_of_strings = ['one', 'two', 'three', 'four', 'five']
  combined_string = ' '.join(list_of_strings)
  combined_string
  ```

  Out: `'one two three four five'`. Notice that the strings were joined with spaces in between them because the string we called `.join` on contained only a single space. If we called `.join` on a more complex string, it would join on that:

  ```python
  list_of_strings = ['one', 'two', 'three', 'four', 'five']
  combined_string = '~-~-~'.join(list_of_strings)
  combined_string
  ```

  Out:`'one~-~-~two~-~-~three~-~-~four~-~-~five'`

  Note that `.join` is the inverse of `.split`:

  ```python
  csv = 'one,two,three'
  words = csv.split(',')
  new_csv = ','.join(words)
  csv, new_csv
  ```

  Out: `('one,two,three', 'one,two,three')`. The two are identical.

## Loading Data from Text Files

We can open any file and read its text as a string using the following syntax:

```python
with open('fname.txt') as f:
    contents = f.read()
```

Let's break that down:

* `with something as f:` means "run `something` and save it as the variable `f`, but delete the variable when you're done."
* `open('fname.txt')` means open the file `fname.txt` on your computer.
* `contents = f.read()` means read the text of the file and save it as a string in the variable `contents`.

For example, if we have the text file `demo.txt` with the following content in the same directory as our notebook:

```
This is a text file.
It contains several lines.
This last line contains a 	tab character.
```

Then we run this code:

```python
with open('demo.txt') as f:
    contents = f.read()
contents
```

Out: `'This is a text file.\nIt contains several lines.\nThis last line contains a \ttab character.'`

Notice that the file was read in as one big string. The newline characters were converted to `\n` and the tab was converted to `\t`.

This approach works when we want the entire file as a single string (for instance, to count words). If we want to read each line separately, we can do that using the readlines method:

```python
with open('demo.txt') as f:
    contents = f.readlines()
contents
```

Out:

```
['This is a text file.',
 'It contains several lines.',
 'This last line contains a \ttab character.']
```

Or, alternatively, by splitting the string on `\n`:

```python
with open('demo.txt') as f:
    contents = f.read().split('\n')
contents
```

Out:

```
['This is a text file.',
 'It contains several lines.',
 'This last line contains a \ttab character.']
```

Notice we now have a list of strings, instead of one string.
