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

We now bring this data into RStudio, creating seperate data frames for the seperate pages of the excel spreadsheet:

```R
#We make data frames with the data from: Box Office Mojo (bom), The Numbers (numb), and the rating websites (rate)
bom<-read.xlsx("jb_clean.xlsx",1)
numb<-read.xlsx("jb_clean.xlsx",2)
rate<-read.xlsx("jb_clean.xlsx",3)
```

*Note: Before we begin I must acknowledge a typo that was found at the end of the Rating section (thanks for spotting it Brad). The actor of the film "On Her Majesty's Secret Service" is named "George Lazenby", not "George Lazen" as I erronously typed it. This typo remains in the Rating section, but has been fixed for the Box Office sections and beyond. I apologize for any confusion.*

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
Tables are boring, let's visualize this. To do this we must transform our data frame using the 'melt()' function from the reshape 2 library.

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

So now that we've looked at how the franchise ranks on average, and how that average compares to other films, let's look at the ratings of specific films in the series.

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

The lowest score given out to a Bond film is 36/100 from Rotten Tomatoes critics for the film "A View to a Kill", which marks Roger Moore's last outing as Agent 007 (at the ripe old age of 57). The maximum score is 97.5 from LetterBoxd for "Casino Royale", Daniel Craig's first film in the series. We will investigate later how an actor's last and first film are generally rated, but for now we can imagine Moore's and Craig's performance were due to their age and exictment with the role.

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
#Now we create a blank data frame to fill up with the averages of the metrics based on Bond actor
bond_rate<-data.frame()
#Fill up the data frame with the names and averages with a loop and fix the column names
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
#Then we transform our data frame
bond_rate1<-melt(bond_rate,id=c("Bond","Avg.All"))
```


![actor_avg_metric](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/actor_avg_metric.png?raw=TRUE)

So the most recent actor to wear the 007 tux, Daniel Craig, is the most highly rated with an average score of 78/100, and Sean Connery, the Bond that broke in the penguin suit, is the second highest at 76. With only 1 film in the franchise, it was surprising that George Lazen takes the third spot with 75. As we briefly saw earlier and will see again soon fatigue with the character seems to affect the ratings, which might have worked in George Lazen's favour. Brosnan (63.6) just edges out Moore (63.8) to get last place, and Dalton hovers near the franchise average with a 71 rating.

As before, LetterBoxd scores are the highest, and Rotten Tomatoes users are almost always the most critical. Some metrics vary drasticaly across Bonds, while some are all similar: the range of RT critic averages across Bonds is 25 points, and IMDb's is 8 <sup> [2] </sup>. So the averages have a small range, but how much do the ratings change from film to film? This is the focus of the next section.

### Score Changes

In order to study how the ratings change between titles, and ultimately between Bonds, we must create a data frame that contains the difference in rating from film to film for each metric. In our data frame we will have a variable "Change.Number" to indicate which transition is being discussed. For example, Change.Number=1 will indicate the difference in ratings between film 1 and film 2. Change.Number will range from 1-24 since there are 25 titles in the franchise.

```R
#Create a blank data frame and fill it up with the differences
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

Financing movies is [very](http://www.npr.org/sections/money/2010/05/the_friday_podcast_angelina_sh.html) [complicated](https://www.theatlantic.com/business/archive/2011/09/how-hollywood-accounting-can-make-a-450-million-movie-unprofitable/245134/). It is no small task to raise hundreds of millions of dollars, often requiring [many parties with various investment models](https://www.youtube.com/watch?v=kgj0Z7zE0HM). When it comes to making the money back, there are box office sales, Blu-Ray/DVD sales, selling the film to streaming services, cereal campaign tie-ins etc. which the various funders get percentages/chunks from depending on how much invested. This is all kept under wraps . Even box office numberrs, which are quoted regularly in the news and on social media, aren't (*really*)[http://www.slate.com/articles/arts/culturebox/2006/01/ticket_tweaking.html] publically released. Further, the studios (don't even receieve all of the money from box office sales)[http://www.themovieblog.com/2007/10/economics-of-the-movie-theater-where-the-money-goes-and-why-it-costs-us-so-much/]. However, seeing as box office figures are the only consistent film finance data you can get your hands on and that how many people go to the theatre is 

First we state that all numbers in this section are in $USD, inflation calculations were made to May 2017, and 'domestic' refers to the US & Canada box office figures. Our two sources for this section are the websites Box Office Mojo (BOM) and The Numbers. BOM gives box office figures both adjusted and unadjusted for inflation, but only domestically. The Numbers on the other hand, gives domestic and global box office, as well as estimated budgets, all in unadjusted terms. 

While online ratings concern mostly modern opinions of these movies, box office figures are without hindsight. These numbers suffer from societal changes around the world including taste in movies, modifications of global/domestic marketing strategies, general interest in attending movies, and people's willingness to attend a movie based on actors, directors, producers etc. With that in mind, this section will have a lot more explinations of the background of films and actors with the goal of giving context to the box office nubers.

Our first order of business is to get the global box office figures adjusted for inflation.

### Global and Domestic Gross
To adjust our global box office data for inflation, I will create a vector that catalogues the ratio of unadjusted domestic to unadjusted global then multiply the adjusted domestic by this vector.

```R
#We create a vector with the ratio
glb.dom<-data.frame(with(numb,World.BxOf/Dom.BxOf))
#Then create a new column in our Box Office Mojo data frame with the adjusted domestic cross times these ratios
bom$Glb.Adj<-(bom$Adjusted.Gross*glb.dom)
#We do similarly with budgets, adjusting for inflation
bud.dom<-data.frame(with(numb,Production..Budget/Dom.BxOf))
bom$Bdj.Adj<-(bom$Adjusted.Gross*bud.dom)
#We change the adjusted domestic gross column name for consistancy
colnames(bom)[c(6,7,8)]<-c("Dom.Adj","Glb.Adj","Bdg.Adj")
#Then add a 'Profit' column for later
bom$Glb.Profit<-with(bom,Glb.Adj-Bdg.Adj)
```
We use Glb. for global gross, Dom for domestic gross, Prft for profit (i.e. gross - budget) and Bdgt for Budget. From here on out, everything will be in values adjusted for inflation to May 2017, unless stated otherwise. Let's look at the sums and means in table form:

|Figure|Sum|Mean|
|---|---|---|
|Global Adj.|$17,540,101,114|$701,604,045|
|Domestic Adj.|$5,628,582,200|$225,143,288|
|Budget Adj.|$2,723,922,708|$108,956,908|

First we note that over two thirds of the global box office gross is non-domestic. This is not entirely surprising since our secret agent works for Britian, not America <sup> [4]</sup>. Next, the films gross over 6 times their budget on average, which helps explain why it one of films longest-running franchises. Lastly, with a net global box office gross of $17.5 billion,, adjusted for inflation, James Bond is *the* most financially successful film franchise in history, trailed by Star Wars, The Marvel Cinematic Universe and Harry Potter (in that order) <sup> [5] </sup>.

So it's a very successful series overall, but how do the various actors compare? We want to study various things, so we create a new dataframe:

```R
#Where names is the vector of the actors names, in order, that was used earlier
boxoffice<-data.frame(Bond<-c(names,"Average"))
for(i in 1:6){
     boxoffice$Glb.Mean[i]<-colMeans(subset(bom$Glb.Adj,bom$Bond==names[i]))
     boxoffice$Dom.Mean[i]<-colMeans(subset(bom$Dom.Adj,bom$Bond==names[i]))
     boxoffice$Prft.Glb[i]<-colMeans(subset(bom$Prft.Glb,bom$Bond==names[i]))
     boxoffice$Glb.Bdg.Ratio[i]<-with(bom,sum(subset(bom$Glb.Adj,bom$Bond==names[i]))/sum(subset(bom$Bdg.Adj,bom$Bond==names[i])))
}
# Add a term that takes the ratio of global and domestic average grosses and fill in all the averages
boxoffice$Glb.Dom.Ratio<-with(boxoffice,Glb.Mean/Dom.Mean)
for(i in 2:6){
     boxoffice[7,i]<-mean(boxoffice[,i])
}
```

|Bond|Avg. Global Gross|Avg. Domestic Gross|Glb:Dom Ratio|Avg. Global Profit|Glb:Bdg Ratio|
|---|---|---|---|---|---|
|Sean Connery|$857,381,211|$328,071,243|2.6|$806,338,430|16.8|
|George Lazenby|$496,640,912|$138,090,400|3.6|$448,188,140|10.2|
|Roger Moore|$594,410,522|$166,867,700|3.6|$528,506,844|9.0|
|Timothy Dalton|$379,864,046|$93,949,150|4.0|$290,278,294|4.2|
|Pierce Brosnan|$644,678,449|$223,328,250|2.9|$454,928,173|3.4|
|Daniel Craig|$885,619,047|$236,176,975|3.7|$655,951,017|3.8|
|Average|$643,099,031|$206,247,286|3.2|$530,698,483|7.9|


There's a lot to unpack here, so let's go slowly. First we see that while on average Craig grossed the most globally, Connery claims the box office crown domestically. Although Daniel Craig's interpretation of the character is easily the most raw and serious, he comes off a lot more stylish and debonair (read as: British) than Connery. On the other hand, Connery's 007 performance have more of a maverick feeling to them. In my mind, Connery's 007 films are thus more attractive to Americans what with their "Don't Tread on Me" mentality and fascination with the cowboy archetype. Although not in this table we note here that Connery's "Thunderball" and "Goldfinger" take first and second place, followed by Craig's "Skyfall", for highest grossing films both stateside and around the world. Globally, each of these films grossed over $1bn. Domestically "Thunderball" grossed over $600mill, almost twice as much as "Skyfall".

On the other end of the spectrum, at domestic and global box offices, Dalton's films grossed on average the least. Dalton's second film "License to Kill" ranks as the lowest grossing Bond film domestically (one of only two that grossed <$100mill) and globally, while his first, "The Living Daylights", is third from last in both categories. Though "License to Kill" suffered from both a higher-than-usual age classification in Britain <sup>[6]</sup> and a last-minute title change <sup> [7] </sup>, it doesn't explain why the first one was so poorly recieved. Well, what makes Dalton's Bond different from the rest?

After Roger Moore's playful, light-hearted Bond, the grit and realism of Dalton's performances must have been quite jarring for audiences. Dalton wanted the Bond from the novels a darker and more broooding Bond (I must disclose that I have never read any Bond novels). In an interview, Dalton summarized his approach thus: "I think Roger was fine as Bond, but the films had become too much techno-pop and had lost track of their sense of story.  Every film seemed to have a villain who had to rule or destroy the world. If you want to believe in the fantasy on screen, then you have to believe in the characters and use them as a stepping-stone to lead you into this fantasy world. That's a demand I made." <sup> [8] </sup> According to my parents (Dad has read all the original Fleming novels, and Mom has read none) it seems that those who were familiar with the literary charcter were mostly pleased with Dalton's more grounded approach whereas those who only knew the movies didn't enjoy how serious it was. In contrast with this, Daniel Craig's Bond, darker than Dalton's in many ways, was extremelly well recieved and it even followed Brosnan's more light-hearted Moore-esque Bond. This might mean that either Dalton took the unsatisfying middleground between fun and serious, or he helped paved the way for the appreciation of a darker Bond, which Craig reaped.

Next we note that Connery had the lowest ratio of global to domestic gross, while Dalton had the most. As we discussed earlier, Connery's approach is quite American. As we saw earlier, Connery's "Thunderball" and "Goldfiner", and Craig's "Skyfall" are the three highest grossing films domestically and globally, and while all have comparable gross globally, "Thunderball" grossed twice as much as "Skyfall" in the domestica market. So although Connery's ratio is low, it appears that his domestic gross was just much higher than average. Dividng the average gross for connery by average gross for all bonds we find that Connery earned 33% more than average globally and 52% more domestically. 

The movie industry has changed a lot over it's ~100 year lifetime. Directors and producers continually push the medium, to create a more engaging and visercal cinematic experience. More extravagent sets and costumes, more cameras, and better special effects, have caused budgets to continue to pass new milestones every few years <sup> [9] </sup>. With this in mind, it is a little unfair to compare budgets of the films in the James Bond series over it's 55 year run, but hard to work around. So first we will look at the raw numbers, then we'll try to look at how the budgets have changed over time and see how this affects the profits over time.

We quickly create columns to compare the ratio of Profit to Budget and global gross to budget for each film.

```R
bom$Prft.Bdg.Ratio<-bom$Prft.Glb/bom$Bdg.Adj
bom$Glb.Bdg.Ratio<-bom$Glb.Adj/bom$Bdg.Adj
```

As expected due to his tenure early in the franchise, Connery has  the largest average return on investment, with a whopping $800 average profit (and a total global profit of $2.6 bn from his 6 films).  Connery's first film "Dr. No" grossed almost 60 times it's budget, his second and third around 40 times, and his average gross was 17 times his average budget. (60 times would surely please any producer, but it's nowhere near "Paranormal Activity" with a profit nearly 1300 times it's budget <sup>[10]</sup>)
Even at the bottom of this spectrum, Dalton's films profited $290 mill on average, and Pierce Brosnan's films grossed 3.4 times their budget globally. So if you've ever wondered why this franchise has lasted so long, thats why: they're very profitable. 

Based on our table, it seems that profit to budget has decreased with time, let's graph to study this.

![profit_budget_ratio](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/profit_budget_ratio.png?raw=TRUE)

We added a LOESS-method trendline to show the decrease-to-plateau nature of the data. Whether on purpose or by some unintentional/external force the franchise seems to have nestled itself in a 5:2 profit:budget ratio ever since Dalton's last film in 1989. 

Now we look at the films budgets to see if our presumption that they have increased is supported by data.

![budg_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/budg_plot.png?raw=TRUE)

From $10 to $300 million the Bond films budgets have skyrocketd over time, with an average yearly increase of $5 million. It is difficult to say how typical this is without doing a whole project on film budgets over time, but what we can do is look at how the films compare to the record holders at the time. Instead of spending a bunch of time on this, let's look at the first Bond film, the last and one in the middle. In 1963 "Dr. No" cost  $1 million to make and in that same year the Elizabeth Taylor and Richard Burton epic "[Cleopatra](https://en.wikipedia.org/wiki/Cleopatra_(1963_film))" broke a new record with an $31 million budget (neither figure adjusted for inflation). In 1989 Dalton's last Bond movie cost $42 million unadjusted, and a year later "Die Hard 2" broke another budget record with $62 million in 1990 terms. 2015's "Spectre" had an unadjusted budget of $300 million while the record for a single film remains the 4th Johnny Depp "Pirates of the Caribbean" film from 2011, with a $400 million adjusted to 2015 <sup> [9] </sup>. This isn't exactly scientific, early on in the film industry they would produce a lot of low budget movies and a few expensive movies, but nowadays they produce a lot of films of similar budgets. What this treatment does is give you an idea of how the James Bond franchise has turned from just another film series to one of the industry's heavy-hitters.

Now that we've seen how each actor performed relative to eachother, let's see how the domestic and global gross evolved from film to film. First we create a data frame of the changes in various quantities.

```R
for(i in 1:24){
    bo.diff[i,1]<-(bom$Glb.Adj[i+1]-bom$Glb.Adj[i])
    bo.diff[i,2]<-(bom$Dom.Adj[i+1]-bom$Dom.Adj[i])
    bo.diff[i,3]<-i
    bo.diff[i,4]<-bom$Bond[i+1]
}
colnames(bo.diff)[c(1:4)]<-c("Glb.Chng","Dom.Chng","Counter","New.Bond")
#Then make a column of the sign of the entries for coloring our plots
bo.diff$glb.sgn<-ifelse(bo.diff$Glb.Chng>=0,'positive','negative')
bo.diff$dom.sgn<-ifelse(bo.diff$Dom.Chng>=0,'positive','negative')
#Now we divide our change columns by 1,000,000 to make the graph more legible
bo.diff$Glb.Chng1<-with(bo.diff,Glb.Chng/1000000)
bo.diff$Dom.Chng1<-with(bo.diff,Dom.Chng/1000000)
#Lastly we solidfy the order of the names as before
bo.diff$New.Bond<-factor(bo.diff$New.Bond,levels=c("Sean Connery","George Lazenby","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig"))
```

Let's check out this figures quickly. The mean global change is +$10 million and domestic +$1.5 mill
Now we plot the change in gross between film for domestic and global figures

Domestic Change            |  Global Change
:-------------------------:|:-------------------------:
![dom_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/dom_chng_plot.png) | ![glb_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/glb_chng_plot.png)

For the most part they agree on the sign of the change (19/24 times), where most of the differences is the change in gross with the premier of a new actor: For domestic gross, all but once, the change was negative, and for global the change was, all but once, positive. 

In America it seems that theatre-goers are cold to new interpretations of Agent 007, with the acception of Pierce Brosnan. This exception is understandbly so as the previous film was *the* lowest grossing, and the 6 year hiatus in the series must have helped build up the excitement. Globally audiences seem to get excited with a reinvigoration of the series with a new Bond at the helm. The exception for this trend in the global market is George Lazenby, which as we've discussed, had a hard job of living up to Sean Connery.

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
I have a future project planned to study how domestic (US) and global gross compares for your typical wide-release film, so eventually I will be able to give more insight into where this ratio falls

<sup>[5]</sup>: 
This can be found [here](http://www.boxofficemojo.com/franchises/?view=Franchise&sort=sumgross&order=DESC&p=.htm) but requires the same process of finding the global gross adjusted for inflation from the domestic adjusted and global unadjusted. It is a tiresome exercise, and I don't wish to repeat it here.

<sup>[6]</sup>:
[Source](http://www.bbfc.co.uk/releases/licence-kill)

<sup>[7]</sup>:
[Secondary souce](https://www.theguardian.com/film/2008/aug/21/1)

<sup>[8]</sup>:
Source: Rubin, Steven Jay (1995). The Complete James Bond Movie Encyclopedia (Revised ed.). McGraw-Hill/Contemporary Books. ISBN 0-8092-3268-5.

<sup>[9]</sup>:
[Source](https://en.wikipedia.org/wiki/List_of_most_expensive_films#Record-holders) is a wikipedia table of record holders over time in the film industry. Please note that this is not adjusted for inflation.

<sup>[10]</sup>:
[Source](http://www.pajiba.com/seriously_random_lists/percentagewise-the-20-most-profitable-movies-of-all-time.php). This page shows top 20 most profitable movies.

<sup>[11]</sup>:
I briefly looked at the LetterBoxd and Metacritic for the Bond and 538 analysis, respectively, but they were so different I thought it was best to simply ignore them for that analysis. Here is the max/min/mean for the two

||Mean|Max|Min|
|---|---|---|---|
|LetterBoxd|79.1|97.5|57.5|
|Metacritic|59|94|13|

<sup> [12] </sup>:
The 209 titles are 'recent' as of October 2015, when the article was written.

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
 
 

# Appendix

## A
We note in this [graph](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/rate_bymetric.png?raw=TRUE) that the LetterBoxd scores are almost always above the other 3. We will study this observation:

```R
#Create the data frame to store the infor
lb_extra<-data.frame()
#Input into our data frame the difference between the LB rating and the max of the other 3
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
