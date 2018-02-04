# Metis Project 2: Predicting Win Rates for the Top 10% *PlayerUnknown's Battlegrounds* Players
 
![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/pubg.jpg?raw=true "PUBG Logo")

### What is *PUBG*?
##### ... for those unaquainted ...

*PlayerUnknown's Battlegrounds*, often refered to as *PUBG*, is a free-for-all multiplayer shooter in which players must violently compete for survival. With up to 100 players in each round, players are airdropped onto an island and must scavenge for guns, ammo, bandages and other equipment in order to last the round. As time goes on, a barrier encroaches on players, shrinking playable space and forcing players closer together in deadly confrontatons. The final person out of the beginning 100 is deemed the winner. Think of *PUBG* as the videogame version of *The Hunger Games*, or *Battle Royale*.

![](https://github.com/JeremyLyleBrown/JeremyLyleBrown.github.io/blob/master/images/media-20180204.gif?raw=true "PUBG GIF 1")

Released into early access in early 2017 and officially released at the end of the year, *PUBG* was the fastest growing game of 2017, with nearly 30 million owners on PC at the time of this writing. It made Steam's Top Sellers 2017 and Most Concurrent Players 2017, and is one of the top viewed games on the popular Twitch platform. With this massive success, some think the game has potential as an eSport, and a competitive scene has been growing with the growth of the game.

### The Project

Given *PUBG*'s potential as an eSport, I decided to investigate: can we predict who is tournament worthy? Given the all-or-nothing nature of the game, I determined that win-rate would be the determining factor in dictating tournament worthiness. My central question:
#### Out of the top 10% of players, how can we predict the best of the best? Who is tournament worthy?

### Data Collection

Lucky for me, a number of websites exist which keep track of *PUBG* player performance statistics. I decided to go with [pubgtracker.com](https://pubgtracker.com/) as a source to scrape player statistics from. Out of 27 million players, the website had statistics on 11,000,000. To limit my search due to time constraints for scraping, I decided to limit my search to North American servers coupled with the Solo 3rd Person game mode. This brought my top 10% search to about 19,000 players.

Data features on each player included three classifications. The first was behavior within a round, like many kills and heals a player had as well as distance walked across all rounds and on average, for example. The second was "best of" stats across all rounds, lik
