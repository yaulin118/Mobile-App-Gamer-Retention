# Background

We've been hired by a mobile game company. Like most mobile games, this game has a store where players can buy a vast array of different items. Matches are composed of two players going head-to-head against each other. These two facts mean that there is a rich store of four tables:

As it is the game's one-year anniversary, my manager has asked me and one other team member to investigate player retention. Of specific interest is counting rolling 30-day retention and expressing it as a fraction of the total playerbase at the time. The tools we used are BigQuery and Google Sheets.

To address the question, we used the two following tables for our investigation:

1. Match information, including the players who matched against each other, and the outcome
2. Player information, including information like the player's age and when they joined
