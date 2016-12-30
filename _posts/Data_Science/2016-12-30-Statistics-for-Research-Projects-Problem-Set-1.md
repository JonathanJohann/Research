---
layout: post
hidden_category: ds
title: Data Science - 6.S085 Statistics for Research Projects
weight: 10
hard_wrap: false
---
# Problem Set 1
## What should you turn in?

Your writeup should have:
* The code you used to do the analysis
* Any plots that were asked for
* Answers in plain English to all the qualitative questions

Your writeup should not have:
* Excessive amounts of copy-pasted computer output with no explanations

## The assignment
* Generate 30 Gaussian random variables, all with the same mean 10 and standard deviation 2. Compute the sample average. Repeat this process 100 times and plot the sample averages you get. Show at least two different ways of plotting the set of sample averages. What kind of plot (or kinds of plots) are most appropriate here and why? Briefly describe and explain the resulting plot.

For the remainder of the problem set, you'll be working with the cereal dataset. This contains data on several breakfast cereals. For more details about the data, see original source, but note that we've eliminated many of the columns that aren't as interesting for this assignment, and the "shelf" variable is "low", "middle", or "high".

* Plot the data and explore the relationships between the variables. In a sentence or two, describe your findings.

* Is there a significant difference between the sugar content of cereals in the middle shelf vs. the top shelf? How about the fiber content? Why might there be a difference? Your explanation for why should be convincing and understandable by a fourth grader. Justify which test you chose and the settings you used for that test (one-sided vs two-sided, pooled vs non-pooled* variance, matched pairs, etc.).

* Suppose you are asked to download the full data with all the nutrition information (see link above) and repeat question 2 for every nutrient and for every pair of shelves. Why is this a bad idea? What might you do to obtain a result that's still valid? (Hint: see this xkcd)


```python
import numpy as np
import pandas as pd
import random
import scipy.stats as ss
%matplotlib inline
random.seed(11122016) #Today's Date
```

### Generating Gaussian Random Variable Means
Due to pandas visualization capabilities, I threw the generator function's output array into a dataframe after creating the 30 sample guassian random variables with the same mean 10 and standard deviation 2.


```python
sample_means = pd.DataFrame([np.mean([np.random.normal(10,2) for i in range(30)]) for i in range(100)],columns=['Means'])
```

Below, I show 4 different ways that you could go about plotting the means. Although the normal line plot is not too great, I wanted to show the output. If given this task by an employer, I would definitely show either a histogram, boxplot, or kde. I also tested bar charts and that was absolutely horrendous. Therefore, a histogram, boxplot, or kde would be the best plots to really get an idea of what the means look like.


```python
sample_means.Means.plot(kind='hist')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x93d4fd0>




![png](output_5_1.png)



```python
sample_means.Means.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x94fde48>




![png](output_6_1.png)



```python
sample_means.Means.plot(kind='kde')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x972c860>




![png](output_7_1.png)



```python
sample_means.Means.plot(kind='box')
```




    <matplotlib.axes._subplots.AxesSubplot at 0xaf296d8>




![png](output_8_1.png)


### Cereal Dataset Questions
#### Question 1


```python
df = pd.read_csv('cereal_simple.csv',sep=',',header=0)
print 'The column values are:'
for col in df.columns.values:
    print col
```

    The column values are:
    name
    mfr
    sodium
    fiber
    carbo
    sugars
    shelf



```python
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>mfr</th>
      <th>sodium</th>
      <th>fiber</th>
      <th>carbo</th>
      <th>sugars</th>
      <th>shelf</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100%_Bran</td>
      <td>N</td>
      <td>130</td>
      <td>10.0</td>
      <td>5.0</td>
      <td>6</td>
      <td>high</td>
    </tr>
    <tr>
      <th>1</th>
      <td>100%_Natural_Bran</td>
      <td>Q</td>
      <td>15</td>
      <td>2.0</td>
      <td>8.0</td>
      <td>8</td>
      <td>high</td>
    </tr>
    <tr>
      <th>2</th>
      <td>All-Bran</td>
      <td>K</td>
      <td>260</td>
      <td>9.0</td>
      <td>7.0</td>
      <td>5</td>
      <td>high</td>
    </tr>
    <tr>
      <th>3</th>
      <td>All-Bran_with_Extra_Fiber</td>
      <td>K</td>
      <td>140</td>
      <td>14.0</td>
      <td>8.0</td>
      <td>0</td>
      <td>high</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Apple_Cinnamon_Cheerios</td>
      <td>G</td>
      <td>180</td>
      <td>1.5</td>
      <td>10.5</td>
      <td>10</td>
      <td>low</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.dtypes
```




    name       object
    mfr        object
    sodium      int64
    fiber     float64
    carbo     float64
    sugars      int64
    shelf      object
    dtype: object




```python
df.describe()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sodium</th>
      <th>fiber</th>
      <th>carbo</th>
      <th>sugars</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>74.000000</td>
      <td>74.000000</td>
      <td>74.000000</td>
      <td>74.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>162.364865</td>
      <td>2.175676</td>
      <td>14.729730</td>
      <td>7.108108</td>
    </tr>
    <tr>
      <th>std</th>
      <td>82.769787</td>
      <td>2.423391</td>
      <td>3.891675</td>
      <td>4.359111</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>5.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>135.000000</td>
      <td>0.250000</td>
      <td>12.000000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>180.000000</td>
      <td>2.000000</td>
      <td>14.500000</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>217.500000</td>
      <td>3.000000</td>
      <td>17.000000</td>
      <td>11.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>320.000000</td>
      <td>14.000000</td>
      <td>23.000000</td>
      <td>15.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.corr()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>sodium</th>
      <th>fiber</th>
      <th>carbo</th>
      <th>sugars</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>sodium</th>
      <td>1.000000</td>
      <td>-0.070735</td>
      <td>0.328409</td>
      <td>0.037059</td>
    </tr>
    <tr>
      <th>fiber</th>
      <td>-0.070735</td>
      <td>1.000000</td>
      <td>-0.379084</td>
      <td>-0.150949</td>
    </tr>
    <tr>
      <th>carbo</th>
      <td>0.328409</td>
      <td>-0.379084</td>
      <td>1.000000</td>
      <td>-0.452069</td>
    </tr>
    <tr>
      <th>sugars</th>
      <td>0.037059</td>
      <td>-0.150949</td>
      <td>-0.452069</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



As a bit of preliminary data analysis, we can see that the variables vary greatly. You have cereals that have a lot to little sodium, fiber, carbs, and sugars. You can also see some of the relationships between the variables as shown in the correlation matrix. It does not look like there are any very significant correlations though between the variables.


```python
print df[['mfr','carbo']].groupby('mfr').describe().unstack()
df[['mfr','carbo']].boxplot(by='mfr')
```

        carbo                                                     
        count       mean       std   min   25%    50%    75%   max
    mfr                                                           
    A     1.0  16.000000       NaN  16.0  16.0  16.00  16.00  16.0
    G    22.0  14.727273  3.347804  10.5  12.0  14.25  16.75  21.0
    K    23.0  15.130435  4.465060   7.0  12.0  15.00  19.00  22.0
    N     5.0  15.000000  5.958188   5.0  15.0  16.00  19.00  20.0
    P     9.0  13.222222  1.922094  11.0  12.0  13.00  14.00  17.0
    Q     7.0  11.571429  1.988060   8.0  11.0  12.00  12.50  14.0
    R     7.0  18.142857  3.132016  15.0  16.0  17.00  20.00  23.0





    <matplotlib.axes._subplots.AxesSubplot at 0xaf3ca90>




![png](output_16_2.png)



```python
print df[['mfr','sodium']].groupby('mfr').describe().unstack()
df[['mfr','sodium']].boxplot(by='mfr')
```

        sodium                                                          
         count        mean        std    min    25%    50%    75%    max
    mfr                                                                 
    A      1.0    0.000000        NaN    0.0    0.0    0.0    0.0    0.0
    G     22.0  200.454545  44.021345  140.0  180.0  200.0  217.5  290.0
    K     23.0  174.782609  84.132988    0.0  132.5  170.0  225.0  320.0
    N      5.0   29.000000  56.833089    0.0    0.0    0.0   15.0  130.0
    P      9.0  146.111111  55.157602   45.0  135.0  160.0  180.0  210.0
    Q      7.0  105.714286  99.600392    0.0    7.5  135.0  185.0  220.0
    R      7.0  197.857143  61.227523   95.0  170.0  200.0  235.0  280.0





    <matplotlib.axes._subplots.AxesSubplot at 0xb22c2e8>




![png](output_17_2.png)



```python
print df[['mfr','sugars']].groupby('mfr').describe().unstack()
df[['mfr','sugars']].boxplot(by='mfr')
```

        sugars                                                 
         count      mean       std  min   25%   50%   75%   max
    mfr                                                        
    A      1.0  3.000000       NaN  3.0  3.00   3.0   3.0   3.0
    G     22.0  7.954545  3.872704  1.0  3.75   8.5  10.0  14.0
    K     23.0  7.565217  4.500768  0.0  3.00   7.0  12.0  15.0
    N      5.0  2.200000  3.033150  0.0  0.00   0.0   5.0   6.0
    P      9.0  8.777778  4.576510  3.0  5.00  10.0  12.0  15.0
    Q      7.0  6.142857  4.775932  0.0  3.00   6.0   9.5  12.0
    R      7.0  5.857143  3.760699  2.0  3.00   5.0   8.5  11.0





    <matplotlib.axes._subplots.AxesSubplot at 0xc8ae898>




![png](output_18_2.png)



```python
print df[['mfr','fiber']].groupby('mfr').describe().unstack()
df[['mfr','fiber']].boxplot(by='mfr')
```

        fiber                                              
        count      mean       std  min  25%  50%  75%   max
    mfr                                                    
    A     1.0  0.000000       NaN  0.0  0.0  0.0  0.0   0.0
    G    22.0  1.272727  1.241421  0.0  0.0  1.5  2.0   4.0
    K    23.0  2.739130  3.193991  0.0  1.0  1.0  3.0  14.0
    N     5.0  4.600000  3.049590  3.0  3.0  3.0  4.0  10.0
    P     9.0  2.777778  2.333333  0.0  0.0  3.0  5.0   6.0
    Q     7.0  1.142857  0.899735  0.0  0.5  1.0  2.0   2.0
    R     7.0  2.000000  1.632993  0.0  0.5  3.0  3.0   4.0





    <matplotlib.axes._subplots.AxesSubplot at 0xcddb898>




![png](output_19_2.png)



```python
df2 = df.groupby(['mfr','shelf'])['mfr'].count().unstack('shelf').fillna(0)
print df2
df2.plot(kind='bar',stacked=True)
```

    shelf  high  low  middle
    mfr                     
    A       0.0  0.0     1.0
    G       9.0  6.0     7.0
    K      12.0  4.0     7.0
    N       1.0  3.0     1.0
    P       6.0  2.0     1.0
    Q       4.0  0.0     3.0
    R       3.0  4.0     0.0





    <matplotlib.axes._subplots.AxesSubplot at 0xb08ada0>




![png](output_20_2.png)


In addition, I decided to take a look at how variables varied from manufacturer to manufacturer. We can see that G and K have many brands while others have much fewer in comparison. The other brands vary along the spectrum for each variable also.

#### Question 2


```python
question2_df = df[df.shelf!='low']
```


```python
question2_df[['shelf','sugars']].boxplot(by='shelf')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1559cda0>




![png](output_24_1.png)



```python
question2_df[['shelf','fiber']].boxplot(by='shelf')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x151eac18>




![png](output_25_1.png)


Assuming that shelf location doesn't have to do with any sort of prestige, sugary cereals are problably on the middle shelf so it's at eye-level for kids to see.


```python
print "Using the Kolmogorov-Smirnov statistic on 2 samples:\n"
print "p-value for the sugars:"
print ss.ks_2samp(question2_df[question2_df.shelf=='high'].sugars,question2_df[question2_df.shelf=='middle'].sugars).pvalue

print "\np-value for the fiber:"
print ss.ks_2samp(question2_df[question2_df.shelf=='high'].fiber,question2_df[question2_df.shelf=='middle'].fiber).pvalue
```

    Using the Kolmogorov-Smirnov statistic on 2 samples:
    
    p-value for the sugars:
    0.0051557054694
    
    p-value for the fiber:
    0.000239674684342


As we can see, we cannot reject the null hypothesis for both scenarios where the null hypothesis is that the sample distributions are the same. The Kolmogorov-Smirnov test compares the two cumulative distribution functions of the samples and returns the largest difference between the two CDF functions. The p-value is something that is generously provided by the scipy.stats package and gives us an indication of whether or not we can reject the null hypothesis that the distributions are the same. The KS-test provides a two-sided p-value since CDF functions can vary to either end of the spectrum.

#### Question 3

Downloading all of the data and repeating the data analysis requested in question 2 for every nutrient and for every pair of shelves is potentially a bad idea. While there may be some interesting insights, repeating this process for everything will lead to multiple cases where certain relationships occurred by chance. Also, from an insights perspective, an analyst who tries to look at every relationship will ultimately overfit what is seen in some way. The analysis becomes too complex and ultimately not that beneficial. Something to obtain a result that might still be valid is to use a tree method such as a normal decision tree or random forest to determine feature importances. This might provide insight into what relationships are consistently significant when a subset of the features are looked at or one is trying to predict a category or value.
