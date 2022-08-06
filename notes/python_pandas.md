# Pandas

## Intro

Pandas allows to create dataframes, which are tables with rows and columns. Rows have an index.

There are also Series for data with only one column. Most functions applied to df can be applied to Series as well.

Most functions return a DataFrame or a Series.

To use it, it must be installed. In a terminal, `pip show pandas` to check if it is installed and if not install with `pip install pandas`.

Then to use in the code:

```python
import pandas as pd
```

## Read csv

Can also read other plain text extensions as `.txt` specyfing the separators

```python
df1 = pd.read_csv('file.csv')
```

### Read from outside the local machine

It can be specified a url to download the file

```python
df1 = pd.read_csv('https://raw.githubusercontent.com/JackyP/testing/master/datasets/nycflights.csv')
```

### Change separator between cells in the csv

```python
df1 = pd.read_csv('file.csv', sep = ';')
```

### Specify decimal point in the csv

```python
df1 = pd.read_csv('file.csv', decimal = ',')
```

### Indicate no header in the csv

```python
df1 = pd.read_csv('file.csv', header = None)
```

### Specify a csv column to use as index of the df

```python
df1 = pd.read_csv('file.csv', index_col = 2)
```

```python
df1 = pd.read_csv('file.csv', index_col = ';')
```

### Specify how many rows to read from the csv

```python
df1 = pd.read_csv('file.csv', nrows = 500)
```

## Read excel

Can be used a lot of parameters from the csv reader as well.

```python
df1 = pd.read_excel('file.xlsx')
```

## Read json

Can be used a lot of parameters from the csv reader as well.

```python
df1 = pd.read_excel('file.json')
```



## Obtain information about a df

### Obtain general information

Displays for each value: Name, non-null values count and data type. Also, the total of columns, index range and type and memory usage.

```python
df1.info()
```

### Obtain column names

Returns an iterable

```python
df1.columns
```

### Obtain number of rows and columns

```python
df1.shape[0] # Number of rows
df1.shape[1] # Number of columns
```

### Obtain statistical data about the columns

Returns values only for numerical data

```python
df1.mean(numeric_only = True) # Returns mean value of the columns
df1.std(numeric_only = True) # Returns standard deviation of the columns
df1.min(numeric_only = True) # Returns min value of the columns
df1.max(numeric_only = True) # Returns max value of the columns
df1.describe(numeric_only = True) # Returns previous values and quartiles of the columns
```

## Obtain information about a column

### Obtain statistical data

```python
df1.col1.mean() # Returns mean value
df1.col1.std() # Returns standard deviation
df1.col1.min() # Returns min value
df1.col1.max() # Returns max value
df1.col1.describe() # Returns previous values and quartiles
```

## Sort values of a df

Returns a df and does not modify the original. Ascending by default.

```python
df1.sort_values(by = 'name')
```

## Access to the df

### Acces to all the df

Only for reading. Prints all the rows and columns

```python
df1.to_string()
```

### Access to the df sorted

Returns a df and does not modify the original. Ascending by default. nan position bottom by default in both directions unless specified in top.

```python
df1.sort_values(by = 'col1') # df sorted by col1
df1.sort_values(by = 'col1', ascending = False) # df sorted by col1 descending
df1.sort_values(by = 'col1', na_position = 'first') # df sorted by col1 and with nan values at the top
df1.sort_values(by = ['col1', 'col2']) # df sorted by col1 and then col2
df1.sort_values(by = ['col1', 'col2'], ascending = [True, False]) # df sorted by col1 ascending and then col2 descending
```

### Access to first and last rows of a df

Only to read. Returns a df. 5 rows by default.

```python
df1.head(10) # Obtain first 10 rows
df1.tail() # Obtain first 5 rows
```

### Access to the index of a df

Only to read. Returns an iterable

```python
df1.index.values
```

### Access to a column

Returns a Series. Two ways:

```python
df1.col1
df1['name_col1']
```

### Access to unique values of a column

Returns an array

```python
df1.tipo.unique()
```

### Access to several columns

Returns a df.

```python
df1[['name_col1', 'name_col2']]
```

### Access to all but certain columns

Returns a df and does not modify the original.

```python
df1.drop(columns = 'col1') # df with all columns but col1
df1.drop(columns = ['col1', 'col2']) # df with all columns but col1 and col2
```

### Alter to certain or all columns in certain order

We obtain all the columns names in a list. Those values are use to write the name of the columns to show in the wanted order

```python
cols = list(df1.index.values) # Obtained list of all column names
df1[cols[-3:-1]] + df1[cols[3]] + df1[cols[0:3]] + df1[cols[-3]] # df with two last column, then fourth column, then the three first columns and then the antepenultimate
```

### Access to rows by their position

Returns a Series or df. Can be used an int, a list of integers, or a slice object.

```python
df1.iloc[0] # Acces to first column
df1.iloc[[0]] # Acces to first column and returns it in a df
df1.iloc[0:6:2] # Access to columns 0, 2 and 4
df1.iloc[[0, 2]] # Acces to first and third rows
```

### Access to cells by their position

It is specified the rows and then the columns. For each can be used an int, a list of integers, or a slice object.

```python
df1.iloc[[0,3], 0:3] # Acces first and third values of the three first columns
df1.iloc[:, [1, 3]] # Acces to all rows values of the second and fourth columns
```

### Access to rows with certain index

```python
df1.loc[20] # Acces to rows with index 20
df1.loc[[10, 20]] # Acces to rows with index 10 or 20
df1.loc[10:40] # Acces to rows with index from 10 to 40 both included
```

### Access to cells with certain index

```python
df1.loc[10:40, 'col1'] # Acces col1 values with index from 10 to 40 both included
df1.loc[20, ['col1', 'col'] # Acces to values of col1 and col2 with index 20
```

### Access to rows with certain condition

Returns a df.

```python
df1.loc[df1.col1 = 1] # Access to rows whose col1 value contains is 1
```

```python
df1.loc[df1.col1.str.contains('1')] # Access to rows whose col1 value contains a 1
```

`str` allows to apply any string functions to the pandas cells, like indexing or gettint the length.

```python
df1.loc[df1.col1.str[0] == 'A'] # Access to rows whose col1 value starts by 'A'
```

```python
df1.loc[df1.col1.str.len() == 4] # Access to rows whose col1 length is 4
```

It can be used a list of values for the condition.

```python
df1.loc[df1.col1.isin(['Sergi', 'Joan'])] # Returns rows whose col1 value is either 'Sergi' or 'Joan'
```

To make a is not in, use `~` operator.

```python
df1.loc[~df1.col1.isin(['Sergi', 'Joan'])] # Returns rows whose col1 value is not 'Sergi' nor 'Joan'
```

It can also be used regex with re module.

```python
import re

df1.loc[df1.col3.str.contains('gmail | outlook', regex = True)] # Access to rows whose col3 value contains either gmail or outlook
```

It can also specified to ignore caps.

```python
import re

df1.loc[df1.col3.str.contains('Gmail | Outlook', flags = re.I, regex = True)] # Access to rows whose col3 value contains either gmail or outlook with either lower or upper case.
```

```python
df1.loc[df1.col2.str.contains('^se', flags = re.I, regex = True)] # Access to rows whose col2 value is starting with se (either lower or upper case).
```

It can also be put more than one condition. `& ` and `|` should be used instead of `and` `or`.

```python
df1.loc[(df1.col1 > 2) | (df1['col2'] != 'Q')] # Access rows whose col1 value is bigger than 2 and whose col2value is not a 'Q'
```

### Access to rows with a nan value

```python
df1.loc[df1.col2.notnull()] # Access to rows whose col2 value is not null
```

Can be used `~` to access to only rows with the nan value

### Access to rows with no nan value

```python
df1.loc[~df1.col2.notnull()] # Access to rows whose col2 value is null
```

### Access to cells with certain condition

Same as for rows but specifying which columns we want to get. Returns a df.

```python
df1.loc[df1.col1 == 2, 'col3']  # Access to col3 values whose col1 value is 2
```

## Group by the df

Can group the rows depending on a column.

```python
df1.groupby(['col1']).mean().col2 # Group df by col1 and then showing col2 mean for each col1 value
```

Values can be sorted by either the results or a column

```python
df1.groupby(['col1']).sum().sort_values(by = 'col1')['col2', 'col3'] # Group df by col1 and then showing col2 and col3 sums for each col1 value. Sorted by col1 ascending
```

```python
df1.groupby(['col1']).count().sort_values(ascending = False).col2 # Group df by col1 and then showing col2 std for each col1 value. Sorted by bigger to lower std
```

For count, useful to creates a column named count. Then, after making the count show only the created column.

## Modify the df

A lot of pandasfunctions carried in a df allows to set a parameter `inplace` to True in order to make changes to the df being used.

### Changing the index

Index of a new df can have intermittent values.

```python
df1.reset_index(inplace = True, drop = True) # We obtain index as 0, 1, 2... and old index is specified not to be saved as a new column of the df
```

### Creating a column

Simply referring to a df column name that does not exists, so it is created.

```python
df1['colNew'] = 10 # Creating a column with all values being 10
df1['colNew2'] = df1['col1'] + 2 * df1['col2'] # Creating a column from others
```

### Remove columns

```python
df1 = df1.drop(columns = 'col1') # removes col1 from df1
df1 = df1.drop(columns = ['col1', 'col2']) # removes col1 and col2 from df1
```

### Remove rows with null values

By default, removes rows with at least one null value. Can be specified for:

- Delete rows with null values in specific columns.
- Delete rows with all null values.
- Keep rows with at least a certain amount of non-null values.
- Lookin in just a set of columns

```python
df1 = df1.dropna() # Removes rows with at least one null value
```

```python
df1 = df1.dropna(how = all) # Removes rows with all null values
```

```python
df1 = df1.dropna(subset = ['col1', 'col2'], how = all) # Removes rows with null values in col2 and col2
```

```python
df1 = df1.dropna(subset = ['col1', 'col2'], how = all) # Removes rows with null values in col2 or col2
```

```python
df1 = df1.dropna(thresh = 3) # Removes rows with less than 3 non-null values
```

### Remove columns with null values

Is has to be specified to delete in the vertical axis. Can also be used parameters as when deleting rows.

```python
df1 = df1.dropna(axis = 'columns') # Removes rows with any null value
```

### Change substring of a column

```python
df1['col3'] = df1['col3'].replace({'hotmail': 'outlook'}, regex=True) # replaces substrings 'hotmail' with 'outlook' from col3
```

Can also make several changes.

```python
df1['col3'] = df1['col3'].replace({'hotmail': 'outlook', 'es': 'com'}, regex=True) # replaces substrings 'hotmail' with 'outlook' and 'es' with 'com' from col3
```

### Change rows values with certain condition

```python
df1.loc[df1['col3'] == 'A+', 'col2'] = 10 # Change values of col2 to 10 whose col3 value is 'A+'
```

```python
df1.loc[df1['col3'] >= 5 , ['col2', 'col4']] = [10, True] # Change values of col2 to 10 and col4 values to True whose col3 value is bigger or equal to 5
```

## Modify a column

pandas.apply

## Save df in csv

### Set default directory for created files

```python
import os
os.chdir(r'D:\Users\sergi\Desktop') # Files will be saved in Desktop by default
```

### Save df in csv

```python
df1.to_csv(name.csv)
```

### Save df in csv with certain separator

```python
df1.to_csv(name.csv, sep = ';')
```

### Save df in csv with certain decimal point

```python
df1.to_csv(name.csv, decimal = ',')
```

### Save df in csv without index

Index are saved in csv by default, but it can be otherwise.

```python
df1.to_csv(name.csv, index = False)
```

## Save df in excel

```python
df1.to_excel(name.xlsx, index = False)
```
