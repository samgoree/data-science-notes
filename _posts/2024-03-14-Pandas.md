# Chapter 6: Pandas

In previous chapters, we have explored methods for data which is all of a uniform data type, either strings, integers or floats. In this chapter, we will introduce another key data science library: Pandas, which turns Python into a direct substitute for spreadsheet software like Microsoft Excel.

## Importing Pandas

Like Numpy and Matplotlib, Pandas is an external library that is installed alongside the anaconda distribution of Python. Pandas was developed by Wes McKinney and is short for **Pan**el **D**ata **A**nalysi**s**.

Like Numpy, Pandas adds new data structures to Python for working with data organized into tables. Most real-world datasets used by analysts are *tabular* (meaning they are stored in tables), so this library is very useful.

Also like Numpy, we have to import Pandas before using it. Traditionally, we import pandas using the following line of code:

```python
import pandas as pd
```

In this chapter, assume that we have run the following code cell at the start of our notebook:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

## The Pandas `Series`

The first data structure added by Pandas is called a `Series`. A series is like a fusion between a Numpy array and a Python dictionary.

### Creating Series from other data types

We can create a Series from a list or array with the `pd.Series(lst)` method (notice the capitalization! `Series`, not `series`):

```python
srs = pd.Series([2,3,5])
srs
```

Out:

```
0    2
1    3
2    5
dtype: int64
```

In this printout, there are two numbers on each line. The first is the index, which by default works just like a list or array index. The second are the actual values that we added to the series. At the bottom it tells us the data type, int64, or 64-bit integer.

We can access the index itself using `.index` and converting the result to a list:

````
list(srs.index)
````

Out: `[0, 1, 2]`. 

We can access the values in a series using the index values much like a list or array:

```
data = np.arange(2,12)
print(data[2])
srs = pd.Series(data)
print(srs[2])
```

Out:

```
4
4
```

In this example, notice that we are creating a series from a Numpy array, created by the arange function. If you recall, this function generates an array of integers from the first value up to, but not including, the second value: `array([2, 3, 4, 5, 6, 7, 8, 9, 10, 11])`. The series preserves this indexing.

Series also work like dictionaries because we can add new index values non-sequentially:

```
srs = pd.Series([2,3,5])
srs[10] = 20
srs
```

Out:

```
0      2
1      3
2      5
10    20
dtype: int64
```

Notice that the index 10 is included without causing an index error.

Series can contain other data types as well. For example, strings:

```python
sentence = "I love pandas data types"
pd.Series(sentence.split(' '))
```

Out:

```
0         I
1      love
2    pandas
3      data
4     types
dtype: object
```

Recall that the split method splits a string into a list of substrings around the given delimiter, in this case the space character `' '`. Converting the resulting list into a series assigns integer indices to each of the elements.

### Series with custom index

When creating a Pandas series, the default index is a sequence of integers starting from 0 and ascending. But we can use any list or array as an index. For example:

```python
names = ['Alice', 'Bob', 'Carol', 'Dave']
ages = [65, 62, 54, 50]
name_age_srs = pd.Series(ages, index=names)
name_age_srs
```

Out:

```
Alice    65
Bob      62
Carol    54
Dave     50
dtype: int64
```

Now, in the place of the index numbers, there are index strings. Notice that the dtype is still int64, because the values are integers.

We can index this series using the index values as strings.

```python
name_age_srs['Bob']
```

Out: `62` because the age value associated with the index 'Bob' is 62.

### Slicing Series

Recall that Python lists and Numpy arrays could be sliced using a `:` in their index. This works for series as well.

```
number_srs = pd.Series([2,4,6,8,10,12,14,16])
print(number_srs[2:5])
```

Out:

``` 
2     6
3     8
4    10
dtype: int64
```

```
names = ['Alice', 'Bob', 'Carol', 'Dave']
ages = [65, 62, 54, 50]
name_age_srs = pd.Series(ages, index=names)
name_age_srs['Bob':'Dave']
```

Out:

```
Bob      62
Carol    54
Dave     50
dtype: int64
```

### Masking Series

We can also use Numpy-style boolean masks on Pandas series. These work exactly the same way. For example, using the mod and equality operators:

```
number_srs % 3 == 0
```

Out:

```
0    False
1    False
2     True
3    False
4    False
5     True
6    False
7    False
dtype: bool
```

These two operators give us the values in number_srs which are divisible by 3. When we use these as an index, we only get the rows where the value was true:

```python
divisible_by_three = number_srs[number_srs % 3 == 0]
divisible_by_three
```

Out:

```
2     6
5    12
dtype: int64
```

Notice that the index column is preserved! To get the value 6, you would have to do `divisible_by_three[2]` or `divisible_by_three[divisible_by_three.index[0]]`. This is different than numpy arrays.

## The Pandas DataFrame

Dataframes are Pandas' model of a data table, like you might be familiar with from spreadsheet software. A dataframe is kind of like a two dimensional series. The outer series is indexed by the column names, while the inner series are all indexed by the row number.

For example, let's turn this table into a dataframe:

|      | column_1 | column_2 |
| ---- | -------- | -------- |
| 0    | A        | 2        |
| 1    | B        | 4        |
| 2    | C        | 6        |

Here, 0,1,2 is the index and column_1 and column_2 are the column names.

In Python, that would look like this:

```python
df = pd.DataFrame({
   'column_1': ['A', 'B', 'C'],
   'column_2': [2,4,6]
})
df
```

Out:

```
  column_1  column_2
0        A         2
1        B         4
2        C         6
```

### Ways to create DataFrames

After importing Pandas (`import pandas as pd`), we can create dataframes in many different ways. Most involve the pd.DataFrame() function.

* Create a dataframe from a dictionary of lists:

  ```python
  data = {
  	'col1': [1,2,3],
  	'col2': [2,3,4],
  	'col3': ['alice', 'bob', 'carol']
  }
  df = pd.DataFrame(data)
  df
  ```

  Out:

  ```
     col1  col2   col3
  0     1     2  alice
  1     2     3    bob
  2     3     4  carol
  ```

* Create a dataframe from a list of dictionaries:

  ```python
  data = []
  data.append({'col1': 1, 'col2': 2, 'col3': 'alice'})
  data.append({'col1': 2, 'col2': 4, 'col3': 'bob'})
  data.append({'col1': 3, 'col2': 6, 'col3': 'carol'})
  df = pd.DataFrame(data)
  df
  ```

  Out:

  ```
     col1  col2   col3
  0     1     2  alice
  1     2     3    bob
  2     3     4  carol
  ```

* Create a dataframe from a numpy array:

  ```python
  arr = np.arange(12).reshape(3,4)
  df = pd.DataFrame(arr)
  df
  ```

  Out:

  ```
     0  1   2   3
  0  0  1   2   3
  1  4  5   6   7
  2  8  9  10  11
  ```

  Recall that `arange(n)` generates a series of integers from 0 to n-1 and reshape changes them from an array of shape (12,) to shape (3,4). Notice that since column names were not provided, they were set to integers by default to match with the numpy indexing. We can set the column and index values with arguments:

  ```python
  arr = np.arange(12).reshape(3,4)
  df = pd.DataFrame(arr, columns=('a', 'b', 'c', 'd'), index=(2,4,6))
  df
  ```

  Out:

  ```
     a  b   c   d
  2  0  1   2   3
  4  4  5   6   7
  6  8  9  10  11
  ```

## !!! Saving and Loading DataFrames from Files

In addition to creating dataframes using `pd.DataFrame`, we can programmatically read data from files using Pandas.

### Opening CSV files with Pandas

Recall that a csv file is a "comma separated values" file that uses commas as delimiters between items in a row, and newline characters as delimiters between rows. Let's say we have a CSV file `students.csv` that looks like this:

```
name,age,grade
Sam,20,85
Emma,19,95
Josh,21,88
Natasha,22,85
Adam,23,80
```

We could read this data into a Pandas DataFrame using the `read_csv` function:

```python
df = pd.read_csv('students.csv')
df
```

Out:

```
      name  age  grade
0      Sam   20     85
1     Emma   19     95
2     Josh   21     88
3  Natasha   22     85
4     Adam   23     80
```

Pandas automatically parsed the data into columns and recognized the first column as headers. There are several options we can use to customize this process, but most CSV files will work just as-is. If it doesn't work to start, some common options: `pd.read_csv(path, sep=',', header='infer', names=None, index_col=None, skiprows=None)`

* `sep` specifies the delimiter character. By default, it is a comma.
* `header` specifies which row number contains column names. By default, Pandas will look for the first row with enough entries and use that line.
* `names` if you want to override the inferred column names, you can specify them in a list using this argument.
* `index_col` specifies a column to be used for the dataframe index. By default, it will use ascending integers for the index and keep all rows as columns.
* `skiprows` specifies how many rows to skip at the top of the file. This is useful if you are working with files that have some kind of description on the first line. Default is 0.

### Opening Excel Files with Pandas

 An excel .xls or .xlsx file is a compressed file, we can't read it in a text editor like a CSV file, but Pandas has a function to open it.

```python
df = pd.read_excel('students.xlsx')
```

Similarly to `read_csv`, this function has a huge number of optional arguments. Some common options: `pd.read_excel(path, sheet_name=0, header=0, names=None, index_col=None, skiprows=None)`

* `sheet_name` is the name for the sheet of the excel spreadsheet to load. Since pandas only handles two dimensional data, we can only load one sheet per dataframe. If you pass in a list of sheet names here, you will get a dictionary mapping sheet names to dataframes.

The rest of the common arguments are identical to `read_csv`:

* `header` specifies which row number contains column names. By default, Pandas will look for the first row with enough entries and use that line.
* `names` if you want to override the inferred column names, you can specify them in a list using this argument.
* `index_col` specifies a column to be used for the dataframe index. By default, it will use ascending integers for the index and keep all rows as columns.
* `skiprows` specifies how many rows to skip at the top of the file. This is useful if you are working with files that have some kind of description on the first line. Default is 0.

## Indexing DataFrames

For the following examples, assume we have this dataframe:

```python
df = pd.read_csv('students.csv')
df
```

Out:

```
      name  age  grade
0      Sam   20     85
1     Emma   19     95
2     Josh   21     88
3  Natasha   22     85
4     Adam   23     80
```

Since a dataframe is a series of series, we index it much the same way we index a series.

```python
df['name']
```

Out:

```
0        Sam
1       Emma
2       Josh
3    Natasha
4       Adam
Name: name, dtype: object
```

The result is a series containing the values from the name column, indexed by the dataframe index.

If we want a specific element, we can simply index the resulting series:

```python
names = df['name']
names[2]
```

Out: `'Josh'`

Or more concisely:

```python
df['name'][2]
```

Out: `'Josh'`

We use two sets of brackets because we are implicitly using the index operation twice. The first set (`['name']`) gets the name column and the second (`[2]`) gets the element of the name column at index 2.

### .loc Indexing and Slicing

In addition to basic indexing using just `[]`, Dataframes also support a second mode: `.loc[]` indexing (I pronounce this "dot lohk" as loc is short for locate, I'm not sure if there is an official pronunciation), which allows us to index the dataframe like a numpy array. For example:

```python
df.loc[2, 'name']
```

Out: `Josh`

Notice that the two indices are reversed: we use the same style of indexing as numpy arrays where we do the row, then the column.

<details><summary>How `.loc` works</summary>Pandas is implemented using Numpy under the hood. Each dataframe has an internal array it uses to store all the data in the dataframe, and the internal column Series only access parts of that dataframe. When .loc is used, it translates a variety of different indexing operations into numpy indexing directly, instead of going through the component Series.</details>

This indexing mode also allows us to use slicing, which was not possible with basic indexing. Note that this slicing works differently than numpy array slicing! With numpy arrays a slice `[i:j]` would yield `i` up to but not including `j`. With `.loc` slicing, `j` is included in the slice. For example:

```python
df.loc[2:4, 'name':'grade']
```

Out:

```
      name  age  grade
2     Josh   21     88
3  Natasha   22     85
4     Adam   23     80
```

Notice how row 4 and column 'grade' are included, even though they are the end of the slice.

### Masking

Boolean masking is very useful for dataframes, as it allows us to filter the dataframe based on the value of one column. For example:

```python
mask = df['grade'] > 85
smaller_df = df.loc[mask]
smaller_df
```

Out:

```
   name  age  grade
1  Emma   19     95
2  Josh   21     88
```

This is a useful technique when we want to look at a subset of a large dataset. We can now do all of our analysis on `smaller_df`. For example, if we wanted to know the number of students (out of these five) whose grades were above an 85, we could use the len function on our new dataframe:

```python
len(smaller_df)
```

Out: `2`. 

## Pandas Summary Statistics

Since pandas is backed by numpy, there are pandas functions which will compute all of the same summary statistics included in numpy on Pandas series and dataframe objects. 

For all of the following examples, assume we have a series in memory with the following values:

```python
srs
```

Out:

```
Alice    65
Bob      62
Carol    54
Dave     50
dtype: int64
```

And a dataframe:

```python
df
```

Out:

```
      name  age  grade
0      Sam   20     85
1     Emma   19     95
2     Josh   21     88
3  Natasha   22     85
4     Adam   23     80
```

### Summary Statistic Functions

#### Min, max:

```
srs.min(), srs.max()
```

Out: `(50, 65)`

We can also use these on series that are part of a dataframe:

```python
df['age'].min(), df['age'].max()
```

Out: `(19,23)`

If we call min/max on a dataframe itself, it may not work the way we expect:

```python
df.min()
```

Out:

```
name     Adam
age        19
grade      80
dtype: object
```

Since our dataframe has a string column, pandas will find the minimum string, which in Python is the string that comes alphabetically first. Notice that the min/max of a dataframe is a series: the index is the column names and the values are the minimum values of each column. The datatype is object, because there is a mix of strings and numbers in the value column.

If we don't want to mix datatypes, we can specify that we only want the minimum of the numeric columns:

```python
df.min(numeric_only=True)
```

Out:

```
age      19
grade    80
dtype: int64
```

#### Sum, length:

We can get the sum and length similar to a Python list:

```python
sum(srs), len(srs)
```

Out: `(231, 4)`

We can also get them similar to a numpy array:

```python
srs.sum(), srs.shape[0]
```

Out: `(231, 4)`

And these work for dataframes as well, though text columns once again behave strangely:

```python
print(df.sum())
print(len(df))
print(df.shape)
```

Out:

```
name     SamEmmaJoshNatashaAdam
age                         105
grade                       433
dtype: object
5
(5, 3)
```

The sum function concatenates the strings in the name column. Odds are we don't want this, so we should add the same `numeric_only=True` argument, or just choose a specific column to sum. 

Also notice the shape of the dataframe is ordered just like a numpy array: # of rows, # of columns, so `df.shape[0]` is equivalent to `len(df)`.

#### Mean/Median/Variance/Standard Deviation:

```python
srs.mean(), srs.median(), srs.var(), srs.std()
```

Out: `(57.75, 58.0, 48.25, 6.946221994724902)`

Similarly, we can take the mean of a specific dataframe column by indexing so we only get one series:

```python
df['age'].mean(), df['age'].median(), df['age'].var(), df['age'].std()
```

Out: `(21.0, 21.0, 2.5, 1.5811388300841898)`

With a dataframe, it doesn't make sense to take the mean of text columns. We can take the mean of every numeric column in the dataframe by calling df.mean with the same `numeric_only` argument:

```python
df.mean(numeric_only=True)
```

Out:

```
age      21.0
grade    86.6
dtype: float64
```

And similar for the others.

That's it for our first Pandas chapter! In chapter {{advanced_pandas}}, we will go into some of the advanced functionality that makes pandas an invaluable tool for data science.