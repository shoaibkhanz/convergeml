---
layout: post
title:  "Adaptive models during COVID-19"
date:   2020-04-17 04:00:00
categories: post
comments: true
author: Shoaib Khan
---

<span style="color:#868686;">COVID-19</span> has derailed many aspects of life, and it’s hard to adapt. However, to survive we must. The story is hardly different when we look at our mathematical models, many good models have come crashing down as they failed to adjust to sudden fluctuations in the data. Primarily, this has resulted into 2 outcomes.
<!--more-->

1) We are now building a short-term model i.e. a model that helps you predict the covid-19 period, but the challenge is the dearth of data.

2) We continue to use the same model while waiting for a unique solution.

## What's the Fix ?

There is a solution that has worked for me. As I explain what it is, we must realise there is one caveat, this solution works when we are predicting <span style="color:#ea9808;">continuous</span> values and it’s not for <span style="color:#868686;">classification</span> models.

My use-case is centred around determining the price elasticity of customers and I have used a simple <span style="color:#ea9808;">log-log linear regression</span> model to model this, It helps me translate my coefficient values as percentages. If you want to understand this better, here is a ucla <A  style="color:#ea9808;" href = "https://stats.idre.ucla.edu/other/mult-pkg/faq/general/faqhow-do-i-interpret-a-regression-model-when-some-variables-are-log-transformed/">link</A> that explains it with few examples.

This solution is a <span style="color:#ea9808;"> 6 step process</span> based on linear regression but I believe can be extended to other models as well.

Step1: Fit a Linear Regression model.

Step2: Calculate the Errors.

Step3: Calculate a rolling mean of 2 or 3 or 4 weeks. {% sidenote '1' 'The choice of week depends on the use case. However, if the shock is recent then smaller the number the better (starting with at least 2 points)' %}

Step4: Lag the calculated mean by 1 or 2 weeks {% sidenote '2' 'This depends on the use case as well. The lag here can be interpreted as how soon the model is formed about the errors' %}

Step5: Convert NAs to 0.

Step6: Add the error mean to the Step1.

I have my models written in R, but this solution can be easily be written in any language.

``` r

train_error = train_actual - train_preds_
test_error = test_actual - test_preds_
  
train_preds_ = train_preds_ + lag(as.vector(rollmean(train_error,k = 2,fill = FALSE,align = 'right')),default = 0)
test_preds_ = test_preds_ + lag(as.vector(rollmean(test_error,k = 2,fill = FALSE,align = 'right')),default = 0)\
```

## What have we implemented ?

We have just combined the idea of moving average (Time series) to linear regression.
The way we have implemented this means, we are modifying the intercept (Step6) and thus we are able to make our model better.




