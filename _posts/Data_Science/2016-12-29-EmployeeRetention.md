---
layout: post
hidden_category: ds
title: Data Science - Employee Retention
weight: 10
hard_wrap: false
---


# Employee Retention

#### By: Jonathan Johannemann

The objective is to figure out what drives employee turnover.


```python
import sys
import pandas as pd
import sqlite3
import matplotlib.pyplot as plt
import numpy as np
import datetime as dt
%matplotlib inline
conn = sqlite3.connect('/home/jonathan/Documents/testing.db')
```

Instead of reading in data via csv, I'll be using sqlite3 for laughs.


```python
my_query = """

SELECT * FROM employee_retention_data

"""

df = pd.read_sql(my_query,conn)
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>employee_id</th>
      <th>company_id</th>
      <th>dept</th>
      <th>seniority</th>
      <th>salary</th>
      <th>join_date</th>
      <th>quit_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>13021</td>
      <td>7</td>
      <td>customer_service</td>
      <td>28</td>
      <td>89000</td>
      <td>2014-03-24</td>
      <td>2015-10-30</td>
    </tr>
    <tr>
      <th>1</th>
      <td>825355</td>
      <td>7</td>
      <td>marketing</td>
      <td>20</td>
      <td>183000</td>
      <td>2013-04-29</td>
      <td>2014-04-04</td>
    </tr>
    <tr>
      <th>2</th>
      <td>927315</td>
      <td>4</td>
      <td>marketing</td>
      <td>14</td>
      <td>101000</td>
      <td>2014-10-13</td>
      <td>NA</td>
    </tr>
    <tr>
      <th>3</th>
      <td>662910</td>
      <td>7</td>
      <td>customer_service</td>
      <td>20</td>
      <td>115000</td>
      <td>2012-05-14</td>
      <td>2013-06-07</td>
    </tr>
    <tr>
      <th>4</th>
      <td>256971</td>
      <td>2</td>
      <td>data_science</td>
      <td>23</td>
      <td>276000</td>
      <td>2011-10-17</td>
      <td>2014-08-22</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.join_date = pd.to_datetime(df.join_date,infer_datetime_format=True)
df.quit_date = df.quit_date.replace({'NA':dt.datetime.today()})
df.quit_date = pd.to_datetime(df.quit_date,infer_datetime_format=True)
df.quit_date = df.quit_date.dt.date
df.quit_date = pd.to_datetime(df.quit_date,infer_datetime_format=True)
```


```python
df.salary = df.salary.astype('Float64')    
df.company_id = df.company_id.astype('Float64')
df.seniority = df.seniority.astype('Float64')
```

Above, I've done some fast data type conversions.


```python
df['length'] = df.quit_date-df.join_date
df.length = df.length.apply(lambda x:x/np.timedelta64(1,'D'))
```

I made a feature for the number of days that a person is at a job.


```python
df.describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>company_id</th>
      <th>seniority</th>
      <th>salary</th>
      <th>length</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24702.000000</td>
      <td>24702.000000</td>
      <td>24702.000000</td>
      <td>24702.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>3.426969</td>
      <td>14.127803</td>
      <td>138183.345478</td>
      <td>747.395919</td>
    </tr>
    <tr>
      <th>std</th>
      <td>2.700011</td>
      <td>8.089520</td>
      <td>76058.184573</td>
      <td>387.999373</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>17000.000000</td>
      <td>102.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.000000</td>
      <td>7.000000</td>
      <td>79000.000000</td>
      <td>400.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2.000000</td>
      <td>14.000000</td>
      <td>123000.000000</td>
      <td>675.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>5.000000</td>
      <td>21.000000</td>
      <td>187000.000000</td>
      <td>997.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>12.000000</td>
      <td>99.000000</td>
      <td>408000.000000</td>
      <td>2159.000000</td>
    </tr>
  </tbody>
</table>
</div>



Looking at the valyes, seniority seems a bit off.


```python
df[df.seniority>30].seniority
```




    24700    98.0
    24701    99.0
    Name: seniority, dtype: float64



Highly doubt these outliers are real so for now I'm going to remove them from consideration.


```python
df = df[df.seniority<30]
```


```python
join_df = df.groupby(['company_id','join_date']).employee_id.count().unstack('company_id').fillna(0)
idx = pd.date_range(df.join_date.min(),df.quit_date.max(),freq='D')
join_df = join_df.reindex(idx).fillna(0)
join_df = join_df[join_df.index<'2016-01-01']
```


```python
quit_df = df.sort(columns='quit_date').groupby(['company_id','quit_date']).employee_id.count().unstack('company_id').fillna(0)
quit_df = quit_df.reindex(idx).fillna(0)
quit_df = quit_df[quit_df.index<'2016-01-01']
```

    /usr/local/lib/python2.7/dist-packages/ipykernel/__main__.py:1: FutureWarning: sort(columns=....) is deprecated, use sort_values(by=.....)
      if __name__ == '__main__':



```python
new_df = join_df.subtract(quit_df).fillna(0)
```


```python
for col in new_df.columns.values:
    new_df[col] = new_df[col].cumsum()
```

One of the requests was that the data scientist create a table that kept track of total employment over time. Unfortunately, after looking at multiple graphs that look like the one below, I didn't see too much benefit here.


```python
new_df[new_df.columns.values[4]].plot(title=new_df.columns.values[4])
    
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f41d00ddd50>




![png](output_19_1.png)



```python
df.length.plot(kind='hist',bins=100)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f419b47b7d0>




![png](output_20_1.png)


The length certainly is helpful. We can see a bimodal (perhaps trimodal?) distribution.


```python
print df.length.mode()[0], "Days"
```

    375.0 Days


Pretty close to 1 year. Makes sense, any bonuses would be received at the end of the year. Based on the graph above, we might be able to say that the next popular time to quit would be after 2 years.
