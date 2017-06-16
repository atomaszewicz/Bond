# Analyzing the James Bond Film Franchise

## Set up

Since I saved my cleaned-up Exif CSV as an Excel ".xlsx" file we load a package to bring Excel files into RStudio, another packgage to reshape data and , our favorite plotting package.


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

```R
#We make variables for each rating type, as well as the average of the 4, then make a table of the values
#Note that all the ratings have been set to be out of 100
avg_all<-mean(mean(rate$RT.Crit),mean(rate$RT.User),mean(rate$LetterBoxd),mean(rate$IMDB))
avgs<-data.frame(RT.Crit=(mean(rate$RT.Crit)),RT.User=(mean(rate$RT.User)),LetterBoxd=(mean(rate$LetterBoxd)),IMDB=(mean(rate$IMDB)),Avg.All=(avg_all))
```
Tables are boring, let's visualize this. To do this we must transform our dataframe using the 'melt()' function from the reshape 2 library.

```R
avgs_t<-melt(avgs)
```
![avg_rating_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/avg_rating_plot.png?raw=TRUE)

The average for all the Bond films, based on the 4 metrics of choice is 71%, this translates to a 3.5/5 star rating. Is this a good score? A 70% critic score on Rotten Tomatoes is all that's needed to get a 'Certified Fresh' seal for your movie. How about the other metrics? Thankfully the superstars over at [FiveThirtyEight](https://fivethirtyeight.com) ('538') did an [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) wherein they compared online movie ratings from various sites for 209 titles <sup> [12] </sup>. 

I jumped on [GitHub](https://github.com/) and downloaded the [data](https://github.com/fivethirtyeight/data/tree/master/fandango) used in this article. We now study how the scores of the films analyzed by '538' compare with the scores of the Bond Franchise, to get a grasp on how Bond films stack up. Let's make a table of maxes, mins and means for the overlapping metrics (unfortunately their fourth metric was [Metacritic](http://www.metacritic.com/) instead of our choice LetterBoxd <sup>[11]</sup>).

||RT Crit Max|RT Crit Min|RT Crit Mean|RT User Max|RT User Min|RT User Mean|IMDB Max|IMDB Min|IMDB Mean|Net Mean|
|---|---|---|---|---|---|---|---|---|---|---|
|Bond|96|36|71|89|37|64|80|61|69|68|
|'538'|100|5|61|94|20|64|86|40|67|64|

From this table we see that the max scores are higher for the '538' analysis, while the mean and min scores are higher for the Bond films. Based on this, the Bond movies are above average, but have never thoroughly rocked nor stunk-up the theatre (or home theatre). Being such a long running blockbuster franchise, there is a lot of time, effort, care and **money** put into making a Bond film above average but since they're fairly formulaic, it is harder to 'WOW' the audience into a 100% score or dissapoint them into a 5% rating. In contrast to this, expectations for 'new' movies (i.e. those in the '538' data) can cause them to be anywhere from underground sleeper hits, to tremendous big-budget flops.

One thing that bugged me about the results from the Bond series it that I expected critics's scores to be more, well, critical of movies than that of your average Joe Rotten Tomatoe. The average RT user rating for the Bond films was 10% (7 percentage-points) less than the critic ratings, while the results from the '538' article pegged user ratings 19% (3 percentage-points) higher than critic scores <sup>[1]</sup>. It could be that critics are more harsh on movies in general but they understand the place of the James Bond movies. So while the average theater-goer is keeping their score of Citizen Kane or Casablanca in mind when they pencil in their rating for the 007 films, critics understand that the Bond franchise should be judged for what it is: B-movies about a globe-trotting, babe-charming super spy, not exaclty high-art.

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
[1]    RT.Crit RT.User LetterBoxd IMDB Avg.All         Title
[1]22      95      89       97.5   80   90.375 Casino Royale

rate[which.min(rate$Avg.All),]
[1]     RT.Crit RT.User LetterBoxd IMDB Avg.All            Title
[1] 15      36      41       67.5   63   51.875 A View to a Kill
```

An unsuprising result, the films with the highest and lowest individual ratings are also the ones with the highest and lowest average scores. Since it's only an average over 4, the extreme scores will drag up/down the average significantly, not to mention that each metric is presumably a trustworthy gauge of the quality in and of itself.

The extremes obviously don't tell the whole story, so let's look at all the scores in between. Let's make a point-and-line plot to see how the metrics changed over time.

```R
First we must add a 'date' and 'bond' column to our data
rate$Date<-bom$Release
rate$bond<-bom$Bond
#Note that by doing this we are taking our average of 4 out of the column with all the ratings.
rate_1col<-melt(rate,id=("Title","Date","Avg.All","Bond"))
colnames(rate_1col)[c(4,5)]<-c("Metric","Rating")
```
![rate_metricbond](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/rate_metricbond.png?raw=TRUE)
 
The ratings jump up and down to various degrees, but as we saw with the average of each metric, LetterBoxd scores are generally highest and RT User the lowest. Studying how the ratings fluctuate between bond actors and how the different metrics change from film to film could unlock a lot about the evolution of the Bond series as well as inform about differences and similarities between our metrics. These two topics will be the focus of our next two sections, and in Appendix A we briefly study how much higher the LetterBoxd scores are than the others. 

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

As before, LetterBoxd scores are the highest, and Rotten Tomatoes users are almost always the most critical. Some metrics vary drasticaly across Bonds, while some are all similar: the range of RT critic averages across Bonds is 25 points, and IMDb's is 8 <sup> [2] </sup>. So the averages have a small range, but how much do the ratings change from film to film? This is the focus of the next section.

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


### Conclusion 
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

With a net global box office gross of $17.5 billion James Bond is *the* most financially successful film franchise in history, trailed by *Star Wars*, *The Marvel Cinematic Universe* and *Harry Potter* (in that order) <sup> [5] </sup>. Over two thirds of this gross is non-domestic, which is not entirely surprising since our secret agent works for Britian, not America/Canada <sup> [4]</sup>. This success, along with the gross averaging over six times their budget, and the films profit around $600 mill on average give us insight into how it has become [one of films longest running franchises](https://en.wikipedia.org/wiki/List_of_film_series_with_more_than_twenty_entries).

To give a better idea of the finances of individual films in our gilded franchise, let's take a quick look at the top, middle and bottom entries of each category. All figures are in millions of dollars.

|Figure|Max|Median|Min|
|---|---|---|----|
|Global|Thunderball ($1390)|Die Another Day ($640)|License to Kill ($340)|
|Domestic|Thunderball ($650)|Quantum of Solace ($200)|License to Kill ($80)|
|Profit|Goldfinger ($1360)|Diamonds are Forever ($570)|License to Kill ($250)|
|Budget|Spectre ($270)|Living Daylights ($90)|Dr. No ($10)|

Sean Connery's *Thunderball* is the highest grossing film globally and domestically, while his film *Goldfinger* was the most profitable with a budget in the 16th percentile. We also notice that *Thunderball*'s domestic gross is greater than half the Bond film's global gross, quite a feat for only the fourth film in the series. Timothy Dalton's *License to Kill* is the least successful in all financial respects even though in the rating section it's average of 4 rating is the median with 71/100. It turns out that the newest Bond film (*Spectre*) cost more to make than *License to Kill* profited.  Speaking of budgets, we see that not only is the newest film the most expensive, but the oldest (*Dr. No*) is the least. Assuming a linear increase between these two extremes, this gives that the budget increases by $10 mill every film<sup> [13] </sup>. These budget numbers are intriguing, but we will come back to them later, we will first look at how the global and domestic grosses have evolved over time.

### Domestic & Global Gross

To study the evolution of the gross we make a dataframe with the changes in gross between films, similarly to what we did for ratings.

```R
#Fill the dataframe up with change in global and domestic gross, as well as a counter variable and the names of Bond actors
for(i in 1:24){
    bo.diff[i,1]<-(bom$Glb.Adj[i+1]-bom$Glb.Adj[i])
    bo.diff[i,2]<-(bom$Dom.Adj[i+1]-bom$Dom.Adj[i])
    bo.diff[i,3]<-i
    bo.diff[i,4]<-bom$Bond[i+1]
}
#Add column names
colnames(bo.diff)[c(1:4)]<-c("Glb.Chng","Dom.Chng","Counter","New.Bond")

#Make a column of the sign of the entries for coloring our plots
bo.diff$glb.sgn<-ifelse(bo.diff$Glb.Chng>=0,'positive','negative')
bo.diff$dom.sgn<-ifelse(bo.diff$Dom.Chng>=0,'positive','negative')

#To make our y-axis labels more legible we divide all values by 1,000,000
bo.diff$Glb.Chng1<-with(bo.diff,Glb.Chng/1000000)
bo.diff$Dom.Chng1<-with(bo.diff,Dom.Chng/1000000)

#Lastly we solidfy the order of the names as before
bo.diff$New.Bond<-factor(bo.diff$New.Bond,levels=c("Sean Connery","George Lazenby","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig"))
```

Let's put the key information from this dataframe into a table. 

|Market|Max|Min|Mean|Mean>0|Mean<0|
|---|---|---|---|---|---|
|Global|$664,904,030|-$629,375,924|$11,138,529|$204,679,528|-$182,402,471|
|Domestic|$341,273,000|-$334,894,700|$1,470,679|$94,201,991|-$76,994,277|

Both the domestic and global mean change in gross are positive, with global and domestic gross increasing on average by $11 mill and $1.5 mill between films, respecitvely. The $11 mill change in global gross is weirdly close to our rough calculation of a $10 mill increase in budget between films. Not entirely surprising, the biggest jump domestically and globally are for the transition, from Connery's second film to his third, and the biggest drops likewise for his fourth to fifth films. 

We notice that although the overall global change mean is 7.5 times larger than the domestic, the positive & negative global change means are about twice that of the domestic changes, and on average the global gross is 60% larger than the domestic gross change <sup> [5] </sup>. The unexpectedly high nature of the overall mean ratio might be due to there being 12/24 positive global changes and only 11/24 positive domestic changes <sup> [9] </sup>.

Even though we saw that the largest increases and decreases in domestic and global markets are for the same film, how often do the two markets react similarly? We plot the domestic and global changes side by side to investigate.

Domestic Change            |  Global Change
:-------------------------:|:-------------------------:
![dom_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/dom_chng_plot.png) | ![glb_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/glb_chng_plot.png)

For the most part the two markets agree on the sign, disagreeing on only 5/24 transitions, or 20% of the time. Most of the disagreements are when new actor premieres; for domestic gross, all but once, the change was negative, and for global the change was, all but once, positive. It seems that North Americans are cold to new interpretations of Agent 007, with the exception of Pierce Brosnan, (possibly correlated with the six year hiatus in the series and the lowest grossing film being what he had to live up to). In opposition to this, global audiences seem to liken a new actor to a reinvigoration of the series. The exception in the global case is George Lazenby, which as we've discussed, had a hard job of living up to Sean Connery. 

The two markets generally agree on the sign of the change in gross (80% of the time), but what about the magnitude of the change? In a qualitative look at the two graphs, the magnitudes are generally comparable. To push this to a quantitative sense we have to decide how to compare two data sets. 

The first idea I had was to normalize each change by dividing through by the mean change for that market, then find the ratio of the normalized global change:normalized domestic change. Likely due to the 7.5 time difference between the means, this normalized ratio came out at a whopping 12. This is not very good support for theory that the magnitudes are similar.

The next idea I had was to noramlize by the mean for that subset, for example: normalize the negative domestic changes by the mean of the domestic changes. The normalized ratio for this method turned out to be 1.2, which highly supports our theory that the magnitudes are 'comparable'. The problem with the method is that by dividing by the subsets mean, you are losing information about the sign of the change. So even if the magnitudes of the changes are similar, but have different sign, they are seen as 'close', which is like pretending that +100 & -100 are identical changes. 

If we ignore the 5/24 occurances of differing signs, we get a normalized global change:normalized domestic change ratio of 0.9, and if we find the mean of just these cases of sign-disagreement, we get a 2.2 ratio. This indicates that when the global and domestic changes disagree on sign, the magnitudes are less similar than when they agree on sign (which pleases my gut since, if they can't even agree if the movie got better or worse, why would they agree on the magnitude of the change?). This isn't enough for me, I want to patch these together to get a final anwser number that reflects the ratio of global change to domestic change.

In order to patch these two groups together, we need a way to make the 'disagreer' ratios reflect how they differ in sign. It's at times like this that one must reflect on what these basic operations we take for granted really signify. Thinking about it in bar plot terms, the ratio of global gross to domestic gross is (assuming they have the same sign) how many times the domestic gross could fit into the global gross, but you can also think about it as how the height of the domestic change bar compares to that of the global bar *or* how far away they extremes of the bar are from eachother. To measure how far away disagreers are from eachothercan be accomplished in a fairly straightforward way: find the total difference then divide by the absolute value of domestic change. For example: Say the normalized global change is 500, and the normalized domestic change is -300. The ratio we assign this pair is (500-(-300))/300=2.7

you can think of it as a measure as how far away from how many times it would take the domestic bar to fit into the difference between the two bars. simply 




### Bond Actor

We start by making a dataframe that contains the various actor's mean values for the financial data.

```R
#we fill up the 'Bond' column with the 'names' vector from earlier and 'Average' which will be for averages over all actors
boxoffice<-data.frame(Bond<-c(names,"Average"))
#The loop is over 6 since we won't fill up the 'Average' entry in this way
for(i in 1:6){
     boxoffice$Glb.Mean[i]<-colMeans(subset(bom$Glb.Adj,bom$Bond==names[i]))
     boxoffice$Dom.Mean[i]<-colMeans(subset(bom$Dom.Adj,bom$Bond==names[i]))
     boxoffice$Prft.Glb[i]<-colMeans(subset(bom$Prft.Glb,bom$Bond==names[i]))
     boxoffice$Glb.Bdg.Ratio[i]<-with(bom,sum(subset(bom$Glb.Adj,bom$Bond==names[i]))/sum(subset(bom$Bdg.Adj,bom$Bond==names[i])))
}
# Add a term that takes the ratio of global and domestic average grosses 
boxoffice$Glb.Dom.Ratio<-with(boxoffice,Glb.Mean/Dom.Mean)
#Now fill in the 'Average' row with the average over all columns less the actor column
for(i in 2:6){
     boxoffice[7,i]<-mean(boxoffice[,i])
}
```
Here is what the dataframe looks like as a table:

|Bond|Avg. Global Gross|Avg. Domestic Gross|Glb:Dom Ratio|Avg. Global Profit|Glb:Bdg Ratio|
|---|---|---|---|---|---|
|Sean Connery|$857,381,211|$328,071,243|2.6|$806,338,430|16.8|
|George Lazenby|$496,640,912|$138,090,400|3.6|$448,188,140|10.2|
|Roger Moore|$594,410,522|$166,867,700|3.6|$528,506,844|9.0|
|Timothy Dalton|$379,864,046|$93,949,150|4.0|$290,278,294|4.2|
|Pierce Brosnan|$644,678,449|$223,328,250|2.9|$454,928,173|3.4|
|Daniel Craig|$885,619,047|$236,176,975|3.7|$655,951,017|3.8|
|Average|$643,099,031|$206,247,286|3.2|$530,698,483|7.9|


There's a lot to unpack here, so let's go slowly. First we see that while on average Craig grossed the most globally, Connery claims the box office crown domestically. Although Daniel Craig's interpretation of the character is easily the most raw and serious, he comes off a lot more stylish and debonair (read as: British) than Connery. Connery's 007 performances have more of a maverick feeling to them which could make them more attractive to Americans, with their 'Don't Tread on Me' mentality and fascination with the cowboy archetype. 

On the other end of the spectrum, at domestic and global box offices, Dalton's films grossed on average the least. As we saw, Dalton's second film *License to Kill* ranks as the lowest grossing Bond film domestically & globally, while his first film (*The Living Daylights*) is the third lowest grossing in both categories. Though *License to Kill* suffered from a higher-than-usual age classification in Britain <sup>[6]</sup> and a last-minute title change <sup> [7] </sup>, it doesn't explain why the first one was so poorly recieved. Well, what makes Dalton's Bond different from the rest?

After Roger Moore's playful, light-hearted Bond, the grit and realism of Dalton's performances must have been quite jarring for audiences. Dalton wanted the Bond from the novels, a darker and more broooding Bond (I must disclose that I have never read any Bond novels). In an interview, Dalton summarized his approach thus: "I think Roger was fine as Bond, but the films had become too much techno-pop and had lost track of their sense of story.  Every film seemed to have a villain who had to rule or destroy the world. If you want to believe in the fantasy on screen, then you have to believe in the characters and use them as a stepping-stone to lead you into this fantasy world. That's a demand I made." <sup> [8] </sup> According to my parents (Dad has read all the Fleming novels, and Mom has read none) it seems that those who were familiar with the literary charcter were mostly pleased with Dalton's more grounded approach whereas those who only knew the movies didn't enjoy how serious it was. In contrast with this, Daniel Craig's Bond which is darker than Dalton's in many ways, was extremelly well recieved and it even followed Brosnan's more light-hearted, Moore-esque Bond. This might mean that either Dalton took the unsatisfying middleground between fun and serious, or he helped paved the way for the appreciation of a darker Bond, which Craig reaped.

Next we see that Connery had the lowest ratio of global to domestic gross, while Dalton had the highest. As we discussed earlier, Connery's approach is quite American. While his average global gross is 3% lower than the Craig's (who has the highest average), his domestic gross is 28% higher than the next highest. Further, dividng the average gross for Connery by average gross for all bonds we find that Connery earned 33% more than average globally but 52% more domestically! So although his global:domestic ratio is low, it seems it is more because he has a strong domestic appeal rather than a weak global one.

With grosses rivaled only by Craig but with the lowest budgets, not only does an average Connery film profit over 50% more than the average Bond film, but his Profit:Budget ratio is over twice as high as the the average and this ratio is 60% higher than the next highest actor's ratio. It is hard to pretend that this is a completely fair analysis, the movie industry has changed a lot over the last 55 years. Directors and producers continually push the medium to create a more engaging and visercal cinematic experience. More extravagent sets and costumes, new cameras, and better special effects have caused budgets to [continue to pass new milestones every few years](https://en.wikipedia.org/wiki/List_of_most_expensive_films#Record-holders). It is hard to get around this since both the budgets and peoples willingness to go to the theatres (i.e. box office gross) are a product of their time, but it won't hurt to have a look at how the budgets have evolved over time.

### Budgets

Before we begin we create two new columns for the Profit:Budget and globa gross:Budget ratios in our 'bom' dataframe so we can look at the budgets film-by-film. We note here that we treat 'Profit' as Global Gross less Budget since films are a global product.

```R
bom$Prft.Bdg.Ratio<-bom$Prft.Glb/bom$Bdg.Adj
bom$Glb.Bdg.Ratio<-bom$Glb.Adj/bom$Bdg.Adj
```

We have seen that Connery has the largest average return on investment with a whopping $800 mill average profit and a total profit of $2.6 bn from his 6 films. These new columns show us that Connery's first film *Dr. No* grossed almost 60 times it's budget, his second and third around 40 times, and his average gross was 17 times his average budget (A 600% return on investmentis fantastic, but it's nowhere near *Paranormal Activity* which had a profit [nearly 1300 times it's budget](http://www.pajiba.com/seriously_random_lists/percentagewise-the-20-most-profitable-movies-of-all-time.php).).
Even at the bottom of this spectrum, Dalton's films profited $290 mill on average, and Pierce Brosnan's films grossed 3.4 times their budget. So if you've ever wondered why this franchise has lasted so long, here's why: they're very profitable movies. 

Our previous analysis showed that on average it seems that Bond films budgets have increased by $10mill/movie based on the highest and lowest grossing movies being the newest and oldest films in the franchise. We want to see if this is true, so we plot the budgets over time.

![budg_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/budg_plot.png?raw=TRUE)

So our two extremes were not anomalies, the film's budgets have defienitly increased, and quite dramatically. We add a linear regression to our plot to help visulize the upwards trend in budgets.  We note that our highest grossing film *Thunderball* had quite a large budget compared with the films around it (which might be partly due to it's [extensive underwater scenes](https://youtu.be/cuMM72G5k48?t=4m3s)). It cost three times as much to make as the proceeding film and twice as much as the average budget of the six succeeding films.  The $91 mill budget was not surpassed until Moore's $110 mill *Moonraker*, 15 years later.  

It is difficult to say how typical this is without doing a whole project on film budgets over time, but what we can do is look at how the films compare to the record holders at the time. Instead of spending a bunch of time on this, let's look at the first Bond film, the last and one in the middle. 

**First**: In 1963 *Dr. No* cost  $1 million to make and in that same year the Elizabeth Taylor and Richard Burton epic [*Cleopatra*](https://en.wikipedia.org/wiki/Cleopatra_(1963_film)) broke a new record with an $31 million budget (neither figure adjusted for inflation since we simply want to compare). 

**Median**: Roger Moore's 1983 *Octopussy* was made on a $27.5 million budget, and the record holder at the time was the 1978 movie [*Superman*](https://en.wikipedia.org/wiki/Superman_(1978_film)) with a budget of $88 million (when adjusted to 1983).

**Last**: 2015's *Spectre* had an unadjusted budget of $300 million while the record for a single film remains the [4th Johnny Depp *Pirates of the Caribbean*](https://en.wikipedia.org/wiki/Pirates_of_the_Caribbean:_On_Stranger_Tides) film from 2011, with a $400 million adjusted to 2015. 

This isn't exactly scientific: early on in the film industry they would produce a lot of low budget movies and a few expensive movies, whereas nowadays they produce a lot of films of similar budgets. What this treatment does is give one an idea of how the James Bond franchise has turned from just another film series to one of the industry's heavy-hitters. 

We've now looked at both budgets and gross, but a better measure of the success of a product is return on investment. So let's plot the ratio of profit:budget to see how our series looks to accountants.

![profit_budget_ratio](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/profit_budget_ratio.png?raw=TRUE)

We added a LOESS-method trendline to show the decrease-to-plateau nature of the data. Whether on purpose or by some unintentional/external force the franchise seems to have nestled itself in a 5:2 profit:budget ratio ever since Dalton's last film in 1989. 

Now we look at the films budgets to see if our presumption that they have increased is supported by data.




# Footnotes
<sup>[1]</sup> : In the FiveThirtyEight [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) I referenced, the point of interest is this paragraph: "The ratings from IMDb, Metacritic and Rotten Tomatoes were typically in the same ballpark, which makes this finding unsurprising: Fandango’s star rating was higher than the IMDb rating 79 percent of the time, the Metacritic aggregate critic score 77 percent of the time, the Metacritic user score 86 percent of the time, the Rotten Tomatoes critic score 62 percent of the time, and the Rotten Tomatoes user score 74 percent of the time." Therefore to see how much higher user scores are than the critics scores, we simply divide the two averages to eliminate the Fandango term: (RT.Crit)/(RT.User)=1.19 which gives us our quoted 19%. 

If we wish to continue this analysis:

IMDb vs. RT.Crit: Our metric suggests RT critics rate Bond films 3% higher than IMDb while FiveThirtyEight says that RT critcs scores are 21% lower.

IMDb vs. RT.User: Our metric says RT Users scores are 7% lower than IMDb and FiveThirtyEight puts this number at 6%.


<sup> [2] </sup>:
To find the range of averages for a metric across bond actors you simply subtract the max from the min. For example, with IMDb

```R
max(bond_rate$IMDB)-min(bond_rate$IMDB)
[1] 7.75
```

<sup>[3]</sup>:
18 years before he finally accepted the role, Timothy Dalton said the following about turning down an offer in 1968 to play Bond in "Oh Her Majesty's Secret Service": "Originally I did not want to take over from Sean Connery. He was far too good, he was wonderful. [...] When you've seen Bond from the beginning, you don't take over from Sean Connery." Source (https://www.youtube.com/watch?v=amuodiP-8z4) *This link has been taken down since I posted it. Sadly I cannot find a working version*

<sup>[4]</sup>:
I have a future project planned to study how domestic (US) and global gross compares for your typical wide-release film, so eventually I will be able to give more insight into how this ratio stacks up to similar films.

<sup>[5]</sup>: 
This can be calculated from figures [here](http://www.boxofficemojo.com/franchises/?view=Franchise&sort=sumgross&order=DESC&p=.htm), with the same process of finding the global gross adjusted for inflation from the domestic adjusted and global unadjusted.

<sup> [5] </sup>:
bo.diff$glb.dom.raw.ratio<-with(bo.diff,bo.diff$Glb.Chng/bo.diff$Dom.Chng)
mean(bo.diff$glb.dom.raw.ratio)
[1] 0.6324663

<sup>[6]</sup>:
[Source](http://www.bbfc.co.uk/releases/licence-kill)

<sup>[7]</sup>:
[Secondary souce](https://www.theguardian.com/film/2008/aug/21/1)

<sup>[8]</sup>:
Source: Rubin, Steven Jay (1995). The Complete James Bond Movie Encyclopedia (Revised ed.). McGraw-Hill/Contemporary Books. ISBN 0-8092-3268-5.

<sup> [9] </sup>:
To show why this could affect our values, let's replace this 13th negative domestic change with a positive one (the negative element and positive element are the respective signs medians): this would give us a mean of $5,818,575, which is almost exactly half of the $11 mill global change mean. 
 
In order to 'neaturalize' a negative element, I added the negative of the median negative domestic change (which is then a positive number) and the median positive domestic change to the sum before dividing by the number of elements, which gives the mean. 

NEW MEAN CHANGE=((REAL SUM OF DOMESTIC CHANGES)+(-MEDIAN NEGATIVE DOMESTIC CHANGE)+(MEDIAN POSITIVE DOMETIC CHANGE))/24

<sup>[11]</sup>:
I briefly looked at the LetterBoxd and Metacritic for the Bond and 538 analysis, respectively, but they were so different I thought it was best to simply ignore them for that analysis. Here is the max/min/mean for the two

||Mean|Max|Min|
|---|---|---|---|
|LetterBoxd|79.1|97.5|57.5|
|Metacritic|59|94|13|

<sup> [12] </sup>:
The 209 titles are 'recent' as of October 2015, when the article was written.

<sup>[13]</sup>:
The newest film has the highest budget and the oldest film has the lowest. So we find the increase as follows:

($270-$10)mill/(54years)=$4.8 mill/year

With 25 films in 54 years that gives us an average of 2.2 years/film. This means that there is a $4.8x2.2 = 10 mill/film increase.

<sup> [14]</sup>:
[For example](https://en.wikipedia.org/wiki/George_Lazenby#Leaving_Bond) is George Lazenby who quit said: "prior to the release of the film, Lazenby announced that he no longer wished to play the role of James Bond, saying, '[The Producers] made me feel like I was mindless. They disregarded everything I suggested simply because I hadn't been in the film business like them for about a thousand years.' "  

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
