# Background Story

![image](https://user-images.githubusercontent.com/94856154/154996331-b3b41f71-8e17-4302-afc8-09b0253173d9.png)
(Picture from Microsoft)

We've been hired by a mobile game company. Like most mobile games, this game has a store where players can buy a vast array of different items. Matches are composed of two players going head-to-head against each other. These two facts mean that there is a rich store of four tables:

As it is the game's one-year anniversary, my manager has asked me and one other team member to investigate player retention. Of specific interest is counting rolling 30-day retention and expressing it as a fraction of the total playerbase at the time. The tools we used are BigQuery and Google Sheets.

To address the question, we used the two following tables for our investigation:

1. Match information, including the players who matched against each other, and the outcome
2. Player information, including information like the player's age and when they joined

The SQL table should be including the folloing columns:
1. The day in question
2. The total number of players who joined that day
3. Of the players who joined, how many were retained
4. The fractional retention (the third column divided by the second column).



# Q1: Is 30-day rolling retention increasing or decreasing over the lifecycle of the game?
![Increasing or decreasing over the lifecycle of the game_](https://user-images.githubusercontent.com/94856154/155636714-af4473ef-619e-46f0-b7cc-c324c78c8896.png)

We excluded the last 30 days of the year from our analysis because those who joined in the previous 30 days would have automatically been categorized as Not-Retained. As a result, notably, we saw a dramatic decline at the end of the year. Therefore, we only choose the data from Day 1 to Day 335 for this project.

Given the data above, we observed that from the trendline, overall, the retention rate was relatively consistent throughout the year(Average 65.46%). From a growth perspective, while the retention rate is stable in the trendline, the growth hasn't significantly increased since the Q1. There appear to be a few noticeable peaks in the retained player count (e.g. day 55 & 277), but it quickly adjusted back to the average line. The result may be a signal to remind us, perhaps, it's time to reshift our foucs back on Long-term retention and YOY growth strategies.

# Q2: Do players with rolling 30-day retention come from specific regions?
![Regions with Retention](https://user-images.githubusercontent.com/94856154/155785811-1577e20a-7def-4b74-a941-93ac43973bd3.png)

As indicated by the visualizations provided, we have a total of 28859 people with rolling 30-day retention. The highest retention player number is located in South America(4863), followed by North America(4855) and Oceania(4832).

It's fairly to say that the players are equally distributed in all regions since the percentage of the differences between the highest player region amount and the lowest is less than 3%. 

To sum up the indication, the region doesn't matter. Although there are some days we could see total retained players' differences in the year, the result would always regress to the reference value if we look at the pictures from a quarter to quarter basis.






