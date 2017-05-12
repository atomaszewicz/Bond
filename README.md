# Bond

6 Actors, 55 years, 25 films, over $17 billion in box office gross (worldwide, adjusted for inflation), and innumerable car chases, gadgets, martinis and babes. Agent 007 is one of film's most iconic characters and his film francise ranks as the fourth most financially successful in history (after Star Wars, Harry Potter, and Marvel, in that order). The mixture of exaggerated villainy, epic heroism and bad puns are just right to make us both invested in the plot and characters yet able to laugh it off when Bond [jumps over a road in a motorboat](https://youtu.be/cODPt3T0cHE?t=51s).

# Outline
After countless discussions with friends about which is the best/worst bond film, the hottest babe, the best bond car, the best evil master plan, and of course which is the best bond, I decided to anwser some of these questions in the most objective way possible: by crunching some numbers. I pull financial and film-rating data from various online sources, organize in Excel, then analyze in RStudio.

# Data

For this project, we use box office data from [Box Office Mojo](http://www.boxofficemojo.com/franchises/chart/?id=jamesbond.htm), box office and budget data from [The Numbers](http://www.the-numbers.com/movies/franchise/James-Bond#tab=summary), and online rating scores from IMDB, Letterboxd and both critic and users scores from Rotten Tomatoes (RT). If you want more information on the data collection and 

There are 26 Bond films total, with 24 of them having been produced by Eon productions. The two films not produced by Eon are: Casino Royale (1967) staring David Niven as Bond, and Never Say Never Again (1983) where Sean Connery reprises his role as 007 over a decade after his last performance as the super spy. I decided to disclude Casino Royale, but include Never Say Never Again because.... lmao.


# Improvements
One thing missing from this analysis is the amount paid to an actor for a given film. That is because this info is missing for many films and when it is there, it is rather complicated as some actors recieved just a paycheck while some recieved a percentage of net merch or film gross. 

Another idea I had was to analyse the IMDB data for the Bond films by age and gender. The site has this data available for any given title (example, but not easily available for 
