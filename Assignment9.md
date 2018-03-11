---
title: "Assignment 9"
author: "Jonathan Flores"
date: "March 6, 2018"
output: 
  html_document:
    keep_md: true
---



## Harry Potter Cast

Question 1 - In the IMDB, there are listings of full cast members for movies. Navigate to http://www.imdb.com/title/tt1201607/fullcredits?ref_=tt_ql_1. Feel free to View Source to get a good idea of what the page looks like in code.


```r
url <- 'http://www.imdb.com/title/tt1201607/fullcredits?ref_=tt_ql_1'

## Loading Library to read html

library(rvest)
```

```
## Warning: package 'rvest' was built under R version 3.4.3
```

```
## Loading required package: xml2
```

```
## Warning: package 'xml2' was built under R version 3.4.3
```

```r
## Assign URL to variable

webpage <- read_html(url)
```


## Question 1.b

Scrape the page with any R package that makes things easy for you. Of particular interest is the table of the Cast in order of crediting. Please scrape this table (you might have to fish it out of several of the tables from the page) and make it a data.frame() of the Cast in your R environment



```r
## Load respective libraries: magrittr to extract tables within html format, kntir to format tables in html and stringr to parse the words within a column


library(magrittr)
```

```
## Warning: package 'magrittr' was built under R version 3.4.3
```

```r
library(knitr)
library(stringr)
```

```
## Warning: package 'stringr' was built under R version 3.4.3
```

```r
## Review the nodes on the webpage

nodestable <- html_nodes(webpage,'table')
```


## Question 1.c - d

Clean up the table
. It should not have blank observations or rows, a row that should be column names, or just '.'
. It should have intuitive column names (ideally 2 to start - Actor and Character)
. In the film, Mr. Warwick plays two characters, which makes his row look a little weird. Please replace his character column with just "Griphook / Professor Filius Flitwick" to make it look better. . One row might result in "Rest of cast listed alphabetically" - remove this observation.



```r
## Drill down to the relevant table and naming it castlist

castall <- html_table(webpage)%>%
  extract2(3)

## Removing blanks from cast

castlist <- castall$X2[-1]

## Removing "Rest of cast listed alphabetically"

castlist2 <- castall$X2[-93]

## Extracting First Name - Raw

ActorName1 <- word(castlist2,1)
FirstName <-ActorName1[-1]

## Extracting Surname Name - Raw

ActorName2 <- word(castlist2,2)
Surname <-ActorName2[-1]


## Binding Names

ActorNames <- cbind(FirstName,Surname)

## Name Corrections for those Actors that have 3 names

ActorNames[12,1] <- 'Helena Bonham'
ActorNames[12,2] <- 'Carter'
ActorNames[36,1] <- 'Sian Grace'
ActorNames[36,2] <- 'Phillips'
ActorNames[78,1] <- 'Peter G'
ActorNames[78,2] <- 'Reed'
ActorNames[81,1] <- 'Bob Ives Van'
ActorNames[81,2] <- 'Hellenberg Hubar'
ActorNames[86,1] <- 'Daphne'
ActorNames[86,2] <- 'de Beistegui'
ActorNames[95,1] <- 'Michael Henbury'
ActorNames[95,2] <- 'Ballan'
ActorNames[106,1] <- 'Gioachinno Jim'
ActorNames[106,2] <- 'BCuffaro'
ActorNames[111,1] <- 'Grace Meurisse'
ActorNames[111,2] <- 'Francis'
ActorNames[112,1] <- 'Sean Francis'
ActorNames[112,2] <- 'George'
ActorNames[134,1] <- 'Sara Jane'
ActorNames[134,2] <- "O'Neill"


## Display First Section of the Table

colnames(ActorNames,c("FirstName","Surname"))
```

[1] "FirstName" "Surname"  

```r
knitr::kable(head(ActorNames,12))
```



FirstName       Surname   
--------------  ----------
Ralph           Fiennes   
Michael         Gambon    
Alan            Rickman   
Daniel          Radcliffe 
Rupert          Grint     
Emma            Watson    
Evanna          Lynch     
Domhnall        Gleeson   
Clémence        Poésy     
Warwick         Davis     
John            Hurt      
Helena Bonham   Carter    

```r
## Assigning character and removing blanks

characterraw <- castall$X4[-1]

## Removing "Rest of cast listed alphabetically"

Character <- characterraw[-92]
knitr::kable(head(Character))
```



|x                          |
|:--------------------------|
|Lord Voldemort             |
|Professor Albus Dumbledore |
|Professor Severus Snape    |
|Harry Potter               |
|Ron Weasley                |
|Hermione Granger           |

```r
## Extracting First and Second of Character Field

CastTable <- cbind(ActorNames,Character)

## Fix Records

CastTable[10,3] <- "Griphook/Professor Filius Flitwick"
CastTable[22,3] <- "Hogsmeade Death Eater as Benjamin Northover"
CastTable[33,3] <- "Dean Thomas as Alfred Enoch"
CastTable[69,3] <- "The sorting hat voice"
CastTable[87,3] <- "Lilly Potter 19 years later"
CastTable[88,3] <- "James Potter 19 years later"
```

## Question 1.e

Present the first 10 rows of the data.frame() - It should have only FirstName, Surname, and Character columns.



```r
knitr::kable(head(CastTable,10))
```



FirstName   Surname     Character                          
----------  ----------  -----------------------------------
Ralph       Fiennes     Lord Voldemort                     
Michael     Gambon      Professor Albus Dumbledore         
Alan        Rickman     Professor Severus Snape            
Daniel      Radcliffe   Harry Potter                       
Rupert      Grint       Ron Weasley                        
Emma        Watson      Hermione Granger                   
Evanna      Lynch       Luna Lovegood                      
Domhnall    Gleeson     Bill Weasley                       
Clémence    Poésy       Fleur Delacour                     
Warwick     Davis       Griphook/Professor Filius Flitwick 

## SportsBall 2.a and 2.b

On the ESPN website, there are statistics of each NBA player. Navigate to the San Antonio Spurs current statistics (likely http://www.espn.com/nba/team/stats/_/name/sa/san-antonio-spurs) 
You are interested in the Shooting Statistics table.


```r
spursurl <- 'http://www.espn.com/nba/team/stats/_/name/sa/san-antonio-spurs'

webpagespurs <- read_html(spursurl)

spursnodestable <- html_nodes(webpagespurs,'table')

shootingtable <- html_table(webpagespurs,header =TRUE )%>%
  extract2(2)

names(shootingtable)[1:15] <- c("PLAYER", "FGM","FGA","FG%","3PM","3PA","3P%","FTM","FTA","FT%","2PM","2PA","2P%","PPS","AFG%")


nbashooting=shootingtable[-1,]

knitr::kable(nbashooting)
```

     PLAYER                  FGM    FGA    FG%    3PM   3PA    3P%    FTM    FTA    FT%    2PM    2PA    2P%    PPS     AFG% 
---  ----------------------  -----  -----  -----  ----  -----  -----  -----  -----  -----  -----  -----  -----  ------  -----
2    LaMarcus Aldridge, PF   8.7    17.5   .498   0.4   1.3    .313   4.4    5.2    0.83   8.3    16.2   .513   1.268   0.51 
3    Kawhi Leonard, SF       5.8    12.3   .468   1.2   3.9    .314   3.4    4.2    0.82   4.6    8.4    .539   1.315   0.52 
4    Rudy Gay, SF            4.3    9.1    .473   0.7   2.0    .325   2.0    2.6    0.78   3.6    7.1    .515   1.241   0.51 
5    Pau Gasol, C            3.9    8.6    .459   0.7   1.7    .387   2.1    2.8    0.76   3.2    6.9    .477   1.243   0.50 
6    Patty Mills, PG         3.3    8.0    .417   1.8   4.8    .381   1.2    1.3    0.88   1.5    3.2    .473   1.211   0.53 
7    Manu Ginobili, SG       3.2    7.3    .439   1.0   3.1    .327   1.5    1.8    0.86   2.2    4.2    .518   1.225   0.51 
8    Danny Green, SG         3.2    8.0    .407   1.7   4.5    .382   0.6    0.8    0.78   1.5    3.5    .439   1.109   0.51 
9    Tony Parker, PG         3.8    7.8    .485   0.1   0.7    .192   1.0    1.5    0.69   3.7    7.1    .513   1.118   0.49 
10   Kyle Anderson, SF       3.2    6.2    .518   0.2   0.7    .275   1.6    2.1    0.73   3.0    5.5    .548   1.316   0.53 
11   Dejounte Murray, PG     3.2    7.2    .440   0.1   0.4    .261   1.1    1.6    0.73   3.1    6.8    .450   1.051   0.45 
12   Bryn Forbes, SG         2.8    6.6    .418   1.2   3.2    .383   0.5    0.9    0.61   1.6    3.4    .449   1.097   0.51 
13   Davis Bertans, C        2.3    5.2    .443   1.3   3.6    .371   0.6    0.8    0.82   1.0    1.6    .604   1.265   0.57 
14   Joffrey Lauvergne, C    2.0    3.7    .533   0.0   0.1    .000   0.6    1.0    0.65   2.0    3.6    .549   1.234   0.53 
15   Derrick White, PG       0.8    1.6    .480   0.3   0.5    .500   0.8    1.1    0.67   0.5    1.1    .471   1.600   0.56 
16   Brandon Paul, SG        0.9    2.0    .445   0.3   0.9    .286   0.4    0.6    0.61   0.6    1.1    .574   1.200   0.51 
17   Darrun Hilliard, SG     0.4    1.4    .263   0.0   0.4    .000   0.4    0.5    0.86   0.4    1.0    .385   .842    0.26 
18   Matt Costello, SF       0.3    1.0    .333   0.0   0.0    .000   0.0    0.0    0.00   0.3    1.0    .333   .667    0.33 
19   Totals                  39.0   84.9   .459   8.6   24.3   .355   15.7   20.3   .775   30.3   60.5   .501   1.21    0.51 


## Question 2.c

Clean up the table (You might get some warnings if you're working with tibbles)
. You'll want to create an R data.frame() with one observation for each player. Make sure that you do not accidentally include blank rows, a row of column names, or the Totals row in the table as observations.
. The column PLAYER has two variables of interest in it: the player's name and their position, denoted by 1-2 letters after their name. Split the cells into two columns, one with Name and the other Position.
. Check the data type of all columns. Convert relevant columns to numeric. Check the data type of all columns again to confirm that they have changed!


```r
## REMOVING THE COMMA FROM THE PLAYER COLUMN

PLAYER <- gsub("[[:punct:]]", "", nbashooting$PLAYER)

## SEPARATING THE PLAYER COLUMN INTO TWO COUMNS: NAME AND POSITION

NAME <- word(PLAYER,1,2)
POSITION <- word(PLAYER,3)

## FIXING RECORDS

NAME[18]<-"TOTALS"
POSITION[18]<-" "

## CREATING NEW DATA FRAME AND BINDING THE 2 NEW CREATED COLUMNS

playerandposition <- cbind(NAME,POSITION)
nbashooting1 <- nbashooting[-1]
nbashooting1 <- cbind(playerandposition,nbashooting1)
knitr::kable(nbashooting1)
```

     NAME                POSITION   FGM    FGA    FG%    3PM   3PA    3P%    FTM    FTA    FT%    2PM    2PA    2P%    PPS     AFG% 
---  ------------------  ---------  -----  -----  -----  ----  -----  -----  -----  -----  -----  -----  -----  -----  ------  -----
2    LaMarcus Aldridge   PF         8.7    17.5   .498   0.4   1.3    .313   4.4    5.2    0.83   8.3    16.2   .513   1.268   0.51 
3    Kawhi Leonard       SF         5.8    12.3   .468   1.2   3.9    .314   3.4    4.2    0.82   4.6    8.4    .539   1.315   0.52 
4    Rudy Gay            SF         4.3    9.1    .473   0.7   2.0    .325   2.0    2.6    0.78   3.6    7.1    .515   1.241   0.51 
5    Pau Gasol           C          3.9    8.6    .459   0.7   1.7    .387   2.1    2.8    0.76   3.2    6.9    .477   1.243   0.50 
6    Patty Mills         PG         3.3    8.0    .417   1.8   4.8    .381   1.2    1.3    0.88   1.5    3.2    .473   1.211   0.53 
7    Manu Ginobili       SG         3.2    7.3    .439   1.0   3.1    .327   1.5    1.8    0.86   2.2    4.2    .518   1.225   0.51 
8    Danny Green         SG         3.2    8.0    .407   1.7   4.5    .382   0.6    0.8    0.78   1.5    3.5    .439   1.109   0.51 
9    Tony Parker         PG         3.8    7.8    .485   0.1   0.7    .192   1.0    1.5    0.69   3.7    7.1    .513   1.118   0.49 
10   Kyle Anderson       SF         3.2    6.2    .518   0.2   0.7    .275   1.6    2.1    0.73   3.0    5.5    .548   1.316   0.53 
11   Dejounte Murray     PG         3.2    7.2    .440   0.1   0.4    .261   1.1    1.6    0.73   3.1    6.8    .450   1.051   0.45 
12   Bryn Forbes         SG         2.8    6.6    .418   1.2   3.2    .383   0.5    0.9    0.61   1.6    3.4    .449   1.097   0.51 
13   Davis Bertans       C          2.3    5.2    .443   1.3   3.6    .371   0.6    0.8    0.82   1.0    1.6    .604   1.265   0.57 
14   Joffrey Lauvergne   C          2.0    3.7    .533   0.0   0.1    .000   0.6    1.0    0.65   2.0    3.6    .549   1.234   0.53 
15   Derrick White       PG         0.8    1.6    .480   0.3   0.5    .500   0.8    1.1    0.67   0.5    1.1    .471   1.600   0.56 
16   Brandon Paul        SG         0.9    2.0    .445   0.3   0.9    .286   0.4    0.6    0.61   0.6    1.1    .574   1.200   0.51 
17   Darrun Hilliard     SG         0.4    1.4    .263   0.0   0.4    .000   0.4    0.5    0.86   0.4    1.0    .385   .842    0.26 
18   Matt Costello       SF         0.3    1.0    .333   0.0   0.0    .000   0.0    0.0    0.00   0.3    1.0    .333   .667    0.33 
19   TOTALS                         39.0   84.9   .459   8.6   24.3   .355   15.7   20.3   .775   30.3   60.5   .501   1.21    0.51 

## Question 2.d

Create a colorful bar chart that shows the Field Goals Percentage Per Game for each person. It will be graded on the following criteria.
. Informative Title, centered
. Relevant x and y axis labels (not simply variables names!)
. Human-readable axes with no overlap (you might have to flip x and y to fix that). Note: You do not have to convert the decimal to a percentage.
. Color the columns by the team member's position (so, all PF's should have the same color, etc.)



```r
## Loading libraries

library(plotly)
```

```
## Warning: package 'plotly' was built under R version 3.4.3
```

```
## Loading required package: ggplot2
```

```
## Warning: package 'ggplot2' was built under R version 3.4.3
```

```
## 
## Attaching package: 'plotly'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
```

```
## The following object is masked from 'package:stats':
## 
##     filter
```

```
## The following object is masked from 'package:graphics':
## 
##     layout
```

```r
library(rlang)
```

```
## Warning: package 'rlang' was built under R version 3.4.3
```

```
## 
## Attaching package: 'rlang'
```

```
## The following object is masked from 'package:magrittr':
## 
##     set_names
```

```
## The following object is masked from 'package:xml2':
## 
##     as_list
```

```r
## Removing Totals row to create chart

nbashooting2 <-nbashooting1[-18,]
knitr::kable(nbashooting2)
```

     NAME                POSITION   FGM   FGA    FG%    3PM   3PA   3P%    FTM   FTA   FT%    2PM   2PA    2P%    PPS     AFG% 
---  ------------------  ---------  ----  -----  -----  ----  ----  -----  ----  ----  -----  ----  -----  -----  ------  -----
2    LaMarcus Aldridge   PF         8.7   17.5   .498   0.4   1.3   .313   4.4   5.2   0.83   8.3   16.2   .513   1.268   0.51 
3    Kawhi Leonard       SF         5.8   12.3   .468   1.2   3.9   .314   3.4   4.2   0.82   4.6   8.4    .539   1.315   0.52 
4    Rudy Gay            SF         4.3   9.1    .473   0.7   2.0   .325   2.0   2.6   0.78   3.6   7.1    .515   1.241   0.51 
5    Pau Gasol           C          3.9   8.6    .459   0.7   1.7   .387   2.1   2.8   0.76   3.2   6.9    .477   1.243   0.50 
6    Patty Mills         PG         3.3   8.0    .417   1.8   4.8   .381   1.2   1.3   0.88   1.5   3.2    .473   1.211   0.53 
7    Manu Ginobili       SG         3.2   7.3    .439   1.0   3.1   .327   1.5   1.8   0.86   2.2   4.2    .518   1.225   0.51 
8    Danny Green         SG         3.2   8.0    .407   1.7   4.5   .382   0.6   0.8   0.78   1.5   3.5    .439   1.109   0.51 
9    Tony Parker         PG         3.8   7.8    .485   0.1   0.7   .192   1.0   1.5   0.69   3.7   7.1    .513   1.118   0.49 
10   Kyle Anderson       SF         3.2   6.2    .518   0.2   0.7   .275   1.6   2.1   0.73   3.0   5.5    .548   1.316   0.53 
11   Dejounte Murray     PG         3.2   7.2    .440   0.1   0.4   .261   1.1   1.6   0.73   3.1   6.8    .450   1.051   0.45 
12   Bryn Forbes         SG         2.8   6.6    .418   1.2   3.2   .383   0.5   0.9   0.61   1.6   3.4    .449   1.097   0.51 
13   Davis Bertans       C          2.3   5.2    .443   1.3   3.6   .371   0.6   0.8   0.82   1.0   1.6    .604   1.265   0.57 
14   Joffrey Lauvergne   C          2.0   3.7    .533   0.0   0.1   .000   0.6   1.0   0.65   2.0   3.6    .549   1.234   0.53 
15   Derrick White       PG         0.8   1.6    .480   0.3   0.5   .500   0.8   1.1   0.67   0.5   1.1    .471   1.600   0.56 
16   Brandon Paul        SG         0.9   2.0    .445   0.3   0.9   .286   0.4   0.6   0.61   0.6   1.1    .574   1.200   0.51 
17   Darrun Hilliard     SG         0.4   1.4    .263   0.0   0.4   .000   0.4   0.5   0.86   0.4   1.0    .385   .842    0.26 
18   Matt Costello       SF         0.3   1.0    .333   0.0   0.0   .000   0.0   0.0   0.00   0.3   1.0    .333   .667    0.33 

```r
## Chart creation

plotly::plot_ly(x=nbashooting2$"FG%",y=nbashooting2$NAME,type = 'bar',orientation='h',color = nbashooting2$POSITION)%>%
  layout(title="Field Goals Percentage per Game",xaxis=list(title="Field Goals Percentage per Game",yaxis=list(title="NBA Players")),font=list(size=8))
```

```
## Warning: package 'bindrcpp' was built under R version 3.4.3
```

<!--html_preserve--><div id="28b841567c2c" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="28b841567c2c">{"x":{"visdat":{"28b87f07857":["function () ","plotlyVisDat"]},"cur_data":"28b87f07857","attrs":{"28b87f07857":{"x":[".498",".468",".473",".459",".417",".439",".407",".485",".518",".440",".418",".443",".533",".480",".445",".263",".333"],"y":["LaMarcus Aldridge","Kawhi Leonard","Rudy Gay","Pau Gasol","Patty Mills","Manu Ginobili","Danny Green","Tony Parker","Kyle Anderson","Dejounte Murray","Bryn Forbes","Davis Bertans","Joffrey Lauvergne","Derrick White","Brandon Paul","Darrun Hilliard","Matt Costello"],"orientation":"h","color":["PF","SF","SF","C","PG","SG","SG","PG","SF","PG","SG","C","C","PG","SG","SG","SF"],"alpha":1,"sizes":[10,100],"type":"bar"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"title":"Field Goals Percentage per Game","xaxis":{"domain":[0,1],"title":"Field Goals Percentage per Game","yaxis":{"title":"NBA Players"},"type":"category","categoryorder":"array","categoryarray":[".263",".333",".407",".417",".418",".439",".440",".443",".445",".459",".468",".473",".480",".485",".498",".518",".533"]},"font":{"size":8},"yaxis":{"domain":[0,1],"type":"category","categoryorder":"array","categoryarray":["Brandon Paul","Bryn Forbes","Danny Green","Darrun Hilliard","Davis Bertans","Dejounte Murray","Derrick White","Joffrey Lauvergne","Kawhi Leonard","Kyle Anderson","LaMarcus Aldridge","Manu Ginobili","Matt Costello","Patty Mills","Pau Gasol","Rudy Gay","Tony Parker","TOTALS"]},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":[{"name":"Collaborate","icon":{"width":1000,"ascent":500,"descent":-50,"path":"M487 375c7-10 9-23 5-36l-79-259c-3-12-11-23-22-31-11-8-22-12-35-12l-263 0c-15 0-29 5-43 15-13 10-23 23-28 37-5 13-5 25-1 37 0 0 0 3 1 7 1 5 1 8 1 11 0 2 0 4-1 6 0 3-1 5-1 6 1 2 2 4 3 6 1 2 2 4 4 6 2 3 4 5 5 7 5 7 9 16 13 26 4 10 7 19 9 26 0 2 0 5 0 9-1 4-1 6 0 8 0 2 2 5 4 8 3 3 5 5 5 7 4 6 8 15 12 26 4 11 7 19 7 26 1 1 0 4 0 9-1 4-1 7 0 8 1 2 3 5 6 8 4 4 6 6 6 7 4 5 8 13 13 24 4 11 7 20 7 28 1 1 0 4 0 7-1 3-1 6-1 7 0 2 1 4 3 6 1 1 3 4 5 6 2 3 3 5 5 6 1 2 3 5 4 9 2 3 3 7 5 10 1 3 2 6 4 10 2 4 4 7 6 9 2 3 4 5 7 7 3 2 7 3 11 3 3 0 8 0 13-1l0-1c7 2 12 2 14 2l218 0c14 0 25-5 32-16 8-10 10-23 6-37l-79-259c-7-22-13-37-20-43-7-7-19-10-37-10l-248 0c-5 0-9-2-11-5-2-3-2-7 0-12 4-13 18-20 41-20l264 0c5 0 10 2 16 5 5 3 8 6 10 11l85 282c2 5 2 10 2 17 7-3 13-7 17-13z m-304 0c-1-3-1-5 0-7 1-1 3-2 6-2l174 0c2 0 4 1 7 2 2 2 4 4 5 7l6 18c0 3 0 5-1 7-1 1-3 2-6 2l-173 0c-3 0-5-1-8-2-2-2-4-4-4-7z m-24-73c-1-3-1-5 0-7 2-2 3-2 6-2l174 0c2 0 5 0 7 2 3 2 4 4 5 7l6 18c1 2 0 5-1 6-1 2-3 3-5 3l-174 0c-3 0-5-1-7-3-3-1-4-4-5-6z"},"click":"function(gd) { \n        // is this being viewed in RStudio?\n        if (location.search == '?viewer_pane=1') {\n          alert('To learn about plotly for collaboration, visit:\\n https://cpsievert.github.io/plotly_book/plot-ly-for-collaboration.html');\n        } else {\n          window.open('https://cpsievert.github.io/plotly_book/plot-ly-for-collaboration.html', '_blank');\n        }\n      }"}],"cloud":false},"data":[{"x":[".459",".443",".533"],"y":["Pau Gasol","Davis Bertans","Joffrey Lauvergne"],"orientation":"h","type":"bar","name":"C","marker":{"fillcolor":"rgba(252,141,98,0.5)","color":"rgba(252,141,98,1)","line":{"color":"transparent"}},"xaxis":"x","yaxis":"y","frame":null},{"x":[".498"],"y":["LaMarcus Aldridge"],"orientation":"h","type":"bar","name":"PF","marker":{"fillcolor":"rgba(141,160,203,0.5)","color":"rgba(141,160,203,1)","line":{"color":"transparent"}},"xaxis":"x","yaxis":"y","frame":null},{"x":[".417",".485",".440",".480"],"y":["Patty Mills","Tony Parker","Dejounte Murray","Derrick White"],"orientation":"h","type":"bar","name":"PG","marker":{"fillcolor":"rgba(231,138,195,0.5)","color":"rgba(231,138,195,1)","line":{"color":"transparent"}},"xaxis":"x","yaxis":"y","frame":null},{"x":[".468",".473",".518",".333"],"y":["Kawhi Leonard","Rudy Gay","Kyle Anderson","Matt Costello"],"orientation":"h","type":"bar","name":"SF","marker":{"fillcolor":"rgba(166,216,84,0.5)","color":"rgba(166,216,84,1)","line":{"color":"transparent"}},"xaxis":"x","yaxis":"y","frame":null},{"x":[".439",".407",".418",".445",".263"],"y":["Manu Ginobili","Danny Green","Bryn Forbes","Brandon Paul","Darrun Hilliard"],"orientation":"h","type":"bar","name":"SG","marker":{"fillcolor":"rgba(255,217,47,0.5)","color":"rgba(255,217,47,1)","line":{"color":"transparent"}},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1}},"base_url":"https://plot.ly"},"evals":["config.modeBarButtonsToAdd.0.click"],"jsHooks":{"render":[{"code":"function(el, x) { var ctConfig = crosstalk.var('plotlyCrosstalkOpts').set({\"on\":\"plotly_click\",\"persistent\":false,\"dynamic\":false,\"selectize\":false,\"opacityDim\":0.2,\"selected\":{\"opacity\":1}}); }","data":null}]}}</script><!--/html_preserve-->


