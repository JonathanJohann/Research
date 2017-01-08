---
layout: post
title: Realization with Multicollinearity
---

So recently, I've been going through the penn Stats 501 notes on multicollinearity and I realized in one of my final projects for Data Mining at MIT, I made a huge mistake!

To those of you unfamiliar with multicollinearity, multicollinearity is when two or more predictors are correlated. How does this cause a problem? Simple: 

When you have completely uncorrelated predictors, the slopes or "b" values do not change as you add in new predictors. However, if these values are even slightly correlated, the predictor slopes begin to change. This suddenly means that the slopes depend on what variables are in the model.

**But how does this tie into your old final project?**

I tried to be fancy and use *ridge regression coefficients* to figure out feature importance. However, I neglected to check for multicollinearity! 

**But what does this mean?**

When there's multicollinearity, we can't really snag regression slopes and say "hey, look, the regression slope for housing square foot is 10, therefore, every added foot adds $10." The reason I can no longer make sense of the slope coefficient is that it does not make sense to hold values constant when they are correlated. A move in one predictor would infer moves in other predictors!

If you come across my Data Mining Final Project in the data science section, you'll see what I did.
