# Introduction to Pandas

## Introduction

Pandas is a Python library that plays a pivotal role in data science. It is used both for data wrangling and for calculations, and merges well with machine learning libraries, too. It has plenty of applications, covering a lot of the lost ground that Python had versus R in the past. You need to install it with the following command in the terminal.

```console
pip install pandas
```

Pandas uses _numpy_ under the hood. _numpy_ is a numerical library that enhances Python's computational capabilities. Pandas is well thought so that we do not need to explicitly invoke _numpy_ often, but it will nevertheless appear every now and then.

We will mentioned _arrays_ sometimes, and we will be referring to numpy's _ndarray_ object. You may think of it loosely as a homogeneus list of numbers.

## Series

_Series_ and _DataFrame_ are the two workhorses of pandas. _Series_ is a one-dimensional object containing a sequence of values and an associated array of data labels called _index_.

Let's define our first _series_ (the _pprint_ is not necessary).

```python
from pprint import pprint
import pandas as pd
obj = pd.Series([1, 10, 5, 2])
pprint(obj)
```

We can access the _array_ and _index_ attributes easily.

```python
obj.array
obj.index
```

Sometimes we want the _index_ to consist of labels instead of integers. The labels can be used then to access single values or sets of values.

```python
obj_ = pd.Series([1, 10, 5, 2], index=['a', 'b', 'c', 'd'])
obj_
obj_['a']
obj_[['a', 'c']]
```

Note that we need to put the indexes in a list.

We can filter a _Series_ and apply numerical operations to it.

```python
obj_[obj_ > 3]
obj_ * 3
import numpy as np
np.log(obj_)
```

You may think of a _Series_ as an ordered dictionary, meaning that we can apply a similar syntax to the one used in dictionaries.

```python
'a' in obj_
'other_index' in obj_
```

Indeed, we can easily create a Series from a dictionary.

```python
savings = {'Ann': 10, 'Bob': 20, 'Charlie':15, 'Diane': 5}
obj_s = pd.Series(savings)
```

We can enforce a particular order for the indexes, but if we push one that does not have a value, a `NA` will appear. `NA` is a missing value and can be detected with the Python functions `isna()` and `notna()`.

```python
people = ('Ann', 'Bob', 'Charlie', 'Diane', 'Eddie')
obj_n = pd.Series(savings, index=people)
obj_n
```

```python
pd.isna(obj_n)
pd.notna(obj_n)
obj_n.isna()
```

Indexes are useful because they align the Series automatically when operating with them.

```python
lump = {'Ann':2, 'Bob': 3, 'Charlie':0, 'Eddie':2}
obj_l = pd.Series(lump)
obj_n + obj_l
```

Note that the `NaN` is "contagious", affecting the operations where it is involved.

Both the Series object and its index have a name that can be modified. The index can also be altered by assignment.

```python
obj_n.name = 'savings'
obj_n.index.name = 'people'
obj_n
```

## Dataframes

A DataFrame is a rectangular table of data that contains a ordered, named collection of columns. One common way to build a dataframe is with a dictionary of lists or arrays.

```python
companies = {'name': ['Tesla', 'Berkshire', 'Nvidia', 'Tencent'],
             'country': ['US', 'US', 'US', 'China'],
             'market_cap': [1000, 700, 600, 550],
}
df = pd.DataFrame(companies)
print(df)
```

The order of the columns can be specified.

```python
df = pd.DataFrame(companies, columns=['country', 'name', 'market_cap'])
print(df)
```

Columns that are not contained in the constructor will be full of `NaN`s.

```python
df = pd.DataFrame(companies, columns=['country', 'name', 'market_cap', 'revenues'])
print(df)
```

Columns can be accessed either as keys or as attributes.

```python
df.name
df['name']
```

Rows can be accessed with the `loc` attribute and their index.

```python
df.loc['two']
```

Columns can be modified by assignment of a scalar value or of an array or list of values.

```python
df.revenues = 100
df.revenues = [400, 300, 200, 100]
```

In this assignment, either the value's length is the same as that of the DataFrame, or it is embedded in a Series with matching indexes. Columns can be deleted with the `del` command.

```python
earnings = pd.Series([20, 50], index=['two', 'four'])
df['earnings'] = earnings
del df['revenues']
df
```

Note that columns returned from indexing are a _view_ of the dataframe, and therefore in-place modifications will be reflected in the underlying DataFrame. To prevent this, we can use the _copy_ method.

Indexes are immutable, which makes them safer to share between DataFrames.

```python
obj = pd.Series(range(3), index=['first', 'second', 'third'])
index_ = obj.index
index_
# index_[0] = 'primero' # this will fail
obj2 = pd.Series(range(10, 13), index=index_)
obj2
```

## Basic operations

### Introduction

We will now walk through the most usual Python functionalities.

### Reindexing

`reindex` rearranges the data according to a new index.

```python
obj = pd.Series([10, 20, 25, 40, 30], index=['Annie', 'Bob', 'Charlie', 'Doug', 'Elaine'])
obj.reindex(['Fred', 'Elaine', 'Doug', 'Charlie', 'Bob', 'Annie'])
df = pd.DataFrame({'manufacturer': ['Tesla', 'Ford', 'Toyota']}, index=['Model 3', 'Mondeo', 'Corolla'])
df.reindex(['Corolla', 'Model 3', 'Mondeo', 'Golf'])
```

Columns of a DataFrame can also be reindexed with the `reindex` method or, alternatively, with `loc`, which we will cover shortly.

```python
df.reindex(columns=['price', 'manufacturer'])
df.loc[:,['price', 'manufacturer']]
```

### Dropping entries

Dropping entries (i.e. rows) can be done with the `drop` method.

```python
df.drop('Mondeo')
```

Dropping columns requires to pass the argument `axis`.

```python
df.drop('price', axis='columns')
```

Note that the changes are not permanent unless we set the argument `inplace` equal to `True`.

```python
df.drop('price', axis='columns', inplace=True)
```

### Indexing, selection, and filtering
#### Indexing Series
Indexing bears similarities with dictionaries. Let's take a look at some examples.

```python
obj = pd.Series([5, 10, 0], index=['bonds', 'stocks', 'cash'])
obj['bonds']
obj[0]
```

Ranges of entries and filters can also be applied.

```python
obj[0:2]
obj[['cash', 'bonds']]
obj[[1, 0]]
obj[obj < 5]
```

Note how the index can be accessed through an integer regardless of whether the index is an integer. This is error-prone and, to avoid it, the recommended way to index is with the `loc` and `iloc` operators, for label and integer indexes respectively.

```python
obj.loc[['cash', 'bonds']]
obj.iloc[[2, 0]]
```

Slicing with labels is similar to normal Python slicing, but includes the endpoints.

```python
obj.loc['stocks':'cash']
obj.loc['stocks':'cash'] = 7
obj
```

#### Indexing DataFrames

Like in Series, we can select a subset of the rows and columns of a DataFrame with `loc` and `iloc`. These are a few examples.

```python
df.loc[['Model 3', 'Corolla'], 'manufacturer']
```

```python
df.iloc[[0, 2], 0]
```

```python
df.iloc[:, :2][df.price > 30000]
```
