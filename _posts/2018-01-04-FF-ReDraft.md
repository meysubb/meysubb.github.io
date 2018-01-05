---
layout: post
title: "Redrafting the 2017 Fantasy Football draft"
description: "FF 2017"
category: sports analytics
tags: [football, R]
comments: false
---

-   [Player Performance](#player-performance)
-   [Draft Results](#draft-results)
-   [Draft Valuation](#draft-valuation)
-   [Re-Drafting](#re-drafting)

I've been commish in this league for a while now. We've tested about everything from different engines, to different rules, etc. Earlier in the year, my league had a discussion about re-evaluating our draft and looking for high value players.

## Player Performance

<iframe class="huge" src="/images/plots/pre_lim_FFP.html"
    style="max-width = 100%"
    sandbox="allow-same-origin allow-scripts"
    width="150%"
    height="600"
    scrolling="no"
    seamless="seamless"
    frameborder="0">
</iframe>

Clearly it seems that QB's are in a league of their own, but that's pretty standard I would say across all leagues. This is a standard ESPN league with a few tweaks. I did however come across an interesting article suggesting that QB rules should be more rewarding and penalizing at the same time. 6 points for TDs and -4 for interceptions. Thus creating more separation for the elite QBs and potentially discourage streaming QBs to a playoff spot.

It's pretty easy to spot the injuries, like David Johnson on the bottom left. But it's definitely interesting to point out how well Todd Gurley, Mark Ingram, Marvin Jones, and Melvin Gordon (to name a few) looked. They all out-performed their projections. Gurley of course shattered his expectations, that probably stems from the help he got from Jared Goff and the rest of the Rams offense.

Just a note the projections were taken from Fantasy Football Analytics, they develop projections using custom rules from a league and a combination of different sources which includes: CBS Sports, ESPN, FantasyPros, NFL.com, Yahoo, etc. Go check it out <http://fantasyfootballanalytics.net/>.

## Draft Results

So let's take a quick look at the draft results. We should note that one or two injuries can immediately skew projections to be much greater than the actual points scored from players in each round. So that's something to keep in mind.

![_config.yml]({{ site.baseurl }}/images/FF_img/unnamed-chunk-2-1.png)

I'm a bit surprised to see Round 4's projections to be quite low. Unsurprisingly, our Round 4 was filled with some serious injuries: Dalvin Cook, Allen Robinson, Terrelle Pryor, and Emmanuel Sanders. The catch in that round Kennan Allen and then Jarvis Landry. That was a rough one for sure.

As we move on to the later rounds, projected and actual average value seem to catch up with each other. Alright let's move on to the draft valuation step.

## Draft Valuation

It took a bit to figure out exactly how I wanted to go about the redraft process. At first, I was thinking about creating a valuation process and going from there. But that wouldn't capture the effect of different positions.

Eventually, it came down to running a simple linear regression. Draft position was compared against players position, expected points, and expected position rank.

    ##
    ## Call:
    ## lm(formula = draft_pos ~ exp_pts + proj_posrank + playerposition,
    ##     data = dat_lin)
    ##
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max
    ## -62.197 -13.186   0.714  12.428  48.403
    ##
    ## Coefficients:
    ##                  Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)      117.6460    15.4368   7.621 3.65e-12 ***
    ## exp_pts           -0.2323     0.1122  -2.071  0.04023 *  
    ## proj_posrank       2.4475     0.3195   7.661 2.94e-12 ***
    ## playerpositionK   17.4705     9.5927   1.821  0.07074 .  
    ## playerpositionQB  12.4530    21.8001   0.571  0.56877    
    ## playerpositionRB -78.9569    11.7289  -6.732 4.13e-10 ***
    ## playerpositionTE -23.8165     8.2819  -2.876  0.00467 **
    ## playerpositionWR -83.3243    11.2549  -7.403 1.19e-11 ***
    ## ---
    ## Signif. codes:  0 *** 0.001 ** 0.01 * 0.05 . 0.1 ' ' 1
    ##
    ## Residual standard error: 20.2 on 138 degrees of freedom
    ## Multiple R-squared:  0.7983, Adjusted R-squared:  0.7881
    ## F-statistic: 78.05 on 7 and 138 DF,  p-value: < 2.2e-16

Remember, the high value draft picks are at lower numbers. That should make it a bit easier to understand why some of the coefficients are negative. So with every added expected point, the players draft stock increase by -0.2.

It also captures the position battles very well, all RBs/WRs are to be drafted much earlier than most other positions. BA player immediately moves up about 78 spots in the draft, if they were a WR and about 83 spots for RBs. This suggests that K and QBs are to be drafted later (again it would be worth changing the rules for QBs). But there isn't much to be had here as these variables are not very significant. The default position is D/ST, this was chosen by R not myself.

This model isn't perfect but should be very capable of capturing an adequate redraft. Using the regression, the draft can be redone using actual points, actual position rank and of course position.

### Model Diagnostics

Before we move forth with reconstructing the draft based on actual results, let's study the validity of our model.

![_config.yml]({{ site.baseurl }}/images/FF_img/unnamed-chunk-4-1.png)

A couple of things we look for when running a linear regression, (1) is the data linear?, (2) do we have homoscedasticity?, (3) are the error terms normally distributed? and (4) are there many outliers?

Looking at the two plots on the left, we can come to conclusions about (1) and (2). The Residuals vs. Fitted plot shows a slight cure here, but besides that it mostly depicts a linear relationship. There is some concern, but not enough to warrant adding a quadratic term. The variance of the error terms are equal. The Scale-Location plot, in an ideal situation would have a horizontal blue line. However, we don't have trends going in either direction and can thus claim that it is satisfactory enough to be homoscedastic.

The Q-Q plot helps understand whether the errors are normally distributed. An exactly linear relationship would show that errors are normally distributed. In this case with the exception of 3 outliers, there is a linear relationship.

Lastly, Cook's distance is used to identify outliers. Here there are only 3 outliers, therefore there isn't much to worry about and can continue using this regression model.

Overall the model looks satisfactory. We can move on to re-drafting now. Finally, the exciting stuff AM I RIGHT?!

## Re-Drafting

I'll go ahead and give you two different options to look at the redraft either through a table or a plot. The plot is interactive so hopefully that gives you a bit more detail.

<table class="table table-hover" style="width: auto !important; margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;font-weight: bold;color: white;background-color: #D7261E;">
Player
</th>
<th style="text-align:right;font-weight: bold;color: white;background-color: #D7261E;">
Redrafted Position
</th>
<th style="text-align:right;font-weight: bold;color: white;background-color: #D7261E;">
Redrafted Round
</th>
<th style="text-align:right;font-weight: bold;color: white;background-color: #D7261E;">
Draft Position
</th>
<th style="text-align:right;font-weight: bold;color: white;background-color: #D7261E;">
Draft Round
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Todd Gurley
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
LeVeon Bell
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
DeAndre Hopkins
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Kareem Hunt
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
56
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
Antonio Brown
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
Melvin Gordon
</td>
<td style="text-align:right;">
6
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
Keenan Allen
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
Mark Ingram
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
73
</td>
<td style="text-align:right;">
8
</td>
</tr>
<tr>
<td style="text-align:left;">
Tyreek Hill
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
60
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
LeSean McCoy
</td>
<td style="text-align:right;">
10
</td>
<td style="text-align:right;">
1
</td>
<td style="text-align:right;">
8
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
Marvin Jones
</td>
<td style="text-align:right;">
11
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
139
</td>
<td style="text-align:right;">
14
</td>
</tr>
<tr>
<td style="text-align:left;">
Leonard Fournette
</td>
<td style="text-align:right;">
12
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Julio Jones
</td>
<td style="text-align:right;">
13
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
Brandin Cooks
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Ezekiel Elliott
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
9
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
Larry Fitzgerald
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
57
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
Jordan Howard
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
A.J. Green
</td>
<td style="text-align:right;">
18
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
16
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
Carlos Hyde
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
Adam Thielen
</td>
<td style="text-align:right;">
20
</td>
<td style="text-align:right;">
2
</td>
<td style="text-align:right;">
102
</td>
<td style="text-align:right;">
11
</td>
</tr>
<tr>
<td style="text-align:left;">
Davante Adams
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
Devonta Freeman
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
7
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
Doug Baldwin
</td>
<td style="text-align:right;">
23
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
22
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Lamar Miller
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
24
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Jarvis Landry
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
Christian McCaffrey
</td>
<td style="text-align:right;">
26
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
Alshon Jeffery
</td>
<td style="text-align:right;">
27
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
33
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
C.J. Anderson
</td>
<td style="text-align:right;">
28
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
62
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
Stefon Diggs
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
83
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
Frank Gore
</td>
<td style="text-align:right;">
30
</td>
<td style="text-align:right;">
3
</td>
<td style="text-align:right;">
55
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
Golden Tate
</td>
<td style="text-align:right;">
31
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
59
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
Marshawn Lynch
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
29
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Mike Evans
</td>
<td style="text-align:right;">
33
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
1
</td>
</tr>
<tr>
<td style="text-align:left;">
Latavius Murray
</td>
<td style="text-align:right;">
34
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
131
</td>
<td style="text-align:right;">
14
</td>
</tr>
<tr>
<td style="text-align:left;">
Duke Johnson
</td>
<td style="text-align:right;">
35
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
128
</td>
<td style="text-align:right;">
13
</td>
</tr>
<tr>
<td style="text-align:left;">
Demaryius Thomas
</td>
<td style="text-align:right;">
36
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
32
</td>
<td style="text-align:right;">
4
</td>
</tr>
<tr>
<td style="text-align:left;">
Russell Wilson
</td>
<td style="text-align:right;">
37
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
68
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
Tevin Coleman
</td>
<td style="text-align:right;">
38
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
81
</td>
<td style="text-align:right;">
9
</td>
</tr>
<tr>
<td style="text-align:left;">
T.Y. Hilton
</td>
<td style="text-align:right;">
39
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
21
</td>
<td style="text-align:right;">
3
</td>
</tr>
<tr>
<td style="text-align:left;">
Dez Bryant
</td>
<td style="text-align:right;">
40
</td>
<td style="text-align:right;">
4
</td>
<td style="text-align:right;">
17
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
DeMarco Murray
</td>
<td style="text-align:right;">
41
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
15
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
Michael Crabtree
</td>
<td style="text-align:right;">
42
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
53
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
Rob Gronkowski
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
19
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
Derrick Henry
</td>
<td style="text-align:right;">
44
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
103
</td>
<td style="text-align:right;">
11
</td>
</tr>
<tr>
<td style="text-align:left;">
Amari Cooper
</td>
<td style="text-align:right;">
45
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
14
</td>
<td style="text-align:right;">
2
</td>
</tr>
<tr>
<td style="text-align:left;">
Travis Kelce
</td>
<td style="text-align:right;">
46
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
43
</td>
<td style="text-align:right;">
5
</td>
</tr>
<tr>
<td style="text-align:left;">
Bilal Powell
</td>
<td style="text-align:right;">
47
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
58
</td>
<td style="text-align:right;">
6
</td>
</tr>
<tr>
<td style="text-align:left;">
Cam Newton
</td>
<td style="text-align:right;">
48
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
64
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
Sammy Watkins
</td>
<td style="text-align:right;">
49
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
63
</td>
<td style="text-align:right;">
7
</td>
</tr>
<tr>
<td style="text-align:left;">
Isaiah Crowell
</td>
<td style="text-align:right;">
50
</td>
<td style="text-align:right;">
5
</td>
<td style="text-align:right;">
25
</td>
<td style="text-align:right;">
3
</td>
</tr>
</tbody>
</table>
For the sake of keeping the table simple, we shall only look at the first five rounds. So the re-draft is already showing some changes, by only suggesting 3 of the original first rounders to remain in the first round. Looks like Hopkins, Hunt and Ingram were all steals outside of the first round.

Even with Zeke's suspensions, he is a 2nd rounder. To be fair though, the guy who drafted Zeke had two first round draft picks and Zeke was his second pick. Yep, I went with the Billicheck and tried to get more picks in the top 50 by trading down. Needless to say it was not my year, not a lot of my players panned out. But, looking back at the draft and the re-draft. I would trade down AGAIN.

It's surprising to see a couple of 11 rounders and onwards up here. But you have to factor into account injuries to some of the other players. Latavius Murray wouldn't be so high on the list if it wasn't for Dalvin's injury.

Anyway's I'll lead you browse through the rest of it. If you want all of the data, you can download it from the chart below.

<iframe class="huge" src="/images/plots/redraft_FF.html"
    style="max-width = 100%"
    sandbox="allow-same-origin allow-scripts"
    width="150%"
    height="600"
    scrolling="no"
    seamless="seamless"
    frameborder="0">
</iframe>

For Injuries: look in the top left corner. (Michael Thomas, David Johnson, etc) For Busts: look for anyone above the 100 redrafted position. Don't forget about the waiver wire! For Steals: look in the top right corner. (Adam Thienlen, Duke Johnson, Marvin Jones, etc)

So much for all the hype on Crowell, Johnson is the guy to have from Cleveland which makes sense. Cleveland always goes down, and then they need their pass-catching back in the game. Hell they even run him out of the slot. Hopefully the Browns can turn it around sometime soon.


## Code

I performed everything in R, data extraction, analysis and even visualization. I normally prefer to go with Python and BeautifulSoup, but it was pretty simple with R and the rvest package.

To extract the data from your own league, make sure that it is a public league on ESPN. For Yahoo, they have an API key you can sign up for and extract data from. Not sure about NFL.com, I try to avoid playing in those leagues!

[Data Extraction](https://github.com/meysubb/Fantasy_Football_League/blob/master/Post_Season_Draft_Analysis/draft_data.R)   
[Analysis](https://github.com/meysubb/Fantasy_Football_League/blob/master/Post_Season_Draft_Analysis/writeup.Rmd)   

If you are interested in re-creating the interactive visuals, I used the R wrapper to the highcharter (JS). The code can be found in the Rmarkdown for the analysis.
