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
This project provides a comprehensive analysis of 110 active player profiles across Europe’s top five leagues: Premier League, La Liga, Bundesliga, Serie A, and Ligue 1. The project examines players' age, market value, and career statistics to compare how these metrics vary across continents, teams, leagues, and other players.

### Datasets
- Player Profile: Details such as name, birthdate, jersey number, and market value.
- Player Statistics: Metrics including games, goals, and assists.
- Team Info: Information about the team and their league affiliation.
- League Info: Names of the leagues.
- Geographical Data: Information on the players' countries.

  
### Tools
- Excel - Used for data creation and initial data management.
  - [Download Data Here](https://www.kaggle.com/datasets/collinsemensah/europes-top-5-league-player-analysis)
- PostgreSQL - Imported the Excel Data into Postgres SQL for data cleaning and analysis.
    - [Download PostgreSQL Here](https://www.postgresql.org/download/windows/)
- Power BI - Created visuals for my data.
  	- [Download Power BI Here](https://www.microsoft.com/en-us/power-platform/products/power-bi/downloads)
 
### Skills Applied
- JOINs
- Aggregations
- CTEs
- Window Functions
- Case Statements
- Date Functions
- Grouping and Sorting
  



### Questions Explored 
1. What is the distribution of players across the 5 leagues?
2. How does player distribution differ by continent?
3. Which country has the highest representation of players in this dataset?
4. How does the market value of players vary among the leagues?
5. How are teams ranked based on their players' average and total market values?
6. How does the average market value of players differ across various age groups?
7. What are the player rankings based on the number of years they've played professionally?
8. What is the average length of a professional playing career by position?
9. Who are the top 10 players with the most career goals?
10. Who are the top 20 players with the most career assists?
11. Who are the top 10 players with the most cards accumulated?
12. How many players with over 150 games have never received a red card in their career?
13. Which players have been with their club for at least 5 years?
14. Who are the top 5 most expensive players?

### Interesting Queries
Q4- How does the market value of players vary among the leagues?

```` SQL
 	WITH Leage_Market_Value AS (
		SELECT Name, League_Name, Market_Value
		FROM league L
		INNER JOIN Team T ON T.League_ID = L.League_ID
		INNER JOIN Player P ON P.Team_ID = T.Team_ID
					)
			 ---Side Note: '€' || To_CHAR('FM99,999,999,990.00'):  Formats the value with a Euro symbol and two decimal places
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
Select
	Name,
	Team_Name, 
	Extract(YEAR FROM CURRENT_DATE) - Career_Debut AS Years_Playing_Professional,
	RANK () OVER (PARTITION BY t.Team_ID ORDER BY Extract(YEAR FROM CURRENT_DATE) - 		Career_Debut DESC) AS Career_Length_Rank_Within_Team,
	RANK () OVER (ORDER BY Extract(YEAR FROM CURRENT_DATE) - Career_Debut DESC) AS Overall_Ranking
FROM
	player P
INNER JOIN Team T ON T.Team_ID = P.Team_ID
ORDER BY Team_Name, Career_Length_Rank_Within_Team;
````
Q13- Which players have been with their club for at least 5 years?
```` SQL
SELECT
    Name,
   EXTRACT(YEAR FROM CURRENT_DATE) - Year_Joined_Club AS Current_Club_Duration
FROM
  Player 
where
  EXTRACT (YEAR FROM CURRENT_DATE) - Year_Joined_Club >=5
ORDER BY Current_Club_Duration DESC;
````

### Summary of Findings
- The dataset shows that the majority of players play in the Premier League, followed by Ligue 1 and Bundesliga. Serie A and La Liga have the fewest players represented.
- Over 70% of the players are from Europe, with France leading in representation. North America has the fewest players featured in the dataset.
- The Premier League leads in both total and average market value of players, while Ligue 1 ranks the lowest. Four of the highest-valued players are from the Premier League or La Liga.
- Real Madrid has the highest average and total market value among clubs, followed by Manchester City; Sevilla ranks lowest in both metrics.
- Younger players (17-24) have the highest average market value, followed by mid-age players (25-29), with seniors (30+) at the bottom.
- Defenders have the longest average career duration.
- Jesús Navas of Sevilla holds the longest playing career at 21 years, while Lamine Yamal and Pau Cubarsi of Barcelona have the shortest careers, thus far, at just 1 year each.
- Robert Lewandowski of Barcelona leads all players for most career goals; Kevin De Bruyne of Manchester City for the most career assists; and Granit Xhaka of Bayer Leverkusen for the most disciplinary cautions.
- 25 players have played over 150 games without receiving a red card.
- 21 Players have been with their current club for at least 5 years, with Dani Carvajal having the longest tenure at 11 years with Real Madrid.

