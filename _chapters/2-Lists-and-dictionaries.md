---
layout: post
title:  "Chapter 2: Lists and Dictionaries"
number: 2
---

This page describes a key Python data structure: lists. After exploring lists, we will revisit strings and introduce another data structure: dictionaries.

## Lists

The first data structure we're going to use in Python is a list. Lists are declared using square brackets [] with the elements separated by commas.

```python
my_list = [1,3,5]
```

Notice that they are treated just like any other data type and assigned to a variable.

<details>
    <summary>A note about bracket symbols</summary>
    The names for [], (), {},< > and "" vary around the world. In these notes I use the following terms for them:
    <ul>
    <li>() are called "parentheses"</li>
    <li>[] are called "brackets" or "square brackets"</li>
    <li>{} are called "braces" or "curly braces"</li>
    <li><> are called "angle brackets"</li>
    <li>"" are called "quotes" or "double quotes"</li>
    </ul>
</details>

### !! Indexing Lists !!

Each element of the list (the integers 1, 3 and 5 in the list above) is associated with a specific *index value*. For historical reasons, lists are *zero-indexed*, which means the first element has an index of 0 and the last element has an index of the length - 1.

| Index | Element |
| ----- | ------- |
| 0     | 1       |
| 1     | 3       |
| 2     | 5       |

We can access the elements of the list using *square bracket notation*:

```python
my_list = [1,3,5]
my_list[1]
```

Out: `3` because the element in position 1 is 3.

Square bracket notation also works on strings:

```
name = "Samuel Goree"
initials = name[0] + name[7]
initials
```

Out: `SG`

There are exclamation points around this section's header because indexing lists is very important in data science. While we won't be using Python lists for the entire class, we will be using other data structures which share this notation.

### List Errors

There are several error messages that come up when working with lists and strings:

* `SyntaxError`: This usually means there is some problem with your parentheses, brackets or variable names.

* `IndexError`: This usually means the index you used was out of range. A common cause is if you forget that indices only go to the length - 1.
* `TypeError`: This error means that something in your code is not the type you wanted it to be. For example: `lst['a']` will cause a type error for every list because you can't index lists with strings (that's only for dictionaries!). `lst + 2` will cause a type error because you can't add lists and integers together (that's only for numpy arrays!).

### Creating Lists With the `range` Function

In addition to declaring *list literals* where we write down all of the elements using square brackets, we can create lists of ascending integers using the `range` function. But we have to do a little work to use it. By default the range function produces a range object:

```
range(5)
```

Out: `range(0,5)`

But we can convert it to a list with the `list` function:

```
list(range(5))
```

Out: `[0, 1, 2, 3, 4]`

Notice that range(5) starts at 0 and ends at 4, meaning it is a list of length 5.

Range has different behavior based on how many arguments we pass into it. If we call range with two values, it sets the start and end numbers:

```
list(range(3,8))
```

Out: `[3, 4, 5, 6, 7]`

Again, `range` starts at the start and ends one before the end.

If we use a third argument, it controls the step size.

```
list(range(10,20,2))
```

Out: `[10, 12, 14, 16, 18]`

In this case, the range goes from 10 up to but not including 20 and counts up by 2 each time.

### Modifying Lists

We can assign to the elements of lists:

```
lst = [1,3,5]
lst[1] = 2
lst
```

Out: `[1, 2, 5]`. Notice the 3 has changed to a 2 because we assigned to it.

Note that this does not work on strings.

### Slicing

We can "slice" lists and strings by providing two indices separated by a colon. Slicing yields the sublist starting from the first index and running up to, but not including, the second index. For example:

 ```
 lst = [8,6,7,5,3,0,9]
 lst[2:4]
 ```

Out: `[7, 5]`

To better understand, think of cutting up a table with scissors. We are making little snips before 2 and before 4, yielding the values [7,5]

| Index | Element |
| ----- | ------- |
| 0     | 8       |
| 1     | 6       |
| **2** | **7**   |
| **3** | **5**   |
| 4     | 3       |
| 5     | 0       |
| 6     | 9       |

Notice that you can predict the length of the slice. It will always have length equal to the end index minus the start index. In this case, 4 - 2 = 2

If we omit one side of a slice, it indicates the start or end of the list. For example:

```
lst = [8,6,7,5,3,0,9]
lst[:4]
```

Out: `[8, 6, 7, 5]`

```
lst = [8,6,7,5,3,0,9]
lst[4:]
```

Out: `[3, 0, 9]`. Notice how these two slices, one from the start to 4 and one from 4 to the end, contain all of the values originally in the list.

### Nested Lists

We can put lists inside of lists. When we do that, we index the outer list first, then we can index the inner list. For example,

```python
nested_list = [[1,2,3], [4,5,6], [7,8,9]]
sublist = nested_list[1]
print(nested_lst, sublist[2], nested_list[1][2])
```

Out: `[[1, 2, 3], [4, 5, 6], [7, 8, 9]] 6 6`

The nested list contains three sublists. We get the sublist at index position 1 using square bracket notation and save it as another variable, `sublist`. The value at index position 2 in `sublist` is 6. Equivalently, we can use `nested_lst[1][2]` to get 6 because Python resolves the first square bracket notation, than the second one.

### List Arithmetic

Much like strings, lists support two arithmetic expressions: concatenation (+) and repetition (*).

Concatenating two lists combines them into a single list:

```
lst1 = [1,2,3]
lst2 = [4,5,6]
lst1 + lst2
```

Out: `[1, 2, 3, 4, 5, 6]`

Multiplying a list by an integer n repeats its elements n times

```
lst = [1,2,3]
lst * 4
```

Out: `[1, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3]`

### Calling Functions on Lists

#### len()

Just like we can call functions on strings and integers, we can call functions on lists. One of these functions we already know:

```python
lst = [2,4,6,8]
length = len(lst)
length
```

Out: `4`, because there are four elements in the list.

#### sum()

Another useful function for data science is the `sum` function, which adds up all the numbers in a list:

```
lst = [2,4,6,8]
total = sum(lst)
total
```

Out: `20` because 2 + 4 + 6 + 8 is 20.

#### sorted()

Functions can also return lists. For example, the sorted() function takes a list as an argument and returns a copy of that list, sorted from smallest to largest.

```
lst1 = [1,3,6,2,4,7,5]
lst2 = sorted(lst1)
print(lst1, lst2)
```

Out: `[1, 3, 8, 2, 4, 9, 5] [1, 2, 3, 4, 5, 6, 7]`Notice that in lst1, the order is preserved while lst2 is sorted.

#### reversed()

A function that returns a reversed version of a list.

```
lst1 = [1,3,5,2,4]
lst2 = reversed(lst1)
print(lst1, lst2)
```

Out: `[1,3,5,2,4] [4,2,5,3,1]`

## List Methods

Lists also have another kind of function associated with them, called a *method*. Methods are just like functions, except they are called using the dot operator `list_name.method_name()`. The "dot" indicates that the method `method_name` is operating on the list `list_name`. While functions and methods work the same way, there is an idiomatic difference: functions generally don't modify their arguments, but methods often modify the thing they are operating on (e.g. `method_name` might change the internal data of `list_name`).

#### .append()

A perfect example of this behavior is the append method, which appends a new value to the end of a list.

```
lst = [1,2,3]
lst.append(4)
lst
```

Out: `[1, 2, 3, 4]`. Notice how we didn't create a new list, we modified `lst`.

#### .sort()

Another method is the .sort() method. This doesn't return a new list, instead it sorts a list *in-place*, overwriting the original list.

```
lst = [1,3,6,2,4,7,5]
lst.sort()
lst
```

Out: `[1, 2, 3, 4, 5, 6, 7]`. Notice `lst1` has changed and is now sorted.

The .sort method does not return a value. That means if you try to use it like sorted(), it won't work.

```
lst1 = [1,3,6,2,4,7,5]
lst2 = lst1.sort()
lst2
```

If you run this code, the output is blank. If you run `print(lst2)` it will show `None`. That means lst2 doesn't actually contain a list. None is Python's null type, it has no type and no data. If a method returns nothing, it literally returns `None`.

#### .reverse()

Just like .sort, .reverse reverses a list in-place.

```
lst = [1,2,3]
lst.reverse()
lst
```

Out: `[3,2,1]`

## Dictionaries

In addition to lists, Python has another central data structure called a dictionary. Dictionaries are similar to lists in that they contain elements, but the way we access these elements is different.

To review: lists are declared with square brackets. They are indexed by integer positions. Every list starts from index 0 and goes up:

```
my_list = [1,3,5]
my_list[2]
```

Out: `5`

| Index | Element |
| ----- | ------- |
| 0     | 1       |
| 1     | 3       |
| 2     | 5       |

Dictionaries, on the other hand, are declared with curly braces. They contain key-value pairs and are indexed using keys, which do not have to be integers.

````
my_dict = {'a':1, 'b':3, 'c':5}
my_dict['c']
````

Out: `5` 

This dictionary contains three key-value pairs. They are separated by commas and the key and value are separated by colons (:). The keys are 'a', 'b' and 'c' and the values are 1, 3 and 5. We say that each key is *mapped* to the corresponding value.

| Key  | Value |
| ---- | ----- |
| 'a'  | 1     |
| 'b'  | 3     |
| 'c'  | 5     |

Dictionaries are *unordered* which means that there is no way to get "the first element." Indexing is done using the keys. For that reason, the keys have to be unique, but the values can contain duplicates.

The best analogy for dictionaries is, well, the dictionary. A dictionary is a big book that maps words to their definitions. The words are the keys and the definitions are the values.

```
animals = {
	'horse': 'A large, domesticated hoofed mammal',
	'cow': 'A mature female cattle',
	'donkey': 'A domesticated hoofed animal with long ears and a loud bray'
}
```

It can also be helpful to think of dictionaries like houses on a street. Every house has a unique street number, that tells you where the house is along the street. People live in the houses, but sometimes they move in or out. In this analogy, the addresses are the keys and the people are the values.

```
maple_street = {
	10: 'Sam',
	11: 'Erica',
	12: 'John',
	14: 'Anna',
	15: None,
	16: 'Trevor',
	17: 'Kevin'
}
maple_street[14]
```

Out: `'Anna'`

### Modifying Dictionaries

We can assign to the elements of dictionaries just like lists, except using the key.

```
dct = {2:4, 3:6, 4:8, 5:9}
dct[5] = 10
dct
```

Out: `{2: 4, 3: 6, 4: 8, 5: 10}`

We can use similar syntax to add new elements to dictionaries:

```
dct = {2:4, 3:6, 4:8, 5:10}
dct[6] = 12
dct[6]
```

Out: `12`

### Converting Dictionaries to Lists

If we want to get a list of elements out of a dictionary, we need to impose an order on either the keys or the values, using the `.keys()` or `.values()` methods by sorting them:

```
dct = {2:8, 3:7, 4:6, 5:5}
keys_list = sorted(dct.keys())
keys_list
```

Out: `[2, 3, 4, 5]`. This approach (sorting the dictionary) returns a sorted list ordered from smallest to largest, dropping the values. Equivalently we can  get the values sorted from smallest to largest with the `.values()` method:

```
dct = {2:8, 3:7, 4:6, 5:5}
values_list = sorted(dct.values())
values_list
```

Out: `[5, 6, 7, 8]`

If we want both the keys and values paired up, we can use another method: `.items()`

```
dct = {2:8, 3:7, 4:6, 5:5}
items_list = sorted(dct.items())
items_list
```

Out: `[(2, 8), (3, 7), (4, 6), (5, 5)]`

Now, instead of just having the keys and values alone in a list, we have a list of *tuples*, each of which contains two elements which used to be key-value pairs.

### Tuples

This is our first encounter with tuples. Tuples are another data structure, more like lists than dictionaries. They have two key differences from lists:

1. Tuples are notated with parentheses () instead of brackets [].
2. Tuples are *immutable*: they do not have an append method and we cannot assign to their elements.

For example:

```
tup = (1,3,5)
```

This code declares a tuple containing three elements. We can do concatenation and repetition on tuples, much like lists:

```
tup1 = (1,3,5)
tup2 = (2,4,6)
tup1 + tup2, tup1 * 2
```

Out: `(1, 3, 5, 2, 4, 6), (1, 3, 5, 1, 3, 5)`

You may have noticed that parentheses have several different uses in Python:

1. Parentheses determine the order of operations: things inside parentheses happen before things outside of them. e.g. `(1 + 1) * 3` evaluates to `6`.
2. Parentheses are used when calling functions: e.g. `print('Hello')`
3. Parentheses are used to declare tuples e.g. `('a', 'b')` is a tuple containing the strings "a" and "b."

To distinguish tuples from order-of-operations parentheses, tuples must always contain a comma. So single element tuples are declared with a comma at the end, e.g. `(2,)`.

While tuples are not as common as lists, they sometimes come up in data science, so it is good to be aware of them.