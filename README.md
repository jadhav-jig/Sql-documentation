# Sql-documentation
A documentation of Sql functions and methods for reference in future.

#How to deal with missing values?

-- Replace missing values 

SELECT Country, coalesce(country, IncidentState, city) AS Location
FROM Incidents
WHERE Country IS NULL

--or use isnull

SELECT IncidentState, isnull(IncidentState, city) AS Location
FROM Incidents
-- Filter to only return missing values from IncidentState
WHERE IncidentState is null 

#How to use Case?

SELECT DurationSeconds, 

      case when (DurationSeconds <= 120) then 1
         
       when (DurationSeconds > 120 AND DurationSeconds <= 600) then 2
           
       when (DurationSeconds > 601 AND DurationSeconds <= 1200) then 3
             
       when (DurationSeconds > 1201 AND DurationSeconds <= 5000) then 4
   
       ELSE 5 
       END AS SecondGroup   
FROM Incidents

