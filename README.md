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
       END AS SecondGroup   FROM Incidents



Dates:

#how to return the difference in date 
#Use datediff
-- Return the difference in OrderDate and ShipDate
SELECT OrderDate, ShipDate, 
       Datediff(DD, OrderDate, ShipDate) AS Duration
FROM Shipments

#how to use dateadd
-- Return the DeliveryDate as 5 days after the ShipDate
SELECT OrderDate, 
       Dateadd(dd,5, shipdate) AS DeliveryDate
FROM Shipments

-- how to use Truncate 
SELECT Cost, 
       round(cost,0,1) AS TruncateCost
FROM Shipments




#How to use while loops?

DECLARE @counter INT 
SET @counter = 20

-- Create a loop
While @counter <30

-- Loop code starting point
Begin
	SELECT @counter = @counter + 1
-- Loop finish
End

-- Check the value of the variable
SELECT @counter

#How to use Common table expression(CTE)? 
--Used to use derived table as normal tables.

with BloodGlucoseRandom (MaxGlucose) 
AS (SELECT MAX(BloodGlucoseRandom) AS MaxGlucose FROM Kidney)

SELECT a.Age, b.MaxGlucose
FROM Kidney a
-- Join the CTE on blood glucose equal to max blood glucose
JOIN BloodGlucoseRandom b
on a.BloodGlucoseRandom= b.MaxGlucose;

-- Create the CTE
WITH BloodPressure(MaxBloodPressure) 
AS (select max(BloodPressure) as MaxBloodPressure from Kidney)
 
-- 2nd query
 
SELECT *
FROM Kidney a
-- Join the CTE  
join BloodPressure b
on a.BloodPressure= b.MaxBloodPressure;

#how to use window function
--with lag and lead:

SELECT TerritoryName, OrderDate, 
       -- Specify the previous OrderDate in the window
       lag(OrderDate) 
       -- Over the window, partition by territory & order by order date
       over(partition BY TerritoryName order BY OrderDate) AS PreviousOrder,
       -- Specify the next OrderDate in the window
       lead(OrderDate) 
       -- Create the partitions and arrange the rows
       over(partition by TerritoryName order by OrderDate) AS NextOrder
FROM Orders

--with first_value and last_value

SELECT TerritoryName, OrderDate, 
       -- Select the first value in each partition
       first_value(OrderDate) 
       -- Create the partitions and arrange the rows
       OVER(PARTITION BY TerritoryName order by OrderDate) AS FirstOrder
FROM Orders

#Creating running totals
SELECT TerritoryName, OrderDate, 
       -- Create a running total
       sum(orderprice) 
       -- Create the partitions and arrange the rows
       over(partition by TerritoryName order by OrderDate) AS TerritoryTotal	  
FROM Orders

#Assigning row numbers

SELECT TerritoryName, OrderDate, 
       -- Assign a row number
       Row_Number() 
       -- Create the partitions and arrange the rows
       OVER(PARTITION BY TerritoryName ORDER BY OrderDate) AS OrderCount
FROM Orders


#Calculating standard deviation
SELECT OrderDate, TerritoryName, 
       -- Calculate the standard deviation
	   STDEV(ORDERPRICE) 
       OVER(PARTITION BY TerritoryName ORDER BY OrderDate) AS StdDevPrice	  
FROM Orders

#Calculating Mode

-- Create a CTE Called ModePrice which contains two columns
WITH ModePrice (OrderPrice, UnitPriceFrequency)
AS
(
	SELECT OrderPrice, 
	ROW_NUMBER() 
	OVER(PARTITION BY OrderPrice ORDER BY OrderPrice) AS UnitPriceFrequency
	FROM Orders 
)

-- Select everything from the CTE
Select * from ModePrice

-- how to find max mode?


-- CTE from the previous exercise
WITH ModePrice (OrderPrice, UnitPriceFrequency)
AS
(
	SELECT OrderPrice,
	ROW_NUMBER() 
    OVER (PARTITION BY OrderPrice ORDER BY OrderPrice) AS UnitPriceFrequency
	FROM Orders
)

-- Select the order price from the CTE
SELECT OrderPrice AS ModeOrderPrice
FROM ModePrice
-- Select the maximum UnitPriceFrequency from the CTE
WHERE UnitPriceFrequency IN ( SELECT max(UnitPriceFrequency) FROM ModePrice)
