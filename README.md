# Europe's Top 5 League Analysis 

## Table of Contents

- [Project Overview](#project-overview)
- [Datasets](#datasets)
- [Tools](#tools)
- [Skills Applied](#skills-applied)
- [Questions Explored](#questions-explored)
- [Interesting Queries](#interesting-queries)
- [Summary of Findings](#summary-of-findings)


## Project Overview


This project offers an in-depth analysis of the performance and profiles of 110 active soccer players from Europe's top five leagues: the Premier League, La Liga, Bundesliga, Serie A, and Ligue 1. It examines key metrics such as player distribution across leagues, countries, and continents, market values across age groups, and player rankings based on experience and career achievements. By analyzing these factors, the project aims to reveal insights into how players compare across different teams, leagues, and regions, highlighting patterns and trends that influence their performance and market value.



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

## Questions Explored 
1. What is the distribution of players across the 5 leagues?
2. How does player distribution differ by continent?
3. Which country has the highest representation of players in this dataset?
4. How does the market value of players vary among the leagues?
5. How are teams ranked based on their players' average and total market values?
6. How does the average market value of players differ across various age groups?
7. What are the player rankings based on the number of years they've played professionally?
8. What is the average length of a professional playing career by position?
9. Among forwards with over 200 career games, who has scored the most goals?
10. Which midfielder with over 200 career appearances has the highest number of assists?
11. Among defenders with more than 200 career games, who has accumulated the most disciplinary cards?
12. How many players with over 150 games have never received a red card in their careers?
13. Which players have been with their current club for at least 5 years?

### Interesting Queries
Q4- How does the market value of players vary among the leagues?

```` SQL
 	WITH Leage_Market_Value AS (
		SELECT full_name, League_Name, Market_Value_In_Millions AS Market_Value
		FROM league L
		INNER JOIN Team T ON T.League_ID = L.League_ID
		INNER JOIN Player P ON P.Team_ID = T.Team_ID
					)
		SELECT 
		League_Name,
		'€' || To_CHAR(MIN(Market_Value), 'FM99,999,999,990.00') AS "Lowest Market Value", 
		'€' || To_CHAR(Max(Market_Value), 'FM99,999,999,990.00') AS "Highest Market Value", 	
		'€' || To_CHAR(SUM(Market_Value), 'FM99,999,999,990.00') AS "Sum Market Value", 	
		'€' || To_CHAR(ROUND(AVG(Market_Value),2),'FM999,999,999,990.00')  AS "Average Market Value" 
		FROM Leage_Market_Value 
		GROUP BY League_Name;
````
Q7- What are the player rankings based on the number of years they've played professionally?
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
Q13- Which players have been with their current club for at least 5 years?
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
- Most players in the dataset play in the Premier League, followed by Ligue 1 and Bundesliga, with Seria A and La Liga having the fewest players.
- Over 70% of the players are from Europe, with France and England leading in representation, while North America has the lowest number of representation.
- The Premier League has the highest total and average market value of players, with Ligue 1 ranked lowest in both. Four of the highest-valued individual players are from either the Premier League or La Liga.
- Real Madrid has the highest average and total market value, followed by Manchester City; Sevilla ranks lowest in both
- Younger players (17-24) have the highest average market value, followed by mid-age players (25-29), with seniors (30+) at the bottom.
- Defenders have the longest average career length (8.74 years), followed by forwards (7.94 years) and midfielders (7.86 years).
- Jesús Navas of Sevilla has the longest playing career at 21 years, while Lamine Yamal and Pau Cabarsi of Barcelona have the shortest at 1 year respectively.
- With players over 200 career games, Kevin De Bruyne of Manchester City has the most assists among midfielders, Robert Lewandowski of Barcelona has the most goals among forwards,  and Dani Carvajal of Real Madrid has the most disciplinary cautions among defenders.
- 25 players have played over 150 games without receiving a red card.
- 21 Players have been with their current club for at least 5 years, with Dani Carvajal having the longest tenure at 11 years with Real Madrid.

