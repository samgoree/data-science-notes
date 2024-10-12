---
layout: post
title:  "Chapter 8: Advanced Pandas"
number: 8
---

In Chapters {{site.chapter_numpy}} and {{site.chapter_pandas}}, we introduced Boolean masking for arrays and dataframes as a way to select specific rows for analysis. In this chapter, we will expand that concept of indexing and introduce several other ways to select, group and connect dataframes.

## Fancy Indexing

In the following examples, let's say we have a dataframe:

```python
df = pd.DataFrame({
    'name': ['Sam', 'Emma', 'Josh', 'Natasha', 'Adam'],
    'age': [20,19,21,22,23],
    'grade': [85,95,88,85,80]
})
df
```

Out: 

```
	name 	age grade
0 	Sam 	20 	85
1 	Emma 	19 	95
2 	Josh 	21 	88
3 	Natasha 22 	85
4 	Adam 	23 	80
```

### Review: Pandas Indexing

Recall that we index dataframes using square brackets. If we wanted the "age" column:

```python
df['age']
```

Out:

```
0    20
1    19
2    21
3    22
4    23
Name: age, dtype: int64
```

And then we could get row 0 of the age column using two sets of brackets:

```python
df['age'][0]
```

Out: `20`. The first part gets the series, and the second square bracket gets an element of the series.

We can also slice using this approach:

```python
df['age'][1:3]
```

Out:

```
1    19
2    21
Name: age, dtype: int64
```

If we want to slice in multiple directions, for instance, get age and grade for these two students, or get any fancier than that, we need to use `.loc` syntax, which uses numpy-style (row first) indexing, with two indices in the same square brackets separated by commas:

```python
df.loc[1:3,'age':'grade']
```

Out:

```
 	age grade
1 	19 	95
2 	21 	88
3 	22 	85
```

### Masking

If we use a boolean operation on a dataframe column, we can generate a boolean mask:

```python
mask = df['grade'] > 85
mask
```

Out:

```
0    False
1     True
2     True
3    False
4    False
Name: grade, dtype: bool
```

And then we can use that to index:

```python
df[mask]
```

Out:

```python
	name 	age grade
1 	Emma 	19 	95
2 	Josh 	21 	88
```

Notice that the index of the original dataframe is preserved, so to get the first row of these results, we would have to do `[1]`, not `[0]` (or more generally `.head(1)`).

If we have two or more masks, we can combine them using bitwise operators "and" (`&`) and "or" (`|` the pipe character above the enter key). For example:

```python
mask1 = df['grade'] > 85
mask2 = df['age'] > 22
combined_mask = mask1 | mask2
df[combined_mask]
```

Out:

```
 	name 	age grade
1 	Emma 	19 	95
2 	Josh 	21 	88
4 	Adam 	23 	80
```

We can also invert a mask using the "not" operator (`~` above the tab key on your keyboard).

```
mask1 = df['grade'] > 85
mask2 = df['age'] > 22
combined_mask = mask1 & (~mask2)
df[combined_mask]
```

Out:

```
	name 	age grade
1 	Emma 	19 	95
2 	Josh 	21 	88
```

Because Emma and Josh's grades are higher than 85 and their ages are *not* greater than 22.

**Note:** These masks can be used in-line as well, without including a variable. But if you do that, make sure to use parentheses, as Python's order of operations puts binary operators *above* comparisons:

```python
mask = df['grade'] > 85 & ~df['age'] > 22
```

This code will produce an error. Instead, use this code:

```python
mask = (df['grade'] > 85) & ~(df['age'] > 22)
```

Or just split it onto multiple lines using variables like we did above.

### List indexing

We can also select nonconsecutive indices by using a list of indices.

```python
df['age'][[2,4]]
```

Out:

```
2    21
4    23
Name: age, dtype: int64
```

This selects rows 2 and 4 from the age column. Make sure you have a list within the square brackets (i.e. two sets of square brackets) otherwise you'll get an error.

Or in Numpy:

```python
arr = np.array([[1,2,3,4,5], [6,7,8,9,10]])
arr[:,[1,2]]
```

Out:

```
array([[2, 3],
       [7, 8]])
```

We can use list indexing to reorder dataframes or arrays:

```python
df['age'][[4,2]]
```

Out:

```
4    23
2    21
Name: age, dtype: int64
```

This is particularly useful in combination with `np.nonzero(arr)`, which finds the indices of all nonzero elements. E.g. if we wanted to find the mean of all the nonzero elements of an array:

```
arr = np.array([[1,0,3,0,0], [6,0,8,9,0]])
arr[np.nonzero(arr)].mean()
```

Out: `5.4`, which is the mean of 1, 3, 6, 8 and 9.

## Groupby

A common pattern in data analysis is computing summary statistics for each group in a larger dataset. Pandas has a function to speed this process along: `df.groupby('column')`. This function is very powerful, but is a little difficult to use because of its return value.

Let's say we have a dataframe with a key variable and a data variable:

```python
df = pd.DataFrame({
    'key': ['A', 'B', 'C', 'A', 'B', 'C', 'A', 'B', 'C'],
    'data': [0,5,10,5,10,15,10,15,20]
})
```

When we call groupby on this dataframe, we get a "groupby object"

```python
df.groupby('key')
```

Out: `<pandas.core.groupby.generic.DataFrameGroupBy object at 0x000002A06266E690>`

In order to actually see the groups, we need to aggregate them somehow. We can do that to the groupby object directly:

```python
df.groupby('key').sum()
```

Out:

```
 	data
key 	
A 	15
B 	30
C 	45
```

In other words, we've split the data up by value in the column `key` and then summed the elements of each group.

The most helpful way to understand this analysis is through this figure from Wes McKinney's book:

![img](../assets/images/groupby.png)

We can also use a Python structure called a *for loop* to run code on each group:

```python
for value, group in df.groupby('key'):
    print(group)
```

Out:

```
  key  data
0   A     0
3   A     5
6   A    10
  key  data
1   B     5
4   B    10
7   B    15
  key  data
2   C    10
5   C    15
8   C    20
```

This loop syntax is very general: we can do basically any data manipulation on these group dataframes.

Groupby can also be used on multiple columns if a list of column names is passed.

## Pivots

Another common pattern in data analysis is that we have "long-form data" with several key columns and a value column, and want to reshape it into "wide-form data" with one of the two keys as the column names. For example:

```python
df = pd.DataFrame({
	'Day': [1,2,3,4,5,6,1,3,4,5,6,7,1,2,3,5,6,7],
	'Associate': ['Alice', 'Alice', 'Alice', 'Alice', 'Alice', 'Alice', 
                  'Bob', 'Bob', 'Bob', 'Bob', 'Bob', 'Bob',
                  'Carol', 'Carol', 'Carol', 'Carol', 'Carol', 'Carol'],
    'Sales Total': [150, 120, 100, 105, 102, 160, 80, 85, 88, 90, 92, 100, 90, 95, 102, 108, 105, 140]
})
df
```

Out:

```
	Day	Associate Sales Total
0 	1 	Alice 	  150
1 	2 	Alice 	  120
2 	3 	Alice 	  100
3 	4 	Alice 	  105
4 	5 	Alice 	  102
5 	6 	Alice 	  160
6 	1 	Bob 	  80
7 	3 	Bob 	  85
8 	4 	Bob 	  88
9 	5 	Bob 	  90
10 	6 	Bob 	  92
11 	7 	Bob 	  100
12 	1 	Carol 	  90
13 	2 	Carol 	  95
14 	3 	Carol 	  102
15 	5 	Carol 	  108
16 	6 	Carol 	  105
17 	7 	Carol 	  140
```

This is long-form because each row is an "entry" with properties represented by columns. We can use .pivot to reshape it into wide-form data:

```python
df.pivot(columns='Associate', index='Day', values='Sales Total')
```

Out:

```
Associate 	Alice 	Bob 	Carol
Day 			
1 	        150.0 	80.0 	90.0
2 	        120.0 	NaN 	95.0
3 	        100.0 	85.0 	102.0
4 	        105.0 	88.0 	NaN
5 	        102.0 	90.0 	108.0
6 	        160.0 	92.0 	105.0
7 	        NaN 	100.0 	140.0
```

Now, the columns do not represent properties of entries, each row represents a day and each column represents an associate. Notice that missing combinations, instead of just being absent, are explicitly NaN here.

Note: Pivoting a table with duplicate keys (e.g. if there were two entries for Alice on day 1) causes an error, so aggregate your data with groupby before pivoting!

## Pandas Dates and Times

A common data type in tabular data is the "datetime" type: representing a specific date and time. Datetimes are notoriously difficult to work with: see this [list of falsehoods programmers believe about time](https://gist.github.com/timvisee/fcda9bbdff88d45cc9061606b4b923ca) for some examples of why.

Pandas has resolved most of these issues in its `Timestamp` type, which represents a date and time in a particular time zone. It is based on the [Numpy `datetime64` type](https://numpy.org/doc/stable/reference/arrays.datetime.html).

We can create Timestamps directly:

```python
pd.Timestamp(day=12, month=10, year=2024, hour=11, minute=27, second=25, tz='EST')
```

Out: `Timestamp('2024-10-12 11:27:25-0500', tz='EST')`.

### Converting a String date or time column to Timestamps

Let's say we have a column of date strings:

```python
dt_strings = pd.Series(['2017-08-01', '2017-08-15', '2017-09-01', '2017-09-15'])
```

If all of the strings are in a similar, commonplace datetime format, we can easily convert them to Timestamps using `pd.to_datetime()`:

```python
pd.to_datetime(dt_strings)
```

Out:

```
0   2017-08-01
1   2017-08-15
2   2017-09-01
3   2017-09-15
dtype: datetime64[ns]
```

Notice the dtype is datetime64. That means Pandas is storing these values as Timestamps, stored using a 64 bit integer counting the number of milliseconds between that date and 12 AM GMT on January 1st, 1970 (the ["Unix Epoch"](https://en.wikipedia.org/wiki/Unix_time)).

Sometimes, datetimes will be encoded differently in your data table. For example, they may be presented in the European Day/Month/Year format:

```python
dt_strings = pd.Series(['01/08/2017', '15/08/2017', '01/09/2017', '15/09/2017'])
```

If we just try to use `to_datetime` to parse these, we end up with wrong answers:

```
0   2017-01-08
1   2017-08-15
2   2017-01-09
3   2017-09-15
dtype: datetime64[ns]
```

It should be August 1st and September 1st, not January 8th and 9th.

To fix this problem we need to specify a *format string*. This is a string specifying where each bit of datetime information is in the string. For example, the format string `'%d/%m/%Y'` uses three directives: `%d` indicates where the day number is, `%m` indicates where the month number is and `%Y` indicates where the four digit year number is. We can pass this format string into `to_datetime`:

```
dates = pd.to_datetime(dt_strings, format='%d/%m/%Y')
dates
```

```
0   2017-08-01
1   2017-08-15
2   2017-09-01
3   2017-09-15
dtype: datetime64[ns]
```

You can see the full list of datetime directives [here](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior).

### Accessing the Parts of a Timestamp

We can access the parts of a single Timestamp object using properties with intuitive names:

```python
dates[0].month
```

Out: `8`

```python
dates[1].day
```

Out: `15`

Other options include: `hour`, `minute`, `second`, `year` and `tz`.

These properties cause errors, however, if we use them on the Series as a whole. Instead , if we want to get the parts of *every* Timestamp in a Series, we have to use the `.dt` property first:

```python
dates.dt.month
```

Out:

```
0    8
1    8
2    9
3    9
dtype: int64
```



### Working with Timedeltas

Sometimes your analysis will involve differences between times, e.g. "find 911 calls which happened within one day of Thanksgiving." We can do this sort of analysis using Timedeltas, which store differences between Timestamps.

For example:

```python
timediff = dates - pd.Timestamp(day=1, month=7, year=2017)
timediff
```

Out:

```
0   31 days
1   45 days
2   62 days
3   76 days
dtype: timedelta64[ns]
```

We can also create timedeltas directly:

```python
sixty_days = pd.Timedelta(days=60)
sixty_days
```

Out: `Timedelta('60 days 00:00:00')`

We can also do boolean operations on timedeltas:

```python
timediff > sixty_days
```

Out:

```
0    False
1    False
2     True
3     True
dtype: bool
```

### Using Datetimes as Indexes

We can also set the index of a dataframe to a datetime column. That allows us to index and slice the dataframe using datetimes:

```python
df = pd.DataFrame({'dates': ["2011-01-02", "2011-01-05", "2011-01-07", "2011-01-08", "2011-01-10", "2011-01-12"],
              'sales': [2,4,12,35,8,14]})
df['dates'] = pd.to_datetime(df['dates'])
df = df.set_index('dates')
df
```

Out:

```
            sales
dates
2011-01-02       2
2011-01-05       4
2011-01-07      12
2011-01-08      35
2011-01-10       8
2011-01-12      14
```

Now we can slice the dataframe using dates:

```python
 start = pd.to_datetime("2011-01-05")
 end = pd.to_datetime("2011-01-10")
 df.loc[start:end]
```

Out:

```
            sales
dates
2011-01-05       4
2011-01-07      12
2011-01-08      35
2011-01-10       8
```

Datetimes also work well when put into Matplotlib:

```
plt.plot(df.index, df['sales'])
```

![](..\assets\images\datetimes.png)

## Joining Tables

Another common situation in data science is that we have data in multiple different files or dataframes and need to do analysis that combines them. This process is called "joining" or "merging" tables.

The simplest way to carry out this operation, assuming you have a column in common between the tables, is using `pd.merge`:

```python
df1 = pd.DataFrame({
	'name': ['Alice', 'Bob', 'Carol', 'Dave', 'Ethel'],
	'age': [24, 60, 54, 45, 70]
})

df2 = pd.DataFrame({
	'name': ['Alice', 'Alice', 'Bob', 'Ethel', 'Dave', 'Fran'],
	'expense': [20,40,25,35,40,10]
})

pd.merge(df1, df2)
```

Out:

```
    name  age  expense
0  Alice   24       20
1  Alice   24       40
2    Bob   60       25
3   Dave   45       40
4  Ethel   70       35
```

Notice that Alice's age is duplicated because there are two expense rows with a name of "Alice". Carol is also not present, as Carol does not have data in df2. Similarly, Fran is not present, as she does not have data in df1.

We describe this merge as an "inner join on the name column." It is "on" the name column because we use name to match rows between the tables. It is an "inner" join because we are taking the set intersection between the two name columns. There are three other kinds of joins:

<img src="..\assets\images\pandas-dataframe-join-311790591.png" alt="pandas-dataframe-join-311790591" style="zoom: 33%;" />

Image from https://www.analyticsvidhya.com/blog/2021/06/join-the-dataframes-like-sql-tables-in-python-using-pandas/

We can customize the kind of join we are doing:

```python
pd.merge(df1, df2, left_on='name', right_on='name', how='inner')
```

This will produce the same result, but we can change the columns that we join on in the left and right dataframe by changing left_on and right_on. We can also change the kind of join with the "how" argument. If, for example, we used an outer join:

```python
pd.merge(df1, df2, left_on='name', right_on='name', how='outer')
```

Out:

```
    name   age  expense
0  Alice  24.0     20.0
1  Alice  24.0     40.0
2    Bob  60.0     25.0
3  Carol  54.0      NaN
4   Dave  45.0     40.0
5  Ethel  70.0     35.0
6   Fran   NaN     10.0
```

When there is no data associated with a given value in the column, Pandas will fill in the missing values with NaN ("not a number"). See Chapter 6 for information on what to do with NaNs.