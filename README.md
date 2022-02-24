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

Query:

SELECT
  joined AS Day_joined,
  COUNT(player_id) Players_joined,
  SUM(Retention_Status) Players_retained,
  ROUND((SUM(Retention_Status)/COUNT(player_id)),2) as fraction_retention,

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

# Q1: Is 30-day rolling retention increasing or decreasing over the lifecycle of the game?
