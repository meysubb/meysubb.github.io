---
layout: post
title: "Scrape MLB Standings per Division"
description: "Scrape Data with R"
category: baseball
tags: [MLB, R, Python]
comments: false
---

I think I mentioned in one of my previous posts that my friend is one of the writers for the Crawfish Boxes SB Nation blog (Houston Astros). Well, this year he writes weekly reviews of the AL West division ([SB Nation’s Crawfish boxes](http://www.crawfishboxes.com/2016/8/8/12402328/the-good-the-bad-and-the-ugly-a-weekly-al-west-roundup-week-18?_ga=1.206349203.1040503687.1461935942). Knowing that I liked sports analytics and wanted to get more practice in, he asked whether I be able to scrape pertinent data for him. 

Through the course of this post, I will walk you through scraping this data via R. Hopefully, in the near future I will be able to replicte this using python. I started with the basics, scraping and plotting standings data. After that, I moved on to getting offensive data and eventually pitching data. None of this would not have been possible without the help of [Baseball Reference](http://www.baseball-reference.com/games/standings.cgi?year=2016&month=8&day=21&submit=Submit+Date). 

### AL West Division Standings

```r
library(XML)
library(plyr)
library(dplyr)
library(reshape2)
library(ggplot2)

date_scrape <- function(y,m,d,div) {
  url <- paste0("http://www.baseball-reference.com/games/standings.cgi?year=",y,"&month=",m, "&day=",d,"&submit=Submit+Date")
  d <- readHTMLTable(url, stringsAsFactors = FALSE)
  d <- as.data.frame(d[div])
  d
}

year <- 2016
month <- 8
day <- 22
div <- 4

date <- paste0(year,"/",month,"/",day)
overall_standings <- date_scrape(year,month,day,div)
```

<!--- [_config.yml]({{ site.baseurl }}/images/Team_Cap_Space.png)
--> 



