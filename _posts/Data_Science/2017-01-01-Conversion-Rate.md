---
layout: post
hidden_category: ds
title: Data Science - Conversion Rate
weight: 10
hard_wrap: false
---

## Conversion Rate
#### By: Jonathan Johannemann

The objective is to predict whether or not one will convert and subsequently to provide recommendations for the product and/or marketing teams to improve conversion.

```python
import pandas as pd
import sqlite3
import os
os.chdir('/home/jonathan/')
```


```python
conn = sqlite3.connect('/home/jonathan/Documents/CollectionDataScience/Challenge_Database.db')

query = """
SELECT *
FROM conversion_data
"""
```


```python
df = pd.read_sql(query,conn)
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>age</th>
      <th>new_user</th>
      <th>source</th>
      <th>total_pages_visited</th>
      <th>converted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>UK</td>
      <td>25</td>
      <td>1</td>
      <td>Ads</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>US</td>
      <td>23</td>
      <td>1</td>
      <td>Seo</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>US</td>
      <td>28</td>
      <td>1</td>
      <td>Seo</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>China</td>
      <td>39</td>
      <td>1</td>
      <td>Seo</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>US</td>
      <td>30</td>
      <td>1</td>
      <td>Seo</td>
      <td>6</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.country = df.country.astype(str)
df.age = df.age.astype(int)
df.new_user = df.new_user.astype(int)
df.source = df.source.astype(str)
df.total_pages_visited = df.total_pages_visited.astype(int)
df.converted = df.converted.astype(int)
```


```python
df.describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>new_user</th>
      <th>total_pages_visited</th>
      <th>converted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>316200.000000</td>
      <td>316200.000000</td>
      <td>316200.000000</td>
      <td>316200.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>30.569858</td>
      <td>0.685465</td>
      <td>4.872966</td>
      <td>0.032258</td>
    </tr>
    <tr>
      <th>std</th>
      <td>8.271802</td>
      <td>0.464331</td>
      <td>3.341104</td>
      <td>0.176685</td>
    </tr>
    <tr>
      <th>min</th>
      <td>17.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>24.000000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>30.000000</td>
      <td>1.000000</td>
      <td>4.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>36.000000</td>
      <td>1.000000</td>
      <td>7.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>123.000000</td>
      <td>1.000000</td>
      <td>29.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df[df.age>70].head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>country</th>
      <th>age</th>
      <th>new_user</th>
      <th>source</th>
      <th>total_pages_visited</th>
      <th>converted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>90928</th>
      <td>Germany</td>
      <td>123</td>
      <td>0</td>
      <td>Seo</td>
      <td>15</td>
      <td>1</td>
    </tr>
    <tr>
      <th>154217</th>
      <td>US</td>
      <td>73</td>
      <td>1</td>
      <td>Seo</td>
      <td>5</td>
      <td>0</td>
    </tr>
    <tr>
      <th>192644</th>
      <td>US</td>
      <td>77</td>
      <td>0</td>
      <td>Direct</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>208969</th>
      <td>US</td>
      <td>72</td>
      <td>1</td>
      <td>Direct</td>
      <td>4</td>
      <td>0</td>
    </tr>
    <tr>
      <th>265167</th>
      <td>US</td>
      <td>79</td>
      <td>1</td>
      <td>Direct</td>
      <td>1</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = df[df.age<100]
```
