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

With a **mean *win-rate* of 5.97%** and a **median of 2.63%**, the data clearly shows that winning a round of *PUBG* is difficult even for the top 10% of *PUBG* players. Luckily for my analysis, it's apparent that winning *PUBG* isn't random chance - every feature had a relationship with *win-rate*. For example, *kill-per-game* had a positive correlation with *win-rate*, as pictured here:

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180205%20(1).png?raw=true "kills-per-game vs win-rate")

Additionally, *heals-per-game* had a negative correlation with *win-rate*:

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180205.png?raw=true "heals-per-game vs win-rate")

It's noteworthy that many of my features were correlated with one another, but none too highly to warrant removing them. I'd eventually let [scikit-learn](http://scikit-learn.org/stable/index.html)'s regularization methods take care of potential errors here.

### Modelling Process

For my own learning, I decided to run my model with incremental corrections, arriving at a more accurate model by the end of the process.

I began with Ordinary Least Squares linear regression run through [statsmodels](https://pypi.python.org/pypi/statsmodels), without any sort of test/train split or cross-validation. I arrived at an r^2 of 7.39 (same for adjusted r^2, so it seemed that there was little-to-no penalization for excessive features). I then manually pruned out features with high p-values, getting rid of *damage-dealt (total)*, *kills (total)*, *most-kills-in-a-round*, and *vehicle-destroys (total)*. Manually removing these features and re-running the model didn't result in any improvement to the r^2 score.

I then ran OLS with a test/train split in the data, and got an r^2 of 7.31. Nest, I ran OLS with cross-validation and automatic feature selection based on p-values through scikit-learn, and ended up with an r^2 of 7.33. This more rigorous methodology didn't vastly reduce my model's score, but it did a little bit, and it was more correct.

Regularization to the rescue! I used Ridge cross-validation regression and got an r^2 of 7.32 - not an improvement. But running Lasso regression resulted in an r^2 of 7.48. This wasn't a significant improvement, but it was an improvement of sorts, and I was able to determine that the 3 most significant features affecting the model were *rounds-played*, *kills-per-game*, and *damage-dealt-per-game*.

To check that the regression is linear and that errors are evenly distributed, I plotted my residuals:
![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180205%20(2).png?raw=true "residuals plot")
Great! No evidence of heteroskedasticity and the errors indeed appear to be evenly distributed.

### Conclusions

With my final model, I was able to predict the *win-rate* of the very good to the very best of *PUBG* players. This model can be useful for eSports teams looking to identify top-performing players to add to their roster.

I'd definitely like to run this project again by collecting data on more players across more regions and in more game modes. It's likely that performance in one game made can have an effect on performance in another, and given more time and processing power, I would have collected these stats. With more data, hopefully I can get a higher performing model, though I am pleased with the result I was able to achieve.

Here's a [link](https://github.com/JeremyLyleBrown/Metis_Project02) to the project repo on Github. Thanks for reading!
