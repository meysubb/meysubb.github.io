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
library(reshape2)
dates <- as.data.frame(seq(as.Date("2016/04/03"), as.Date(date), by = "weeks"))
names(dates) <- "dates" 
dates <- colsplit(dates$dates, "-", c("y", "m", "d"))
head(dates)
```
A complete sequence of dates is needed to scrape division standings over the course of time. The rehsape2 package is loaded, it provides the colsplit function to split the dates into three separate columns. This will be useful later, when providing the date scrape function with those dates. 

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
library(plyr)
library(dplyr)
overall_standings <- dates %>% group_by(y,m,d) %>% do(date_scrape(.$y, .$m, .$d, div))
head(overall_standings)
tail(overall_standings)
```

``%>%" passes object on the left hand side as the first argument of the function on the right hand side. It is more commonly known as a pipe. 

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

Looking at the data above, these are not weekly records. Instead they are records as of 8/14 and 8/21. Not to worry, R's dplyr and plyr package will allow us to calculate the weekly records. 

```r
weekly <- week_record %>% 
  group_by(Tm) %>% 
  mutate(Week_W = W - lag(W),Week_L = L - lag(L),RS_Week = RS - lag(RS), RA_Week = RA-lag(RA)) %>%
  na.omit() %>% 
  select(Tm,Week_W,Week_L,RS_Week,RA_Week) %>% 
  mutate(W.L = signif(Week_W / (Week_W + Week_L),digits=3)) %>% 
  mutate(pythW.L. = signif(RS_Week^1.83/(RS_Week^1.83 + RA_Week^1.83),digits=3))

weekly
```

The above chunk of code calculates the weekly division data. First, it takes week_record (as seen above) and groups it by Team. After that, it calculates the following new column values Week Wins, Week Losses, Runs Scored Weekly, Runs Allowed Weekly, Win/Loss % and the Pythagorean Win/Loss %. 

Lets look at Week_W = W - lag(W). Here the Week Wins is calculated by taking the latest W value for a Team and subtracted it from its previous W value for that Team.  For example, take the Rangers (Tex). The two win values are 69 and 73. The Week_W is 73 - 69, resulting in 4 wins this past week. 

The Win/Loss % and Pytahogream Win/Loss % are calculated using the most recent variables created. Before doing so, only the most recently calculated columns must be chosen. This is done so using the select function. The Win/Loss % is calculated using the current Week Win and Week Loss totals. 

The resulting weekly record is:  

```
     Tm Week_W Week_L RS_Week RA_Week   W.L pythW.L.
  (chr)  (dbl)  (dbl)   (dbl)   (dbl) (dbl)    (dbl)
1   TEX      4      2      28      26 0.667    0.534
2   SEA      4      3      38      33 0.571    0.564
3   HOU      3      3      44      42 0.500    0.521
4   OAK      1      5      21      26 0.167    0.404
5   LAA      3      4      21      29 0.429    0.356
```

### AL West Division Plot (Shown by GB)

![_config.yml]({{ site.baseurl }}/images/AL_West.png) 

For those of you curious how to develop a plot like that, I am about to walk you through the steps. First off, we need to scrape the data. This should be fairly simple given then above examples. 

To create the above plot, we will need data per day. To do this, we will need to make a data frame similar to the dates data frame we made before. But this time, we need to sequence over days instead of weeks (see below).

```r
dates2 <- as.data.frame(seq(as.Date("2016/04/03"), as.Date(date), by = "days"))
names(dates2) <- "days" 
dates2 <- colsplit(dates2$days, "-", c("y", "m", "d"))
head(dates2)
```

The code should similar to the chunk shown earlier in this post, with the exception of sequencing over days instead of weeks. 


```
     y m d
1 2016 4 3
2 2016 4 4
3 2016 4 5
4 2016 4 6
5 2016 4 7
6 2016 4 8
```

Once we have the set of dates to scrape against, we use the date scrape function (as shown earlier).

```R
daily_standings <- dates2 %>% group_by(y,m,d) %>% do(date_scrape(.$y, .$m, .$d, 4))
head(daily_standings)
tail(daily_standings)
```

The output from this looks like: 

```
# head(daily_standings)
      y     m     d    Tm     W     L  W.L.    GB    RS    RA pythW.L.
  (int) (int) (int) (chr) (chr) (chr) (chr) (chr) (chr) (chr)    (chr)
1  2016     4     3   SEA     0     0  .000    --     0     0         
2  2016     4     3   TEX     0     0  .000    --     0     0         
3  2016     4     3   HOU     0     0  .000    --     0     0         
4  2016     4     3   OAK     0     0  .000    --     0     0         
5  2016     4     3   LAA     0     0  .000    --     0     0         
6  2016     4     4   TEX     1     0 1.000    --     3     2     .677


# tail(daily_standings)
      y     m     d    Tm     W     L  W.L.    GB    RS    RA pythW.L.
  (int) (int) (int) (chr) (chr) (chr) (chr) (chr) (chr) (chr)    (chr)
1  2016     8    20   LAA    51    72  .415  21.5   548   593     .464
2  2016     8    21   TEX    73    52  .584    --   582   581     .501
3  2016     8    21   SEA    66    57  .537   6.0   579   528     .542
4  2016     8    21   HOU    64    60  .516   8.5   569   523     .538
5  2016     8    21   OAK    53    71  .427  19.5   495   596     .416
6  2016     8    21   LAA    52    72  .419  20.5   550   593     .466
```

Finally before plotting this data, it requires some post-processing. This should be quite fun. So some of the data we scraped from Baseball Reference shows up as character types, and we need them to be in a numeric format. If data isn't in a numeric format, then the plotting library will treat the data in a discrete manner. 

```r
alW_standings_2016 <- ungroup(daily_standings) %>% mutate(Date = paste0(y, sep = "-", m, sep = "-", d)) %>% select(Date, Tm, GB)
alW_standings_2016$GB <- as.numeric(alW_standings_2016$GB) 
alW_standings_2016$Date <- as.Date(alW_standings_2016$Date)
alW_standings_2016$Tm <- as.factor(alW_standings_2016$Tm)
alW_standings_2016$GB <- ifelse(is.na(alW_standings_2016$GB), 0, alW_standings_2016$GB)
```

First, we apply the ungroup function to remove any existing groupings that may exist in the data frame. After that we combine the following columns: y,m,d into an actual date. The select function, selects the columns of interest. The following statements convert each column into different data types. One into a numeric data type, the other into a date type and lastly a factor. Factors are variables that can only take on a limited number of different values, in this case all possible teams. The if else statement, takes all NA's and replaces them with a 0. This applies mainly for the first couple of days where some teams didn't play a game. 

Finally, the moment you have been waiting for creating the plot. First we create a vector with a list of team colors. 

```r
team_colors = c("SEA" = "#01487E", "TEX" = "#0482CC", "HOU" = "#F7742C", "LAA" = "#CA1F2C", "OAK" = "#003300")
library(ggplot2)
ggplot(alW_standings_2016, aes(Date, GB, colour = Tm)) + 
  geom_line(size = 1.25, alpha = .75) + 
  scale_colour_manual(values = team_colors, name = "Team") + 
  scale_y_reverse(breaks = 0:25) + 
  scale_x_date() + 
  geom_text(aes(label=ifelse(Date == "2016-08-21", as.character(GB),'')),hjust=-.5, size = 4, show.legend = FALSE) +
  labs(title = "AL West Race through August 2016") + 
  theme(legend.title = element_text(size = 12)) + 
  theme(legend.text = element_text(size = 12)) + 
  theme(axis.text = element_text(size = 13, face = "bold"), axis.title = element_text(size = 16, color = "grey50", face = "bold"), plot.title = element_text(size = 30, face = "bold", vjust = 1))
```

Both plots in these post were made using the ggplot2 package. I have recently heard about plotly and its interactive features. Hopefully, I will have time to learn and use these later on. 

![_config.yml]({{ site.baseurl }}/images/GO_AL_West.png) 

