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

In order to plot and see the changes in the division on a weekly or daily basis, we will need to scrape and clean this data. 

```r
dates <- as.data.frame(seq(as.Date("2016/04/03"), as.Date(date), by = "weeks"))
names(dates) <- "dates" 
dates <- colsplit(dates$dates, "-", c("y", "m", "d"))
head(dates)
```
A complete sequence of dates is needed to scrape division standings over the course of time. 

The output is as follows: 

```
     y m  d
1 2016 4  3
2 2016 4 10
3 2016 4 17
4 2016 4 24
5 2016 5  1
6 2016 5  8
```

Using the dates data frame, standings data can be scraped. 

```r
overall_standings <- dates %>% group_by(y,m,d) %>% do(date_scrape(.$y, .$m, .$d, div))
head(overall_standings)
tail(overall_standings)
```

"%>%" passes object on the left hand side as the first argument of the function on the right hand side. It is more commonly known as a pipe. 

The dates data is grouped by year, month, day and then supplied to the date scrape function. The do function is used to iterate the scrape function over all dates (as specified by the data frame).

The output is as follows: 

```
# head(overall_standings)
      y     m     d    Tm     W     L  W.L.    GB    RS    RA pythW.L.
  (int) (int) (int) (chr) (chr) (chr) (chr) (chr) (chr) (chr)    (chr)
1  2016     4     3   SEA     0     0  .000    --     0     0         
2  2016     4     3   TEX     0     0  .000    --     0     0         
3  2016     4     3   HOU     0     0  .000    --     0     0         
4  2016     4     3   OAK     0     0  .000    --     0     0         
5  2016     4     3   LAA     0     0  .000    --     0     0         
6  2016     4    10   OAK     4     3  .571    --    21    20     .522

# tail(overall_standings)
      y     m     d    Tm     W     L  W.L.    GB    RS    RA pythW.L.
  (int) (int) (int) (chr) (chr) (chr) (chr) (chr) (chr) (chr)    (chr)
1  2016     8    14   LAA    49    68  .419  19.0   529   564     .471
2  2016     8    21   TEX    73    52  .584    --   582   581     .501
3  2016     8    21   SEA    66    57  .537   6.0   579   528     .542
4  2016     8    21   HOU    64    60  .516   8.5   569   523     .538
5  2016     8    21   OAK    53    71  .427  19.5   495   596     .416
6  2016     8    21   LAA    52    72  .419  20.5   550   593     .466
```

### Weekly records for the AL West 

The previous lines of code can be furthered to determine the weekly records for the AL West. 

```r
len <- nrow(dates)
first_sunday <- dates$d[len-1]
last_sunday  <- dates$d[len]
week_record <- overall_standings %>% filter(m==month & d>=first_sunday & d<=last_sunday)
week_record[,c(5,6,9,10)] <- sapply(week_record[,c(5,6,9,10)],as.numeric)
week_record #prints week_record
```

The code above, filters the overall_standings data frame for the last two sundays of interest. In this case it filters the previous data between 8/14 and 8/21. The output is as follows. 

```
       y     m     d    Tm     W     L  W.L.    GB    RS    RA pythW.L.
   (int) (int) (int) (chr) (dbl) (dbl) (chr) (chr) (dbl) (dbl)    (chr)
1   2016     8    14   TEX    69    50  .580    --   554   555     .499
2   2016     8    14   SEA    62    54  .534   5.5   541   495     .541
3   2016     8    14   HOU    61    57  .517   7.5   525   481     .540
4   2016     8    14   OAK    52    66  .441  16.5   474   570     .416
5   2016     8    14   LAA    49    68  .419  19.0   529   564     .471
6   2016     8    21   TEX    73    52  .584    --   582   581     .501
7   2016     8    21   SEA    66    57  .537   6.0   579   528     .542
8   2016     8    21   HOU    64    60  .516   8.5   569   523     .538
9   2016     8    21   OAK    53    71  .427  19.5   495   596     .416
10  2016     8    21   LAA    52    72  .419  20.5   550   593     .466
```

Looking at the data above, 

<!--- [_config.yml]({{ site.baseurl }}/images/Team_Cap_Space.png)
--> 



