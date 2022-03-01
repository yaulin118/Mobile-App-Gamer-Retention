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
![image](https://user-images.githubusercontent.com/94856154/156240714-4ebf333f-0500-485f-b3a5-ddfa3846df42.png)

We excluded the last 30 days of the year from our analysis because those who joined in the previous 30 days would have automatically been categorized as Not-Retained. As a result, notably, we saw a dramatic decline at the end of the year. Therefore, we only choose the data from Day 1 to Day 335 for this project.

Given the data above, we observed that from the trendline, overall, the retention rate was relatively consistent throughout the year(Average 65.46%). From a growth perspective, while the retention rate is stable in the trendline, the growth hasn't significantly increased since the Q1. There appear to be a few noticeable peaks in the retained player count (e.g. day 55 & 277), but it quickly adjusted back to the average line. The result may be a signal to remind us, perhaps, it's time to reshift our foucs back on Long-term retention and YOY growth strategies.

```
SELECT
  joined AS Day_joined,
  COUNT(player_id) Players_joined,
  SUM(Retention_Status) Players_retained,
  ROUND((SUM(Retention_Status)/COUNT(player_id)),2)*100 as fractional_retention,  
FROM (
  SELECT
    p.player_id,
    p.joined,
    CASE
      WHEN (MAX(M.day) - MIN(p.joined)) >= 30 THEN 1
    ELSE 0
  END AS Retention_Status
  FROM
    `howard-projects.SQL_Project_Cohort2.matches_info` m
  JOIN
    `howard-projects.SQL_Project_Cohort2.player_info` p
  ON
    p.player_id = m.player_id
  GROUP BY
    p.joined,
    p.player_id) AS retention_status_players
GROUP BY
  joined
  ```

# Q2: Do players with rolling 30-day retention come from specific regions?
![image](https://user-images.githubusercontent.com/94856154/156240987-d22115bf-c781-4953-9165-bbe2d61e4c29.png)

As indicated by the visualizations provided, we have a total of 28859 people with rolling 30-day retention. The highest retention player number is located in South America(4863), followed by North America(4855) and Oceania(4832).

It's fairly to say that the players are equally distributed in all regions since the percentage of the differences between the highest player region amount and the lowest is less than 3%. 

To sum up the indication, the region doesn't matter. Although there are some days we could see total retained players' differences in the year, the result would always regress to the reference value if we look at the pictures from a quarter to quarter basis.

```
SELECT 
    location,
    SUM(Retention_Status) as People_retained
        FROM( 
         SELECT
            p.player_id,
            p.joined,
            p.location,
            p.age,
            CASE WHEN (MAX(M.day) - MIN(p.joined)) >= 30 THEN 1 END AS Retention_Status
    
            FROM
            `howard-projects.SQL_Project_Cohort2.matches_info` m
            JOIN
            `howard-projects.SQL_Project_Cohort2.player_info` p
            ON
            p.player_id = m.player_id
            GROUP BY
            p.joined,
            p.player_id,
            p.location,
            p.age)
GROUP BY location
Order by location
LIMIT 1000
```

# Q3: Does people retained or not retained has more winning games?
![image](https://user-images.githubusercontent.com/94856154/156240909-aa21da32-2caf-41ed-97a6-956997412b4c.png)

For our deeper investigation, we wanted to see if players retained after 30 days had more winning rounds than those not retained but winning the games. We presumably think that retained people would be most likely to win more games since they are more experienced in playing it. 

To verify whether the statement is true or false, we first start a query to extract the winning games, then select people who are retained or not retained to make the comparison. The result solidates our presumption. We noticed that 43847 winning games include 26538 games(60%) that people are retained compared to 17309 games(40%) with people not retained. In our findings, people with more practice are more likely to have a higher winning rate, and people with a higher winning rate are more willing to be retained in this game, vice versa.


```
SELECT 
COUNT(CASE WHEN WINS = 1 AND Retention_Status = 1 THEN 0 END) AS Win_people_retained,
COUNT(CASE WHEN WINS = 1 AND Retention_Status = 0 THEN 0 END) AS Win_people_NOT_retained

FROM
(SELECT
    DISTINCT player_id AS Winner_ID,    
    SUM(wins) as wins,  
    Retention_Status,
 
FROM(
    SELECT
    Distinct m.player_id,
    outcome,
    CASE WHEN outcome = "win" Then 1
    else 0
    END AS wins,
    CASE WHEN outcome = "loss" Then 2
    else 0
    END AS loss,
    CASE WHEN (MAX(M.day) - MIN(p.joined)) >= 30 THEN 1 -- Retained or not?
    ELSE 0
    END AS Retention_Status
    FROM  `howard-projects.SQL_Project_Cohort2.matches_info` m
    JOIN  `howard-projects.SQL_Project_Cohort2.player_info` p
    ON  p.player_id = m.player_id
    GROUP BY outcome, player_id)

GROUP BY player_id, Retention_Status) 

WHERE WINS = 1 
```


# Conclusion

![image](https://user-images.githubusercontent.com/94856154/156036765-f3ee23b5-f418-4425-9a08-4609860a39bc.png)

In short sentences, the datasets provided us with some threads that was able to help us find the trend. While there is much more we could further investigate, we were able to tell the retention rate is quite stable throughout the year. However, it isn't necessarily a negative indication, but it's worth focusing on long-term strategies. Also, we saw players are distributed to all regions, which indicates that our marketing strategies is equally launched by geography. Last but not least, in our findings, people with a higher winning rate are more willing to be retained in this game. If we want to increase retention, we could perhaps redesign the winning criteria to increase the retention rate. 

 Also we can see that game is popular among all the locations and people who play it more are in their 20's. We had a limited data so our analysis are limited. Retention status is consistent but at the end it has a big drop. Which could be caused by different elements either game is too easy for the players or too too tough or their is not enough interesting elements in the game for certain age groups. So people join and get bored of it.

