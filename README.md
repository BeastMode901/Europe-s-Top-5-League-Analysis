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
  1. How are players distributed across the 5 leagues?
  2. How does the distribution of players vary by continent?
  3. Which country has the most players in this analysis?
  4. How does the market value of players compare across the leagues?
  5. How are teams ranked by their players' average and total market values?
  6. How does the average market value of players vary across different age groups?
  7. What are the rankings of players based on their years of playing professionally?
  8. What is the average length of a playing career based on positions?
  9. Which forward with over 200 career games has scored the most goals?
  10. Which midfielder with over 200 career appearances has the most assists?
  11. Which defender with over 200 career games has received the most disciplinary cards?
  12. How many players who have played over 150 games have never received a red card in their playing career?
  13. Which players have played for their current club for at least 5 years?

### Interesting Queries
Q4- How does the market value of players compare across the leagues?

```` SQL
 	WITH Leage_Market_Value AS (
		SELECT full_name, League_Name, Market_Value_In_Millions AS Market_Value
		FROM league L
		INNER JOIN Team T ON T.League_ID = L.League_ID
		INNER JOIN Player P ON P.Team_ID = T.Team_ID
			)
		SELECT 
		League_Name,
		'€' || To_CHAR(MIN(Market_Value), 'FM99,999,999,990.00') AS "Lowest Market Value", -- 'E' || To_Char, 'FM99,999,999,990.00': This expression formats the market value as a Euro Currency string with 2 commas and 2 decimal places
		'€' || To_CHAR(Max(Market_Value), 'FM99,999,999,990.00') AS "Highest Market Value", 	
		'€' || To_CHAR(SUM(Market_Value), 'FM99,999,999,990.00') AS "Sum Market Value", 	
		'€' || To_CHAR(ROUND(AVG(Market_Value),2),'FM999,999,999,990.00')  AS "Average Market Value" FROM Leage_Market_Value 
````
Q7- What are the rankings of players based on their years of playing professionally?
```` SQL
Select full_name, 
Team_Name, 
Extract(YEAR FROM CURRENT_DATE) - Debut_career AS Years_Playing_Professional,
RANK () OVER (PARTITION BY t.Team_ID ORDER BY Extract(YEAR FROM CURRENT_DATE) - Debut_career DESC) AS Career_Length_Rank_Within_Team,
RANK () OVER (ORDER BY Extract(YEAR FROM CURRENT_DATE) - Debut_career DESC) AS Overal_Ranking
FROM player P
INNER JOIN Team T ON T.Team_ID = P.Team_ID
ORDER BY Team_Name, Career_Length_Rank_Within_Team;
````
Q13- Which players have played for their current club for at least 5 years?
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

