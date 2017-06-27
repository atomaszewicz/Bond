# Analyzing the James Bond Film Franchise

## Set up

The data from our online rating sites, and our two box office figure websites all have their own page in our [excel spreadsheet](https://github.com/atomaszewicz/Bond/blob/master/Data/jb_raw.xlsx). To read more about the sources of this data, you can look at the [Data folder](https://github.com/atomaszewicz/Bond/tree/master/Data) or the [README](https://github.com/atomaszewicz/Bond/blob/master/Data/README.md) in the Data folder.

We are going to analyze these excel sheets in RStudio, so we will need to turn them into R dataframes. To handle the '.xlsx' excel file we load a package to bring Excel files into RStudio. We also load my favorite plotting package and a package to reshape data before we forget.


```R
install.packages("xlsx") 
install.packages("ggplot2")
install.packages("reshape2")
require("xlsx")
require("ggplot2")
require("reshape2")
```

The data from our online rating sites, and our two box office figure websites all have their own page in our[excel spreadsheet](https://github.com/atomaszewicz/Bond/blob/master/Data/jb_raw.xlsx). To read more about the sources of this data, you can look at the [Data folder](https://github.com/atomaszewicz/Bond/tree/master/Data) or the [README](https://github.com/atomaszewicz/Bond/blob/master/Data/README.md) in the Data folder.

We now bring this data into RStudio, creating seperate dataframes for the seperate pages of the excel spreadsheet:

```R
#We make dataframes with the data from: Box Office Mojo (bom), The Numbers (numb), and the rating websites (rate)
bom<-read.xlsx("jb_clean.xlsx",1)
numb<-read.xlsx("jb_clean.xlsx",2)
rate<-read.xlsx("jb_clean.xlsx",3)
```

*Note: Before we begin I must acknowledge a typo that was found at the end of the Rating section (thanks for spotting it Brad). The actor of the film *On Her Majesty's Secret Service* is named "George Lazenby", not "George Lazen" as I erronously typed it. This typo remains in the Rating section, but has been fixed for the Box Office sections and beyond. I apologize for any confusion.*

## Ratings

Let's take a look at the online ratings first. The 4 metrics we used are Rotten Tomatoes (RT) critic scores, RT user scores, LetterBoxd user scores and IMDb user scores. These were chosen because they had ratings for all of the films in the franchise and because of their popularity (all the user-based ones have ⪆10,000 votes per Bond film). Each user-rating site has their own method to aggregate the users scores into a single number, which help fight bots and spammers, but they also tend to rate different types movies differently. With that in mind, let's take a look at how the different metrics score the Bond films differently.


### Metrics

First let's look at how the different metrics rated the Bond franchise on average. 

Note: 'Avg.dumb' is named so because originally I tried some weighted averages to account for the different means of the metrics. I ultimately decided against it, but the variable name remains.

```R
#We make variables for each rating type, as well as the average of the 4, then make a table of the values
#Note that all the ratings have been set to be out of 100
avg.dumb<-mean(mean(rate$RT.Crit),mean(rate$RT.User),mean(rate$LetterBoxd),mean(rate$IMDB))
avgs<-data.frame(RT.Crit=(mean(rate$RT.Crit)),RT.User=(mean(rate$RT.User)),LetterBoxd=(mean(rate$LetterBoxd)),IMDB=(mean(rate$IMDB)),Avg.Dumb=(avg.dumb))
```
Tables are boring, let's visualize this. To do this we must transform our dataframe using the 'melt()' function from the reshape 2 library.

```R
avgs_t<-melt(avgs)
```
![avg_rating_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/avg_rating_plot.png?raw=TRUE)

The average for all the Bond films, based on the 4 metrics of choice is 71%, this translates to a 3.5/5 star rating. Is this a good score? A 70% critic score on Rotten Tomatoes is all that's needed to get a 'Certified Fresh' seal for your movie. How about the other metrics? Thankfully the superstars over at [FiveThirtyEight](https://fivethirtyeight.com) ('538') did an [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) wherein they compared online movie ratings from various sites for 209 titles <sup> [1] </sup>. 

I jumped on [GitHub](https://github.com/) and downloaded the [data](https://github.com/fivethirtyeight/data/tree/master/fandango) used in this article. We now study how the scores of the films analyzed by '538' compare with the scores of the Bond Franchise, to get a grasp on how Bond films stack up. Let's make a table of maxes, mins and means for the overlapping metrics (unfortunately their fourth metric was [Metacritic](http://www.metacritic.com/) instead of our choice LetterBoxd <sup>[2]</sup>).

||RT Crit Max|RT Crit Min|RT Crit Mean|RT User Max|RT User Min|RT User Mean|IMDB Max|IMDB Min|IMDB Mean|Net Mean|
|---|---|---|---|---|---|---|---|---|---|---|
|Bond|96|36|71|89|37|64|80|61|69|68|
|'538'|100|5|61|94|20|64|86|40|67|64|

From this table we see that the max scores are higher for the '538' analysis, while the mean and min scores are higher for the Bond films. Based on this, the Bond movies are above average, but have never thoroughly rocked nor stunk-up the theatre (or home theatre). Being such a long running blockbuster franchise, there is a lot of time, effort, care and **money** put into making a Bond film above average but since they're fairly formulaic, it is harder to 'WOW' the audience into a 100% score or dissapoint them into a 5% rating. In contrast to this, expectations for 'new' movies (i.e. those in the '538' data) can cause them to be anywhere from underground sleeper hits, to tremendous big-budget flops.

One thing that bugged me about the results from the Bond series it that I expected critics's scores to be more, well, critical of movies than that of your average Joe Rotten Tomatoe. The average RT user rating for the Bond films was 10% (7 percentage-points) less than the critic ratings, while the results from the '538' article pegged user ratings 19% (3 percentage-points) higher than critic scores <sup>[3]</sup>. It could be that critics are more harsh on movies in general but they understand the place of the James Bond movies. So while the average theater-goer is keeping their score of Citizen Kane or Casablanca in mind when they pencil in their rating for the 007 films, critics understand that the Bond franchise should be judged for what it is: B-movies about a globe-trotting, babe-charming super spy, not exaclty high-art.

Now that we've looked at how the franchise ranks on average, and how that average compares to other films, let's look at the ratings of specific films in the series.

```R
#We reshape the 'rate' dataframe to have all the numerical ratings in one column
rate_1col<-melt(rate,id="Title")
colnames(rate_1col)[c(2,3)]<-c("Metric","Rating")
#Now we find the row information for the max/min value in the 'Rating' column
rate1_col[which.max(rate_1col$Rating),]
[1]            Title     Metric Rating
[1] 72 Casino Royale LetterBoxd   97.5

rate1_col[which.min(rate_1col$Rating),]
[1]               Title  Metric Rating
[1] 15 A View to a Kill RT.Crit     36
```

The lowest score given out to a Bond film is 36/100 from Rotten Tomatoes critics for the film *A View to a Kill*, which marks Roger Moore's last outing as Agent 007 (at the ripe old age of 57). The maximum score is 97.5 from LetterBoxd for *Casino Royale*, Daniel Craig's first film in the series. We will investigate later how an actor's last and first film are generally rated, but for now we can imagine Moore's and Craig's performance were due to their age and exictment with the role. We also note that although Craig takes first place for highest rating, Sean Connery's three first films (*Dr. No*, *From Russia, with Love* & *Goldfinger*) all tie for second with a 96 from RT critics.

It is not surprising that LetterBoxd claims the highest rated film since it has the highest average rating of 79/100, which is almost 12 percentage-points higher than the average of our 4 metrics. Yet while RT critic score was the second highest average rating among our 4 metrics, 71/100, we see that it claims the lowest score: a mere 36.

So these two films had very extreme ratings in one metric, but how do the other metrics rate the movies?

```R
rate[which.max(rate$Avg.All),]
[1]    RT.Crit RT.User LetterBoxd IMDB Avg.Dumb        Title
[1]22      95      89       97.5   80   90.375 Casino Royale

rate[which.min(rate$Avg.All),]
[1]     RT.Crit RT.User LetterBoxd IMDB Avg.Dumb            Title
[1] 15      36      41       67.5   63   51.875 A View to a Kill
```

An unsuprising result, the films with the highest and lowest individual ratings are also the ones with the highest and lowest average scores. Since it's only an average over 4, the extreme scores will drag up/down the average significantly, not to mention that each metric is presumably a trustworthy gauge of the quality in and of itself.

The extremes obviously don't tell the whole story, so let's look at all the scores in between. Let's make a point-and-line plot to see how the metrics changed over time.

```R
First we must add a 'date' and 'bond' column to our data
rate$Date<-bom$Release
rate$bond<-bom$Bond
#Note that by doing this we are taking our average of 4 out of the column with all the ratings.
rate_1col<-melt(rate,id=("Title","Date","Avg.Dumb","Bond"))
colnames(rate_1col)[c(4,5)]<-c("Metric","Rating")
```
![rate_metricbond](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/rate_metricbond.png?raw=TRUE)
 
The ratings jump up and down to various degrees, but as we saw with the average of each metric, LetterBoxd scores are generally highest and RT User the lowest. Studying how the ratings fluctuate between bond actors and how the different metrics change from film to film could unlock a lot about the evolution of the Bond series as well as inform about differences and similarities between our metrics. These two topics will be the focus of our next two sections, and in Appendix A we briefly study how much higher the LetterBoxd scores are than the others. 

Before we continue to these, I want to briefly see what the overall trend for the average film rating is throughout the series

```R
avg.score.fit<-lm(rate$Date ~ rate$Avg.Dumb,data=rate)
#Then we print out the summary to learn more. I will edit it some for readability and relevance 
summary(avg.score.fit)

Call:
lm(formula = rate$Avg.Dumb ~ rate$Date, data = rate)

Residuals:
     Min       1Q   Median       3Q      Max 
-18.6619 -10.6572  -0.1755   6.1802  22.6602 

Coefficients:
              Estimate Std. Error t value    
(Intercept) 72.5588382  3.2072146  22.624
rate$Date   -0.0003596  0.0004012  -0.897  
---
Multiple R-squared:  0.03377,	Adjusted R-squared:  -0.008244 
```
The R-square is quite poor, but nonetheless we see that the slope is negative! -0.0003596 translates to a -0.131254 change in average rating per year, and assuming a film every two years, a -0.26 change in average score every film.

Now we can look at how the different Bond actors scored on average.



### Bond Actor

First order of business is to compare Bond actors in the most straightforward way possible: comparing their average scores. 

```R
#First we create a vector of the names
names<-c("Sean Connery","George Lazen","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig")
#Now we create a blank dataframe to fill up with the averages of the metrics based on Bond actor
bond_rate<-data.frame()
#Fill up the dataframe with the names and averages with a loop and fix the column names
for(i in 1:6){
     bond_rate[i,1]=names[i]
     bond_rate[i,2]=mean(subset(rate$RT.Crit,rate$bond==names[i]))
     bond_rate[i,3]=mean(subset(rate$RT.User,rate$bond==names[i]))
     bond_rate[i,4]=mean(subset(rate$LetterBoxd,rate$bond==names[i]))
     bond_rate[i,5]=mean(subset(rate$IMDB,rate$bond==names[i]))
     bond[i,6]=mean(subset(rate$Avg.All,rate$bond==names[i]))
}
colnames(bond_rate)[c(1,2,3,4,5)]<-c("Bond","RT.Crit","RT.User","LetterBoxd","IMDB","Avg.All")
#We bolt down the order for the names as ggplot2 is untrustworthy with plotting strings in the correct order
bond_rate$Bond<-factor(bond_rate$Bond,levels=c("Sean Connery","George Lazen","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig"))
#Then we transform our dataframe
bond_rate1<-melt(bond_rate,id=c("Bond","Avg.All"))
```


![actor_avg_metric](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/actor_avg_metric.png?raw=TRUE)

So the most recent actor to wear the 007 tux, Daniel Craig, is the most highly rated with an average score of 78/100, and Sean Connery, the Bond that broke in the penguin suit, is the second highest at 76. With only 1 film in the franchise, it was surprising that George Lazen takes the third spot with 75. As we briefly saw earlier and will see again soon fatigue with the character seems to affect the ratings, which might have worked in George Lazen's favour. Brosnan (63.6) just edges out Moore (63.8) to get last place, and Dalton hovers near the franchise average with a 71 rating.

As before, LetterBoxd scores are the highest, and Rotten Tomatoes users are almost always the most critical. Some metrics vary drasticaly across Bonds, while some are all similar: the range of RT critic averages across Bonds is 25 points, and IMDb's is 8 <sup> [4] </sup>. So the averages have a small range, but how much do the ratings change from film to film? This is the focus of the next section.

### Score Changes

In order to study how the ratings change between titles, and ultimately between Bonds, we must create a dataframe that contains the difference in rating from film to film for each metric. In our dataframe we will have a variable "Change.Number" to indicate which transition is being discussed. For example, Change.Number=1 will indicate the difference in ratings between film 1 and film 2. Change.Number will range from 1-24 since there are 25 titles in the franchise.

```R
#Create a blank dataframe and fill it up with the differences
diff<-data.frame()
for(i in 1:24){
    diff[i,1]<-(rate$RT.Crit[i+1]-rate$RT.Crit[i])
    diff[i,2]<-(rate$RT.User[i+1]-rate$RT.User[i])
    diff[i,3]<-(rate$LetterBoxd[i+1]-rate$LetterBoxd[i])
    diff[i,4]<-(rate$IMDB[i+1]-rate$IMDB[i])
    diff[i,5]<-i
    diff[i,6]<-rate$bond[i+1]
}
#Fix the column names
colnames(diff)[c(1,2,3,4,5,6)]<-c("RT.Crit","RT.User","LetterBoxd","IMDB","Change.Number","New.Bond")
#'New Bond' refers to the bond in the second movie in the transition
#We quicly re-order this so it shows up correctly later
diff$New.Bond<-factor(diff$New.Bond,levels=c("Sean Connery","George Lazen","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig"))
```
We can now look at various properties of the changes between films.

```R
max(diff$RT.Crit)
[1] 37
min(diff$RT.Crit)
[1] -32
mean(diff$RT.Crit)
[1] -1.29
mean(subset(diff$RT.Crit,diff$RT.Crit>0))
[1] 19.3
#etc...
```
There is a lot of information here, so let's look at it in a table to see what we can discover.

|Metric|Max|Min|Mean|Mean>0|Mean<0|
|---|---|---|---|---|---|
|RT.Crit|37|-32|-1.29|19.3|-18.7|
|RT.User|48|-33|-0.83|18.2|-14.4|
|LetterBoxd|40|-27.5|-0.42|13|-10.7|
|IMDB|19|-13|-0.21|5.5|-4.6|
|Avg|35.5|-26.4|-0.69|14|-12|

The average change between films is -0.7 despite the fact that the mean>0 is always greater than the mean<0 (in absolute value) and that the biggest jump is always larger than the biggest drop (in absolute value). That means that audiences find most films worse than the last one, but when a film is better, they are very enthusiastic about the improvment (see Appendix B for a numerical study of this).

Although RT users have the biggest one-time increase and decrease (48 and -33), RT critic scores take the cake as the most sporadic with an overall average of -1.3 and mean positive & negative changes of 19 & -19 respectively. Once again we see that IMDb's ratings are quite muffled, it's largest increase & decrease changes are around half the magnitude as those of the other metrics, and it's mean positive and negative changes are less than a third the size of these figures for RT users & critics.

The above table only tells us about the net properties of the changes, so let's make a plot to look at the individual changes between films. We quickly reshape using 'melt()' and add a column for the sign of the change to help visualization the positive/negative fluctuations in score.

```R
#We transform to put all the ratings in one column to plot and analyze
diff_1col<-melt(diff,id=c("Change.Number","New.Bond"))
colnames(diff_1col)[c(2,3,4)]<-c("Bond","Metric","Rating.Change")
diff_1col$sign<-ifelse(diff_1col$Rating.Change>=0,'positive','negative')
```

![chng_rat_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/chng_rat_plot.png?raw=TRUE)

It seems that each metric generally agrees on the magnitude of the change. In other words, when a metric has a big/small change (relative to it's mean) then the others have similarly big/small changes. This reinforces my presumption that these online rating metrics are all roughly capturing the same sentiment towards movies, just with different audiences and aggregation methods. Another point of aggrement between metrics is seems to be the sign of the change i.e. they agree whether the newest film is better/worse than the last one. Let's see how often this is true by creating a column that gives a binary result to whether they all have the same sign, then suming this column.

```R
diff$sign_chng<-ifelse(sign(diff$RT.Crit)==sign(diff$RT.User)&sign(diff$RT.User)==sign(diff$LetterBoxd)&sign(diff$LetterBoxd)==sign(diff$IMDB),1,0)
sum(diff$sign_chng)
[1] 15
```
So 15/24 occurances of total agreement on the sign of the change for all of our metrics. We note that due to the nature of the 'sign()' function, zero has it's own sign, but if we count the 3 occurances of no change in one metric while the other three agree on sign, this ratio rises to 18/24. This makes only 6/24 (25%) times where the metrics disagree on whether the change in rating was positive or negative. When do these disagreements occur? And how do the changes in Bonds affect this? In order to study these questions we cook up a plot similar to above, with a specific focus on Bond actors.

![bond_rat_chng](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/bond_rat_chng.png?raw=TRUE)

Before we look at this graph there are a few points I would like to make. First, the scales are different for each Bond. The second point is that the ratings stack; originally I made this graph with only the average scores and then the sumof the changes, but I felt that these left out information about whether the metrics agree with one another from the previous section.

As we noticed from the previous graph, the metrics agree on the change in sign most of the time. The above graph reveals that these seem to occur on the last film in a Bond's tenure. The only actor for which this isn't true is Daniel Craig, though he isn't finished his run as Bond yet, and the only exception to the one-time nature of these mixed reviews is Roger Moore, whose first and last films are as such (note that we ignore occasions where 3 agree on sign while 1 has no change). One might wish to conclude that the mixed reviews caused the actors to quit, but some made up their minds to leave the series before their last film even premiered <sup> [14] </sup>. So due to the variety of situations surrounding 007 actors quitting the series, it is difficult to analyze why their last films all have positive and negative responses. Nevertheless, it is interesting that most Bond actors went out on mixed reviews.

Every time a new Bond actor premieres a film, the rating all improve, with the exception of George Lazen and Roger Moore who had mixed reviews for their first films. I believe it is worth noting that both exceptions followed Sean Connery, who has the second highest ratings, and as Dalton put it "When you've seen Bond from the beginning, you don't take over from Sean Connery" <sup>[3]</sup>, presumably because he embodied the character so well. This first-film boost is likely due to the previous actor tiring of the role, affecting their peformance, and the extra effort and care that the studio theoretically puts into the first film so that the audiences don't sour to the actor they just signed to a multi-film contract. 

Studying the (dis)agreements among metrics for the sign of the change of ratings between films is quite a long way from taking an average. Let's briefly look at how we got here, and summarize the results along the way.


### Summary
Throughout the Rating section, we have seen how our four metrics (Rotten Tomatoes Users, Rotten Tomatoes Critics, LetterBoxd and IMDb ratings/100) ratings differ between the 25 films and 6 actors in the Bond series. We saw that the series' average rating, based on our four metrics, was 71/100. LetterBoxd was the most generous rating, with a mean score of 79, and Rotten Tomatoe Users the harshest, scoring only 64 on average.  We found that on average Daniel Craig was the highest rated and Pierce Brosnan the lowest, with the mean score across all films and metrics of 78 and 63 respectively. We then studied how the ratings changed between films, and between actors, and saw that when a new Bond actor premiers, the metrics usually agree that it was a positive change, and on an actor's last film, the metrics' mostly disagree on the change in quality. 

This is only ~~half~~ some of the story; as much as I like to think that the quality of movies, and by extension online ratings, is what matters, the trush is that the movie industry is a business and the bottom line rules. So let's put aside our tables of online ratings for now and follow the dollars.

## Finances 

Financing movies is [very](http://www.npr.org/sections/money/2010/05/the_friday_podcast_angelina_sh.html) [complicated](https://www.theatlantic.com/business/archive/2011/09/how-hollywood-accounting-can-make-a-450-million-movie-unprofitable/245134/). It is no small task to raise hundreds of millions of dollars, often requiring [many parties with various investment models](https://www.youtube.com/watch?v=kgj0Z7zE0HM). When it comes to making the money back, there are box office sales, Blu-Ray/DVD sales, selling the title to streaming services, [product tie-ins](http://www.allday.com/10-completely-pointless-star-wars-product-tie-ins-that-make-no-sense-2180810453.html) etc.. These figures, as with most corporate finances, are not publically released. Even box office numbers, which go through some [tweaks](http://www.slate.com/articles/arts/culturebox/2006/01/ticket_tweaking.html) before being released to media outlets, [aren't pure profit for studios](http://www.themovieblog.com/2007/10/economics-of-the-movie-theater-where-the-money-goes-and-why-it-costs-us-so-much/). That being said, box office figures are the only consistently released film finance data, so we will use them. Lastly I'd like to note that I think it is a positive to have one metric that is a product of it's time (box office) as well as one that uses the power of hindsight (online ratings).

Before we get started let's briefly discuss the specifics of the box office data. All numbers in this section are in $USD, inflation calculations are made to May 2017, and 'domestic' refers to the combined US & Canada box office figures. Our two sources for this section are the websites [Box Office Mojo (BOM)](http://www.boxofficemojo.com/) and [The Numbers](http://www.the-numbers.com/). BOM gives domestic box office figures both adjusted and unadjusted for inflation. The Numbers has domestic and global box office, as well as estimated budgets, all in unadjusted terms. Our first order of business is to get everything adjusted for inflation.

### Box Office Gross

We recall that we already loaded our BOM and The Numbers data into dataframes named 'bom' and 'numb', respecitvely. 

So to achieve our goal of getting all our values adjusted for inflation what I will do is as follows, with the example of global gross: Find the ratio of unadjusted global gross to unadjusted domestic gross, then multiply the adjusted domestic gross by this factor, giving us the adjusted global gross. Due to this method, the 'bom' dataframe will be our main dataframe for this section.

```R
#We create a vector with the ratio of global to domestic box office gross
glb.dom<-data.frame(with(numb,World.BxOf/Dom.BxOf))
#Then create a new column in our BOM dataframe with the adjusted domestic cross multiplied by these ratios
bom$Glb.Adj<-(bom$Adjusted.Gross*glb.dom)
#We do similarly with budgets, adjusting for inflation
bud.dom<-data.frame(with(numb,Production.Budget/Dom.BxOf))
bom$Bdj.Adj<-(bom$Adjusted.Gross*bud.dom)
#We change the adjusted domestic gross column name for consistency
colnames(bom)[6]<-("Dom.Adj")
#Then add a 'Profit' column, which is strictly global profit
bom$Glb.Profit<-with(bom,Glb.Adj-Bdg.Adj)
#Finally add a non-domestic column that is global gross less domestic gross
bom$non.dom<-(bom$Glb.Adj-bom$Dom.Adj)
```
We note that from here on out, unless stated otherwise, all values will be adjusted for inflation to May 2017. Let's look at the sums and means now that we have all of our information adjusted for inflation.

|Figure|Sum|Mean|
|---|---|---|
|Global|$17,540,101,114|$701,604,045|
|Domestic|$5,628,582,200|$225,143,288|
|Non-Domestic|$11,911,518,914|$476,460,757|
|Budget|$2,723,922,708|$108,956,908|
|Profit|$14,816,178,405|$592,647,136|

With a net global box office gross of $17.5 billion James Bond is *the* most financially successful film franchise in history, trailed by *Star Wars*, *The Marvel Cinematic Universe* and *Harry Potter* (in that order) <sup> [4] </sup>. Over two thirds of this gross is non-domestic, which is not entirely surprising since our secret agent works for Britian, not America/Canada. This success, along with the gross averaging over six times their budget, and the films profit around $600 mill on average give us insight into how it has become [one of films longest running franchises](https://en.wikipedia.org/wiki/List_of_film_series_with_more_than_twenty_entries).

To give a better idea of the finances of individual films in our gilded franchise, let's take a quick look at the top, middle and bottom entries of each category. All figures are in millions of dollars.

|Figure|Max|Median|Min|
|---|---|---|----|
|Global|Thunderball ($1390)|Die Another Day ($640)|License to Kill ($340)|
|Domestic|Thunderball ($650)|Quantum of Solace ($200)|License to Kill ($80)|
|Non-Domestic|Skyfall ($860)|Dr. No ($440)|A View to Kill ($250)|
|Profit|Goldfinger ($1360)|Diamonds are Forever ($570)|License to Kill ($250)|
|Budget|Spectre ($270)|Living Daylights ($90)|Dr. No ($10)|

Sean Connery's *Thunderball* is the highest grossing film globally and domestically, while *Skyfall* produced the most in the foreign markets. The most profitable film was *Goldfinger* with a budget in the bottom 20%. We also notice that *Thunderball* pulled in more gross domestically than half the Bond film's did globally, quite a feat for only the fourth film in the series. Timothy Dalton's *License to Kill* is the least successful in all financial respects even though in the rating section it's average of 4 rating is the median with 71/100. It turns out that the newest Bond film (*Spectre*) cost more to make than *License to Kill* profited.  Speaking of budgets, we see that not only is the newest film the most expensive, but the oldest (*Dr. No*) is the least. Assuming a linear increase between these two extremes, this gives that the budget increases by $10 mill every film <sup> [6] </sup>. These budget numbers are intriguing, but we will come back to them later, we will first look at how the global and domestic grosses have evolved over time.

### Domestic & Global Gross

To study the evolution of the gross we make a dataframe with the changes in gross between films, similarly to what we did for ratings.

```R
#Fill the dataframe up with change in global and domestic gross, as well as a counter variable and the names of Bond actors
for(i in 1:24){
    bo.diff[i,1]<-(bom$Glb.Adj[i+1]-bom$Glb.Adj[i])
    bo.diff[i,2]<-(bom$Dom.Adj[i+1]-bom$Dom.Adj[i])
    bo.diff[i,3]<-(bom$Non.Dom.Adj[i+1]-bom$Non.Dom.Adj[i])
    bo.diff[i,4]<-i
    bo.diff[i,5]<-bom$Bond[i+1]
}
#Add column names
colnames(bo.diff)[c(1:5]<-c("Glb.Chng","Dom.Chng","Non.Dom.Chng","Counter","New.Bond")

#Make a column of the sign of the entries for coloring our plots
bo.diff$glb.sgn<-ifelse(bo.diff$Glb.Chng>=0,'positive','negative')
bo.diff$dom.sgn<-ifelse(bo.diff$Dom.Chng>=0,'positive','negative')
bo.diff$non.dom.sgn<-ifelse(bo.diff$Non.Dom.Chng>=0,'positive','negative')

#To make our y-axis labels more legible we divide all values by 1,000,000
bo.diff$Glb.Chng1<-with(bo.diff,Glb.Chng/1000000)
bo.diff$Dom.Chng1<-with(bo.diff,Dom.Chng/1000000)
bo.diff$Non.Dom.Chng1<-with(bo.diff,Non.Dom.Chng/1000000)

#Lastly we solidfy the order of the names as before
bo.diff$New.Bond<-factor(bo.diff$New.Bond,levels=c("Sean Connery","George Lazenby","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig"))
```

Let's put the key information from this dataframe into a table. 

|Market|Max|Min|Mean|Mean>0|Mean<0|
|---|---|---|---|---|---|
|Global|$664,904,030|-$629,375,924|$11,138,529|$204,679,528|-$182,402,471|
|Domestic|$341,273,000|-$334,894,700|$1,470,679|$94,201,991|-$76,994,277|
|Non-Domestic|$359,561,264|-$294,481,224|$9,667,850|$163,571,903|-$100,263,617|

Generally the gross increases in both domestic and foreign markets; the non-domestic (read as: foreign) market increases on average seven times more than the domestic market does. I note with a degree of skepticism  that the $11 mill change in global gross is weirdly close to our rough calculation of a $10 mill increase in budget between films. The biggest jump domestically and globally are for the transition from Connery's second film to his third, and all three markets agree that the biggest drop was from his fourth to fifth film.

Foriegn markets have only seen gross increase relative to the last film 9/24 time, domestic markets have had 11 such occasions, while globally this number is 12. The two markets  thus seem to work together to counter-act eachothers negative change in gross to bring the overall global increase in gross rate to 50%. 

Foreign and domestic markets agree on the sign of the change in gross 70% of the time (17/24), but when do they agree and disagree, and how do their magnitudes compare?

Domestic Change            |  Foreign
:-------------------------:|:-------------------------:
![dom_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/dom_chng_plot.png?raw=TRUE) | ![glb_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/non_dom_chng_plot.png?raw=TRUE)

Most of the disagreements (4/7) are when new actor premieres; for domestic gross, all but once, the change was negative, and in the foreign markets the change was, all but once, positive. It seems that North Americans are cold to new interpretations of Agent 007, with the exception of Pierce Brosnan, (possibly correlated with the six year hiatus in the series and the lowest grossing film being what he had to live up to). In opposition to this, foreigners audiences seem to like a reinvigoration of the series. The exception in the foreign case is George Lazenby, which as we've discussed, had a hard job of living up to Sean Connery. This is quite a powerful result, and I believe this could be used by the studio to decide where and how to spend advertising bucks when they finally choose a successor to Daniel Craig.

The three remaining disagreements occur around the halfway point for the actors: Sean Connery's third of six, Moore's fourth of seven, Brosnan's second of four. Even though with new actors domestic audiences were underhwlemed, in all 4 of these 'midterm' cases, the domestic market had a positive change, while the foreign markets also flipped and reacted negatively to these. This is difficult to interpret seeing as there is no way audiences (or even the actor) know how long they will continue the roll for. Regardless, it is an intersting result I thought were mentioning.

The patterns that the domestic and foreign changes in gross generally follow are helpful in understanding the evolution series, but we have yet to study the most obvious evolutionary factor: the actor.

### Bond Actor

To study how much the various 007 actors pulled in at the box office we must first create a dataframe that contains the various actor's mean values for the financial data.

```R
#we fill up the 'Bond' column with the 'names' vector from earlier and 'Average' which will be for averages over all actors
boxoffice<-data.frame(Bond<-c(names,"Average"))
#The loop is over 6 since we won't fill up the 'Average' entry in this way
for(i in 1:6){
     boxoffice$Glb.Mean[i]<-mean(subset(bom$Glb.Adj,bom$Bond==names[i]))
     boxoffice$Dom.Mean[i]<-mean(subset(bom$Dom.Adj,bom$Bond==names[i]))
     boxoffice$Non.Dom.Mean[i]<-mean(subset(bom$Non.Dom,bom$Bond==names[i]))
     boxoffice$Prft.Glb[i]<-mean(subset(bom$Prft.Glb,bom$Bond==names[i]))
     boxoffice$Bdg[i]<-mean(subset(bom$Bdg.Adj,bom$Bond==names[i]))
     boxoffice$Glb.Bdg.Ratio[i]<-with(bom,sum(subset(bom$Glb.Adj,bom$Bond==names[i]))/sum(subset(bom$Bdg.Adj,bom$Bond==names[i])))
}
# Add a term that takes the ratio of non-domestic (foreign) and domestic average grosses 
boxoffice$For.Dom.Ratio<-with(boxoffice,Non.Dom.Mean/Dom.Mean)
#Now fill in the 'Average' row with the average over all columns less the actor column
for(i in 2:8){
     boxoffice[7,i]<-mean(boxoffice[,i])
}
```
Here is what the dataframe looks like as a table:

|Actor|Avg. Global Gross|Avg. Domestic Gross|Avg. Foreign Gross|For:Dom Ratio|Avg. Global Profit|Avg. Budget|Glb:Bdg Ratio|
|---|---|---|---|---|---|---|---|
|Connery|$857,381,211|$328,071,243|$529,309,968|1.6|$806,338,430|$51,042,781|16.8|
|Lazenby|$496,640,912|$138,090,400|$358,550,512|1.9|$448,188,140|$48,452,772|10.2|
|Moore|$594,410,522|$166,867,700|$427,542,822|2.6|$528,506,844|$65,903,678|9.0|
|Dalton|$379,864,046|$93,949,150|$285,914,896|3.0|$290,278,294|$89,585,752|4.2|
|Brosnan|$644,678,449|$223,328,250|$421,350,199|1.9|$454,928,173|$189,750,275|3.4|
|Craig|$885,619,047|$236,176,975|$649,442,072|2.7|$655,951,017|$229,668,030|3.8|
|Average|$643,099,031|$206,247,286|$445,351,745|2.2|$530,698,483|$112,400,548|7.9|
     
There's a lot to unpack here, so let's go slowly. First we see that while on average Craig grossed the most in global and foreign markets, Connery claims the box office crown domestically. Although Daniel Craig's interpretation of the character is easily the most raw and serious, he comes off a lot more stylish and debonair (read as: British) than Connery. Connery's 007 performances have more of a maverick feeling to them which Americans might like since part of their core identity is the 'Don't Tread on Me' mentality and fascination with the cowboy archetype.

On the other end of the spectrum, at domestic and global box offices, Dalton's films grossed on average the least. As we saw, Dalton's second film *License to Kill* ranks as the lowest grossing Bond film domestically & globally, and his his first film (*The Living Daylights*) is the third lowest in both categories. Though *License to Kill* suffered some misfortunes including a [higher-than-usual age classification in Britain](http://www.bbfc.co.uk/releases/licence-kill) and [a last-minute title change](https://www.theguardian.com/film/2008/aug/21/1), it doesn't explain why the first one was so poorly recieved. Well, what makes Dalton's Bond different from the rest?

After Roger Moore's playful, light-hearted Bond, the grit and realism of Dalton's performances must have been quite jarring for audiences. Dalton wanted the Bond from the novels, a darker and more broooding Bond (I must disclose that I have never read any Bond novels). In an interview, Dalton summarized his approach thus: "I think Roger was fine as Bond, but the films had become too much techno-pop and had lost track of their sense of story.  Every film seemed to have a villain who had to rule or destroy the world. If you want to believe in the fantasy on screen, then you have to believe in the characters and use them as a stepping-stone to lead you into this fantasy world. That's a demand I made." <sup> [7] </sup> According to my parents (Dad has read all the Fleming novels, and Mom has read none) it seems that those who were familiar with the literary charcter were mostly pleased with Dalton's more grounded approach whereas those who only knew the movies didn't enjoy how serious it was. In contrast with this, Daniel Craig's Bond which is darker than Dalton's in many ways, was extremelly well recieved and it even followed Brosnan's more light-hearted, Moore-esque Bond. This might mean that either Dalton took the unsatisfying middleground between fun and serious, or he helped paved the way for the appreciation of a darker Bond, which Craig reaped.

Next we see that Connery had the lowest ratio of foreign to domestic gross, while Dalton had the highest. Connery performed particularly well domestically (52% higher than average) but only slightly above average in the foreign market (16% higher than average). Meanwhile Dalton underperformed to a greater extent domestically (54% under average) than in foreign markets (36% below average).

As we discussed earlier, Connery's approach is quite American. While his average global gross is 18% lower than the Craig's (who has the highest average), his domestic gross is 28% higher than the next highest. Further, Connery performed 16% higher than average in foreign markets but 52% higher domestically! So although his foreign:domestic ratio is low, it seems it is more because he has a strong domestic appeal rather than a weak global one.

With grosses rivaled only by Craig but with the second lowest budgets (half the average), not only does an average Connery film profit over 50% more than the average Bond film, but his Profit:Budget ratio is over twice the average and this ratio is 60% higher than the next highest actor's. It is hard to pretend that this is a completely fair analysis, the movie industry has changed a lot over the last 55 years. Directors and producers continually push the medium to create a more engaging and visercal cinematic experience; more extravagent sets and costumes, new cameras, and better special effects have caused budgets to [continue to pass new milestones every few years](https://en.wikipedia.org/wiki/List_of_most_expensive_films#Record-holders). So to get a real grasp on what these numbers mean, it is imperitive that we study the budgets of the series.

### Budgets

To help in our analysis we want columns of Profit:Budget and Global Gross:Budget ratios. We create them in the 'bom' dataframe.

```R
bom$Prft.Bdg.Ratio<-bom$Prft.Glb/bom$Bdg.Adj
bom$Glb.Bdg.Ratio<-bom$Glb.Adj/bom$Bdg.Adj
```

We have seen that Connery has the largest average return on investment with a whopping $2.6 bn net profit and $800 mill average profit from his 6 films. These new columns show us that Connery's first film *Dr. No* grossed almost 60 times it's budget, his second and third around 40 times, and his average gross was 17 times his average budget. (A 600% return on investment is fantastic, but it's nowhere near *Paranormal Activity* which had a profit [nearly 1300 times it's budget](http://www.pajiba.com/seriously_random_lists/percentagewise-the-20-most-profitable-movies-of-all-time.php)). Even at the bottom of these two measures, Dalton's films profited an average $290 mill  and Brosnan's films grossed 3.4 times their budget. So if you've ever wondered why this franchise has lasted so long, here's why: they're very profitable!

How has this profitability evolved over time? Namely, how is the return on investment change throughout the series? We plot the ratio of  profit:budget against time to see how our series looks to accountants.

![profit_budget_ratio](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/profit_budget_ratio.png?raw=TRUE)

We added a LOESS-method trendline to show the decrease-to-plateau nature of the data. Whether on purpose or by some unintentional/external force the franchise seems to have nestled itself in a 5:2 profit:budget ratio ever since Dalton's last film in 1989. 

Our previous analysis, based on a rather crude method, showed that on it seems that Bond films budgets have increased by an average $10 mill/movie. Let's take a deeper look at this, first looking at the basic relationship between date of release and budget using the built-in regression powers of ggplot2. We chose a linear regression since we are mostly interested in the overall behaviour of the budget trend.

```R
#First we create the fit based on the multiple liner ('lm') method
budget.fit<-lm(bom$Bdj.Adj ~ bom$Release, data=bom)
#Then look at the details, which I have edited for readibility and relevance 
summary(budget.fit)

Residuals:
      Min        1Q    Median        3Q       Max 
-74109371 -26628614  -4977318  22375695  68159902 

Coefficients:
            Estimate Std. Error t value
(Intercept) 41159739   10142613   4.058
bom$Release    12353       1269   9.737
---
Multiple R-squared:  0.8048,	Adjusted R-squared:  0.7963 
```

The slope is 12353 which means to the budget increasing by $12,353 a day (with an error of about $1269) or $4.6 mill a year (with an error of $463,185). Since the franchises averages one film every two years, the budget increases by $9.2 mill every film (with an error of $0.9 mill), fairly close to our rough calculation of $10 mill. 

![budg_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/budg_plot.png?raw=TRUE)

So our two extremes were not anomalies, the film's budgets have defienitly increased, and quite dramatically. Our highest grossing film globally, *Thunderball*, had quite a large budget compared with the films around it (which might be partly due to it's [extensive underwater scenes](https://youtu.be/cuMM72G5k48?t=4m3s)). It cost three times as much to make as the proceeding film and twice as much as the average budget of the six succeeding films. The $91 mill budget was not surpassed until *Moonraker*'s $110 mill pricetag 15 years later.  

It is difficult to say how typical this is without doing a whole project on film budgets over time, but what we can do is look at how the budgest of the 007 films compare to the budget record-holders of the time. Instead of spending a bunch of time on this, let's look at the first Bond film, the last and one in the middle. 

**First**: In 1963 *Dr. No* cost  $1 million to make and in that same year the Elizabeth Taylor and Richard Burton epic [*Cleopatra*](https://en.wikipedia.org/wiki/Cleopatra_(1963_film)) broke a new record with a $31 million budget (neither figure adjusted for inflation since we simply want to compare). 

**Median**: Roger Moore's 1983 *Octopussy* was made on a $27.5 million budget, and the record holder at the time was the 1978 movie [*Superman*](https://en.wikipedia.org/wiki/Superman_(1978_film)) with a budget of $88 million (when adjusted to 1983).

**Last**: 2015's *Spectre* had an unadjusted budget of $300 million while the record for a single film remains the [4th Johnny Depp *Pirates of the Caribbean*](https://en.wikipedia.org/wiki/Pirates_of_the_Caribbean:_On_Stranger_Tides) film from 2011, with a $400 million adjusted to 2015. 

This isn't exactly scientific: early on in the film industry they would produce a lot of low budget movies and a few expensive movies, whereas nowadays they produce a lot of films of similar budgets. What this treatment does is give one an idea of how the James Bond franchise has turned from just another film series to one of the industry's heavy-hitters. 

### Summary
The James Bond film franchise has become one of the biggest blockbuster franchises in the world. The first film was made on a $10 million budget, which is peanuts compared to the $300 million of the most recent entry. It is *the* highest grossing film franchise of all time, when adjusted for inflation, grossing a huge $17.5 bill over 25 films, and profiting $14.8 billion. From $10 million budgets all the way to $300 million, the franchise has The highest grossing film in the series is *Thunderball*, making $1.39 bill globally and the most profitable was *Goldfinger* taking home $1.36 bill. The foreign markets grosses about twice as much as domestic markets. When it comes to new Bonds, the foreign market generally reacts positively, and domestic market negatively. Daniel Craig grossed the most in the global markets on average, but Sean Connery has the highest ratio of global gross to budget and the highest average domestic gross. The ratio of profit:budget has nestled into a 5:2 ratio since 1990, down from an initial 60:1 ratio.

## Come Together...
You don't need to have read this analysis to know that the James Bond franchise is one of the biggets blockbuster franchises in the world, but it does help to quantify this fame with some numbers. We have seen that they are 'Ceritifed Fresh' according to Rotten Tomatoes, receiveing an average score of 71/100 based on 4 online rating scores, and that the entries in the series profit over $100 million on average. While Connery and Craig seemed to be neck-and-neck in most categories, on average Craig wins in my book, scoring an average of 78/100 and $890 million average global gross. 

These numbers, although helpful in understanding the series and often supporting eachother (such as in the case of Daniel Craig), are frustratingly disjoint. To bridge this gap, we will create a new dataframe with both financial and online rating data.

```R
#Make new dataframe with the financial and rating data we are interested in
rate.finc<-data.frame(bom$Title,bom$Release,bom$Bond,bom$Glb.Adj,bom$Bgd.Adj,rate$Avg.Dumb)
#Since R likes to mess up the names of our columns we correct them
colnames(rate.finc)[c(1,2,3,4,5)]<-c("Title","Release","Bond","Glb.Adj","Bdg.Adj","Avg.Dumb")
```
My first idea to combine the two ways to view a movie is to create a combined score based on the mean rating and the global gross. To capture how each film performed in the context of the series, we will normalize by the relevant mean. This method then gives us a way of putting the two measurmenets on the same scale.

```R
rate.finc$Rate.Norm<-with(rate.finc,rate.finc$Avg.Dumb/avgs$Avg.All)
rate.finc$Glb.Norm<-with(rate.finc,rate.finc$Glb.Adj/mean(rate.finc$Glb.Adj))
```
Now that we have the two sets of normalized measurments, we can take the mean of the two to create a 'combined score'.

```R
#We will use the name 'score' for the mean of the two normalized measurements
for(i in 1:25){
    rate.finc$score[i]<-mean(c(rate.finc$Rate.Norm[i],rate.finc$Glb.Norm[i]))
}
```
Let's take a look at how the combined scores look on average for each actor

|Bond|Score|
|---|---|
|Connery|1.15|
|Lazenby|0.89|
|Moore|0.87|
|Dalton|0.77|
|Brosnan|0.91|
|Craig|1.18|

Yet again Craig just edges out Connery, and Dalton takes last place. However, Connery's *Thunderball* and *Goldfinger* take the two top spots with scores of 1.63 and 1.57 respectively. Now that we have this data, we can look at it in a graph, adding a linear regression to show the franchise's trend.

![score_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/score_plot.png?raw=TRUE)

According to this regression, the combined score has decreased over the serie's lifetime. We have seen that online ratings decrease throughout the franchise (although the ratings only change by an average of -0.26 per film), and it can be shown that global gross also decreases over time, but it is nevertheless interesting to see the trendline drop almost a tenth of a point over the course of the series. One way to avoid how these ratings change over time is to not compare against date of release.

In fact, the reason I started this project was to look at how the online ratings and global gross compare, to *hopefully* prove something that I have long believed: The higher the quality of a movie, the more it will gross. This avoids the problem of the gross and ratings changing over time, get's down to the point of if quality leads to money. Let's take a look!

![glb_rate_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/glb_rate_plot.png?raw=TRUE)

Quality is positively correlated to financial gross! A simple 1 point increase in the aggregated online rating translates to a $13.5 million increase in global gross. I'm sure most of us don't take online scores seriously enough that a 1 point difference will affect our likelihood of seeing the film, but in the James Bond series, it can be worth millions of dollars of box office revenue.



## I'll Get You Next Time 007!
I hope that this analysis has helped you understand both the financial and online rating landscapes for the Bond series, but this analysis is in no way final or complete. The code throughout sections is not always of the same style, and the plots don't always align in word choice. This is partly due to my having worked on this project for over a month, and thus different days different methods and ideas coming to me. The reason I am formally ending this now is so that I can use it as a finite project when applying for jobs.

Beyond simple editing, there are sections and ideas I would like to expand or study. If all goes according to plan, this section will slowly disappear over the following months.  These ideas include: 

- Working more with changes in gross between movies. I compared the sign of the changes, but I would also like to compare the magnitudes of the change, normalizing in some way for the size of the region, to see how dramatic each region's attitude is towards the new film 

- Look at how budgets and aggregated online rating relate.

- Do more with my combined score. An idea I had was to look at the changes between films, in a similar vein to with ratings and box office gross.

- Study how the movie industry has evolved over time in the context of budgets, how box office grosses have evolved over time and how domestic and foreign markets compare usually.




Thanks for reading!



# Footnotes

<sup> [1] </sup>:
The 209 titles are 'recent' as of October 2015, when the article was written.

<sup>[2]</sup>:
I briefly looked at the LetterBoxd and Metacritic for the Bond and 538 analysis, respectively, but they were so different I thought it was best to simply ignore them for that analysis. Here is the max/min/mean for the two

||Mean|Max|Min|
|---|---|---|---|
|LetterBoxd|79.1|97.5|57.5|
|Metacritic|59|94|13|

<sup>[3]</sup> : In the FiveThirtyEight [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) I referenced, the point of interest is this paragraph: "The ratings from IMDb, Metacritic and Rotten Tomatoes were typically in the same ballpark, which makes this finding unsurprising: Fandango’s star rating was higher than the IMDb rating 79 percent of the time, the Metacritic aggregate critic score 77 percent of the time, the Metacritic user score 86 percent of the time, the Rotten Tomatoes critic score 62 percent of the time, and the Rotten Tomatoes user score 74 percent of the time." Therefore to see how much higher user scores are than the critics scores, we simply divide the two averages to eliminate the Fandango term: (RT.Crit)/(RT.User)=1.19 which gives us our quoted 19%. 

If we wish to continue this analysis:

IMDb vs. RT.Crit: Our metric suggests RT critics rate Bond films 3% higher than IMDb while FiveThirtyEight says that RT critcs scores are 21% lower.

IMDb vs. RT.User: Our metric says RT Users scores are 7% lower than IMDb and FiveThirtyEight puts this number at 6%.


<sup> [4] </sup>:
To find the range of averages for a metric across bond actors you simply subtract the max from the min. For example, with IMDb

```R
max(bond_rate$IMDB)-min(bond_rate$IMDB)
[1] 7.75
```

<sup>[3]</sup>:
18 years before he finally accepted the role, Timothy Dalton said the following about turning down an offer in 1968 to play Bond in "Oh Her Majesty's Secret Service": "Originally I did not want to take over from Sean Connery. He was far too good, he was wonderful. [...] When you've seen Bond from the beginning, you don't take over from Sean Connery." Source (https://www.youtube.com/watch?v=amuodiP-8z4) *This link has been taken down since I posted it. Sadly I cannot find a working version*

<sup>[4]</sup>: 
This can be calculated from figures [here](http://www.boxofficemojo.com/franchises/?view=Franchise&sort=sumgross&order=DESC&p=.htm), with the same process of finding the global gross adjusted for inflation from the domestic adjusted and global unadjusted.

<sup> [5] </sup>:
bo.diff$glb.dom.raw.ratio<-with(bo.diff,bo.diff$Glb.Chng/bo.diff$Dom.Chng)
mean(bo.diff$glb.dom.raw.ratio)
[1] 0.6324663

<sup>[6]</sup>:
The newest film has the highest budget and the oldest film has the lowest. So we find the increase as follows:

($270-$10)mill/(54years)=$4.8 mill/year

With 25 films in 54 years that gives us an average of 2.2 years/film. This means that there is a $4.8x2.2 = 10 mill/film increase.

<sup>[7]</sup>:
Source: Rubin, Steven Jay (1995). The Complete James Bond Movie Encyclopedia (Revised ed.). McGraw-Hill/Contemporary Books. ISBN 0-8092-3268-5.

# Plot Code

I break the plot info into multiple elements so that it is easier to edit if I change something and because then you don't have to scroll in GitHub to see it all.

## avg_rating_plot
A bar plot that shows the average rating by metric 
```R
#We create a new, transposed, dataframe using 'melt()' from the reshape2 library for the plot
avgs_t<-melt(avgs)
#We plot only the first 4, as we will visualize the average of the 4 in another form
avg_rating_plot<-ggplot(avgs_t[1:4,],aes(x=variable,y=value,fill=variable))+geom_col()+coord_cartesian(ylim=c(60,80))
labels1<-xlab("Rating Metric")+ylab("Average Score/100")+ggtitle("Bond Film Average Rating by Metric")
labels2<-annotate("text",x="IMDB",y=72,label="Average of \n4 Metrics",fontface='italic')+labs(fill="Metric")
avg_line<-geom_hline(aes(yintercept=avgs_t[5,2]),linetype="dashed")
```
## rate_metricbond
A line and point graph that shows how our 4 metrics change over time
```R
#Note that by doing this we are taking our average of 4 out of the column with all the ratings.
rate_1col<-melt(rate,id=c("Title","Date","Avg.All","Bond"))
colnames(rate_1col)[c(4,5)]<-c("Metric","Rating")
#Not that we add colour and linetype to amplify the distinction between lines
rate_bymetric<- ggplot(rate1,aes(x=Date,y=value))+geom_line(aes(col=Metric))+geom_point(aes(shape=bond))
labels<-ylab("Rating/100")+ggtitle("James Bond Film Ratings by Metric")+labs(shape="Bond",col="Metric")
```

## actor_avg_metric
A grouped bar plot that shows how different bonds were rated by our 4 metrics (and the average of the 4)
```R
actor_avg_metric<-ggplot(bond_rate1,aes(x=Bond,y=value))+geom_bar(aes(fill=variable),stat="identity",position="dodge")
labels<-ggtitle("Average Bond Ratings by Metric")+xlab("Bond Actor")+ylab("Rating")+labs(fill="Metric")
coord_avg<-coord_cartesian(ylim=c(55,90))+geom_errorbar(aes(ymax=Avg.All,ymin=Avg.All))
#Where this last element is our custom y-axis limits and errorbars, a 'hack' to get horizontal bars for the averages
```

## chng_rat_plot:
A bar plot that shows the change in score between movies, separated by metric 
```R
#Now we graph, making the 4 different metrics on 4 different graphs but in the same plot
chng_rat_plot<-ggplot(diff_1col,aes(x=Change.Number,y=Rating.Change,fill=sign))+geom_col()+facet_grid(Metric ~ .)
coloring<-scale_fill_manual(values=c("positive"="BLUE","negative"="RED"))
labels1<-xlab("Change Between Films")+ylab("Change in Rating/100 from Previous")
labels2<-ggtitle("James Bond Film Change in Rating from Previous",subtitle="Sorted by Metric")
linebreaks<-scale_x_continuous(breaks=c(0,2,4,6,8,10,12,14,16,18,20,22,24))
```

## bond_rat_chng
A bar plot that shows the change in scores between movies, separated by Bond actor and with metric shown in differenc colours
```R
bondchng<-ggplot(diff_1col,aes(x=Change.Number,y=Rating.Change,fill=Metric))+geom_col()
#The 'scales' term is to make each plot have it's own scale and the second term is where we rotate the text for the facet labels
facet<-facet_grid(Bond ~ .,scales="free")+theme(strip.text.y = element_text(angle = 0))
#Create a black line to better visualize the +/- change
xaxis<-geom_hline(aes(yintercept=0))+scale_x_continuous(breaks=c(0,4,8,12,16,24))
labels1<-ggtitle("James Bond Film Change in Rating from Previous",subtitle="Sorted by the New Bond Actor")
labels2<-xlab("Film in Series")+ylab("Change in Rating/100 from Previous")
 ```
 
 ## profit_budget_ratio
 A line plot with a LOESS trendline looking at profit to budget ratios over time
 ```R
profit_budget_plot<-ggplot(bom,aes(x=Release,y=Prft.Bdg.Ratio))+geom_line()+geom_point(aes(shape=Bond))
#use LOESS method for trendline, set se (error visualisation) to zero for readibilty
trendline<-geom_smooth(method='loess',se=FALSE)
#horizontal line to help visualize trend, plus label
line<-geom_hline(yintercept=2.5,linetype="dashed",colour="RED")+annotate("text",x=as.Date("1963-01-01"),y=4.5,label="x=2.5",col="RED")
labels<-ggtitle("James Bond Film Profit to Budget Ratio")+ylab("Profit to Budget Ratio")
```

## budg_plot
A line plot of budgets over time with a linear regression line superimposed
```R
budg_plot<-ggplot(bom,aes(x=Release,y=Bdg.Adj))+geom_line()+geom_point(aes(shape=Bond)) trendline<-geom_smooth(method='lm',se=FALSE)
y_axis<-scale_y_continuous(breaks=c(1e+07,1e+08,2e+08,3e+08),labels=c("$10 mill","$100 mill","$200 mill","$300 mill"))
labels<-ggtitle("James Bond Film Budgets",subtitle="Adjusted for Inflation to May 2017")+ylab("Budget")
```

## glb_chng_plot
 glb.chng<-ggplot(bo.diff,aes(x=Counter,y=Glb.Chng1,fill=glb.sgn))+geom_bar(stat='identity')+facet_grid(New.Bond ~ .,scales="free")+theme(strip.text.y = element_text(angle = 0))+scale_fill_manual("sign",values=c("positive"="BLUE","negative"="RED"))+geom_hline(aes(yintercept=0))+ylab("Change in Global Gross")+xlab("Film in Series")+ggtitle("Global Gross Change, Bond Franchise",subtitle="Adjusted for Inflation to May 2017, $mill")

## dom_chng_plot
dom.chng<-ggplot(bo.diff,aes(x=Counter,y=Dom.Chng1,fill=dom.sgn))+geom_bar(stat='identity')+facet_grid(New.Bond ~ .,scales="free")+theme(strip.text.y = element_text(angle = 0))+scale_fill_manual("sign",values=c("positive"="BLUE","negative"="RED"))+geom_hline(aes(yintercept=0))+ylab("Change in Domestic Gross")+xlab("Film in Series")+ggtitle("Domestic Gross Change, Bond Franchise",subtitle="Adjusted for Inflation to May 2017, $mill")

 

# Appendix

## A
We note in this [graph](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/rate_bymetric.png?raw=TRUE) that the LetterBoxd scores are almost always above the other 3. We will study this observation:

```R
#Create the dataframe to store the infor
lb_extra<-data.frame()
#Input into our dataframe the difference between the LB rating and the max of the other 3
for(i in 1:25){
    lb_extra[i,1]<-(rate$LetterBoxd[i]-max(c(rate$RT.Crit[i],rate$RT.User[i],rate$IMDB[i])))
}
colnames(lb_extra)[1]<-"LB.Diff.Max"

#What are the biggest/smallest differences and the mean difference
max(lb_extra$LB.Diff.Max)
[1] 12.5
min(lb_extra$LB.Diff.Max)
[1] -8.5
mean(lb_extra$Score.Above.Max)
[1] 3.82
#And the total sum of the differences, which we could find using the mean, but that's boring
extra_sum=0
for(i in 1:25){
     extra_sum<-extra_sum+lb_extra$LB.Diff.Max[i]
}
print(extra_sum)
[1] 95.5
```
At it's highest LetterBoxd score is 12.5 points above the next highest, at it's lowest, it is 8.5 points below the highest, it's average height above the next heighest is 3.8 points and the total sum of it's height above the others is 95.5. 


Now we visualize this data:
```R
#We now tag the numbers as positive and negative for later
lb_extra$sign<-ifelse(lb_extra$LB.Diff.Max >=0,'positive','negative')
#Now we plot, with red being negative and blue being positive
lb_diff_plot<-ggplot(lb_extra,aes(x=c(1:25),y=LB.Diff.Max,fill=sign))+geom_col()
colouring<-scale_fill_manual(values=c("positive"="BLUE","negative"="RED"))
labels<-xlab("Film Number in Series")+ylab("Difference in Points for Rating/100")+ggtitle("Difference Between LetterBoxd Score and Max of the Other 3*")+labs(caption="*Other 3: RT critic, RT User, IMDb")
```
![lb_diff_plo](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/lb_diff_plot.png?raw=TRUE)

## B

We verify our result that there must be more negative changes than positive ones, due to the mean change being negative, but the mean positive change being larger than the mean negative change.

```R
pos_chng=0
neg_chng=0
no_chng=0
#The loop is out of 100 because there are 4 ratings, and each rating has 24 changes between the 25 films
for(i in 1:96){
     if(diff_1col$Rating.Change[i]>0){
         pos_chng<-pos_chng+1
     }
     else if(diff_1col$Rating.Change[i]<0){
         neg_chng<-neg_chng+1
     }
     else no_chng<-no_chng+1
}
print(c(pos_chng,neg_chng,no_chng))
[1] 40 4 52
```

We verify that we have computed the correct number of positive, negative and nil changes in movie ratings between movies using basic algebra and knowledge of how means of subsets relate to mean of the set. We multiply the mean of the different signs by the number of entries of that sign, and that should sum up to the total mean multiplied by the total number of entries. 

(avg of positives)\*(# of positives)+(avg of zeroes)\*(# of zeroes)+(avg of negatives)\*(# negatives)=(avg of set)\*(# in set)

Left Hand Side: 14\*40+0\*4-12.04\*52=560-626.08=-66.08 \n
Right Hand Side: -0.687\*96=-65.9

Which agrees within signficant figures.
