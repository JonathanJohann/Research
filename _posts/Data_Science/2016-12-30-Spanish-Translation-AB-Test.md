
# Spanish Translation A/B Test
#### By: Jonathan Johannemann
So for this challenge, I had to confirm that a test a Spanish website's experiment of incorporating local translations to generate better conversion rates is actually negative. Next, I need to figure out what is happening. Finally, I need to make a function that one can plug and chug for future cases.

Total time for analysis took me about 35 minutes. For future reference, I'm going to try to find a way where I can have better proof of how much time these challenges take. (I find that I learn more when I put myself under pressure)


```python
import pandas as pd
import sqlite3
```


```python
conn = sqlite3.connect("/home/jonathan/Documents/CollectionDataScience/Challenge_Database.db")
```


```python
user_query = """
select *
FROM test_table
LEFT JOIN user_table
ON test_table.user_id=user_table.user_id 
"""
```

There were two tables, one with conversion bool values and through what medium. The user table describes the user in terms of age,sex, and country. I joined them together to make things simpler.


```python
df = pd.read_sql(user_query,conn)
```


```python
df.conversion = df.conversion.astype(int)
df.test = df.test.astype(int)
df.age = df.age.astype("Float64")
df.date = pd.to_datetime(df.date,infer_datetime_format=True)
df.country = df.country.astype(str)
df = df[df.country!='None']
```

Data types are not what I want, so I did some quick easy changes to make the data more friendly for manipulation.


```python
control = df[df.test==1]
test = df[df.test==0]
```

Splitting up the dataframes into control and test samples.


```python
print "Control results:",control.conversion.mean()

print "Test results:",test.conversion.mean()
```

    Control results: 0.0434111616784
    Test results: 0.0551555718642


At a first glance, test looks a lot better (changing translations to local translations).


```python
c1 = control[control.country!='Spain'].groupby(['country']).conversion.mean()
t1 = test[test.country!='Spain'].groupby(['country']).conversion.mean()

print t1-c1
print sum(t1-c1)
```

    country
    Argentina      0.001346
    Bolivia        0.001468
    Chile         -0.003188
    Colombia       0.001519
    Costa Rica    -0.002482
    Ecuador        0.000165
    El Salvador    0.005607
    Guatemala      0.001996
    Honduras       0.003366
    Mexico        -0.001692
    Nicaragua     -0.001530
    Panama        -0.002575
    Paraguay      -0.000736
    Peru          -0.000690
    Uruguay       -0.000859
    Venezuela      0.001365
    Name: conversion, dtype: float64
    0.0030812045936



```python
c2 = control[control.country!='Spain'].groupby(['country']).conversion.count()
t2 = test[test.country!='Spain'].groupby(['country']).conversion.count()

print (t2-c2)/c2
```

    country
    Argentina     -0.749686
    Bolivia       -0.004306
    Chile         -0.003136
    Colombia       0.004301
    Costa Rica     0.004153
    Ecuador        0.022522
    El Salvador    0.010081
    Guatemala      0.015860
    Honduras       0.036606
    Mexico        -0.001027
    Nicaragua      0.034806
    Panama        -0.009572
    Paraguay      -0.012713
    Peru           0.004286
    Uruguay       -0.888411
    Venezuela      0.015341
    Name: conversion, dtype: float64


So, compared to the control group, there is a major problem with representationin Uruguay and Argentina.


```python
from scipy.stats import ks_2samp
for c in pd.unique(test[test.country!='Spain'].country):
    print "%-12s" % c,":",ks_2samp(control[control.country==c].conversion,test[test.country==c].conversion)[1]   
```

    Mexico       : 0.999987516773
    Colombia     : 1.0
    El Salvador  : 0.99999994984
    Nicaragua    : 1.0
    Peru         : 1.0
    Chile        : 0.999999999749
    Argentina    : 1.0
    Ecuador      : 1.0
    Venezuela    : 1.0
    Guatemala    : 1.0
    Honduras     : 1.0
    Panama       : 1.0
    Paraguay     : 1.0
    Costa Rica   : 1.0
    Bolivia      : 1.0
    Uruguay      : 1.0


None of the p values are significant so the test is negative.


```python
def representation_checker(test,control,threshold):
    c2 = control[control.country!='Spain'].groupby(['country']).conversion.count()
    t2 = test[test.country!='Spain'].groupby(['country']).conversion.count()
    return (threshold > max(abs((t2-c2)/c2)))

print representation_checker(test,control,0.25)
```

    False


For the future, we can use this basic function (assuming we removed the "None" country value) to see if the country representations are similar. Also, regardless we would have to use a 2 sample ks test to determine whether or not there was a statistically significant improvement or detriment. Returns false if the representation difference exceeds a predetermined percentage threshold.
