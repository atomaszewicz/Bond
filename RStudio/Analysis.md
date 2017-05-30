# Analyzing the James Bond Film Franchise

## Set up

To begin we open RStudio, and change our directory to that which contains the James Bond data.

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

*Note*: Before we begin I must acknowledge a typo that was found at about the halway point in the project, at the end of the Rating section (thanks for spotting it Brad). The actor of the film "On Her Majesty's Secret Service" is named "George Lazenby", not "George Lazen" as I erronously typed it. This error remains in the Rating section, but has been fixed for the Box Office sections and beyond. I apologize for any confusion.

## Ratings

### Metrics
First let's look at how the different sites average ratings relate.

```R
#We make variables for each rating type, as well as the average of the 4, then make a table of the values
#Note that all the ratings have been set to be out of 100
avg_all<-mean(mean(rate$RT.Crit),mean(rate$RT.User),mean(rate$LetterBoxd),mean(rate$IMDB))
avgs<-data.frame(RT.Crit=(mean(rate$RT.Crit)),RT.User=(mean(rate$RT.User)),LetterBoxd=(mean(rate$LetterBoxd)),IMDB=(mean(rate$IMDB)),Avg.All=(avg_all))
```
We wish to visualize this, but first we must transform our data frame using the 'melt()' function from the reshape 2 library

```R
avgs_t<-melt(avgs)
```
![avg_rating_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/avg_rating.png?raw=TRUE)

The average for all the Bond films, based on the 4 metrics of choice is 71% which translates to a 3.5/5 star rating or in American Colleges, a C- or 1.67/4.00 GPA. This is not a great score, but then again the Bond films aren't necessarily great 'films'. They're B-movies at heart, not high art. What they're going for is a compelling cinematic adventure with an invincible super spy at the helm, and at this they excel. Anyways, back to those ratings.

It seems that Rotten Tomatoe users think lower of these films than users do critics, with the average user rating being 9% less than the critic ratings. This is an interesting result because in an intuitive sense I expected critics to be more ... critical of movies than your average Joe Rotten Tomatoes. This theory is reinforced by a FiveThirtyEight [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) where they compare online movie ratings for ~200 titles. In this article Rotten Tomatoes user ratings were on average 19% higher <sup>[1]</sup> than Rotten Tomatoes critic scores. On the other hand, critics probably better understand the place of the James Bond movies while our average theater-goer is keeping their score of Citizen Kane or Lawrence of Arabia in mind when they pencil in their rating for the 007 films.

Now let's see how individual films were rated. First let's look at the max and min scores.

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
So the minimum score of all 4 metrics is 36/100 from Rotten Tomatoes critics for the Bond film "A View to a Kill", which marks Roger Moore's last outing as Agent 007, and the maximum score is 97.5 from LetterBoxd for "Casino Royale" Daniel Craig's first film in the series. We will see later how an actor's last and first film are generally rated, but for now we can take this as Moore being bored with the role and Craig being very excited, giving it his all.

It is not surprising that LetterBoxd claims the highest rated film since it has the highest average rating of 79.1/100 (12% higher than the average of our 4 metrics). RT critic score was on average the second highest rating (70.8/100) among our 4 metrics but according to critics "A View to a Kill" was the weakest entry in the series.

We now check what is the highest/lowest rated films on average:
```R
rate[which.max(rate$Avg.All),]
[1]    RT.Crit RT.User LetterBoxd IMDB Avg.All         Title
[1]22      95      89       97.5   80   90.375 Casino Royale

rate[which.min(rate$Avg.All),]
[1]     RT.Crit RT.User LetterBoxd IMDB Avg.All            Title
[1] 15      36      41       67.5   63   51.875 A View to a Kill
```
An unsuprising result, the films with the highest and lowest average scores are also the ones with the highest and lowest individual ratings. Since it's only an average over 4 the high/low rating will drag up/down the rating significantly, not to mention that each individual rating is presumably a trustworthy metric on the quality of the film. 

Let's visualize how our 4 metrics change over time. 

```R
First we must add a 'date' and 'bond' column to our data
rate$Date<-bom$Release
rate$bond<-bom$Bond
#Note that by doing this we are taking our average of 4 out of the column with all the ratings.
rate_1col<-melt(rate,id=("Title","Date","Avg.All","Bond"))
colnames(rate_1col)[c(4,5)]<-c("Metric","Rating")
```
![rate_metricbond](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/rate_metricbond.png?raw=TRUE)
 
The ratings jump up and down to various degrees, but as we saw with the average of each metric LetterBoxd scores are generally higher and RT User are generally lower. In the next section we will look at how the different actors potrayal of agent 007 stack up, then after that we will investigate the change between movies (i.e. how big the jumps up and down are). In Appendix A we briefly study how LetterBoxd scores are almost always above the others.

### Bond Actor

We wish to examine how each Bond Actor was scored on average.

```R
#First we create a vector of the names and a blank data frame to store our average by bond and by metric
names<-c("Sean Connery","George Lazen","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig")
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
Before we look at a graph, we briefly make up a table to examine the average scores of Bond actor (based on our 4 metrics).

|Bond|Avg.All|
|---|---|
|Sean Connery|76|
|George Lazen|75|
|Roger Moore|64|
|Timothy Dalton|71|
|Pierce Brosnan|63|
|Daniel Craig|78|

So the newest actor to adorn the well-worn 007 tuxedo, Daniel Craig, is the most highly rated, and Sean Connery, who first broke it in, the second highest. Suprisingly, with only 1 film in the franchise, George Lazen takes the third spot. Now let's plot all the metrics to see how they compare across actors.

![actor_avg_metric](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/actor_avg_metric.png?raw=TRUE)

As before LetterBoxd scores are always the highest, and Rotten Tomatoes users are very critical of the Bond films.

In fact, the range of averages in IMDb scores for the different actors is only 7.8 points <sup> [2] </sup>, whereas for RT Critic, RT User, and LetterBoxd are 24.8, 17.6 and 16.25 respectively. In other words, IMDb has less than half the range of the next lowest, and less than a third the range of the rating with the biggest spread across Bond actors. This is a very intriguing result, and warrants further investigation.

### Score Changes

To start let's look into how much each rating changes between titles.

```R
#Create a blank data frame and fill it up with the differences
diff<-data.frame()
for(i in 1:23){
    diff[i,1]<-(rate$RT.Crit[i+1]-rate$RT.Crit[i])
    diff[i,2]<-(rate$RT.User[i+1]-rate$RT.User[i])
    diff[i,3]<-(rate$LetterBoxd[i+1]-rate$LetterBoxd[i])
    diff[i,4]<-(rate$IMDB[i+1]-rate$IMDB[i])
    diff[i,5]<-i
    diff[i,6]<-rate$bond[i+1]
}
#Fix the column names
colnames(diff)[c(1,2,3,4,5,6)]<-c("RT.Crit","RT.User","LetterBoxd","IMDB","Change.Number","New.Bond")
#I want to clarify that 'Change Number' refers to the transition number
#For example, the change in rating from film 2 to film 3 is referred to by the change number 2.
#Also 'New Bond' refers to the bond in the second movie in the transition
#We quicly re-order this so it shows up correctly later
diff$New.Bond<-factor(diff$New.Bond,levels=c("Sean Connery","George Lazen","Roger Moore","Timothy Dalton","Pierce Brosnan","Daniel Craig"))
```
Now we can analyze these numbers how are different ratings change between entries in the franchise.
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
We summarize the results in a table

|Metric|Max|Min|Mean|Mean>0|Mean<0|
|---|---|---|---|---|---|
|RT.Crit|37|-32|-1.29|19.3|-18.7|
|RT.User|48|-33|-0.83|18.2|-14.4|
|LetterBoxd|40|-27.5|-0.42|13|-10.7|
|IMDB|19|-13|-0.21|5.5|-4.6|
|Avg|35.5|-26.4|-0.69|14|-12|

The largest change was a 48 point boost between films, the smallest a -33 point drop, and on average, the films dropped about -0.7 points between films. So overall it appears the films have dropped in quality very slightly. Furhtermore, the max is always larger than the min (in absolute value), the mean>0 is always greater than the mean<0 and yet the overall mean is always negative! 

This means that there must be more negative changes than positive ones, but that the positive changes are generally larger. Interpreting this, we see that generally people think the movies are worse than the last one, but when they think it has improved, they are very enthusiastic about it, giving the new film very high ratings.


```R
#We transform to put all the ratings in one column to plot and analyze
diff_1col<-melt(diff,id=c("Change.Number","New.Bond"))
colnames(diff_1col)[c(2,3,4)]<-c("Bond","Metric","Rating.Change")

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
As expected there are more negative entries than positive entries. We can verify these numbers with a little algebra, see Appendix B for this treatment. 

Before we graph these results, we add a column of the sign of the change to help visualization the positive/negatrive gain/loss in score
```R
diff_1col$sign<-ifelse(diff_1col$Rating.Change>=0,'positive','negative')
```

![chng_rat_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/chng_rat_plot.png?raw=TRUE)

It seems that most of the time, the metrics agree on the sign of the change, i.e. they agree whether the newest film is better/worse than the last one. Let's see how often this is true. We simply check that all four metrics have the same sign.

```R
diff$sign_chng<-ifelse(sign(diff$RT.Crit)==sign(diff$RT.User)&sign(diff$RT.User)==sign(diff$LetterBoxd)&sign(diff$LetterBoxd)==sign(diff$IMDB),1,0)
sum(diff$sign_chng)
[1] 15
```
We note that due to the nature of the 'sign()' function zero has it's own sign. If we count the 3 occurances of a zero change this ratio rises to 18/24 or 75%! Thus around three quarters of the time our 4 metrics agree on whether a movie got better or worse.

Now we can look at how much it changes between Bond actors! We already have the infrastructure set up to plot this, so we do:

![bond_rat_chng](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/bond_rat_chng.png?raw=TRUE)

Before we look at this graph there are a few points I would like to make. First, the scales are different for each Bond. The second point is that the ratings stack; originally I made this graph with only the average scores, but I felt that this left out information about whether the metrics agree with one another from the previous section.

Every time a new Bond actor premieres a film, the rating change is positive, with the exception of George Lazen (but Sean Connery is hard to live up to! <sup> [3] </sup>). This is likely due to a double effect of the previous Bond actor growing bored with the character, with this affecting their performance, and the studio putting a lot of effort and care into the new film so that the audience doesn't sour to the new Bond. 

We also notice that every Bond actor -less Daniel Craig- has one (and only one) entry where the sign of the change in score doesn't agree across metrics and it is always their last film. Then again, Daniel Craig hasn't finished his tenure as Bond, so this may still hold for him. Most of the actors grew tired of the role and eventually quit, with the exceptions of Lazenby who quit due to overly-contorlling producers and Dalton who quit after the franchise went through a long legal battle. One might jump to conclude that the mixed reviews caused the actors to quit, but some made up their minds to leave the series before their last film even premiered.  So due to the variety of situations surrounding 007 actors quitting the series, it is difficult to analyze why their last films all have positive and negative responses. Nevertheless, it is interesting that all the Bond actors have gone out on mixed reviews.
 
 
### Conclusion
Throughout the Rating section, we have seen how our four metrics (Rotten Tomatoes Users, Rotten Tomatoes Critics, LetterBoxd and IMDb ratings/100) ratings differ between films and actors in the Bond series. We saw that the series' average rating, based on our four metrics, was 71/100. LetterBoxd was the most generous rating, with an average score of 79, and Rotten Tomatoe Users the harshest scoring only 64.  We found that on average Daniel Craig was the highest rated and Pierce Brosnan the lowest, with scores of 78 and 63 respectively. We then studied how the ratings changed between films, and between actors, and saw that when a new Bond actor premiers, the ratings usually (all but George Lazenby) make a positive jump, and on an actor's last film, the metrics' disagree on the change in quality. In the next section we will analyze the box office performance of the films and actors.

## Box Office

First we state that all numbers in this section are in $USD, inflation calculations were made to May 2017, and 'domestic' refers to the US box office figures. Our two sources for this section are the websites Box Office Mojo (BOM) and The Numbers. BOM gives box office figures both adjusted and unadjusted for inflation, but only domestically. The Numbers on the other hand, gives domestic and global box office, as well as estimated budgets, all in unadjusted terms. 

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

After Roger Moore's playful, light-hearted Bond, the grit and realism of Dalton's performances must have been quite jarring for audiences. Dalton wanted the Bond from the novels a darker and more broooding Bond (I must disclose that I have never read any Bond novels). In an interview, Dalton summarized his approach thus: "I think Roger was fine as Bond, but the films had become too much techno-pop and had lost track of their sense of story. [...] Every film seemed to have a villain who had to rule or destroy the world. If you want to believe in the fantasy on screen, then you have to believe in the characters and use them as a stepping-stone to lead you into this fantasy world. That's a demand I made [...]." <sup> [8] </sup>. According to my parents (my Dad has read all the original Ian Fleming novels, and Mom has read none) it seems as though at the time those who were familiar with the literary charcter were mostly pleased with Dalton's more grounded approach whereas those who only knew the movies didn't enjoy how serious it was. In contrast with this, Daniel Craig's Bond, darker than Dalton's in many ways, was extremelly well recieved and it even followed Brosnan's more light-hearted Moore-esque Bond. This might mean that either Dalton took the unsatisfying middleground between fun and serious, or he helped paved the way for the appreciation of a darker Bond, which Craig reaped.

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

From it's 'humble' $10 million beginning to "Spectre"'s recent $300 million budget, the Bond films have seen their budgets increase signficantly. It is difficult to say how typical this is without doing a whole project on film budgets over time. What we can do is look at how the films compare to the record holders at the time. In 1963 "Dr No" was released, having been filmed on a $1 million. In that same year the Elizabeth Taylor and Richard Burton epic " [Cleopatra](https://en.wikipedia.org/wiki/Cleopatra_(1963_film) " broke a new record with an estimated $31 million (unadjusted) budget. 2015's "Spectre" had an unadjusted budget of $300 million while the record for a single film remains the 4th Johnny Depp "Pirates of the Caribbean" from 2011 with a $400 million adjusted for inflation to 2015 <sup> [9] </sup>.





# Footnotes
<sup>[1]</sup> : In the FiveThirtyEight [article](https://fivethirtyeight.com/features/fandango-movies-ratings/) I referenced, the point of interest is this paragraph: "The ratings from IMDb, Metacritic and Rotten Tomatoes were typically in the same ballpark, which makes this finding unsurprising: Fandango’s star rating was higher than the IMDb rating 79 percent of the time, the Metacritic aggregate critic score 77 percent of the time, the Metacritic user score 86 percent of the time, the Rotten Tomatoes critic score 62 percent of the time, and the Rotten Tomatoes user score 74 percent of the time." Therefore to see how much higher user scores are than the critics scores, we simply divide the two averages to eliminate the Fandango term: RT.Crit / RT. User =1.19 which gives us our quoted 19%. 

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
I say this partly in jest, but Timiothy Dalton, originally offered the role in "Oh Her Majesty's Secret Service" in 1968, turned it down because "Originally I did not want to take over from Sean Connery. He was far too good, he was wonderful. [...] When you've seen Bond from the beginning, you don't take over from Sean Connery." Source (https://www.youtube.com/watch?v=amuodiP-8z4)

<sup>[4]</sup>:
I have a future project planned to study how domestic (US) and global gross compares for your typical wide-release film, so eventually I will be able to give more insight into where this ratio falls

<sup>[5]</sup>: 
This can be found [here](http://www.boxofficemojo.com/franchises/?view=Franchise&sort=sumgross&order=DESC&p=.htm) but requires the same process of finding the global gross adjusted for inflation from the domestic adjusted and global unadjusted. It is a tiresome exercise, and I don't wish to repeat it here.

<sup>[6]</sup>
[Source](http://www.bbfc.co.uk/releases/licence-kill)

<sup>[7]</sup>
[Secondary souce](https://www.theguardian.com/film/2008/aug/21/1)

<sup>[8]</sup>
Source: Rubin, Steven Jay (1995). The Complete James Bond Movie Encyclopedia (Revised ed.). McGraw-Hill/Contemporary Books. ISBN 0-8092-3268-5.

<sup>[9]</sup>
[Source](https://en.wikipedia.org/wiki/List_of_most_expensive_films#Record-holders) is a wikipedia table of record holders over time in the film industry. Please note that this is not adjusted for inflation.

<sup>[10]</sup>
[Source](http://www.pajiba.com/seriously_random_lists/percentagewise-the-20-most-profitable-movies-of-all-time.php). This page shows top 20 most profitable movies.



# Plot Code

I break the plot info into multiple elements so that it is easier to edit if I change something and because then you don't have to scroll in GitHub to see it all.

## avg_rating_plot
A bar plot that shows the average rating by metric 
```R
#Since our 'avgs' data frame is in table form, we create a new, transposed, dataframe using 'melt()' from the reshape2 library
avgs_t<-melt(avgs)
avg_rating_plot<-ggplot(avgs_t,aes(x=variable,y=value))+geom_col()+coord_cartesian(ylim=c(60,80))
labels<-xlab("Rating Metric")+ylab("Average Score/100")+ggtitle("Bond Film Average Rating by Metric")
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
labels<-ggtitle("Average Bond Ratings by Metric")+xlab("Bond Actor")+ylab("Rating")+labs(fill="Metric",caption="Black Bar = Avg. of 4)
coord_avg<-coord_cartesian(ylim=c(55,90))+geom_errorbar(aes(ymax=Avg.All,ymin=Avg.All))
#Where this last element is our custom y-axis limits and errorbars, a 'hack' to get horizontal bars for the averages
```

## chng_rat_plot:
A bar plot that shows the change in score between movies, separated by metric 
```R
#Now we graph, making the 4 different metrics on 4 different graphs but in the same plot
chng_rat_plot<-ggplot(diff1,aes(x=Change.Number,y=Rating.Change,fill=sign))+geom_col()+facet_grid(Metric ~ .)
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

We verify that we have computed the correct number of positive, negative and nil changes in movie ratings between movies using basic algebra and knowledge of how means of subsets relate to mean of the set. We multiply the mean of the different signs by the number of entries of that sign, and that should sum up to the total mean multiplied by the total number of entries. 

(avg of positives)\*(# of positives)+(avg of zeroes)\*(# of zeroes)+(avg of negatives)\*(# negatives)=(avg of set)\*(# in set)

Left Hand Side: 14\*40+0\*4-12.04\*52=560-626.08=-66.08 \n
Right Hand Side: -0.687\*96=-65.9

Which agrees within signficant figures.
