# Europe's Top 5 League Analysis 

## Table of Contents

- [Project Overview](#project-overview)
- [Datasets](#datasets)
- [Tools](#tools)
- [Skills Applied](#skills-applied)
- [Questions Explored](#questions-explored)
- [Interesting Queries](#interesting-queries)
- [Summary of Findings](#summary-of-findings)


### Project Overview


This project analyzes the performance and profiles of 110 active soccer players from Europe’s top five leagues: Premier League, La Liga, Bundesliga, Serie A, and Ligue 1. By analazying these metrics, we seek to identify how they compare across teams, league, and continents. 



### Datasets
- Player Overview  - Contains player information such as name, birthdate, jersey number, etc.
- Player Statistics  -  In-depth career statistics for players, including appearances, goals, and assists.
- Team Info -  Teams and their affiliations with specific leagues.
- League Info - Overview of the 5 leagues.
- Geographical Data - Provides geographical information on the countries of origin for the players.

### Tools
- Excel - Used for data creation and initial data management.
  - [Download Data Here](https://www.kaggle.com/datasets/collinsemensah/europes-top-5-league-player-analysis)
- Postgres SQL - Imported the Excel Data into Postgres SQL for Data Analysis and quering.
    - [Download Postgres SQL Here](https://www.postgresql.org/download/windows/)
 
### Skills Applied
- JOINs
- Aggregations
- CTEs
- Windows Functions

### Questions Explored 
  1. What is the Distribution of Players among the  Leagues?
  2. How does the distribution of players vary by continent?
  3. How are players from different continents distributed across the leagues?
  4. How does the market value of players compare across the 5 leagues?
  5. How does the market value of players compare across continents?
  6. How does the average market value of players vary across different age groups?
  7.  How do teams within the 5 leagues rank based on their lowest, highest, average, and total market values?
  8.  What is the ranking of forwards based on their goal-to-game ratio?
  9.  What is the ranking of midfielders based on their assists-to-game ratio?
  10.  What is the ranking of defenders based on their cards-to-game- ratio?
  11.  Which players have achieved over 100 games, 100 goals, and 100 assists?
  12.  How many players have never received a red card in their playing career?
  13.  How are teams ranked based on the total number of career goals scored, assists made, and disciplinary cards received by their players?
  14.  How are the leagues ranked according to the total number of career goals scored, assists made, and disciplinary cards received by their players?
  15.  How do the average age, youngest, and oldest players vary across the leagues?
  16.  What are the rankings of players within their teams and all players based on their years of professional experience?
  17.  Which players have been playing for their current club for at least 5 years?

### Interesting Queries
Q4- How does the market value of players compare across the 5 leagues?

```` SQL
 	WITH Leage_Market_Value AS (
		SELECT full_name, League_Name, Market_Value_In_Millions AS Market_Value
		FROM league L
		INNER JOIN Team T ON T.League_ID = L.League_ID
		INNER JOIN Player P ON P.Team_ID = T.Team_ID
			)
		SELECT 
		League_Name,
		'€' || To_CHAR(MIN(Market_Value), 'FM99,999,999,990.00') AS "Lowest Market Value", -- 'E' || To_Char, 'FM99,999,999,990.00' : This expression formats the market value as a Euro Currency string with 2 commas and 2 decimal places
		'€' || To_CHAR(Max(Market_Value), 'FM99,999,999,990.00') AS "Highest Market Value", 	
		'€' || To_CHAR(SUM(Market_Value), 'FM99,999,999,990.00') AS "Sum Market Value", 	
		'€' || To_CHAR(ROUND(AVG(Market_Value),2),'FM999,999,999,990.00')  AS "Average Market Value" FROM Leage_Market_Value 
````
Q11- Which players have achieved over 100 games, 100 goals, and 100 assists?
```` SQL
Select Full_Name
FROM Player P
Inner Join Career_Stats CS ON CS.Player_ID = P.Player_ID
WHERE Games >= 100 AND Goals >= 100 AND Assists >= 100;
````
Q17- Which players have been playing for their current club for at least 5 years?
```` SQL
SELECT
    full_name,
   EXTRACT(YEAR FROM CURRENT_DATE) - Year_Joined_Club Club_Duration
FROM
  Player p
where
  EXTRACT (YEAR FROM CURRENT_DATE) - Year_Joined_Club >=5
````

### Summary of Findings

