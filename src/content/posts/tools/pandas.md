---

title: Pandas tutorial

published: 2024-11-26

description: 'rt'

image: ''

tags: ["tutorial", "cheatsheet"]

category: 'tools'

draft: false 

---

```
pd.Series(np.random.random(4), index=['a', 'b', 'c', 'd']  , name="uniform draws")  
```

Series 可以通过下标和label访问，可以通过字典初始化

```
s3 = pd.Series({"lions":2, "tigers":1, "bears":3}, name="oh my")

s3[1:]
s3[np.array([len(i) == 5 for i in s3.index])]
s3[ ["tigers", "bears"] ]
```

Series 之间做运算得到的结果index 是二者的并集，也就是说index决定了两个series如何运算

| Method    | Returns                                               |
| --------- | ----------------------------------------------------- |
| abs()     | Object with absolute values taken (of numerical data) |
| argmax()  | The index label of the maximum value                  |
| argmin()  | The index label of the minimum value                  |
| count()   | The number of non-null entries                        |
| cumprod() | The cumulative product over an axis                   |
| cumsum()  | The cumulative sum over an axis                       |
| max()     | The maximum of the entries                            |
| mean()    | The average of the entries                            |
| median()  | The median of the entries                             |
| min()     | The minimum of the entries                            |
| mode()    | The most common element(s)                            |
| prod()    | The product of the elements                           |
| sum()     | The sum of the elements                               |
| var()     | The variance of the elements                          |

```
pd.date_range("7/1/2000", "7/3/2000", freq='D')
DatetimeIndex(['2000-07-01', '2000-07-02', '2000-07-03'],
dtype='datetime64[ns]', freq='D')
```

| Method            | Description                                             |
| ----------------- | ------------------------------------------------------- |
| append()          | Concatenate two or more Series.                         |
| drop()            | Remove the entries with the specified label or labels   |
| drop_duplicates() | Remove duplicate values                                 |
| dropna()          | Drop null entries                                       |
| fillna()          | Replace null entries with a specified value or strategy |
| reindex()         | Replace the index                                       |
| sample()          | Draw a random entry                                     |
| shift()           | Shift the index                                         |
| unique()          | Return unique values                                    |

## Dataframe

Dataframe是series的集合，一列就是一个series

```
df1 = pd.DataFrame({"series 1": x, "series 2": y})
```

```
df1["series1"].dropna() # recovers a series
```

```
>>> data = np.random.random((3, 4))
>>> pd.DataFrame(data, index=['A', 'B', 'C'], columns=np.arange(1, 5))
	1 		2 		  3 		4
A 0.065646 0.968593 0.593394 0.750110
B 0.803829 0.662237 0.200592 0.137713
C 0.288801 0.956662 0.817915 0.951016
3 rows 4 columns
```
可以行切片，可以使用loc(label)和iloc(index)
```
df = pd.DataFrame(np.random.randn(4, 2), index=['a', 'b', 'c', 'd'],columns = ['I', 'II'])

df[:2]
I II
a 0.758867 1.231330
b 0.402484 -0.955039
```

```
>>> # select rows a and c, column II
>>> df.loc[['a','c'], 'II']

a 1.231330
c 0.556121
Name: II, dtype: float64

>>> # select last two rows, first column
>>> df.iloc[-2:, 0]

c -0.475952
d -0.518989
Name: I, dtype: float64
```

df["Ⅱ"] df.Ⅱ也可以

```
>>> studentInfo = pd.DataFrame({'ID': ID, 'Name': name, 'Sex': sex, 'Age': age, 'Class': rank})
>>> otherInfo = pd.DataFrame({'ID': ID, 'GPA': GPA, 'Financial_Aid': aid})
>>> mathInfo = pd.DataFrame({'ID': mathID, 'Grade': mathGd, 'Math_Major': major })
```

isin() is a useful way to find certain values in a DataFrame. It compares the input (a list, dictionary, or Series) to the DataFrame and returns a boolean Series showing whether or not the values match. You can then use this boolean array to select appropriate locations.  

```
>>> # SELECT ID, Age FROM studentInfo
>>> studentInfo[['ID', 'Age']]
>>> # SELECT ID, GPA FROM otherInfo WHERE Financial_Aid = 'y'
>>> otherInfo[otherInfo['Financial_Aid']=='y'][['ID', 'GPA']]
>>> # SELECT Name FROM studentInfo WHERE Class = 'J' OR Class = 'Sp'
>>> studentInfo[studentInfo['Class'].isin(['J','Sp'])]['Name']
```

```
# SELECT * FROM studentInfo INNER JOIN mathInfo ON studentInfo.ID = -
mathInfo.ID
>>> pd.merge(studentInfo, mathInfo, on='ID') # INNER JOIN is the default
```

## Analyzing Data

可以直接np.log(z)

(x + y).dropna()  去掉NaN

x.fillna(0)  将NaN填充0

## Data I/O

### read_csv() 

​	• delimiter: This argument specifies the character that separates data fields, often a comma ora whitespace character.

​	• header: The row number (starting at 0) in the CSV file that contains the column names.

​	• index_col: If you want to use one of the columns in the CSV file as the index for the DataFrame,set this argument to the desired column number.

​	• skiprows: If an integer n, skip the first n rows of the file, and then start reading in the data.If a list of integers, skip the specified rows.

​	• names: If the CSV file does not contain the column names, or you wish to use other columnnames, specify them in a list assigned to this argument.

df.to_csv("my_df.csv")  
