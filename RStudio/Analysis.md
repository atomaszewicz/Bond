# Analyzing the James Bond Film Franchise

We open RStudio, and begin by changing our directory to that which contains the James Bond data.

```R
setwd("C:/Users/Alex/Documents/Data/Movies/Franchise/Bond")
```

Then we load a package to handle Excel files (since I saved my cleaned-up Exif CSV as an Excel ".xlsx" file), our favorite graph creating package and a tool to reshape our data.
```R
install.packages("xlsx") 
install.packages("ggplot2")
install.packages("reshape2")
require("xlsx")
require("ggplot2")
require("reshape2")
```

At this point, I have already scraped the data from the individual pages into an [excel spreadsheet](https://github.com/atomaszewicz/Bond/blob/master/Data/jb_raw.xlsx). You can read more about the sources of this data, you can look at the [Data folder](https://github.com/atomaszewicz/Bond/tree/master/Data) or the [README](https://github.com/atomaszewicz/Bond/blob/master/Data/README.md) in the Data folder.

We now bring this data into RStudio, creating seperate variables for the seperate pages of the excel spreadsheet:

```R
#We make data frames with the data from: Box Office Mojo (bom), The Numbers (numb), and the rating websites (rate)
bom<-read.xlsx("jb_clean.xlsx",1)
numb<-read.xlsx("jb_clean.xlsx",2)
rate<-read.xlsx("jb_clean.xlsx",3)
```

## Ratings
First let's look at how the different sites average ratings relate.

```R
#We make variables for each rating type, as well as the average of the 4, then make a table of the values
#Note that all the ratings have been set to be out of 100
avg_rtc<-mean(rate$RT.Crit)
avg_rtu<-mean(rate$RT.User)
avg_Ltbx<-mean(rate$LetterBoxd)
avg_imdb<-mean(rate$IMDB)
avg_all<-mean(c(avg_imdb,avg_Ltbx,avg_rtc,avg_rtu))
avgs<-data.frame(RT.Crit=(avg_rtc),RT.User=(avg_rtu),LetterBoxd=(avg_Ltbx),IMDB=(avg_imdb),Avg.All=(avg_all))
```
Now we wish to visualize this:

```R
#Since our 'avgs' data frame is in table form, we create a new, transposed, dataframe using 'melt()' from the reshape2 library
avgs_t<-melt(avgs)
avg_rating_plot<-ggplot(avgs_t,aes(x=variable,y=value))+geom_col()+coord_cartesian(ylim=c(60,80))
labels<-xlab("Rating Metric")+ylab("Average Score/100")+ggtitle("Bond Film Average Rating by Metric")
```
![avg_rating_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/avg_rating.png)

The average for all the Bond films, based on the 4 metrics of choice is 71% which translates to a 3.5/5 star rating or in American Colleges, a C- or 1.67/4.00 GPA. 

It seems that Rotten Tomatoe users think lower of these films than users do critics, with the average user rating being 9% less than the critic ratings. This is an interesting result because in an intuitive sense I expected critics to be more ... critical of movies than your average Joe Rotten Tomatoes. This theory is reinforced by a FiveThirtyEight [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) where they compare online movie ratings for ~200 titles. In this article Rotten Tomatoes user ratings were on average 19% higher <sup>[1]</sup> than Rotten Tomatoes critic scores. On the other hand, critics probably better understand the place of the James Bond movies while our average theater-goer is keeping their score of Citizen Kane or Lawrence of Arabia in mind when they pencil in their rating for the 007 films.

Now let's see how individual films were rated. 

```R
#We reshape the 'rate' dataframe to have all the numerical ratings in one column
rate_1col<-melt(rate,id="Title")
colnames(rate_1col)[c(1,2,3)]<-c("Title","Metric","Rating")
#Now we find the row information for the max/min value in the 'Rating' column
rate1_col[which.max(rate_1col$Rating),]
[1]            Title     Metric Rating
[1] 72 Casino Royale LetterBoxd   97.5

rate1_col[which.min(rate_1col$Rating),]
[1]               Title  Metric Rating
[1] 15 A View to a Kill RT.Crit     36
```
So the minimum score of all 4 metrics is 36/100 from Rotten Tomatoes critics for the Bond film "A View to a Kill" and the maxium score is 97.5 from LetterBoxd for "Casino Royale". It is not surprising that LetterBoxd has the highest rated film since it's average rating was the highest at 79.1 (12% higher than the average of our 4 metrics). RT critic score was actually the second highest among our 4 metrics with an average rating of 70.76, but evidently "A View to a Kill" was a weak entry in the series according to critics. 

We now check what is the highest/lowest rated films on average:
```R
rate[which.max(rate$Avg.Dumb),]
[1]   RT.Crit RT.User LetterBoxd IMDB Avg.Dumb         Title
[1]22      95      89       97.5   80   90.375 Casino Royale

rate[which.min(rate$Avg.Dumb),]
[1]    RT.Crit RT.User LetterBoxd IMDB Avg.Dumb            Title
[1] 15      36      41       67.5   63   51.875 A View to a Kill
```
An unsuprising result, the films with the highest and lowest average scores are also the ones with the highest and lowest individual ratings. Since it's only an average over 4 the high/low rating will drag up/down the rating significantly, not to mention that each individual rating is presumed a trustworthy metric on the quality of the film. 



 


Footnotes:
<sup>[1]</sup> : In the FiveThirtyEight [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) I referenced, the point of interest is this paragraph: "The ratings from IMDb, Metacritic and Rotten Tomatoes were typically in the same ballpark, which makes this finding unsurprising: Fandango’s star rating was higher than the IMDb rating 79 percent of the time, the Metacritic aggregate critic score 77 percent of the time, the Metacritic user score 86 percent of the time, the Rotten Tomatoes critic score 62 percent of the time, and the Rotten Tomatoes user score 74 percent of the time." Therefore to see how much higher user scores are than the critics scores, we simply divide the two averages to eliminate the Fandango term: RT.Crit / RT. User =1.19 which gives us our quoted 19%. 

If we wish to continue this analysis:

IMDb vs. RT.Crit: Our metric suggests RT critics rate Bond films 3% higher than IMDb while FiveThirtyEight says that RT critcs scores are 21% lower.

IMDb vs. RT.User: Our metric says RT Users scores are 7% lower than IMDb and FiveThirtyEight puts this number at 6%.
