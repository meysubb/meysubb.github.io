---
layout: post
title: "pRedictive models for CFB bowl season"
description: "CFB model building"
category: sports analytics
tags: [college football, R, python, college sports]
comments: false
---

-   [Data](#data)
-   [Methods](#methods)
-   [Models](#models)
    -   [Random Forests (RF)](#random-forests-rf)
    -   [XGBoost (XGB)](#xgboost-xgb)
    -   [Support Vector Machines (SVM)](#support-vector-machines-svm)
-   [Concerns](#concerns)

I've always loved college sports, especially college football. Bowl season is arguably the closest thing to March Madness when it comes to building a bracket or in this case participating in a pick'em. I've done pick'ems in the past with friends and co-workers. Last year, I ventured out to build and use ML models to predict the outcomes.

However, at the time I didn't really have as much of an expertise in hyperparmater tuning or what the pitfalls of many models might be. Having started a Business Analytics program this past year, I'm much more familiar with all my potential ML models and tuning parameters.

I decided to try to predict outcomes again this year and am going to use this post to describe the process. For those interested in what the predictions look like, you can find them [here](http://meysubb.github.io/sports%20analytics/2017/12/20/CFB_Bowl_Predictions.html).

Data
----

Let's first take a look at the data. The data includes

``` r
dim(ind_game_df)
```

    ## [1] 773  25

``` r
colnames(ind_game_df)
```

    ##  [1] "away_first_downs"          "away_rushing_yds"         
    ##  [3] "away_pass_yds"             "away_tot_offense"         
    ##  [5] "away_pen_number"           "away_pen_yds"             
    ##  [7] "away_pun_yds"              "away_pun_re_yds"          
    ##  [9] "away_kick_re_yds"          "home_first_downs"         
    ## [11] "home_rushing_yds"          "home_pass_yds"            
    ## [13] "home_tot_offense"          "home_pen_number"          
    ## [15] "home_pen_yds"              "home_pun_yds"             
    ## [17] "home_pun_re_yds"           "home_kick_re_yds"         
    ## [19] "away_third_down_conv_pct"  "home_third_down_conv_pct"
    ## [21] "away_fourth_down_conv_pct" "home_fourth_down_conv_pct"
    ## [23] "away_turnover_margin"      "home_turnover_margin"     
    ## [25] "home_win"

There's about 20 or so columns making it hard to fit the table on one page, so I'll just go with the column names above. The training data contains statistics for all Division 1 NCAA games, this includes games against FCS opponents. Data covers 773 games for the 2017 season.

``` r
dim(bowl_games_df)
```

    ## [1] 37 24

The bowl game data had the same columns as the training set. Each team was pitted against each other and their stats over the course of the season were predicted on. All the statistics here were averages over the season. This is inconsistent to predict using the averages but build using individual game statistics. At the moment, I'm not sure how to handle this. I'll address further concerns a bit later.

Note that not all bowl games are included, data was missing for certain opponents. I predicted the outcome of 37 bowl games, there are 39 bowl games in total + the playoff championship.

Methods
-------

I decided to run three different models to compare prediction accuracy. The models built are Random Forests, XGBoost and Support Vector Machines (SVMs). I decided to approach this problem as a classification instead of a prediction. I'll try and keep the model building part short!

The original training data was split into a training and validation set to identify the proper parameters for each method. I used a 75-25 split.

``` r
set.seed(22)
sample <- sample.int(n = nrow(ind_game_df), size = floor(.75*nrow(ind_game_df)), replace = F)
train <- ind_game_df[sample, ]
test  <- ind_game_df[-sample, ]

y <- "home_win"
x <- setdiff(names(ind_game_df), y)
```

Models
------

I'm going to use the `h20` package to build my tree based methods. In the past, I've found them to be more computationally efficient by leveraging parallel proccesses.

### Random Forests (RF)

For RF, a grid-search approach was taken to identify the optimal `mtry`, `ntree`, and `min_rows`. Optimal values were selected to ensure the highest out-of-sample accuracy.

``` r
library(h2o)
h2o.init(nthreads = 3, max_mem_size = "6G")
h2o.no_progress()
```

``` r
tunegrid <- expand.grid(.mtry=c(12,15,20), .ntree=c(200,500, 700),.min_rows=c(5,7,10))
# hide progress bar for markdown
tunegrid$acc <- 0

dat_tr <- as.h2o(train)
dat_ts <- as.h2o(test)

for(i in 1:nrow(tunegrid)){
  mtries <- tunegrid$.mtry[i]
  num_trees <- tunegrid$.ntree[i]
  min_rows <- tunegrid$.min_rows[i]
  ptr_statement <- paste0("Working on ",i/nrow(tunegrid)*100, "%", sep=" ")

  print(ptr_statement)
  rf_fit1 <- h2o.randomForest(x = x,
                              y = y,
                              training_frame = dat_tr,
                              model_id = "rf_fit1",
                              seed = 1,
                              ntrees = num_trees,
                              mtries = mtries,
                              min_rows = min_rows)

  y_pred <- h2o.predict(rf_fit1,
                  newdata = dat_ts)

  y_pred_df <- as_data_frame(y_pred)
  conf_mat <- table(test$home_win,y_pred_df$predict)
  tunegrid$acc[i] <- sum(diag(conf_mat))/sum(conf_mat)
}
```

    ## [1] "Working on 3.7037037037037% "
    ## [1] "Working on 7.40740740740741% "
    ## [1] "Working on 11.1111111111111% "
    ## [1] "Working on 14.8148148148148% "
    ## [1] "Working on 18.5185185185185% "
    ## [1] "Working on 22.2222222222222% "
    ## [1] "Working on 25.9259259259259% "
    ## [1] "Working on 29.6296296296296% "
    ## [1] "Working on 33.3333333333333% "
    ## [1] "Working on 37.037037037037% "
    ## [1] "Working on 40.7407407407407% "
    ## [1] "Working on 44.4444444444444% "
    ## [1] "Working on 48.1481481481481% "
    ## [1] "Working on 51.8518518518518% "
    ## [1] "Working on 55.5555555555556% "
    ## [1] "Working on 59.2592592592593% "
    ## [1] "Working on 62.962962962963% "
    ## [1] "Working on 66.6666666666667% "
    ## [1] "Working on 70.3703703703704% "
    ## [1] "Working on 74.0740740740741% "
    ## [1] "Working on 77.7777777777778% "
    ## [1] "Working on 81.4814814814815% "
    ## [1] "Working on 85.1851851851852% "
    ## [1] "Working on 88.8888888888889% "
    ## [1] "Working on 92.5925925925926% "
    ## [1] "Working on 96.2962962962963% "
    ## [1] "Working on 100% "

### XGBoost (XGB)

Simiarly for XGBoost, a grid-search approach was taken to identify the optimal `learn_rate`, `max depth`, and `booster`. Note for classification models, only gbtree and dart are valid booster functions.

``` r
xg_tunegrid <- expand.grid(.learn_rate=seq(0.1,0.9,by=0.2), .booster=c("gbtree","dart"),.max_depth=c(6,10,20,30))
xg_tunegrid$.booster <- as.character(xg_tunegrid$.booster)
xg_tunegrid$acc <- 0

for(i in 1:nrow(xg_tunegrid)){
  lr <- xg_tunegrid$.learn_rate[i]
  br <- xg_tunegrid$.booster[i]
  md <- xg_tunegrid$.max_depth[i]
  ptr_statement <- paste0("Working on ",i/nrow(xg_tunegrid)*100, "%", sep=" ")
  print(ptr_statement)
  rf_fit2 <- h2o.xgboost(x=x,
                         y=y,
                         training_frame = dat_tr,
                         model_id = "rf_fit1",
                         seed = 1,
                         learn_rate = lr,
                         booster = br,
                         max_depth = md)

  y_pred2 <- h2o.predict(rf_fit2,newdata = dat_ts)
  y_pred_df <- as_data_frame(y_pred2)
  conf_mat <- table(test$home_win,y_pred_df$predict)
  xg_tunegrid$acc[i] <- sum(diag(conf_mat))/sum(conf_mat)
}
```

    ## [1] "Working on 2.5% "
    ## [1] "Working on 5% "
    ## [1] "Working on 7.5% "
    ## [1] "Working on 10% "
    ## [1] "Working on 12.5% "
    ## [1] "Working on 15% "
    ## [1] "Working on 17.5% "
    ## [1] "Working on 20% "
    ## [1] "Working on 22.5% "
    ## [1] "Working on 25% "
    ## [1] "Working on 27.5% "
    ## [1] "Working on 30% "
    ## [1] "Working on 32.5% "
    ## [1] "Working on 35% "
    ## [1] "Working on 37.5% "
    ## [1] "Working on 40% "
    ## [1] "Working on 42.5% "
    ## [1] "Working on 45% "
    ## [1] "Working on 47.5% "
    ## [1] "Working on 50% "
    ## [1] "Working on 52.5% "
    ## [1] "Working on 55% "
    ## [1] "Working on 57.5% "
    ## [1] "Working on 60% "
    ## [1] "Working on 62.5% "
    ## [1] "Working on 65% "
    ## [1] "Working on 67.5% "
    ## [1] "Working on 70% "
    ## [1] "Working on 72.5% "
    ## [1] "Working on 75% "
    ## [1] "Working on 77.5% "
    ## [1] "Working on 80% "
    ## [1] "Working on 82.5% "
    ## [1] "Working on 85% "
    ## [1] "Working on 87.5% "
    ## [1] "Working on 90% "
    ## [1] "Working on 92.5% "
    ## [1] "Working on 95% "
    ## [1] "Working on 97.5% "
    ## [1] "Working on 100% "

    ## Are you sure you want to shutdown the H2O instance running at http://localhost:54321/ (Y/N)?

    ## [1] TRUE

### Support Vector Machines (SVM)

H2o doesn't support SVM, hence my use of caret. I've been meaning to trying caret anyways. I really do wish there was a parallel to sklearn in R. Maybe caret is that?

Lastly for SVM, a grid-search approach was taken to identify the optimal `sigma` and `slack (C)`

``` r
library(doParallel)
registerDoParallel(cores=3)
geomSeries <- function(base, max) {
  base^(0:floor(log(max, base)))
}

svm_grid <- expand.grid(sigma = c(0,0.01, 0.02, 0.025, 0.03, 0.04,
                          0.05, 0.06, 0.07,0.08, 0.09, 0.1, 0.25, 0.5, 0.75,0.9),
                              C = sort(unique(c(geomSeries(base=10, max=10^-5),
                                               geomSeries(base=10, max=10^5)))))

library(caret)
trctrl <- trainControl(method = "repeatedcv", number = 10, repeats = 3,
                       classProbs =  TRUE)

ind_game_df$home_win <- as.factor(make.names(ind_game_df$home_win))
svm_Radial <- train(home_win ~., data = ind_game_df, method = "svmRadial",
                      trControl=trctrl,
                      preProcess = c("center", "scale"),
                      tuneLength = 10,
                      tuneGrid = svm_grid)
### come back and print just the best value
svm_Radial
```

    ## Support Vector Machines with Radial Basis Function Kernel
    ##
    ## 773 samples
    ##  24 predictor
    ##   2 classes: 'X0', 'X1'
    ##
    ## Pre-processing: centered (24), scaled (24)
    ## Resampling: Cross-Validated (10 fold, repeated 3 times)
    ## Summary of sample sizes: 695, 695, 696, 696, 695, 696, ...
    ## Resampling results across tuning parameters:
    ##
    ##   sigma  C      Accuracy   Kappa       
    ##   0.000  1e-05  0.6106084   0.000000000
    ##   0.000  1e-04  0.6106084   0.000000000
    ##   0.000  1e-03  0.6106084   0.000000000
    ##   0.000  1e-02  0.6106084   0.000000000
    ##   0.000  1e-01  0.6106084   0.000000000
    ##   0.000  1e+00  0.6106084   0.000000000
    ##   0.000  1e+01  0.6106084   0.000000000
    ##   0.000  1e+02  0.6106084   0.000000000
    ##   0.000  1e+03  0.6106084   0.000000000
    ##   0.000  1e+04  0.6106084   0.000000000
    ##   0.000  1e+05  0.6106084   0.000000000
    ##   0.010  1e-05  0.6106084   0.000000000
    ##   0.010  1e-04  0.8353495   0.670756230
    ##   0.010  1e-03  0.8382860   0.677557308
    ##   0.010  1e-02  0.8382860   0.677660856
    ##   0.010  1e-01  0.8753502   0.738277904
    ##   0.010  1e+00  0.8801118   0.748318513
    ##   0.010  1e+01  0.8710983   0.727902211
    ##   0.010  1e+02  0.8469383   0.677469838
    ##   0.010  1e+03  0.8326802   0.644525759
    ##   0.010  1e+04  0.8331296   0.645600001
    ##   0.010  1e+05  0.8322638   0.643704394
    ##   0.020  1e-05  0.6369488   0.080976149
    ##   0.020  1e-04  0.8413329   0.681668175
    ##   0.020  1e-03  0.8395793   0.679292294
    ##   0.020  1e-02  0.8408889   0.681855998
    ##   0.020  1e-01  0.8753667   0.738184179
    ##   0.020  1e+00  0.8796846   0.746827885
    ##   0.020  1e+01  0.8590374   0.702404784
    ##   0.020  1e+02  0.8349106   0.649481555
    ##   0.020  1e+03  0.8365596   0.653721922
    ##   0.020  1e+04  0.8348666   0.649481517
    ##   0.020  1e+05  0.8353106   0.650735813
    ##   0.025  1e-05  0.6394965   0.088598073
    ##   0.025  1e-04  0.8413163   0.680948573
    ##   0.025  1e-03  0.8404560   0.680748939
    ##   0.025  1e-02  0.8400177   0.679852492
    ##   0.025  1e-01  0.8766600   0.741308725
    ##   0.025  1e+00  0.8775312   0.742627285
    ##   0.025  1e+01  0.8512563   0.686909488
    ##   0.025  1e+02  0.8374529   0.655175956
    ##   0.025  1e+03  0.8365816   0.653661796
    ##   0.025  1e+04  0.8352994   0.650440062
    ##   0.025  1e+05  0.8352994   0.651348618
    ##   0.030  1e-05  0.6270032   0.050729505
    ##   0.030  1e-04  0.8408726   0.679346066
    ##   0.030  1e-03  0.8425931   0.684304979
    ##   0.030  1e-02  0.8425931   0.684430598
    ##   0.030  1e-01  0.8792244   0.747074226
    ##   0.030  1e+00  0.8715148   0.730037353
    ##   0.030  1e+01  0.8473766   0.677872034
    ##   0.030  1e+02  0.8378804   0.657171562
    ##   0.030  1e+03  0.8400503   0.661792715
    ##   0.030  1e+04  0.8365982   0.654222686
    ##   0.030  1e+05  0.8395844   0.660638007
    ##   0.040  1e-05  0.6106084   0.000000000
    ##   0.040  1e-04  0.8498811   0.696665962
    ##   0.040  1e-03  0.8464563   0.690631506
    ##   0.040  1e-02  0.8469001   0.691739542
    ##   0.040  1e-01  0.8792188   0.747759907
    ##   0.040  1e+00  0.8697943   0.725596902
    ##   0.040  1e+01  0.8452397   0.672948872
    ##   0.040  1e+02  0.8396176   0.660978942
    ##   0.040  1e+03  0.8378860   0.657003026
    ##   0.040  1e+04  0.8400449   0.661505091
    ##   0.040  1e+05  0.8383133   0.658736306
    ##   0.050  1e-05  0.6106084   0.000000000
    ##   0.050  1e-04  0.8485933   0.692800433
    ##   0.050  1e-03  0.8477275   0.691900178
    ##   0.050  1e-02  0.8473167   0.691279288
    ##   0.050  1e-01  0.8766271   0.742381567
    ##   0.050  1e+00  0.8654929   0.716135674
    ##   0.050  1e+01  0.8504677   0.683600259
    ##   0.050  1e+02  0.8443739   0.671457244
    ##   0.050  1e+03  0.8473767   0.677929965
    ##   0.050  1e+04  0.8491247   0.681771876
    ##   0.050  1e+05  0.8478316   0.679171819
    ##   0.060  1e-05  0.6106084   0.000000000
    ##   0.060  1e-04  0.8464453   0.690343740
    ##   0.060  1e-03  0.8494756   0.694478588
    ##   0.060  1e-02  0.8473002   0.690771089
    ##   0.060  1e-01  0.8705722   0.730625003
    ##   0.060  1e+00  0.8637558   0.711861854
    ##   0.060  1e+01  0.8504566   0.683640804
    ##   0.060  1e+02  0.8508620   0.685295834
    ##   0.060  1e+03  0.8495687   0.681207553
    ##   0.060  1e+04  0.8491304   0.681006887
    ##   0.060  1e+05  0.8504345   0.683618299
    ##   0.070  1e-05  0.6106084   0.000000000
    ##   0.070  1e-04  0.8524782   0.699602685
    ##   0.070  1e-03  0.8525004   0.699767497
    ##   0.070  1e-02  0.8503414   0.696067898
    ##   0.070  1e-01  0.8645445   0.718989504
    ##   0.070  1e+00  0.8603478   0.704732534
    ##   0.070  1e+01  0.8529990   0.688413940
    ##   0.070  1e+02  0.8508455   0.683866542
    ##   0.070  1e+03  0.8504126   0.683330557
    ##   0.070  1e+04  0.8478152   0.677655081
    ##   0.070  1e+05  0.8499797   0.682343819
    ##   0.080  1e-05  0.6106084   0.000000000
    ##   0.080  1e-04  0.8511907   0.695370744
    ##   0.080  1e-03  0.8499084   0.694449806
    ##   0.080  1e-02  0.8503359   0.695324990
    ##   0.080  1e-01  0.8606812   0.712316312
    ##   0.080  1e+00  0.8560463   0.695560039
    ##   0.080  1e+01  0.8495523   0.681300577
    ##   0.080  1e+02  0.8491414   0.680291431
    ##   0.080  1e+03  0.8487139   0.679165433
    ##   0.080  1e+04  0.8487194   0.679320633
    ##   0.080  1e+05  0.8474318   0.676738613
    ##   0.090  1e-05  0.6106084   0.000000000
    ##   0.090  1e-04  0.8507578   0.691379546
    ##   0.090  1e-03  0.8511963   0.696653477
    ##   0.090  1e-02  0.8512017   0.696311244
    ##   0.090  1e-01  0.8546760   0.700680759
    ##   0.090  1e+00  0.8538599   0.690776843
    ##   0.090  1e+01  0.8470045   0.675591368
    ##   0.090  1e+02  0.8478647   0.677694538
    ##   0.090  1e+03  0.8474263   0.676096984
    ##   0.090  1e+04  0.8435413   0.668484605
    ##   0.090  1e+05  0.8483032   0.678372228
    ##   0.100  1e-05  0.6106084   0.000000000
    ##   0.100  1e-04  0.8455908   0.672919409
    ##   0.100  1e-03  0.8503414   0.693600219
    ##   0.100  1e-02  0.8507799   0.694745053
    ##   0.100  1e-01  0.8490648   0.689858435
    ##   0.100  1e+00  0.8508461   0.683742056
    ##   0.100  1e+01  0.8491633   0.679920733
    ##   0.100  1e+02  0.8482919   0.677892386
    ##   0.100  1e+03  0.8478812   0.677013603
    ##   0.100  1e+04  0.8487415   0.679378945
    ##   0.100  1e+05  0.8508949   0.684278095
    ##   0.250  1e-05  0.6106084   0.000000000
    ##   0.250  1e-04  0.6106084   0.000000000
    ##   0.250  1e-03  0.7952440   0.545589564
    ##   0.250  1e-02  0.8287737   0.638553164
    ##   0.250  1e-01  0.8322259   0.645878773
    ##   0.250  1e+00  0.8262531   0.626153392
    ##   0.250  1e+01  0.8211188   0.613600536
    ##   0.250  1e+02  0.8250038   0.622577328
    ##   0.250  1e+03  0.8249654   0.622995333
    ##   0.250  1e+04  0.8245598   0.621462571
    ##   0.250  1e+05  0.8236940   0.619472046
    ##   0.500  1e-05  0.6106084   0.000000000
    ##   0.500  1e-04  0.6106084   0.000000000
    ##   0.500  1e-03  0.6106084   0.000000000
    ##   0.500  1e-02  0.7300965   0.367700015
    ##   0.500  1e-01  0.7771178   0.503257088
    ##   0.500  1e+00  0.7784168   0.505721423
    ##   0.500  1e+01  0.7624265   0.458416111
    ##   0.500  1e+02  0.7572428   0.445723514
    ##   0.500  1e+03  0.7607116   0.453498616
    ##   0.500  1e+04  0.7563881   0.442967478
    ##   0.500  1e+05  0.7581142   0.446803927
    ##   0.750  1e-05  0.6106084   0.000000000
    ##   0.750  1e-04  0.6106084   0.000000000
    ##   0.750  1e-03  0.6106084   0.000000000
    ##   0.750  1e-02  0.6097482  -0.001272278
    ##   0.750  1e-01  0.6942050   0.263951102
    ##   0.750  1e+00  0.6929392   0.260321585
    ##   0.750  1e+01  0.6925229   0.257727561
    ##   0.750  1e+02  0.6890599   0.248881139
    ##   0.750  1e+03  0.6916405   0.257033629
    ##   0.750  1e+04  0.6972572   0.271945477
    ##   0.750  1e+05  0.6933061   0.259349716
    ##   0.900  1e-05  0.6106084   0.000000000
    ##   0.900  1e-04  0.6106084   0.000000000
    ##   0.900  1e-03  0.6106084   0.000000000
    ##   0.900  1e-02  0.6106084   0.000000000
    ##   0.900  1e-01  0.6364228   0.087986200
    ##   0.900  1e+00  0.6386038   0.095169328
    ##   0.900  1e+01  0.6472341   0.123425887
    ##   0.900  1e+02  0.6480892   0.122993447
    ##   0.900  1e+03  0.6481111   0.125012960
    ##   0.900  1e+04  0.6519686   0.136581195
    ##   0.900  1e+05  0.6472339   0.121904070
    ##
    ## Accuracy was used to select the optimal model using the largest value.
    ## The final values used for the model were sigma = 0.01 and C = 1.

Feature Importance
--------
<iframe src="/images/plots/vbar.html"
    style="max-width = 100%"
    sandbox="allow-same-origin allow-scripts"
    width="100%"
    height="600"
    scrolling="no"
    seamless="seamless"
    frameborder="0">
</iframe>

Concerns
--------

Caveat: I used home and away as just placeholders to allow me to pit teams against each other. There was no in-built
