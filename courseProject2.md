---
title: "Reproducible Research Course Project"
output: 
  html_document:
    keep_md: true
    self_contained: true
---
*This project is to explore the NOAA Storm Database and answer some basic questions about severe weather events*
*It involves exploring the U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database. This database tracks characteristics of major storms and weather events in the United States, including when and where they occur, as well as estimates of any fatalities, injuries, and property damage.
*
# Sysnopis
*This is an R Markdown document which is used to analyze the [Storm Data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2) based on the [National Weather Service Storm Data](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf). The document is focus in answering the two questions:*

*     1. Across the United States, which types of events (as indicated in the EVTYPE variable) are most harmful with respect to population health?*

*     2. Across the United States, which types of events have the greatest economic consequences?*

* The basic goal of this document is to explore the NOAA Storm Database and understand some basic concepts about weather events.*

#Loading libraries


```r
library(plyr)
library(ggplot2)
library(scales)
```

#Data processing
#Loading data

```r
url1<- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2FStormData.csv.bz2"

if (!file.exists('repdata-data-StormData.csv')) {
download.file(url1, "repdata-data-StormData.csv.bz2")
}
stormData <- read.csv("repdata-data-StormData.csv.bz2", header = TRUE, stringsAsFactors = FALSE)
```

# Checking data

```r
summary(stormData)
```

```
##     STATE__       BGN_DATE           BGN_TIME          TIME_ZONE        
##  Min.   : 1.0   Length:902297      Length:902297      Length:902297     
##  1st Qu.:19.0   Class :character   Class :character   Class :character  
##  Median :30.0   Mode  :character   Mode  :character   Mode  :character  
##  Mean   :31.2                                                           
##  3rd Qu.:45.0                                                           
##  Max.   :95.0                                                           
##                                                                         
##      COUNTY       COUNTYNAME           STATE              EVTYPE         
##  Min.   :  0.0   Length:902297      Length:902297      Length:902297     
##  1st Qu.: 31.0   Class :character   Class :character   Class :character  
##  Median : 75.0   Mode  :character   Mode  :character   Mode  :character  
##  Mean   :100.6                                                           
##  3rd Qu.:131.0                                                           
##  Max.   :873.0                                                           
##                                                                          
##    BGN_RANGE          BGN_AZI           BGN_LOCATI       
##  Min.   :   0.000   Length:902297      Length:902297     
##  1st Qu.:   0.000   Class :character   Class :character  
##  Median :   0.000   Mode  :character   Mode  :character  
##  Mean   :   1.484                                        
##  3rd Qu.:   1.000                                        
##  Max.   :3749.000                                        
##                                                          
##    END_DATE           END_TIME           COUNTY_END COUNTYENDN    
##  Length:902297      Length:902297      Min.   :0    Mode:logical  
##  Class :character   Class :character   1st Qu.:0    NA's:902297   
##  Mode  :character   Mode  :character   Median :0                  
##                                        Mean   :0                  
##                                        3rd Qu.:0                  
##                                        Max.   :0                  
##                                                                   
##    END_RANGE          END_AZI           END_LOCATI       
##  Min.   :  0.0000   Length:902297      Length:902297     
##  1st Qu.:  0.0000   Class :character   Class :character  
##  Median :  0.0000   Mode  :character   Mode  :character  
##  Mean   :  0.9862                                        
##  3rd Qu.:  0.0000                                        
##  Max.   :925.0000                                        
##                                                          
##      LENGTH              WIDTH                F               MAG         
##  Min.   :   0.0000   Min.   :   0.000   Min.   :0.0      Min.   :    0.0  
##  1st Qu.:   0.0000   1st Qu.:   0.000   1st Qu.:0.0      1st Qu.:    0.0  
##  Median :   0.0000   Median :   0.000   Median :1.0      Median :   50.0  
##  Mean   :   0.2301   Mean   :   7.503   Mean   :0.9      Mean   :   46.9  
##  3rd Qu.:   0.0000   3rd Qu.:   0.000   3rd Qu.:1.0      3rd Qu.:   75.0  
##  Max.   :2315.0000   Max.   :4400.000   Max.   :5.0      Max.   :22000.0  
##                                         NA's   :843563                    
##    FATALITIES          INJURIES            PROPDMG       
##  Min.   :  0.0000   Min.   :   0.0000   Min.   :   0.00  
##  1st Qu.:  0.0000   1st Qu.:   0.0000   1st Qu.:   0.00  
##  Median :  0.0000   Median :   0.0000   Median :   0.00  
##  Mean   :  0.0168   Mean   :   0.1557   Mean   :  12.06  
##  3rd Qu.:  0.0000   3rd Qu.:   0.0000   3rd Qu.:   0.50  
##  Max.   :583.0000   Max.   :1700.0000   Max.   :5000.00  
##                                                          
##   PROPDMGEXP           CROPDMG         CROPDMGEXP       
##  Length:902297      Min.   :  0.000   Length:902297     
##  Class :character   1st Qu.:  0.000   Class :character  
##  Mode  :character   Median :  0.000   Mode  :character  
##                     Mean   :  1.527                     
##                     3rd Qu.:  0.000                     
##                     Max.   :990.000                     
##                                                         
##      WFO             STATEOFFIC         ZONENAMES            LATITUDE   
##  Length:902297      Length:902297      Length:902297      Min.   :   0  
##  Class :character   Class :character   Class :character   1st Qu.:2802  
##  Mode  :character   Mode  :character   Mode  :character   Median :3540  
##                                                           Mean   :2875  
##                                                           3rd Qu.:4019  
##                                                           Max.   :9706  
##                                                           NA's   :47    
##    LONGITUDE        LATITUDE_E     LONGITUDE_       REMARKS         
##  Min.   :-14451   Min.   :   0   Min.   :-14455   Length:902297     
##  1st Qu.:  7247   1st Qu.:   0   1st Qu.:     0   Class :character  
##  Median :  8707   Median :   0   Median :     0   Mode  :character  
##  Mean   :  6940   Mean   :1452   Mean   :  3509                     
##  3rd Qu.:  9605   3rd Qu.:3549   3rd Qu.:  8735                     
##  Max.   : 17124   Max.   :9706   Max.   :106220                     
##                   NA's   :40                                        
##      REFNUM      
##  Min.   :     1  
##  1st Qu.:225575  
##  Median :451149  
##  Mean   :451149  
##  3rd Qu.:676723  
##  Max.   :902297  
## 
```

```r
names(stormData) 
```

```
##  [1] "STATE__"    "BGN_DATE"   "BGN_TIME"   "TIME_ZONE"  "COUNTY"    
##  [6] "COUNTYNAME" "STATE"      "EVTYPE"     "BGN_RANGE"  "BGN_AZI"   
## [11] "BGN_LOCATI" "END_DATE"   "END_TIME"   "COUNTY_END" "COUNTYENDN"
## [16] "END_RANGE"  "END_AZI"    "END_LOCATI" "LENGTH"     "WIDTH"     
## [21] "F"          "MAG"        "FATALITIES" "INJURIES"   "PROPDMG"   
## [26] "PROPDMGEXP" "CROPDMG"    "CROPDMGEXP" "WFO"        "STATEOFFIC"
## [31] "ZONENAMES"  "LATITUDE"   "LONGITUDE"  "LATITUDE_E" "LONGITUDE_"
## [36] "REMARKS"    "REFNUM"
```
* Get subset of the data:* 
* column 8 labeled EVTYPE: Type of Event recorded   *
* Column 23 labeled FATALITIES: Number of fatalities recorded *
* column 24 labeled INJURIES: Number of injuries recorded*
* Column 25 & 26, labeled PROPDMG & PROPDMGEP: Amount of property damage recorded *
* ColumnS 27 & 28 labeled CROPDMG & CROPDMGEXP:Amount of crop damage recorded *


```r
df <- subset(stormData[,c(8,23,24,25,26,27,28)])
code <- c("h","H","k","K","m","M","b","B")
multp <- c(100, 100,1000,1000,1000000,1000000,1000000000,1000000000)
```
#Data Analysis 


```r
df <- mutate(df, PROPDMGx = as.numeric(ifelse(PROPDMGEXP %in% code,multp,0)))
df <- mutate(df, CROPDMGx = as.numeric(ifelse(CROPDMGEXP %in% code,multp,0)))
df$PROPDMGEXP.0 <- df$PROPDMG * df$PROPDMGx
df$CROPDMGEXP.0 <- df$CROPDMG * df$CROPDMGx
df <- mutate(df, HARM = FATALITIES + INJURIES)
df <- mutate(df, EXPENSE = PROPDMGEXP.0 + CROPDMGEXP.0)
```


*With the data processing completed, the impact of the weather events was analyzed using the new summary fields HARM and EXPENSE.*


```r
totalHarmsByEVT<-aggregate(df$HARM, list(df$EVTYPE), sum) 
names(totalHarmsByEVT) <- c("EventType", "TotHarm")
totalHarmsByEVT<-totalHarmsByEVT[order(-totalHarmsByEVT$TotHarm),] # sort descending
topHarms <- subset(totalHarmsByEVT[1:10,])  #top 10
```
*Analyz economic affect *

```r
totalExpensesByEVT<-aggregate(df$EXPENSE, list(df$EVTYPE), sum)
names(totalExpensesByEVT) <- c("EventType", "TotExpense")
totalExpensesByEVT<-totalExpensesByEVT[order(-totalExpensesByEVT$TotExpense),]
```
*subset to top ten events by expense*

```r
topExpenses <- subset(totalExpensesByEVT[1:10,])
topExpenses$TotExpense<-topExpenses[,2]/1000000000
```

# Results
*The damage impacts were plotted for each domain of 'Impact to Human Health' and 'Economic Impact' *
# Health

```r
figureH <- ggplot(topHarms, aes(EventType))
figureH <- figureH + geom_bar(aes(weight = TotHarm, fill = "cyan", col = "black")) +
        theme(axis.text.x = element_text(angle = 90, hjust = 1))+
        scale_y_continuous(label = comma) + 
        ggtitle("Population Health Impact by Event Type") + 
        theme(legend.position = "none") + 
        labs(x = "Event Type", y = "Fatalities + Injuries") 
figureH
```

![](courseProject2_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
#Economic

```r
FigureE <- ggplot(topExpenses, aes(EventType))
FigureE <- FigureE + geom_bar(aes(weight = TotExpense, fill = "white", col="black")) +
        theme(axis.text.x = element_text(angle = 90, hjust = 1))+
        scale_y_continuous(label = comma) + 
        ggtitle("Damage Expense Impact by Event Type") + 
        theme(legend.position = "none") + 
        labs(x = "Event Type", y = "Property & Crop Expense ($Billions)")
FigureE
```

![](courseProject2_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

#Conclusion

According to the data analysis result on U.S. National Oceanic and Atmospheric Administration's (NOAA) storm database from 1950 to 2011, tornados have the greatest impact on both human Health an Economic.

**



