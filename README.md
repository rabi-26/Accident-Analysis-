# Accident-Analysis-
-- First I create schema (database). Create a database and I call it "VhclAcc".
-- Next, I import the CSV files to this database as tables.
-- Now, I am going to answer some rephrased questions with SQL to conduct exploratory data analysis.
------------------------------------------------------------------------------------

-- Question 1: What is the distribution of accidents across different area types (urban vs rural)?
SELECT
	[Area],
	COUNT([AccidentIndex]) AS 'Total Accident'
FROM 
	[dbo].[accident]
GROUP BY 
	[Area];

-- Question 2: On which day of the week do accidents occur most frequently?
SELECT 
	[Day],
	COUNT([AccidentIndex]) 'Total Accident'
FROM 
	[dbo].[accident]
GROUP BY 
	[Day]
ORDER BY 
	'Total Accident' DESC;

-- Question 3: What is the average vehicle age for different types of vehicles involved in accidents?
SELECT 
	[VehicleType], 
	COUNT([AccidentIndex]) AS 'Total Accident', 
	AVG([AgeVehicle]) AS 'Average Age'
FROM 
	[dbo].[vehicle]
WHERE 
	[AgeVehicle] IS NOT NULL
GROUP BY 
	[VehicleType]
ORDER BY 
	'Total Accident' DESC;

-- Question 4: How do vehicle age groups correlate with the number of accidents?
SELECT 
	AgeGroup,
	COUNT([AccidentIndex]) AS 'Total Accident',
	AVG([AgeVehicle]) AS 'Average Year'
FROM (
	SELECT
		[AccidentIndex],
		[AgeVehicle],
		CASE
			WHEN [AgeVehicle] BETWEEN 0 AND 5 THEN 'New'
			WHEN [AgeVehicle] BETWEEN 6 AND 10 THEN 'Regular'
			ELSE 'Old'
		END AS AgeGroup
	FROM [dbo].[vehicle]
) AS SubQuery
GROUP BY 
	AgeGroup;

-- Question 5: What weather conditions are associated with the highest severity of accidents?
DECLARE @Sevierity varchar(100)
SET @Sevierity = 'Fatal' --Serious, Fatal, Slight

SELECT 
	[WeatherConditions],
	COUNT([Severity]) AS 'Total Accident'
FROM 
	[dbo].[accident]
WHERE 
	[Severity] = @Sevierity
GROUP BY 
	[WeatherConditions]
ORDER BY 
	'Total Accident' DESC;

-- Question 6: How frequently do accidents involve left-hand side impacts on vehicles?
SELECT 
	[LeftHand], 
	COUNT([AccidentIndex]) AS 'Total Accident'
FROM 
	[dbo].[vehicle]
GROUP BY 
	[LeftHand]
HAVING
	[LeftHand] IS NOT NULL

-- Question 7: How do different journey purposes relate to the severity of accidents?
SELECT 
	V.[JourneyPurpose], 
	COUNT(A.[Severity]) AS 'Total Accident',
	CASE 
		WHEN COUNT(A.[Severity]) BETWEEN 0 AND 1000 THEN 'Low'
		WHEN COUNT(A.[Severity]) BETWEEN 1001 AND 3000 THEN 'Moderate'
		ELSE 'High'
	END AS 'Level'
FROM 
	[dbo].[accident] A
JOIN 
	[dbo].[vehicle] V ON A.[AccidentIndex] = V.[AccidentIndex]
GROUP BY 
	V.[JourneyPurpose]
ORDER BY 
	'Total Accident' DESC;

-- Question 8: What is the average age of vehicles involved in accidents, considering lighting conditions and point of impact?
DECLARE @Impact varchar(100)
DECLARE @Light varchar(100)
SET @Impact = 'Offside' --Did not impact, Nearside, Front, Offside, Back
SET @Light = 'Darkness' --Daylight, Darkness

SELECT 
	A.[LightConditions], 
	V.[PointImpact], 
	AVG(V.[AgeVehicle]) AS 'Average Vehicle Year'
FROM 
	[dbo].[accident] A
JOIN 
	[dbo].[vehicle] V ON A.[AccidentIndex] = V.[AccidentIndex]
GROUP BY 
	V.[PointImpact], A.[LightConditions]
HAVING 
	V.[PointImpact] = @Impact AND A.[LightConditions] = @Light;
