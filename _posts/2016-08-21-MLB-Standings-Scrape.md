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

date_scrape <- function(y,m,d,div) {
  url <- paste0("http://www.baseball-reference.com/games/standings.cgi?year=",y,"&month=",m, "&day=",d,"&submit=Submit+Date")
  d <- readHTMLTable(url, stringsAsFactors = FALSE)
  d <- as.data.frame(d[div])
  d
}
```

The XML library is loaded to scrape data using R. The date scrape function automates scraping MLB division data from Baseball Reference. First the function, determines the url for baseball reference based on the date. After which, it scrapes all HTML tables. Finally, it selects the appropriate division from all the tables. For quick reference

| Division Name | Number        | 
| :-----------: |:-------------:| 
| AL East       | 2 | 
| AL Central    | 3 |  
| AL West       | 4 | 
| NL East       | 5 |
| NL Central    | 6 | 
| NL West       | 7 |    


```r
year <- 2016
month <- 8
day <- 22
div <- 4

date <- paste0(year,"/",month,"/",day)
overall_standings <- date_scrape(year,month,day,div)
```

The output is as follows: 

```
# AL West 

   Tm  W  L W.L.   GB  RS  RA pythW.L.
1 TEX 73 52 .584   -- 582 581     .501
2 SEA 66 57 .537  6.0 579 528     .542
3 HOU 64 60 .516  8.5 569 523     .538
4 OAK 53 71 .427 19.5 495 596     .416
5 LAA 52 72 .419 20.5 550 593     .466
```

<!--- [_config.yml]({{ site.baseurl }}/images/Team_Cap_Space.png)
--> 



