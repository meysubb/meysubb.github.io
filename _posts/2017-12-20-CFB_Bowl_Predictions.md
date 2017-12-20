---
layout: post
title: "2017 CFB Bowl Predictions "
description: "CFB Bowl Predictions"
category: sports analytics
tags: [college football, R, python, college sports]
comments: false
---


-   [Bowl Predictions](#bowl-predictions)
-   [Algorithm 1 - Random Forest vs. Algorithm 2 - XGboost](#algorithm-1---random-forest-vs.-algorithm-2---xgboost)
-   [Predictions](#predictions)

Last year at work, I was part of the college bowl pick'em. Nothing new, I used to do the CFB bowl pick'ems with my college roommates and others. But last year, I decided to scrape the data and use differnt Machine Learning (ML) algorithms to predict winners.

Note: I treated this as a classification problem, 1 - Win, 0 - Lose. For those curious about the ML algorithms and the parameters selected, I'll be writing up a follow-up post. Should be out soon.

Bowl Predictions
----------------

Prediction accuracies will be updated as bowl season progresses. I'm going to include 4 different set of predictions:
1. Personal picks,
2. Algorithm 1 - Random forest,
3. Algorithm 2 - XGBoost,
4. Algorithm 3 - Suppoert Vector Machines (SVM)

Let's look at what each algorithm thinks is important. These were all the possible stats available for all 128 teams in NCAA-FBS (Division I).

    ##  [1] "3rd Down Conversion Pct"         "3rd Down Conversion Pct Defense"
    ##  [3] "4th Down Conversion Pct"         "4th Down Conversion Pct Defense"
    ##  [5] "Fewest Penalties Per Game"       "Fewest Penalty Yards Per Game"  
    ##  [7] "First Downs Defense"             "First Downs Offense"            
    ##  [9] "Kickoff Returns"                 "Net Punting"                    
    ## [11] "Passing Offense"                 "Passing Yards Allowed"          
    ## [13] "Punt Returns"                    "Red Zone Defense"               
    ## [15] "Red Zone Offense"                "Rushing Defense"                
    ## [17] "Rushing Offense"                 "Scoring Defense"                
    ## [19] "Scoring Offense"                 "Team Passing Efficiency"        
    ## [21] "Team Passing Efficiency Defense" "T.O.P"                          
    ## [23] "Total Defense"                   "Total Offense"                  
    ## [25] "Turnover Margin"

Algorithm 1 - Random Forest vs. Algorithm 2 - XGboost
-----------------------------------------------------

Random Forests (RF) are an ensemble learning method using decision trees. The model has the capabillity to select and identify the important variables. XGboost is an *extreme gradient boosting* method applied to decision trees. Similarily to RF it also has the capabillity to identify important variables.

Let's see what we've got here.

![_config.yml]({{ site.baseurl }}/images/Bowl_Var_Imp.png)

Before we dive into this, the models were run to classify/predict whether the home team wins. Above we see the top 10 stats that each model thinks is important to predict home team wins. They both tend to cover the same spectrum, interestingly enough XGB doesn't consider the home\_turnover\_margin in the top 10 variables.

I'd say overall, it tends to do a good job since it captures **(1) turnovers, (2) offensive capabillity, (3) special teams (field position), and (4) clutch conversions (third down %)**. You can also argue/point out that it captures defense since turnovers and the number of total yards the other team gains.

For Support Vector Machines (Algorithm 3 - SVM), they are a bit more complex, and hence don't provide straight forward variable importances. If you want some more detail on this, look for the follow up post.

Anyways let's take a look at the predictions.

Predictions
-----------

<table class="table table-condensed">
<thead>
<tr>
<th style="text-align:right;">
Away Team
</th>
<th style="text-align:right;">
Home Team
</th>
<th style="text-align:right;">
Algorithm 1
</th>
<th style="text-align:right;">
A1 - Confidence
</th>
<th style="text-align:right;">
Algorithm 2
</th>
<th style="text-align:right;">
A2 - Confidence
</th>
<th style="text-align:right;">
Algorithm 3
</th>
<th style="text-align:right;">
A3 - Confidence
</th>
<th style="text-align:right;">
Actual
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:right;">
Troy
</td>
<td style="text-align:right;">
North Texas
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> North Texas </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 79.20%">0.74</span>
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> North Texas </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> North Texas </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 81.60%">0.77</span>
</td>
<td style="text-align:right;">
Troy
</td>
</tr>
<tr>
<td style="text-align:right;">
Georgia St.
</td>
<td style="text-align:right;">
Western Ky.
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Western Ky. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 21.60%">0.02</span>
</td>
<td style="text-align:right;">
<span style="color: green"> <i class="glyphicon glyphicon-ok"></i> Georgia St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 28.80%">0.11</span>
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Western Ky. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 55.20%">0.44</span>
</td>
<td style="text-align:right;">
Georgia St.
</td>
</tr>
<tr>
<td style="text-align:right;">
Boise St.
</td>
<td style="text-align:right;">
Oregon
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Oregon </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 84.80%">0.81</span>
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Oregon </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Oregon </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 72.80%">0.66</span>
</td>
<td style="text-align:right;">
Boise St.
</td>
</tr>
<tr>
<td style="text-align:right;">
Marshall
</td>
<td style="text-align:right;">
Colorado St.
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Colorado St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 86.40%">0.83</span>
</td>
<td style="text-align:right;">
<span style="color: green"> <i class="glyphicon glyphicon-ok"></i> Marshall </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 90.40%">0.88</span>
</td>
<td style="text-align:right;">
<span style="color: red"> <i class="glyphicon glyphicon-remove"></i> Colorado St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 83.20%">0.79</span>
</td>
<td style="text-align:right;">
Marshall
</td>
</tr>
<tr>
<td style="text-align:right;">
Fla. Atlantic
</td>
<td style="text-align:right;">
Akron
</td>
<td style="text-align:right;">
<span style="color: green"> <i class="glyphicon glyphicon-ok"></i> Fla. Atlantic </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 81.60%">0.77</span>
</td>
<td style="text-align:right;">
<span style="color: green"> <i class="glyphicon glyphicon-ok"></i> Fla. Atlantic </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 97.60%">0.97</span>
</td>
<td style="text-align:right;">
<span style="color: green"> <i class="glyphicon glyphicon-ok"></i> Fla. Atlantic </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 76.80%">0.71</span>
</td>
<td style="text-align:right;">
Fla. Atlantic
</td>
</tr>
<tr>
<td style="text-align:right;">
SMU
</td>
<td style="text-align:right;">
Louisiana Tech
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> SMU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 35.20%">0.19</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> SMU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 83.20%">0.79</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Louisiana Tech </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 24.00%">0.05</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Temple
</td>
<td style="text-align:right;">
FIU
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> FIU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 74.40%">0.68</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Temple </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 60.00%">0.50</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> FIU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 49.60%">0.37</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
UAB
</td>
<td style="text-align:right;">
Ohio
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Ohio </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 68.00%">0.60</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Ohio </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Ohio </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 71.20%">0.64</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Wyoming
</td>
<td style="text-align:right;">
Central Mich.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Central Mich. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 48.80%">0.36</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Central Mich. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Central Mich. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 80.00%">0.75</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
South Fla.
</td>
<td style="text-align:right;">
Texas Tech
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> South Fla. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 44.00%">0.30</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> South Fla. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 74.40%">0.68</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> South Fla. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 47.20%">0.34</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Army West Point
</td>
<td style="text-align:right;">
San Diego St.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Army West Point </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 35.20%">0.19</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Army West Point </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 49.60%">0.37</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Army West Point </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 50.40%">0.38</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Appalachian St.
</td>
<td style="text-align:right;">
Toledo
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Toledo </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 63.20%">0.54</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Appalachian St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 87.20%">0.84</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Toledo </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 54.40%">0.43</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Fresno St.
</td>
<td style="text-align:right;">
Houston
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Houston </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 84.00%">0.80</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Houston </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Houston </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 78.40%">0.73</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
West Virginia
</td>
<td style="text-align:right;">
Utah
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Utah </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 51.20%">0.39</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> West Virginia </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 98.40%">0.98</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> West Virginia </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 25.60%">0.07</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Duke
</td>
<td style="text-align:right;">
Northern Ill.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Northern Ill. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 72.00%">0.65</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Northern Ill. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 96.80%">0.96</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Northern Ill. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 56.00%">0.45</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
UCLA
</td>
<td style="text-align:right;">
Kansas St.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> UCLA </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 84.00%">0.80</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> UCLA </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> UCLA </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 48.00%">0.35</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Florida St.
</td>
<td style="text-align:right;">
Southern Miss.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Southern Miss. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 60.00%">0.50</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Southern Miss. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 96.80%">0.96</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Southern Miss. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 59.20%">0.49</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Boston College
</td>
<td style="text-align:right;">
Iowa
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Boston College </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 49.60%">0.37</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Boston College </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 52.80%">0.41</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Boston College </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 27.20%">0.09</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Arizona
</td>
<td style="text-align:right;">
Purdue
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Arizona </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 41.60%">0.27</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Arizona </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 55.20%">0.44</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Arizona </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 83.20%">0.79</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Texas
</td>
<td style="text-align:right;">
Missouri
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Missouri </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 80.00%">0.75</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Missouri </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Missouri </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 87.20%">0.84</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Virginia
</td>
<td style="text-align:right;">
Navy
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Navy </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 92.00%">0.90</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Navy </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Navy </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 93.60%">0.92</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Oklahoma St.
</td>
<td style="text-align:right;">
Virginia Tech
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Oklahoma St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 46.40%">0.33</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Oklahoma St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 85.60%">0.82</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Oklahoma St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 54.40%">0.43</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Stanford
</td>
<td style="text-align:right;">
TCU
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> TCU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 60.00%">0.50</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> TCU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> TCU </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 63.20%">0.54</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Michigan St.
</td>
<td style="text-align:right;">
Washington St.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Washington St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 58.40%">0.48</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Washington St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Washington St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 60.80%">0.51</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Wake Forest
</td>
<td style="text-align:right;">
Texas A&M
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Texas A&M </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 32.80%">0.16</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Wake Forest </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 64.00%">0.55</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Texas A&M </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 35.20%">0.19</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Kentucky
</td>
<td style="text-align:right;">
Northwestern
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Northwestern </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 75.20%">0.69</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Northwestern </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Northwestern </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 47.20%">0.34</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
New Mexico St.
</td>
<td style="text-align:right;">
Utah St.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Utah St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 61.60%">0.52</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> New Mexico St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 95.20%">0.94</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Utah St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 38.40%">0.23</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Ohio St.
</td>
<td style="text-align:right;">
Southern California
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Southern California </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 43.20%">0.29</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Ohio St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 60.00%">0.50</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Ohio St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 33.60%">0.17</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Louisville
</td>
<td style="text-align:right;">
Mississippi St.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Mississippi St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 40.80%">0.26</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Mississippi St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 97.60%">0.97</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Louisville </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 35.20%">0.19</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Iowa St.
</td>
<td style="text-align:right;">
Memphis
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Memphis </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 51.20%">0.39</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Memphis </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Memphis </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 83.20%">0.79</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Washington
</td>
<td style="text-align:right;">
Penn St.
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Penn St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 20.80%">0.01</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Penn St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Penn St. </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 49.60%">0.37</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Miami (FL)
</td>
<td style="text-align:right;">
Wisconsin
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Wisconsin </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 85.60%">0.82</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Wisconsin </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 100.00%">1.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Wisconsin </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 82.40%">0.78</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Michigan
</td>
<td style="text-align:right;">
South Carolina
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Michigan </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 53.60%">0.42</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Michigan </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 71.20%">0.64</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Michigan </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 23.20%">0.04</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Auburn
</td>
<td style="text-align:right;">
UCF
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> UCF </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 20.80%">0.01</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> UCF </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 98.40%">0.98</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> UCF </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 31.20%">0.14</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Notre Dame
</td>
<td style="text-align:right;">
LSU
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Notre Dame </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 32.80%">0.16</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Notre Dame </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 74.40%">0.68</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Notre Dame </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 47.20%">0.34</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Oklahoma
</td>
<td style="text-align:right;">
Georgia
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Georgia </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 44.00%">0.30</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Oklahoma </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 91.20%">0.89</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Oklahoma </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 30.40%">0.13</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
<tr>
<td style="text-align:right;">
Clemson
</td>
<td style="text-align:right;">
Alabama
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Alabama </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 20.00%">0.00</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Alabama </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 99.20%">0.99</span>
</td>
<td style="text-align:right;">
<span style="color: grey"> <i class="glyphicon glyphicon-remove"></i> Alabama </span>
</td>
<td style="text-align:right;">
<span style="display: inline-block; direction: rtl; border-radius: 4px; padding-right: 2px; background-color: pink; width: 34.40%">0.18</span>
</td>
<td style="text-align:right;">
TBD
</td>
</tr>
</tbody>
</table>
Yikes, looks like Algorithm 1 and 3 aren't doing so hot right now. While Algorithm 2 is performing just fine. Let's see how the rest of bowl season goes!

Note, the confidence of each prediction is also provided using the probability the model provided in predicting a home win or away win. I'll update this

I'm quite unhappy with a few picks:
(1) UCLA over Kansas St, I'm a huge Bill Synder fan.
(2) TCU over Stanford, picking against the ground game of Stanford?
(3) Also the confidence in Bama beating Clemson, scares me. 99% for XGB? JEEZ

I wish I had some conference and scheduled based statistics as well. I'd love to incorporate SOS, power-5 opponnets, etc. There are probably a million different things that can be useful. As I continue this yearly, I'll look for more statistics to include. Let me know if you think there are any that I should consider!
