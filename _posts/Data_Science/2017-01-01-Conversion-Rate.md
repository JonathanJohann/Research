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
os.chdir('/home/jonathan/anaconda/lib/python2.7/site-packages')
import sklearn
```

Need to change directory because some temporary problem with paths in Jupyter.


```python
conn = sqlite3.connect('/home/jonathan/Documents/CollectionDataScience/Challenge_Database.db')

query = """
SELECT *
FROM conversion_data
"""
```

Getting the data.


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

Making the data a little more friendly for manipulation.


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



An age of 123? A bit extreme. Let's look at older ages.


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

Let's just remove 123 since it's an outlier and is very far in age from the second oldest user.


```python
from sklearn.preprocessing import LabelEncoder,scale

df.country = LabelEncoder().fit_transform(df.country)
df.source = LabelEncoder().fit_transform(df.source)
df.total_pages_visited = scale(df.total_pages_visited)
df.age = scale(df.age)
```

    sklearn/utils/validation.py:429: DataConversionWarning: Data with input dtype int64 was converted to float64 by the scale function.
      warnings.warn(msg, _DataConversionWarning)


Let's scale the data and transform the countries and sources for model building.


```python
from sklearn.tree import DecisionTreeClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.cross_validation import cross_val_score


cross_val = cross_val_score(DecisionTreeClassifier(),df[df.columns.values[:-1]],df.converted,cv=5)
print "For decision tree classifier mean: ",cross_val.mean()," with std: ",cross_val.std()

cross_val = cross_val_score(LogisticRegression(),df[df.columns.values[:-1]],df.converted,cv=5)
print "For logistic regression mean: ",cross_val.mean()," with std: ",cross_val.std()

cross_val = cross_val_score(RandomForestClassifier(),df[df.columns.values[:-1]],df.converted,cv=5)
print "For random forest mean: ",cross_val.mean()," with std: ",cross_val.std()
```

    For decision tree classifier mean:  0.98420610029  with std:  0.000253184532238
    For logistic regression mean:  0.985569170645  with std:  0.000291582100845
    For random forest mean:  0.984433805925  with std:  0.000296345719065


I thought a tree structure would do the best but it looks like logistic regression is the best for this actually! Highest average accuracy and low standard deviation of accuracy results based on K fold cross validation with 5 folds.


```python
model = LogisticRegression()
model.fit(df[df.columns.values[:-1]],df.converted)
for v in sorted(zip(model.coef_[0],df.columns.values[:-1]),reverse=True):
    print v
```

    (2.527783451306199, 'total_pages_visited')
    (0.47853570982337351, 'country')
    (-0.00094733047390775389, 'source')
    (-0.61427356551775103, 'age')
    (-1.7367747470584551, 'new_user')


* So, total pages visited is by far the most important.
* Being a new user or visiting for the first time is bad.
* Country has a tiny bit of an effect but age seems to have more of an effect. Fewer older people buy from this website.

Therefore, the objectives should be to ideally get people to go through more pages. This might be done by showing related items at the side or bottom of the page. Also, the company should focus on the younger demographic which yields a higher probability. Finally, better efforts need to be made in terms of advertising because people generally don't buy things the first time they create an account.
