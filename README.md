# Sales Analysis Dashboard | SQL Server & Power BI

![github](https://github.com/user-attachments/assets/1619042d-3f6f-4869-b83f-e8d76d5904e6)

## Project Overview
This project presents a comprehensive analysis of sales performance using an interactive Power BI dashboard. The goal was to
transform raw sales data into actionable insights that support strategic business decisions.

The dashboard covers three key areas:
* Sales Overview
* Customer Insights
* Product Performance

## Business Request 
The business request for this SQL & Power BI project was an excetutive sales report for sales manager. The sales manager wants
an interactive Power BI dashboard to track sales and budget over time.

## User Stories
Based on the business request, the following user stories were defined to fulfil delivery and ensure that acceptance criteria
mantained throughout the project
|Role|	Request/Demand |	User Value |	Acceptance Criteria |
| --------|--------|--------|--------|
|Sales Manager|	A dashboard overview of internet sales|	Follow which customers and products sell the most|	Power BI dashboard which update once a day|
|Sales Representative| A detailed overview of internet sales per customers|	Follow up customers that buy the most| A Power BI dashboard which allow filtering for each customers|
|Sales Representative| A detailed overview of internet sales per product|	Follow up products that sell the most|	 A Power BI dashboard which allow filtering for each products|
|Sales Manager|	Dashboard overview of internet sales| Follow sales over time against budget| A Power BI	dashboard with kpi, bar chart, pie chart, map and sales graph compairing against budget|

## Data Cleaning & Transformation
To create the necessary data models for analysis and fulfiling the business needs defined in the users stories, the following 
tables were extracted using SQL.

One data source(sales budget) were provided in Excel Format and were connected in the data model

Below are the SQL statement for cleaning and transforming neccesary data 

``` SQL
-- Cleansed Dim_DateTable --
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  --,[DayNumberOfWeek], 
  [EnglishDayNameOfWeek] AS Day, 
  --,[SpanishDayNameOfWeek]
  --,[FrenchDayNameOfWeek]
  --,[DayNumberOfMonth]
  --,[DayNumberOfYear], 
  [WeekNumberOfYear] AS WeekNr, 
  [EnglishMonthName] AS Month, 
  LEFT([EnglishMonthName], 3) AS MonthShort, 
  --,[SpanishMonthName]
  --,[FrenchMonthName], 
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year --,[CalendarSemester]
  --,[FiscalQuarter]
  --,[FiscalYear]
  --,[FiscalSemester]
FROM 
  [AdventureWorksDW2019].[dbo].[DimDate] 
WHERE 
  CalendarYear >= 2019;
```

``` SQL
-- Cleaned DIM_Customers Table 
SELECT 
  c.customerKey AS CustomerKey, 
  --,[GeographyKey]
  --,[CustomerAlternateKey]
  --,[Title]
  c.firstname AS [First Name], 
  --,[MiddleName]
  c.lastname AS [Last Name], 
  c.firstname + ' ' + c.lastname AS [Full Name], 
  --,[NameStyle]
  --,[BirthDate]
  --,[MaritalStatus]
  --,[Suffix]
  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender, 
  --,[EmailAddress]
  --,[YearlyIncome]
  --,[TotalChildren]
  --,[NumberChildrenAtHome]
  --,[EnglishEducation]
  --,[SpanishEducation]
  --,[FrenchEducation]
  --,[EnglishOccupation]
  --,[SpanishOccupation]
  --,[FrenchOccupation]
  --,[HouseOwnerFlag]
  --,[NumberCarsOwned]
  --,[AddressLine1]
  --,[AddressLine2]
  --,[Phone]
  c.datefirstpurchase AS DateFirstPurchase, 
  --,[CommuteDistance]
  g.city AS [Customer City] -- Joined in customer city from geography table
FROM 
  dbo.DimCustomer AS c 
  LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey 
ORDER BY 
  CustomerKey ASC;
```

``` SQL
-- Cleaned DIM_Product Table
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS ProductItemCode, 
  --,[ProductSubcategoryKey]
  --,[WeightUnitMeasureCode]
  --,[SizeUnitMeasureCode]
  p.[EnglishProductName] AS [Product Name], 
  ps.EnglishProductSubcategoryName AS [Sub Category], 
  -- Joined in from Sub Category Table
  pc.EnglishProductCategoryName AS [Product Category], 
  -- Joined in from Category Table
  -- [SpanishProductName]
  --,[FrenchProductName]
  --,[StandardCost]
  --,[FinishedGoodsFlag]
  p.[Color] AS [Product Color], 
  --[SafetyStockLevel]
  --,[ReorderPoint]
  --,[ListPrice]
  p.[Size] AS [Product Size], 
  --[SizeRange]
  --,[Weight]
  --,[DaysToManufacture]
  p.[ProductLine] AS [Product Line], 
  --[DealerPrice]
  --,[Class]
  --,[Style]
  p.[ModelName] AS [Product Model Name], 
  --[LargePhoto]
  p.[EnglishDescription] AS [Product Description], 
  --[FrenchDescription]
  --,[ChineseDescription]
  --,[ArabicDescription]
  --,[HebrewDescription]
  --,[ThaiDescription]
  --,[GermanDescription]
  --,[JapaneseDescription]
  --,[TurkishDescription]
  --,[StartDate]
  --,[EndDate]
  ISNULL (p.Status, 'Outdated') AS [Product Status] 
FROM 
  [dbo].[DimProduct] AS p 
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey 
ORDER BY 
  p.ProductKey ASC
```

``` SQL
-- Created Cleaned FACTInternetSales Table
SELECT 
  [ProductKey], 
  [OrderDateKey], 
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey] --,[PromotionKey]
  --,[CurrencyKey]
  --,[SalesTerritoryKey]
  , 
  [SalesOrderNumber] --,[SalesOrderLineNumber]
  --,[RevisionNumber]
  --,[OrderQuantity]
  --,[UnitPrice]
  --,[ExtendedAmount]
  --,[UnitPriceDiscountPct]
  --,[DiscountAmount]
  --,[ProductStandardCost]
  --,[TotalProductCost]
  , 
  [SalesAmount] --,[TaxAmt]
  --,[Freight]
  --,[CarrierTrackingNumber]
  --,[CustomerPONumber]
  --,[OrderDate]
  --,[DueDate]
  --,[ShipDate]
FROM 
  [dbo].[FactInternetSales] 
WHERE 
  LEFT (OrderDateKey, 4) >= YEAR(
    GETDATE ()
  ) -2 ---Ensures we always only bring two yers of data from extraction
ORDER BY 
  OrderDateKey ASC;
```

## Data Modelling
<img width="451" height="331" alt="guideddatamodel" src="https://github.com/user-attachments/assets/492153d8-7c1f-4ece-b081-c9aa97291538" />

## Tools & Skills Used
* SQL
* Power BI
* Data Cleaning & Transformation
* Data Modeling
* DAX (Data Analysis Expressions)
* Data Visualization & Storytelling

## Key Insights
Revenue Performance
* Sales exceeded budget by 5.2%
* Strong upward trend toward year-end
* Revenue Concentration Risk
* Heavy reliance on top customers
* Heavy reliance on bike category (~95% contribution)

Geographic Imbalance
* Sales concentrated in specific cities
* Opportunity for expansion in underperforming regions

Product Dependency
* Limited diversification across categories
* Strong SKU concentration

## Strategic Recommendations
Customer Strategy
* Implement loyalty programs for top customers
* Develop mid-tier customer segments
Reduce churn risk with predictive analysis

Product Strategy
* Expand high-performing bike variants
* Bundle accessories to increase cross-sell revenue
* Phase out low-performing product variants

Sales & Marketing Strategy
* Replicate Q4 campaigns earlier in the year
* Introduce mid-year promotional pushes
* Target high-performing cities for expansion

Operational Strategy
* Improve demand forecasting for top SKUs
* Optimize inventory management
* Reduce supply chain risk for best-selling products

## Key Risks
Overdependence on a single product category
Geographic concentration
Customer concentration
Seasonal imbalance

## Conclusion

The business is performing above expectations, but growth is concentrated and exposes several risks. By diversifying products
, expanding geographically, and strengthening customer acquisition strategies, the company can achieve more sustainable
growth.

Author
Ajinifesin Afusat
Microsoft Certified Power BI Data Analyst | Power BI | Data Storytelling Enthusiast

You can interact with the dashboard [here](https://app.powerbi.com/view?r=eyJrIjoiMzg3MTRkNjMtZTMzNS00N2M3LThhODEtYTkwODFkMDc3NDIwIiwidCI6IjE5NDlmNzRjLTQzY2UtNDRhZi1iYTdhLWJhYjRhYjYyNzljNiJ9)
