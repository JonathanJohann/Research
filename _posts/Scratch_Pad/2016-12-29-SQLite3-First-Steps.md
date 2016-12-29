---
layout: post
hidden_category: sp
title: Scratch Pad - SQLite3 First Steps
weight: 10
hard_wrap: false
---

Been trying to download SQL server management studio but the process has been a mess. So I'm gonna have to settle for SQLite3. Doesn't seem like a problem right now, I don't think I'll be dealing with too much data just yet. Alright, this is just a basic test run.


```python
import sqlite3, pandas as pd
```

Blah blah blah, import packages


```python
con=sqlite3.connect('C:\\Users\\Jonathan\\Desktop\\database\\first.db')
cursor = con.cursor()
```

Awesome, so I was able to connect without any complains. Alright, here's what happened:
* Tried whole path. Didn't work. Tried again with double backslahes and it suddenly works. Thanks stack overflow.
* I found this to be really helpful. https://www.youtube.com/watch?v=JVAWKVpdb4Y If you want to dive in, this got me hitting the ground the fastest. Props to this guy.


```python
my_query = """
SELECT *
FROM BostonHousing
LIMIT 5
"""

for row in cursor.execute(my_query):
    print row
```

    (u'CRIM', u'ZN', u'INDUS', u'CHAS', u'NOX', u'RM', u'AGE', u'DIS', u'RAD', u'TAX', u'PTRATIO', u'LSTAT', u'MEDV', u'CAT.MEDV')
    (u'0.00632', u'18', u'2.31', u'0', u'0.538', u'6.575', u'65.2', u'4.09', u'1', u'296', u'15.3', u'4.98', u'24', u'0')
    (u'0.02731', u'0', u'7.07', u'0', u'0.469', u'6.421', u'78.9', u'4.9671', u'2', u'242', u'17.8', u'9.14', u'21.6', u'0')
    (u'0.02729', u'0', u'7.07', u'0', u'0.469', u'7.185', u'61.1', u'4.9671', u'2', u'242', u'17.8', u'4.03', u'34.7', u'1')
    (u'0.03237', u'0', u'2.18', u'0', u'0.458', u'6.998', u'45.8', u'6.0622', u'3', u'222', u'18.7', u'2.94', u'33.4', u'1')


Not really liking this format here...


```python
df = pd.read_sql(my_query,con)
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>field1</th>
      <th>field2</th>
      <th>field3</th>
      <th>field4</th>
      <th>field5</th>
      <th>field6</th>
      <th>field7</th>
      <th>field8</th>
      <th>field9</th>
      <th>field10</th>
      <th>field11</th>
      <th>field12</th>
      <th>field13</th>
      <th>field14</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CRIM</td>
      <td>ZN</td>
      <td>INDUS</td>
      <td>CHAS</td>
      <td>NOX</td>
      <td>RM</td>
      <td>AGE</td>
      <td>DIS</td>
      <td>RAD</td>
      <td>TAX</td>
      <td>PTRATIO</td>
      <td>LSTAT</td>
      <td>MEDV</td>
      <td>CAT.MEDV</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.00632</td>
      <td>18</td>
      <td>2.31</td>
      <td>0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.09</td>
      <td>1</td>
      <td>296</td>
      <td>15.3</td>
      <td>4.98</td>
      <td>24</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02731</td>
      <td>0</td>
      <td>7.07</td>
      <td>0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2</td>
      <td>242</td>
      <td>17.8</td>
      <td>9.14</td>
      <td>21.6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.02729</td>
      <td>0</td>
      <td>7.07</td>
      <td>0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2</td>
      <td>242</td>
      <td>17.8</td>
      <td>4.03</td>
      <td>34.7</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.03237</td>
      <td>0</td>
      <td>2.18</td>
      <td>0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3</td>
      <td>222</td>
      <td>18.7</td>
      <td>2.94</td>
      <td>33.4</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.columns = df.ix[0].as_matrix() #df.columns.values is often a copy. set it on df.columns to change column names
df = df.reindex(df.index.drop(0)).reset_index()
df = df[df.columns.values[1:]]
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CRIM</th>
      <th>ZN</th>
      <th>INDUS</th>
      <th>CHAS</th>
      <th>NOX</th>
      <th>RM</th>
      <th>AGE</th>
      <th>DIS</th>
      <th>RAD</th>
      <th>TAX</th>
      <th>PTRATIO</th>
      <th>LSTAT</th>
      <th>MEDV</th>
      <th>CAT.MEDV</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18</td>
      <td>2.31</td>
      <td>0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>4.09</td>
      <td>1</td>
      <td>296</td>
      <td>15.3</td>
      <td>4.98</td>
      <td>24</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>0</td>
      <td>7.07</td>
      <td>0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>4.9671</td>
      <td>2</td>
      <td>242</td>
      <td>17.8</td>
      <td>9.14</td>
      <td>21.6</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>0</td>
      <td>7.07</td>
      <td>0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>4.9671</td>
      <td>2</td>
      <td>242</td>
      <td>17.8</td>
      <td>4.03</td>
      <td>34.7</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.03237</td>
      <td>0</td>
      <td>2.18</td>
      <td>0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>6.0622</td>
      <td>3</td>
      <td>222</td>
      <td>18.7</td>
      <td>2.94</td>
      <td>33.4</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



That's much better. Turns out I didn't check off a box when importing the csv into SQLite3. 
