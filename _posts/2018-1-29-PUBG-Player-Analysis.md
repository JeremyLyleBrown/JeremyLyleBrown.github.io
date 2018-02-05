# Metis Project 2: Predicting Win Rates for the Top 10% *PlayerUnknown's Battlegrounds* Players
 
![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/pubg.jpg?raw=true "PUBG Logo")

### What is *PUBG*?
##### ... for those unaquainted ...

*PlayerUnknown's Battlegrounds*, often refered to as *PUBG*, is a free-for-all multiplayer shooter in which players must violently compete for survival. With up to 100 players in each round, players are airdropped onto an island and must scavenge for guns, ammo, bandages and other equipment in order to last the round. As time goes on, a barrier encroaches on players, shrinking playable space and forcing players closer together in deadly confrontatons. The final person out of the beginning 100 is deemed the winner. Think of *PUBG* as the videogame version of *The Hunger Games* or *Battle Royale* films.

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180204.gif?raw=true "PUBG GIF 1")

Released into early access in early 2017 and officially released at the end of the year, *PUBG* was the fastest growing game of 2017, with nearly 30 million owners on PC at the time of this writing. It made Steam's Top Sellers 2017 and Most Concurrent Players 2017, and is one of the top viewed games on the popular Twitch platform. With this massive success, some think the game has potential as an eSport, and a competitive scene has been growing with the growth of the game.

### The Project

Given *PUBG*'s potential as an eSport, I decided to investigate: can we predict who is tournament worthy? Given the all-or-nothing nature of the game, I determined that win-rate would be the determining factor in dictating tournament worthiness. My central question:
#### Out of the top 10% of players, how can we predict the best of the best? Who is tournament worthy?

### Data Collection

Lucky for me, a number of websites exist which keep track of *PUBG* player performance statistics. I decided to go with [pubgtracker.com](https://pubgtracker.com/) as a source to scrape player statistics from. Out of 27 million players, the website had statistics on 11,000,000. Due to time constraints for this project (only 2 weeks!), I decided to limit my search to North American servers coupled with the Solo 3rd Person game mode. This brought my top 10% search to about 19,000 players.

Data features on each player included three classifications. The first was behavior within a round, like *average kills*, *heals*, and *distance walked*, for example. The second was "best of" stats across all rounds, such as *most kills within a round*, *longest headshot*, and *longest time survived*. The third was outcome metrics, like *rounds played*, *wins*, and *losses*.

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180204%20(1).png?raw=true "pubgtracker player page")

Before building my scraping script, I had to assess the leaderboard webpages as well as players' indivual pages. I ordered the leaderboard pages by in-game ranking, based on match placement and kills within a match (unfortunately I could not find a definitive answer to the formula for ranking). I felt it was important to order by ranking rather than win-rate because it is entirely possible that a player could play the game a single time and win, therefore attaining a win-rate of 1.00.

Because the data on each players' page was begin generated with JavaScript, I decided to use [Selenium](http://docs.seleniumhq.org/) to collect each players' statistics, as the values weren't accessible through the HTML. I used [Scrapy](https://scrapy.org/) to grab players' usernames from the leaderboards and then constructed the URL's for Selenium to open and take data from. Rather than have Selenium click through each player to get the stats page I needed, constructing the URL's for Selenium to browse meant it could traverse fewer webpages and save valuable time. Scrapy was useful because it allowed me to close my laptop without fear of losing any data - my web crawler ended up taking about 24 hours total to get all the data I needed.

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180204.png?raw=true "pubgtracker leaderboards")

### Data Cleaning

Now that I had my 19,000 datapoints, I used [Pandas](https://pandas.pydata.org/) for the both cleaning and analysis. Lucky for me, my data was quite clean from the start - no missing points, standardized format within columns, and no duplicate entries. After some datatype conversion and unit standardization for times and distances, I was ready to work with my data.

In order to have an easier time running my regression, I didn't bother scraping ratios from the leaderboards, and calculated them myself. *Win-rate* and other ratios on the leaderboards were rounded to the nearest tenth, whereas I wanted as much decimal place precision as possible. I ended up with 30 total features per player.

### Exploratory Data Analysis

With a **mean *win-rate* of 5.97%** and a **median of 2.63%**, the data clearly shows that winning a round of PUBG is difficult even for the top 10% of PUBG players. Luckily for my analysis, it's apparent that winning PUBG isn't random chance - every feature had a relationship with *win-rate*. For example, *kill-per-game* had a positive correlation with *win-rate*, as pictured here:

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180205%20(1).png?raw=true "kills-per-game vs win-rate")

Additionally, *heals-per-game* had a negative correlation with *win-rate*:

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180205.png?raw=true "heals-per-game vs win-rate")

### Modelling Process
