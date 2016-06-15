---
layout: post
title: "March Madness"
description: "Usefull application to fill out march madness brackets"
category: baseketball
tags: [college basketball, R, python, college sports]
comments: false
---

![_config.yml]({{ site.baseurl }}/images/MarchMadness.jpg)

## March Madness Application How-to

For those of you that love filling out NCAA brackets, have you ever struggled to fill out a bracket? Unless you religiously watch college basketball, there is a high chance that you have not seen every team play. It is even more difficult to continuoulsy compare stats between set matchups and potential matchups. 

I wanted to avoid constatly jumping from website to website. So I went ahead and spent a little time developing an application, that allows team-team comparision for the tournament this year. This application has all of the data for the teams in the tournament and will allow you to cross-compare teams and players.  

You can compare teams based on multiple different stats, you can look at individual players on a team or compare the team as a whole. 

The application provides data in the form of plots as well as tables. Additionally, I’ve got every team’s schedule (except for games played on 3/13/2016) and a separate tab that has a picture of the current bracket. 

TLDR: Use this application to fill out your NCAA bracket. 

Link: [NCAA March Madness Application](https://meysubb.shinyapps.io/NCAAB/)


## Source Code

Source Code: [github](https://github.com/meysubb/NCAAB_Scrapper_2016).

### Data Scraping/Cleaning

Data was scraped using python. Rodrigo Zamith provided python code online to scrape basketball data from the NCAA website. The last time his code was updated was in 2013. Some formats have changed since Rodrigo recently made this python code. The hyperlink formats have changed so I edited them for that as well as emphasizing the parser to use in Beautiful Soup.

I also added R code to clean up the data. Some of the R code was taken from Rodrigo's old R code. I have updated it for some other new stats like AST/TO ratio, and PER. Unfortunately, to calculate PER you need to account for pace through the accounting of possesions. I was not able to find possession stats on the NCAA website. I also aimed to make the code a bit more efficient, used sapply and apply in general instead of for loops. I hope this helps.


### Application Development 

The application was developed using the [shiny](http://shiny.rstudio.com/) package on R. The code for the shiny application is not currently up on github. Look for it to be up soon. 