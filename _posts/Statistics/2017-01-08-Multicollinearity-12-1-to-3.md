---
layout: post
hidden_category: ss
title: Multicollinearity - 12.1 to 12.3
weight: 10
hard_wrap: false
---


## Multicollinearity

Since there aren't as many packages that are geared towards regression models as I'd like, I've decided to do this series in R.

```{r cars}
library(RSQLite)
sqlite <- dbDriver("SQLite")
my_db <- dbConnect(sqlite,"/home/jonathan/Documents/Statistics/PennStat501/PennStat501.db")
```

## 12.1 What is Multicollinearity?

**Multicollinearity**: two or more predictors in a regression model are moderately or highly correlated.

* *Structural*: multicollinearity caused by creating new predictors from other pre-existing ones.
* *Data-based*: multicollinearity as a result of a poorly designed experiment, reliance on purely observational data, or inability to manipulate system on which data are collected.


## 12.2 Uncorrelated Predictors

To get a better understanding of the effects of multicollinearity, we start with the uncorrelated case.

```{r pressure}
query <- "SELECT * FROM uncorrpreds"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
cor(results,method="pearson")
```

    ##            x1        x2          y
    ## x1  1.0000000  0.000000 -0.5311656
    ## x2  0.0000000  1.000000 -0.7763190
    ## y  -0.5311656 -0.776319  1.0000000

We start by looking at the first variable model.

```{r}
fit <- aov(y~x1,data=results)
summary(fit)
fit <- lm(y~x1,data=results)
summary(fit)
```

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## x1           1  21.12  21.125   2.358  0.176
    ## Residuals    6  53.75   8.958

    ## 
    ## Call:
    ## lm(formula = y ~ x1, data = results)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -3.500 -2.500  0.500  1.938  3.750 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   52.750      3.346  15.764 4.13e-06 ***
    ## x1            -1.625      1.058  -1.536    0.176    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.993 on 6 degrees of freedom
    ## Multiple R-squared:  0.2821, Adjusted R-squared:  0.1625 
    ## F-statistic: 2.358 on 1 and 6 DF,  p-value: 0.1755


Then the second variable model.


```{r}
fit <- aov(y~x2,data=results)
summary(fit)
fit <- lm(y~x2,data=results)
summary(fit)
```


    ##             Df Sum Sq Mean Sq F value Pr(>F)  
    ## x2           1  45.13   45.13   9.101 0.0235 *
    ## Residuals    6  29.75    4.96                 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = y ~ x2, data = results)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -2.500 -1.688  0.125  1.000  3.500 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  62.1250     4.7888  12.973 1.29e-05 ***
    ## x2           -2.3750     0.7873  -3.017   0.0235 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.227 on 6 degrees of freedom
    ## Multiple R-squared:  0.6027, Adjusted R-squared:  0.5364 
    ## F-statistic: 9.101 on 1 and 6 DF,  p-value: 0.02349

And then both together.

```{r}
fit <- aov(y~x1+x2,data=results)
summary(fit)
fit <- lm(y~x1+x2,data=results)
summary(fit)
```

    ##             Df Sum Sq Mean Sq F value  Pr(>F)   
    ## x1           1  21.12   21.12   12.25 0.01729 * 
    ## x2           1  45.12   45.12   26.16 0.00372 **
    ## Residuals    5   8.62    1.73                   
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = y ~ x1 + x2, data = results)
    ## 
    ## Residuals:
    ##      1      2      3      4      5      6      7      8 
    ##  0.125 -0.875  1.875 -1.125  1.375 -0.625  0.125 -0.875 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  67.0000     3.1494  21.274 4.25e-06 ***
    ## x1           -1.6250     0.4644  -3.499  0.01729 *  
    ## x2           -2.3750     0.4644  -5.115  0.00372 ** 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.313 on 5 degrees of freedom
    ## Multiple R-squared:  0.8848, Adjusted R-squared:  0.8387 
    ## F-statistic:  19.2 on 2 and 5 DF,  p-value: 0.004504

The thing to notice here is that the sum of squares are the same for each predictor regardless of whether or not they were alone or with another variable. Further more, the slope b1 or b2 did not change by introducing a new predictor into the model.

Now what happens when things are slightly correlated?

```{r}
query <- "SELECT * FROM bloodpress"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
cor(results,method="pearson")
```

    ##                 Pt         BP        Age     Weight         BSA       Dur
    ## Pt      1.00000000 0.03113499 0.04269354 0.02485650 -0.03128800 0.1762455
    ## BP      0.03113499 1.00000000 0.65909298 0.95006765  0.86587887 0.2928336
    ## Age     0.04269354 0.65909298 1.00000000 0.40734926  0.37845460 0.3437921
    ## Weight  0.02485650 0.95006765 0.40734926 1.00000000  0.87530481 0.2006496
    ## BSA    -0.03128800 0.86587887 0.37845460 0.87530481  1.00000000 0.1305400
    ## Dur     0.17624551 0.29283363 0.34379206 0.20064959  0.13054001 1.0000000
    ## Pulse   0.11228508 0.72141316 0.61876426 0.65933987  0.46481881 0.4015144
    ## Stress  0.34315169 0.16390139 0.36822369 0.03435475  0.01844634 0.3116398
    ##            Pulse     Stress
    ## Pt     0.1122851 0.34315169
    ## BP     0.7214132 0.16390139
    ## Age    0.6187643 0.36822369
    ## Weight 0.6593399 0.03435475
    ## BSA    0.4648188 0.01844634
    ## Dur    0.4015144 0.31163982
    ## Pulse  1.0000000 0.50631008
    ## Stress 0.5063101 1.00000000

In this example, we are going to use BSA and Stress. The correlation between the two is 0.018. From here, let's do the same thing as before and see how the sum of squares and slopes to change for the regression models.

```{r}
fit <- aov(BP~Stress,data=results)
summary(fit)
fit <- lm(BP~Stress,data=results)
summary(fit)
```

    ##             Df Sum Sq Mean Sq F value Pr(>F)
    ## Stress       1     15   15.04   0.497   0.49
    ## Residuals   18    545   30.27

    ## 
    ## Call:
    ## lm(formula = BP ~ Stress, data = results)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -8.6394 -3.3014  0.0722  2.2181  9.9287 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 112.71997    2.19345  51.389   <2e-16 ***
    ## Stress        0.02399    0.03404   0.705     0.49    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 5.502 on 18 degrees of freedom
    ## Multiple R-squared:  0.02686,    Adjusted R-squared:  -0.0272 
    ## F-statistic: 0.4969 on 1 and 18 DF,  p-value: 0.4899

Next, we do this for BSA (body surface area).

```{r}
fit <- aov(BP~BSA,data=results)
summary(fit)
fit <- lm(BP~BSA,data=results)
summary(fit)
```
    
    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## BSA          1  419.9   419.9   53.93 8.11e-07 ***
    ## Residuals   18  140.1     7.8                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = BP ~ BSA, data = results)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -5.314 -1.963 -0.197  1.934  4.831 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   45.183      9.392   4.811  0.00014 ***
    ## BSA           34.443      4.690   7.343 8.11e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.79 on 18 degrees of freedom
    ## Multiple R-squared:  0.7497, Adjusted R-squared:  0.7358 
    ## F-statistic: 53.93 on 1 and 18 DF,  p-value: 8.114e-07

And finally both.

```{r}
fit <- aov(BP~Stress+BSA,data=results)
summary(fit)
fit <- lm(BP~Stress+BSA,data=results)
summary(fit)
```

    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## Stress       1   15.0    15.0    2.00    0.175    
    ## BSA          1  417.1   417.1   55.44 9.56e-07 ***
    ## Residuals   17  127.9     7.5                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = BP ~ Stress + BSA, data = results)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -5.8992 -1.6483 -0.1643  1.7790  3.8524 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 44.24452    9.26104   4.777 0.000175 ***
    ## Stress       0.02166    0.01697   1.277 0.218924    
    ## BSA         34.33423    4.61110   7.446 9.56e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.743 on 17 degrees of freedom
    ## Multiple R-squared:  0.7716, Adjusted R-squared:  0.7448 
    ## F-statistic: 28.72 on 2 and 17 DF,  p-value: 3.534e-06

So, we see that in the case of a tiny bit of correlation, the predictor slopes and sum of squares vary a little bit. This hints at the problem that will come up with correlated predictors. A small amount of correlation led to changes in the model that included both. A large amount of correlation is sure to have a more dramatic impact. 

## Highly Correlated Predictors

Now for a look at what happens when predictors are moderately or highly correlated.

```{r}
query <- "SELECT * FROM bloodpress"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
cor(results,method="pearson")
```

    ##                 Pt         BP        Age     Weight         BSA       Dur
    ## Pt      1.00000000 0.03113499 0.04269354 0.02485650 -0.03128800 0.1762455
    ## BP      0.03113499 1.00000000 0.65909298 0.95006765  0.86587887 0.2928336
    ## Age     0.04269354 0.65909298 1.00000000 0.40734926  0.37845460 0.3437921
    ## Weight  0.02485650 0.95006765 0.40734926 1.00000000  0.87530481 0.2006496
    ## BSA    -0.03128800 0.86587887 0.37845460 0.87530481  1.00000000 0.1305400
    ## Dur     0.17624551 0.29283363 0.34379206 0.20064959  0.13054001 1.0000000
    ## Pulse   0.11228508 0.72141316 0.61876426 0.65933987  0.46481881 0.4015144
    ## Stress  0.34315169 0.16390139 0.36822369 0.03435475  0.01844634 0.3116398
    ##            Pulse     Stress
    ## Pt     0.1122851 0.34315169
    ## BP     0.7214132 0.16390139
    ## Age    0.6187643 0.36822369
    ## Weight 0.6593399 0.03435475
    ## BSA    0.4648188 0.01844634
    ## Dur    0.4015144 0.31163982
    ## Pulse  1.0000000 0.50631008
    ## Stress 0.5063101 1.00000000

So if you look at the correlation matrix, we see that weight and BSA are highly correlated. Let's see what happens when we build separate models and then a combined model.

```{r}
fit <- aov(BP~BSA,data=results)
summary(fit)
fit <- lm(BP~BSA,data=results)
summary(fit)
```
    
    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## BSA          1  419.9   419.9   53.93 8.11e-07 ***
    ## Residuals   18  140.1     7.8                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = BP ~ BSA, data = results)
    ## 
    ## Residuals:
    ##    Min     1Q Median     3Q    Max 
    ## -5.314 -1.963 -0.197  1.934  4.831 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   45.183      9.392   4.811  0.00014 ***
    ## BSA           34.443      4.690   7.343 8.11e-07 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.79 on 18 degrees of freedom
    ## Multiple R-squared:  0.7497, Adjusted R-squared:  0.7358 
    ## F-statistic: 53.93 on 1 and 18 DF,  p-value: 8.114e-07

Then weight.

```{r}
fit <- aov(BP~Weight,data=results)
summary(fit)
fit <- lm(BP~Weight,data=results)
summary(fit)
```
  
    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## Weight       1  505.5   505.5   166.9 1.53e-10 ***
    ## Residuals   18   54.5     3.0                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = BP ~ Weight, data = results)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -2.6933 -0.9318 -0.4935  0.7703  4.8656 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  2.20531    8.66333   0.255    0.802    
    ## Weight       1.20093    0.09297  12.917 1.53e-10 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.74 on 18 degrees of freedom
    ## Multiple R-squared:  0.9026, Adjusted R-squared:  0.8972 
    ## F-statistic: 166.9 on 1 and 18 DF,  p-value: 1.528e-10

Then both.

```{r}
fit <- aov(BP~BSA+Weight,data=results)
summary(fit)
fit <- lm(BP~BSA+Weight,data=results)
summary(fit)
```

    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## BSA          1  419.9   419.9  138.02 1.39e-09 ***
    ## Weight       1   88.4    88.4   29.07 4.87e-05 ***
    ## Residuals   17   51.7     3.0                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ## 
    ## Call:
    ## lm(formula = BP ~ BSA + Weight, data = results)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -1.8932 -1.1961 -0.4061  1.0764  4.7524 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   5.6534     9.3925   0.602    0.555    
    ## BSA           5.8313     6.0627   0.962    0.350    
    ## Weight        1.0387     0.1927   5.392 4.87e-05 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 1.744 on 17 degrees of freedom
    ## Multiple R-squared:  0.9077, Adjusted R-squared:  0.8968 
    ## F-statistic: 83.54 on 2 and 17 DF,  p-value: 1.607e-09

Yikes! So take a close look. Weight drops from 1.2 to 1.0 and BSA changes from around 34 to around 5 to 6! This means that having highly correlated values results in a model that is dependent on the variables in it. What does this mean though? This means that changing one variable and holding all others constant is no longer viable. This makes sense because the other variables are correlated and the change you are making would only likely be observed if other variables moved with it!

#### The Bottom Line

So, in the presence of multicollinearity, it is okay to use an estimated regression model to predict y as long as you do so within the scope of the model.

Also, we can no longer make much sense of the usual interpretation of a slope coefficient as the change in the mean response for each additional unit increase in the predictor x, *when all the other predictors are held constant*.

## Practice Problems: Correlated Predictors

The following experiment is about the Allen Cognitive Level (ACL) test which is designed to quantify one's cognitive abilities. David and Riley (1990) investigated the relationship of the ACL test to level of psychopathology in a set of 69 patients from a general hospital psychiatry unit.

**First, determine the pairwise correlations among the predictor variables.**

```{r}
query <- "SELECT * FROM allentest"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
cor(results,method="pearson")

```

    ##                 Subj        ACL        SDMT       Vocab    Abstract
    ## Subj      1.00000000 -0.1206839 -0.02731015 -0.02810998 -0.03015208
    ## ACL      -0.12068395  1.0000000  0.52087680  0.25000021  0.35373827
    ## SDMT     -0.02731015  0.5208768  1.00000000  0.55607069  0.57692383
    ## Vocab    -0.02810998  0.2500002  0.55607069  1.00000000  0.69784046
    ## Abstract -0.03015208  0.3537383  0.57692383  0.69784046  1.00000000

**Second, fit the simple linear regression model with y = ACL as the response and x1 = Vocab as the predictor.**


```{r}
lr <- lm(ACL ~ Vocab,data=results)
summary(lr)
predict(lr,data.frame(Vocab = c(25)))
lr <- aov(ACL ~ Vocab,data=results)
summary(lr)
```

    ## 
    ## Call:
    ## lm(formula = ACL ~ Vocab, data = results)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -1.4490 -0.6021 -0.1723  0.7106  1.5999 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)   4.2253     0.3524  11.989   <2e-16 ***
    ## Vocab         0.0298     0.0141   2.113   0.0383 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.7761 on 67 degrees of freedom
    ## Multiple R-squared:  0.0625, Adjusted R-squared:  0.04851 
    ## F-statistic: 4.467 on 1 and 67 DF,  p-value: 0.03829

    ##        1 
    ## 4.970253

    ##             Df Sum Sq Mean Sq F value Pr(>F)  
    ## Vocab        1   2.69  2.6906   4.467 0.0383 *
    ## Residuals   67  40.36  0.6024                 
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1


The value of the estimated slope coefficient of b1 is:
0.0298

The value of the standard error of b1 is:
0.0141

The regression sum of squares, SSR(x1), when x1 is the only predictor in the model is:
2.69

The predicted response of y = ACL when x1 = 25 is:
4.97025

**Next, fit the simple linear regression model with y = ACL as the response and x3 = SDMT as the predictor.**

```{r}
lr <- lm(ACL ~ SDMT,data=results)
summary(lr)
predict(lr,data.frame(SDMT = c(40)))
lr <- aov(ACL ~ SDMT,data=results)
summary(lr)
```

    ## 
    ## Call:
    ## lm(formula = ACL ~ SDMT, data = results)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.56592 -0.48864  0.01671  0.33248  1.52713 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 3.753347   0.252120  14.887  < 2e-16 ***
    ## SDMT        0.028075   0.005621   4.995 4.47e-06 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.6843 on 67 degrees of freedom
    ## Multiple R-squared:  0.2713, Adjusted R-squared:  0.2604 
    ## F-statistic: 24.95 on 1 and 67 DF,  p-value: 4.468e-06

    ##        1 
    ## 4.876343

    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## SDMT         1  11.68  11.680   24.95 4.47e-06 ***
    ## Residuals   67  31.37   0.468                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
What is the value of the estimated slope coefficient b3?
0.028075

What is the value of the standard error of b3?
0.005621

What is the regression sum of squares, SSR(x3), when x3 is the only predictor in the model?
11.68

What is the predicted response of y = ACL when x3 = 40?
4.8763

**Fit the multiple linear regression model with y = ACL as the response and x3 = SDMT as the first predictor and x1 = Vocab as the second predictor.**


```{r}
lr <- lm(ACL ~ SDMT + Vocab,data=results)
summary(lr)
predict(lr,data.frame(Vocab = c(25),SDMT = c(40)))
lr <- aov(ACL ~ SDMT+Vocab,data=results)
summary(lr)
lr.full <- aov(ACL ~SDMT+Vocab,data=results)
lr.sdmt <- aov(ACL~SDMT,data=results)
summary(lr.full)
summary(lr.sdmt)
```

    ## 
    ## Call:
    ## lm(formula = ACL ~ SDMT + Vocab, data = results)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1.55758 -0.44619 -0.01027  0.34114  1.55955 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)  3.845292   0.324380  11.854  < 2e-16 ***
    ## SDMT         0.029795   0.006803   4.379 4.35e-05 ***
    ## Vocab       -0.006840   0.015045  -0.455    0.651    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.6883 on 66 degrees of freedom
    ## Multiple R-squared:  0.2736, Adjusted R-squared:  0.2516 
    ## F-statistic: 12.43 on 2 and 66 DF,  p-value: 2.624e-05

    ##        1 
    ## 4.866083

    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## SDMT         1 11.680  11.680  24.651 5.12e-06 ***
    ## Vocab        1  0.098   0.098   0.207    0.651    
    ## Residuals   66 31.272   0.474                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## SDMT         1 11.680  11.680  24.651 5.12e-06 ***
    ## Vocab        1  0.098   0.098   0.207    0.651    
    ## Residuals   66 31.272   0.474                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    ##             Df Sum Sq Mean Sq F value   Pr(>F)    
    ## SDMT         1  11.68  11.680   24.95 4.47e-06 ***
    ## Residuals   67  31.37   0.468                     
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1


Now, what is the value of the estimated slope coefficient b1 and b3?
b1 is now -0.006840 and b3 is now 0.029795

Now, what is the value of the standard error of b1? and b3?
b1's SE is now 0.015045 and b3's SE is now 0.006803

What is the sequential sum of squares, SSR(X1|X3)?
0.098

What is the predicted response of y = ACL when x1 = 25 and x3 = 40?
4.86608

**Summarize the effects of multicollinearity on various aspects of the regression analyses.**

We can see that multicollinearity can have a significant impact on the model developed. We that in the full model, Vocab receives a negative b value as opposed to the originally positive b value. Furthermore, we also see that Vocab is now no longer significant in the full model. We also see that the F-stat of the SDMT model was much better than the full model and the Vocab model. Fortunately, multicollinearity does not have too much of an adverse effect on this model.
