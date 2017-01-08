---
layout: post
hidden_category: ss
title: Multicollinearity - 12.1 to 12.3
weight: 10
hard_wrap: false
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

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

We start by looking at the first variable model.

```{r}
fit <- aov(y~x1,data=results)
summary(fit)
fit <- lm(y~x1,data=results)
summary(fit)
```

Then the second variable model.


```{r}
fit <- aov(y~x2,data=results)
summary(fit)
fit <- lm(y~x2,data=results)
summary(fit)
```

And then both together.

```{r}
fit <- aov(y~x1+x2,data=results)
summary(fit)
fit <- lm(y~x1+x2,data=results)
summary(fit)
```

The thing to notice here is that the sum of squares are the same for each predictor regardless of whether or not they were alone or with another variable. Further more, the slope b1 or b2 did not change by introducing a new predictor into the model.

Now what happens when things are slightly correlated?

```{r}
query <- "SELECT * FROM bloodpress"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
cor(results,method="pearson")
```

In this example, we are going to use BSA and Stress. The correlation between the two is 0.018. From here, let's do the same thing as before and see how the sum of squares and slopes to change for the regression models.

```{r}
fit <- aov(BP~Stress,data=results)
summary(fit)
fit <- lm(BP~Stress,data=results)
summary(fit)
```

Next, we do this for BSA (body surface area).

```{r}
fit <- aov(BP~BSA,data=results)
summary(fit)
fit <- lm(BP~BSA,data=results)
summary(fit)
```

And finally both.

```{r}
fit <- aov(BP~Stress+BSA,data=results)
summary(fit)
fit <- lm(BP~Stress+BSA,data=results)
summary(fit)
```

So, we see that in the case of a tiny bit of correlation, the predictor slopes and sum of squares vary a little bit. This hints at the problem that will come up with correlated predictors. A small amount of correlation led to changes in the model that included both. A large amount of correlation is sure to have a more dramatic impact. 

## Highly Correlated Predictors

Now for a look at what happens when predictors are moderately or highly correlated.

```{r}
query <- "SELECT * FROM bloodpress"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
cor(results,method="pearson")
```

So if you look at the correlation matrix, we see that weight and BSA are highly correlated. Let's see what happens when we build separate models and then a combined model.

```{r}
fit <- aov(BP~BSA,data=results)
summary(fit)
fit <- lm(BP~BSA,data=results)
summary(fit)
```

Then weight.

```{r}
fit <- aov(BP~Weight,data=results)
summary(fit)
fit <- lm(BP~Weight,data=results)
summary(fit)
```

Then both.

```{r}
fit <- aov(BP~BSA+Weight,data=results)
summary(fit)
fit <- lm(BP~BSA+Weight,data=results)
summary(fit)
```

Yikes! So take a close look. Weight drops from 1.2 to 1.0 and BSA changes from around 34 to around 5 to 6! This means that having highly correlated values results in a model that is dependent on the variables in it. What does this mean though? This means that changing one variable and holding all others constant is no longer viable. This makes sense because the other variables are correlated and the change you are making would only likely be observed if other variables moved with it!

#### The Bottom Line

So, in the presence of multicollinearity, it is okay to use an estimated regression model to predict y as long as you do so within the scope of the model.

Also, we can no longer make much sense of the usual interpretation of a slope coefficient as the change in the mean response for each additional unit increase in the predictor x, *when all the other predictors are held constant*.

## Practice Problems: Correlated Predictors

```{r}
query <- "SELECT * FROM allentest"
results <- dbGetQuery(my_db,query)
results <- data.frame(data.matrix(results))
```

The following experiment is about the Allen Cognitive Level (ACL) test which is designed to quantify one's cognitive abilities. David and Riley (1990) investigated the relationship of the ACL test to level of psychopathology in a set of 69 patients from a general hospital psychiatry unit.

**First, determine the pairwise correlations among the predictor variables.**

```{r}
cor(results,method="pearson")
```

**Second, fit the simple linear regression model with y = ACL as the response and x1 = Vocab as the predictor.**

```{r}
lr <- lm(ACL ~ Vocab,data=results)
summary(lr)
predict(lr,data.frame(Vocab = c(25)))
lr <- aov(ACL ~ Vocab,data=results)
summary(lr)
```

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
