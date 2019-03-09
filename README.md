# Bond

6 Actors, 55 years, 25 films, over $17 billion in box office gross, and innumerable car chases, gadgets, martinis and babes. Agent 007 is one of film's most iconic characters and his film francise ranks as *the* most financially successful, when adjusted for inflation. The mixture of exaggerated villainy, epic heroism and bad puns are just right to let us be invested in the plot but able to laugh it off when Bond [jumps over a road in a motorboat](https://youtu.be/cODPt3T0cHE?t=51s).

# Outline
After countless discussions with friends and family about the best Bond film and the best Bond.. I pull financial and online-rating data from various sources, organize it in excel then study it in RStudio. 

I begin by looking at how 4 online ratings (Rotten Tomatoes Users, Rotten Tomatoes Critics, Letterboxd and IMDb) compare and contrast for the 25 film franchise, and how they rate different films and actors. Next I look at financial data, including box office grosses and budgets, to gauge how successful the films and actors were, and in what markets (domestic & foreign) they stood out. I then briefly combine these two approaches.

# Data

For this project, we use financial data from [Box Office Mojo](http://www.boxofficemojo.com/franchises/chart/?id=jamesbond.htm) and [The Numbers](http://www.the-numbers.com/movies/franchise/James-Bond#tab=summary). The online rating scores from [IMDB](http://www.imdb.com/), [Letterboxd](https://letterboxd.com/) and both critic and users scores from [Rotten Tomatoes](https://www.rottentomatoes.com/) (RT). If you want more information on the sources and methods of data collection, please see the [data section](https://github.com/atomaszewicz/Bond/tree/master/Data) of this project. 

# Analysis

For the full analysis please see [here](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Analysis.md).

## Online Rating

The average of the aggregated score of our 4 metrics for the entire series is 71/100. Letterboxd rates the films the highest and RT users rate it the lowest, with average scores of 79 and 64 respectively. Even though the ratings decrease over the course of the franchise by -0.3 per film, the actor with the highest aggregated score is Daniel Craig with 78/100, followed closely by Sean Connery, the first actor to portray the character, with a score of 76. The worst-rated actor was Pierce Brosnan with a score of 63. 

![rate_metricbond](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/rate_metricbond.png?raw=TRUE)

Our 4 metrics agree 75% of the time on the sign of the change in quality between films. When a new actor premieres the 4 generally agree that the change is positive, and on each Bond's last film the changes are mixed. 

![bond_rate_chng](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/bond_rat_chng.png?raw=TRUE)

## Financial Data

The Bond films have grossed $17 bn when adjusted for inflation, making it the most financially successful film franchise, about two thirds of this is from foreign markets, but [Bond is British](https://youtu.be/1AS-dCdYZbo?t=3m35s) afterall. Despite pulling in the most globally ($1.39bn) and domestically ($650 mill) *Thunderball* was not the most profitable nor did it gross the most in foreign markets (titles which *Goldfinger* and *Skyfall* take, with the former profiting $1.36 bn and the latter grossing $860 mill). 

When a new actor premieres, the foriegn box office gross generally increases relative to the last film, while in domestic markets, it decreases. It could thus be wise for studios to spend a litle more on their advertising campaigns state-side when Daniel Craig's replacement is [finally chosen](https://www.thesun.co.uk/tvandshowbiz/1703522/james-bond-odds-next-007-tom-hardy/).

Domestic Change            |  Foreign
:-------------------------:|:-------------------------:
![dom_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/dom_chng_plot.png?raw=TRUE) | ![glb_chng_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/non_dom_chng_plot.png?raw=TRUE)

Daniel Craig dominates global and foreign markets on average, earning $890 mill and $650 mill per film respectively, but it is Connery who captured the most gross domestically (average of $330 mill per film) and who profited the most ($810 mill). Timothy Dalton does the worst in all 3 markets, as well his film *License to Kill* having grossed the least globally and domestically.

The budgets have skyrocketd over the course of the series, from the first film's $10 mill budget to the most recent film's $300 mill budget, and an average increase of $9.2 mill per film.

![budg_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/budg_plot.png?raw=TRUE)

Looking at return on investment, Connery made the accountants the happiest with a profit:budget ratio of 17:1, whereas our golden boy Craig had the second lowest ratio at 19:5. Quite a large differece, but seeing as Connery's budgets were a quarter those of Craig, this is not as surprising. Over the course of the series this ratio has been around 5:2 since 1990, having fallen there in a remarkabley logarithmic way. 

![profit_budg_ratio](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/profit_budget_ratio.png?raw=TRUE)

Normalizing global gross and online ratings by their means then taking the average of these two to create a 'combined score', we get that, at least in the logic of this metric, Craig is the best overall Bond, scoring 1.18, just ahead of Connery who scored 1.15. The order is then Brosnan in third with 0.91, Lazenby with 0.89, Moore with 0.87 and Dalton in last with 0.77.

Lastly, comparing Global gross directly to online rating we see that a 1 point change in rating is correlated with a $13.5 mill increase in gross. 

![glb_rate_plot](https://github.com/atomaszewicz/Bond/blob/master/RStudio/Plots/glb_rate_plot.png?raw=TRUE)

# Improvements
One thing missing from this analysis is the amount paid to an actor for a given film. That is because this info is missing for many films and when it is there, it is rather complicated as some actors recieved just a paycheck while some recieved a percentage of net merch or film gross. 

Another idea I had was to analyse the IMDB data for the Bond films by age and gender. The site has this data available for any given title.

Work more with the 'combined score', taking into account more factors.

Lastly, something that I started to do, but ended up being confusing and difficult to formalize, was to create a sort of "confidence" metric based on how many submissions go into the aggregated rating. That way I could use a lot more sites scores. The first hitch is that generally the older a film is, the less ratings it has, and devising a schema for the decline in votes with age is a whole project by itself. The second hitch is that critics scores usually involve 10-1000x less votes than do users. 
