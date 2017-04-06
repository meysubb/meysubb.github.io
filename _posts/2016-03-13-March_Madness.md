---
layout: post
title: "March Madness"
description: "Usefull application to fill out march madness brackets"
category: sports analytics
tags: [college basketball, R, python, college sports]
comments: false
---

![_config.yml]({{ site.baseurl }}/images/MarchMadness.jpg)

## NCAA March Madness Stat Visualizer

For those of you that love filling out NCAA brackets, have you ever struggled to fill out a bracket? Unless you religiously watch college basketball, there is a high chance that you haven't seen every team play. It is even more difficult to predict matchups continuoulsy compare stats between set matchups and potential matchups. 

I wanted to avoid constatly jumping from website to website. So I went ahead and spent a little time developing an application, that allows team-team comparision for the tournament this year. This application has data for all tournament teams and will allow you to cross-compare teams and players.  

You can compare teams based on different stats, and even look at individual players or compare the team as a whole. 

TLDR: Use this application to fill out your NCAA bracket. 

2017: Data updated for games played until 3/15/2017.
2016: Additionally, I’ve got every team’s schedule (except for games played on 3/13/2016) and a separate tab that has a picture of the current bracket. 


Application Links:
      [2017: NCAA March Madness Application](https://meysubb.shinyapps.io/2017_marchmadness/)
      [2016: NCAA March Madness Application](https://meysubb.shinyapps.io/NCAAB/)

Note: Older versions are not hosted anymore, see github for code.

## Source Code

Source Code Link: 
        [2017 SRC](https://github.com/meysubb/NCAAB_shiny_app/tree/master/2017/Shiny_App/2017_MarchMadness)
        [2016 SRC](https://github.com/meysubb/NCAAB_shiny_app/tree/master/2016).


### Application Development 

The application was developed using the [shiny](http://shiny.rstudio.com/) package on R. 