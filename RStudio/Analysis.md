# Analyzing the James Bond Film Franchise

We open RStudio, and begin by changing our directory to that which contains the James Bond data.

```R
setwd("C:/Users/Alex/Documents/Data/Movies/Franchise/Bond")
```

Then we load a package to handle Excel files (since I saved my cleaned-up Exif CSV as an Excel ".xlsx" file) and our favorite graph creating package.
```R
install.packages("xlsx") 
install.packages("ggplot2")
require("xlsx")
require("ggplot2")
```

At this point, I have already scraped the data from the individual pages into an [excel spreadsheet](https://github.com/atomaszewicz/Bond/blob/master/Data/jb_raw.xlsx). You can read more about the sources of this data, you can look at the [Data folder](https://github.com/atomaszewicz/Bond/tree/master/Data) or the [README](https://github.com/atomaszewicz/Bond/blob/master/Data/README.md) in the Data folder.

We now bring this data into RStudio, creating seperate variables for the seperate pages of the excel spreadsheet:

```R
#We make data frames with the data from: Box Office Mojo (bom), The Numbers (numb), and the rating websites (rate)
bom<-read.xlsx("jb_clean.xlsx",1)
numb<-read.xlsx("jb_clean.xlsx",2)
rate<-read.xlsx("jb_clean.xlsx",3)
```
To begin, I want to look at how the different sites average ratings relate.

```R
#We make variables for each rating type, as well as the average of the 4, then make a table of the values
avg_rtc<-mean(rate$RT.Crit)
avg_rtu<-mean(rate$RT.User)
avg_Ltbx<-mean(rate$LetterBoxd)
avg_imdb<-mean(rate$IMDB)
avg_all<-mean(c(avg_imdb,avg_Ltbx,avg_rtc,avg_rtu))
avgs<-data.frame(RT.Crit=(avg_rtc),RT.User=(avg_rtu),LetterBoxd=(avg_Ltbx),IMDB=(avg_imdb),Avg.All=(avg_all))
```
Here is what the table looks like:

|RT.Crit|RT.User|LetterBoxd|IMDB|Avg.All|
|---|---|---|---|---|
|70.8|63.8|79.1|68.7|70.585|

