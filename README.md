# Sales Analysis

## Table of contents
- [Project Overview](#Project-Overview)
- [Business Demand Letter](#Business-Demand-Letter)
- [Business Request and user stories](#Business-Request-and-user-stories)
- [Tools used](#Tools-used)
- [Data Preperation](#Data-Preperation)
- [Data Cleansing & Transformation](#Data-Cleansing-&-Transformation)
- [Data Modeling](#Data-Modeling)
- [Data Visualisation](#Data-Visualisation)
- [Data Source](#Data-Source)
- [Results / Findings](#Results-Findings)
- [Conclusion](#Conclusion)

## Project Overview
This Data Analysis Project aims to deliver actionable insights into the sales performance of the company over the past several years. By rigorously analyzing key aspects of the sales data, we seek to uncover trends, identify growth opportunities, and facilitate data-driven decision-making. This comprehensive analysis will provide stakeholders with a deeper understanding of the company's performance dynamics, enabling strategic planning and enhancing competitive positioning in the market. Our findings will serve as a foundation for optimizing sales strategies and driving future business growth.





## Business Demand Letter [view ](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/2.%20Business%20Ticket/Business%20Demand%20-%20img.png)
Steven - Sales Manager:

Hi Manish!

I hope you are doing well. We need to improve our internet sales reports and want to move from static reports to visual dashboards.
Essentially, we want to focus it on how much we have sold of what products, to which clients and how it has been over time.

Seeing as each sales person works on different products and customers it would be beneficial to be able to filter them also.
We measure our numbers against budget so I added that in a spreadsheet so we can compare our values against performance. 

The budget is for 2021 and we usually look 2 years back in time when we do analysis of sales.
Let me know if you need anything else!

-Steven




## Business Request and user stories [view ](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/2.%20Business%20Ticket/Business%20Request%20-%20img.png)
|NO. | As a (role) | I want (request/demand) |
|----|-------------|-------------------------|
|1. |Sales Manager |          -   To get a dashboard overview of internet sales.|
|2. |Sales Representative |   -   A detailed overview of Internet Sales per Customers.|
|3. |Sales Representative |   -   A detailed overview of Internet Sales per Products.|
|4. | Sales Manager |          -   A dashboard overview of internet sales.|

## Tools used
- Excel
- SQL - Data Cleaning/Preperation
- POWER BI - Creating Reports

## Data Preperation
In the initial data preperation phase, we performed the following tasks:

1. Data loading and inspection.
2. Handling Missing Data.
3. Data cleaning and preperation.






## Data Cleansing & Transformation (SQL)
To create the necessary data model for doing data analysis and fulfilling the business needs defined in the user stories the following tables were extracted using SQL.

Below are some SQL statements for cleansing and transforming necessary data.


1. Cleansed FACT_InternetSales Table

``` SQL
SELECT 
  [ProductKey], 
  [OrderDateKey], 
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey], 
  --  ,[PromotionKey]
  --  ,[CurrencyKey]
  --  ,[SalesTerritoryKey]
  [SalesOrderNumber], 
  --  [SalesOrderLineNumber], 
  --  ,[RevisionNumber]
  --  ,[OrderQuantity], 
  --  ,[UnitPrice], 
  --  ,[ExtendedAmount]
  --  ,[UnitPriceDiscountPct]
  --  ,[DiscountAmount] 
  --  ,[ProductStandardCost]
  --  ,[TotalProductCost] 
  [SalesAmount] --  ,[TaxAmt]
  --  ,[Freight]
  --  ,[CarrierTrackingNumber] 
  --  ,[CustomerPONumber] 
  --  ,[OrderDate] 
  --  ,[DueDate] 
  --  ,[ShipDate] 
FROM 
  [AdventureWorksDW2019].[dbo].[FactInternetSales]
WHERE 
  LEFT (OrderDateKey, 4) >= YEAR(GETDATE()) -4 -- Ensures we always only bring four years of date from extraction.
ORDER BY
  OrderDateKey ASC
```

2. Cleansed DIM_Products Table

``` SQL
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS ProductItemCode, 
  --      ,[ProductSubcategoryKey], 
  --      ,[WeightUnitMeasureCode]
  --      ,[SizeUnitMeasureCode] 
  p.[EnglishProductName] AS [Product Name], 
  ps.EnglishProductSubcategoryName AS [Sub Category], -- Joined in from Sub Category Table
  pc.EnglishProductCategoryName AS [Product Category], -- Joined in from Category Table
  --      ,[SpanishProductName]
  --      ,[FrenchProductName]
  --      ,[StandardCost]
  --      ,[FinishedGoodsFlag] 
  p.[Color] AS [Product Color], 
  --      ,[SafetyStockLevel]
  --      ,[ReorderPoint]
  --      ,[ListPrice] 
  p.[Size] AS [Product Size], 
  --      ,[SizeRange]
  --      ,[Weight]
  --      ,[DaysToManufacture]
  p.[ProductLine] AS [Product Line], 
  --     ,[DealerPrice]
  --      ,[Class]
  --      ,[Style] 
  p.[ModelName] AS [Product Model Name], 
  --      ,[LargePhoto]
  p.[EnglishDescription] AS [Product Description], 
  --      ,[FrenchDescription]
  --      ,[ChineseDescription]
  --      ,[ArabicDescription]
  --      ,[HebrewDescription]
  --      ,[ThaiDescription]
  --      ,[GermanDescription]
  --      ,[JapaneseDescription]
  --      ,[TurkishDescription]
  --      ,[StartDate], 
  --      ,[EndDate], 
  ISNULL (p.Status, 'Outdated') AS [Product Status] 
FROM 
  [AdventureWorksDW2019].[dbo].[DimProduct] as p
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey 
order by 
  p.ProductKey asc
```

3. Cleansed DIM_Customers Table

``` SQL
SELECT 
  c.customerkey AS CustomerKey, 
  --      ,[GeographyKey]
  --      ,[CustomerAlternateKey]
  --      ,[Title]
  c.firstname AS [First Name], 
  --      ,[MiddleName]
  c.lastname AS [Last Name], 
  c.firstname + ' ' + lastname AS [Full Name], 
  -- Combined First and Last Name
  --      ,[NameStyle]
  --      ,[BirthDate]
  --      ,[MaritalStatus]
  --      ,[Suffix]
  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender,
  --      ,[EmailAddress]
  --      ,[YearlyIncome]
  --      ,[TotalChildren]
  --      ,[NumberChildrenAtHome]
  --      ,[EnglishEducation]
  --      ,[SpanishEducation]
  --      ,[FrenchEducation]
  --      ,[EnglishOccupation]
  --      ,[SpanishOccupation]
  --      ,[FrenchOccupation]
  --      ,[HouseOwnerFlag]
  --      ,[NumberCarsOwned]
  --      ,[AddressLine1]
  --      ,[AddressLine2]
  --      ,[Phone]
  c.datefirstpurchase AS DateFirstPurchase, 
  --      ,[CommuteDistance]
  g.city AS [Customer City] -- Joined in Customer City from Geography Table
FROM 
  [AdventureWorksDW2019].[dbo].[DimCustomer] as c
  LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey 
ORDER BY 
  CustomerKey ASC -- Ordered List by CustomerKey
```

4. Cleansed DIM_Date Table

``` SQL
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  --[DayNumberOfWeek], 
  [EnglishDayNameOfWeek] AS Day, 
  --[SpanishDayNameOfWeek], 
  --[FrenchDayNameOfWeek], 
  --[DayNumberOfMonth], 
  --[DayNumberOfYear], 
  --[WeekNumberOfYear],
  [EnglishMonthName] AS Month, 
  Left([EnglishMonthName], 3) AS MonthShort,   -- Useful for front end date navigation and front end graphs.
  --[SpanishMonthName], 
  --[FrenchMonthName], 
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year --[CalendarSemester], 
  --[FiscalQuarter], 
  --[FiscalYear], 
  --[FiscalSemester] 
FROM 
 [AdventureWorksDW2019].[dbo].[DimDate]
WHERE 
  CalendarYear >= 2021
```

# Data Modeling

This screenshot showcases the structured data model created in Power BI, illustrating the relationships between key datasets to enable efficient analysis and reporting for the project.


![Data Modelling](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/4.%20Power%20BI/Data%20Model.png)


# Data Visualisation

After loading the processed data and constructing a comprehensive data model, I developed an in-depth data analysis report. The following screenshot presents the visualized data within Power BI, highlighting key insights derived from dynamic and interactive dashboards.



![Data Visualisation](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/4.%20Power%20BI/Dashboard%20-%20img.jpg)

  [Download full Pdf](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/Sales%20Report%20-%20pdf.pdf)

# Data Source

### Sales Data
The primary dataset used for this analysis is the 'Adventure Works.bak' containing detailed information about the sales made by the company.[Adventure Works](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)

### Budget Data
This dataset is provided by the Sales Manager to predict the behavior of sales as per the budget over the past years. [Budget Data](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/1.%20Data/Sales_Budget.xlsx)


### Updating the data
Download and execute the script in SQL to update the database.[Update](https://github.com/itsmanishjoshi/Sales-Analysis/blob/main/1.%20Data/Update_AdventureWorksDW_Data.sql)

# Results / Findings

### Sales Overview
- The highest-selling regions in recent years have been North America, Europe, and Australia, with notable growth in these markets. 

- The top-selling product categories were dominated by Bikes, followed by Accessories and Clothing.

- 2023 stood out as the most profitable year for the business, marking a significant milestone in sales growth. 

- The most profitable months during this period were May, June, October, and November, while February, July, September, and December were identified as the least profitable months.

- A direct correlation between increased budget allocation and higher sales was observed, suggesting that investment in marketing and other areas contributed positively to overall revenue growth.



### Customer Overview
- Most buying customers: Jordan Turner followed by Willie XU and Nichole Nara.

- Most buying regions: North - America, Europe & Australia


### Product Overview

Bikes
- The top-selling bikes included:
- Mountain-200 Black-42
- Mountain-200 Silver-42
- Road-150 Red

Accessories
- In the accessories category, the best-selling products were:
- Sports-100 Helmet in both Red and Blue.
- HL Mountain Tyre.


Clothing
- Among clothing items, the top sellers were:
- Women's Mountain Shorts (L).
- Long Sleeve Logo Jersey (L).
- Short Sleeve Classic Jersey (M).


# Suggestions

### Sales Strategy
- Focus on High-Performing Regions: With North America, Europe, and Australia being the highest-selling regions, it would be beneficial to further invest in targeted marketing and promotional activities within these markets to capitalize on their growth potential.

- Increase Budget for Peak Sales Months: Given that May, June, October, and November were the most profitable months, consider allocating more budget during these periods to maximize sales during high-demand seasons. Additionally, marketing efforts during February, July, September, and December should be reviewed to understand and address the reasons behind lower profitability.

- Leverage Budget-Sales Correlation: The positive relationship between increased budget and sales performance suggests that further investment in marketing, customer outreach, and inventory management during peak months could drive greater returns. Continuous monitoring and optimizing the allocation of resources can ensure sustained sales growth.

### Customer Engagement
- Personalized Offers for Top Customers: With Jordan Turner, Willie Xu, and Nichole Nara being the top buyers, a strategy that rewards their loyalty—such as exclusive offers, early product launches, or personalized experiences—could further increase their purchasing frequency and brand loyalty.

- Expand Customer Base in High-Performing Regions: The customer base in North America, Europe, and Australia is robust, and further segmentation to identify untapped customer profiles or niches within these regions can enhance sales. Targeted campaigns or introducing region-specific products could help in expanding market share.

### Product Focus
- Increase Production of Best-Selling Bikes: Given the strong sales of the Mountain-200 Black-42, Mountain-200 Silver-42, and Road-150 Red models, ensuring sufficient stock and introducing complementary products or accessories for these models could further drive sales in the bikes category.

- Diversify Accessory Offerings: As Sports-100 Helmets in Red and Blue and the HL Mountain Tyre are top-sellers, expanding the accessories line with additional color options or introducing new, related products (e.g., high-performance tires or protective gear) could attract more customers and enhance the accessories portfolio.

- Focus on Popular Clothing Lines: With the Women’s Mountain Shorts (L), Long Sleeve Logo Jersey (L), and Short Sleeve Classic Jersey (M) showing strong performance, expanding these lines with new sizes, colors, or designs may further boost clothing sales. Offering these items as part of bundle deals or in limited editions can create a sense of urgency and increase sales.

- These suggestions focus on optimizing existing strengths and addressing areas for improvement, aligning with observed trends in sales and customer behavior.

## Next Steps / Future Recommendations
- Continue investing in the most profitable regions and explore emerging markets.
- Implement strategies to boost sales during the less profitable months.
- Expand and diversify product offerings based on customer preferences.
- Develop targeted marketing campaigns for top customers to maintain loyalty and increase lifetime value.

# Conclusion
The analysis of sales, customer behavior, and product performance revealed valuable insights into the business's growth trajectory and market dynamics. The positive impact of budget allocation on sales, along with the identification of key profitable months and regions, will serve as a strong foundation for future strategies. 
By leveraging top-performing products and enhancing customer engagement in high-performing regions, the business can continue its growth and capitalize on its strengths.

😃 💻
