---
layout: post
hidden_category: qr
title: Reply to Fitting a Neural Network with Non-Stationary Features
weight: 10
hard_wrap: false
---

# Re: Fitting a Neural Network with Non-Stationary Features

Thoughts from a grad student studying neural networks and who aspires to work in systematic trading.

#### Intro:

Nowadays, everyone is excited by deep learning and what it's capable of in image processing, audo processing, and so on. After looking at Delaney's post, I decided to play around with a couple things myself, make same changes I thought were appropriate, and see what happened. 

Original post: https://www.quantopian.com/posts/london-meetup-machine-learning-and-non-stationarity

```Python
    import matplotlib.pyplot as plt
    import pandas as pd
    import numpy as np
    pricing_data = get_pricing(['AAPL', 'YHOO', 'FB', 'MSFT', 'INTC'], 
                               fields='price', 
                               start_date='2013-1-1', 
                               end_date='2013-6-1')
    pricing_data.columns = [a.symbol for a in pricing_data.columns]
    Y = pricing_data['INTC']
    Y = Y.pct_change()
    Y = Y[1:]
    Y = Y.shift(-1, freq='1d')
    X = pricing_data
    X = X[:-1]
    def process_data_for_ML(pricing_data, Y_lable, forward_window=1):
        Y = pricing_data[Y_lable]
        Y = Y.pct_change()
        Y = Y[1:]
        Y = Y.shift(-forward_window, freq='1d')
        X = pricing_data
        X = X[:-forward_window]
        return X, Y
    X, Y = process_data_for_ML(pricing_data, 'INTC')
```

So if you look at the code above, you'll notice its Delaney's code with a few things taken out. I'm not showing you the output (you can see that in Delaney's original notebook). And then *I made an important change: discrete to continuous values*. I opted out of predicting whether or not the price would go up or down. Based on my experience, this doesn't do much for in the way of determining how you want to rank your portfolio (maybe by confidence level for your up/down prediction? Still, magnitude is important.) A right prediction with low magnitude (low profit) has a much lesser impact than a wrong prediction with high magnitude (high loss).

**So what does that mean for us?** Switching to MLPRegressor.

```Python
    import sklearn.neural_network
    NN = sklearn.neural_network.MLPRegressor(solver='sgd',hidden_layer_sizes = (10,5),
                                             learning_rate='constant',learning_rate_init=0.001)
```

Thoughts:
* If I had access to GridSearchCV, I probably would use it next time. Would be nice to try multiple configurations automatically as opposed to manually.
* I chose sgd because my familiarity is in the field of image processing and I have not use the other solvers before.
* Since I'm using sgd, I tried constant and adaptive learning rates to see if there were any differences. I did not see much.
* I also tried a different initial learning rate of 0.01 and saw no improvement.
* The main thing bothering me is the limited capacity to play with different hidden layer sizes efficiently.

```Python
    from sklearn import preprocessing
    imputer = preprocessing.Imputer()
    scaler = preprocessing.MinMaxScaler()
    X = imputer.fit_transform(X)
    X = scaler.fit_transform(X)


    for k in range(0,101,20):
        max_r_squared = 0
        best_j = 0
        for j in range(10,80,5):
            for i in range(100):
                NN.fit(X[k:k+j],Y[k:k+j])
                r_squared = NN.score(X[k:k+j],Y[k:k+j])
                if r_squared>max_r_squared:
                    max_r_squared = r_squared
                    best_j = j
        print "For k = ",k,"| Best R Squared: ",max_r_squared,"| Best j:",best_j
```

        For k =  0 | Best R Squared:  0.109424496176 | Best j: 10
        For k =  20 | Best R Squared:  0 | Best j: 0
        For k =  40 | Best R Squared:  0.586712017559 | Best j: 10
        For k =  60 | Best R Squared:  0.0207631057575 | Best j: 15
        For k =  80 | Best R Squared:  0.262865930622 | Best j: 15
        For k =  100 | Best R Squared:  0.804934270975 | Best j: 40


As mentioned in "Elements of Statistical Learning", the error function is nonconvex generally in neural networks which means that weight initializations can have different impacts on output. I made an adjustment to see how the neural network performs with respect to R^2 over varying periods where one might expect there to be some level of stationarity and trained the networks multiple times to find the best R^2. In the end, R^2 is pretty inconsistent. This exercise wasn't done as a means to predict but to just choose varying windows to see how well neural networks could explain Intel using the other assets.

```Python
    import sklearn.linear_model
    lr = sklearn.linear_model.LinearRegression()
    lr.fit(X,Y)
    print lr.score(X,Y)
```

        0.0503056654796


Even a much simpler model like linear regression shows that there is not much going in in this relationship. Next steps here are to try different factors and configurations but there's something even more important that I think should be mentioned.


**Recommendation:** (for those interested in using neural networks!)
I'd highly recommend reading the chapter on Neural Networks from "Elements of Statistical Learning" as a bare minimum start. It's fairly short and gives you a little intuition behind what's going on. 

**Concerns:**
Some missing pieces here are also that sklearn does not have all of the capabilities one would need to fully explore this model. In sklearn, it does not appear that one can train a model and then introduce new data and finetune the model based on newer observations. Furthermore, the Research environment is very generous but still lacks some of the things that you would need to do a wholistic study for the implementation of neural networks for predicting returns or prices. 

**Learning Rate:**
The learning rate has an impact on the gradient descent update to determine the weights/parameters of the model. Hypothetically, if you had a modest or dynamic learning rate, you might be able to adapt to new regimes over time by training on new data.

**Weight Decay:**
I don't see a way of using weight decay currently in sklearn. Weight decay provides smoother/more robust boundaries. See link: http://statweb.stanford.edu/~tibs/ElemStatLearn/printings/ESLII_print10.pdf#page=418

**There is hope!**
Just as one can use linear regression to predict prices, you can just as well use neural networks to predict continuous output. In perhaps some of the same ways you may deal with non-stationary data and linear regression models, the same methodologies can be potentially applied to neural networks. While neural networks can seem like a black box, they are not. If you learn the different moving pieces within the model you build, you can come up with a ways to reduce overfitting and come up a regression that predicts values with an ability equal to that of a linear regression model or better due to its capacity for nonlinearity!

**Thanks!**
Thanks for taking a look at my notebook. Hope you enjoyed it. Would be really interesting to see if someone could replace the other assets with different indicators to see what difference is made.
