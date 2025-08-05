# Blinkit_Analysis
To conduct a comprehensive analysis of Blinkit's sales performance, customer satisfaction and inventory distribution to identify key insights and opportunities for optimization using various KPI's and visualizations in PowerBI

KPI's Requirements:
1. Total Sales : The overall revenue  generated from all items sold.
2. Average Sales : The Average revenue per sale.
3. Number Items : The total count of different items sold.
4. Average Rating : Average customer rating  for each items sold.

Granular Requirements
1. Total Sales by Fat Content:
	Objective: Analyze the impact of fat content on total sales.
	Additional KPI Metrics: Assess how other KPIs (Average Sales, Number of Items, Average Rating) vary with fat content.
2. Total Sales by Item Type:
	Objective: Identify the performance of different item types in terms of total sales.
	Additional KPI Metrics: Assess how other KPIs (Average Sales, Number of Items, Average Rating) vary with fat content.
3. Fat Content by Outlet for Total Sales:
	Objective: Compare total sales across different outlets segmented by fat content.
	Additional KPI Metrics: Assess how other KPIs (Average Sales, Number of Items, Average Rating) vary with fat content.
4. Total Sales by Outlet Establishment:
	Objective: Evaluate how the age or type of outlet establishment influences total sales.
Chart’s Requirements
5. Percentage of Sales by Outlet Size:
	Objective: Analyze the correlation between outlet size and total sales.
6. Sales by Outlet Location:
	Objective: Assess the geographic distribution of sales across different locations.
7. All Metrics by Outlet Type:
	Objective: Provide a comprehensive view of all key metrics (Total Sales, Average Sales, Number of Items, Average Rating) broken down by different outlet types.

ALTER TABLE blinkit_data CHANGE `Item Fat Content` item_fat_content VARCHAR(50);
ALTER TABLE blinkit_data CHANGE `Item Identifier` item_identifier VARCHAR(50);
ALTER TABLE blinkit_data CHANGE `Item Type` item_type VARCHAR(100);
ALTER TABLE blinkit_data CHANGE `Outlet Establishment Year` outlet_establishment_year INT;
ALTER TABLE blinkit_data CHANGE `Outlet Identifier` outlet_identifier VARCHAR(50);
ALTER TABLE blinkit_data CHANGE `Outlet Location Type` outlet_location_type VARCHAR(50);
ALTER TABLE blinkit_data CHANGE `Outlet Size` outlet_size VARCHAR(50);
ALTER TABLE blinkit_data CHANGE `Outlet Type` outlet_type VARCHAR(50);
ALTER TABLE blinkit_data CHANGE `Item Visibility` item_visibility DECIMAL(10,8);
ALTER TABLE blinkit_data CHANGE `Item Weight` item_weight DECIMAL(10,2);
ALTER TABLE blinkit_data CHANGE `Total Sales` total_sales DECIMAL(10,2);
ALTER TABLE blinkit_data CHANGE `Rating` rating INT;
ALTER TABLE blinkit_data CHANGE `Item Fat Content` item_fat_content VARCHAR(50);


employeeemployee
SET SQL_SAFE_UPDATES = 0;

UPDATE blinkit_data
SET `Item_Fat_Content` = 
    CASE
        WHEN `Item_Fat_Content` IN ('LF', 'low fat') THEN 'Low Fat'
        WHEN `Item_Fat_Content` = 'reg' THEN 'Regular'
        ELSE `Item_Fat_Content`
    END;

SET SQL_SAFE_UPDATES = 1;  -- (optional) Turn it back on

SELECT DISTINCT item_fat_content FROM blinkit_data;

SELECT 
  CAST(SUM(total_sales) / 1000000.0 AS DECIMAL(10,2)) AS total_sales_million
FROM blinkit_data;

SELECT 
  CAST(AVG(total_sales) AS DECIMAL(10,0)) AS total_average_sales
FROM blinkit_data;

SELECT 
  COUNT(*) AS no_of_items 
FROM blinkit_data;

SELECT 
  CAST(AVG(rating) AS DECIMAL(10,1)) AS avg_rating
FROM blinkit_data;

SELECT 
  item_fat_content, 
  CAST(SUM(total_sales) AS DECIMAL(10,2)) AS total_sales,
  CAST(SUM(total_sales) / 1000000.0 AS DECIMAL(10,2)) AS total_sales_million,
  CAST(AVG(total_sales) AS DECIMAL(10,0)) AS total_average_sales,
  CAST(AVG(rating) AS DECIMAL(10,1)) AS avg_rating
FROM blinkit_data
GROUP BY item_fat_content;


SELECT 
  outlet_location_type,
  item_fat_content,
  CAST(SUM(total_sales) AS DECIMAL(10,2)) AS total_sales,
  CAST(AVG(total_sales) AS DECIMAL(10,1)) AS average_sales,
  COUNT(*) AS no_of_items, 
  CAST(AVG(rating) AS DECIMAL(10,2)) AS avg_rating
FROM blinkit_data	
GROUP BY outlet_location_type, item_fat_content
ORDER BY total_sales desc
LIMIT 5;


SELECT Item_Type, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Item_Type
ORDER BY Total_Sales DESC;

SELECT Outlet_Location_Type,  
       IFNULL(CAST(SUM(CASE WHEN Item_Fat_Content = 'Low Fat' THEN Total_Sales ELSE 0 END) AS DECIMAL(10,2)), 0) AS Low_Fat,
       IFNULL(CAST(SUM(CASE WHEN Item_Fat_Content = 'Regular' THEN Total_Sales ELSE 0 END) AS DECIMAL(10,2)), 0) AS Regular
FROM blinkit_data
GROUP BY Outlet_Location_Type
ORDER BY Outlet_Location_Type;

SELECT outlet_establishment_year, 
       CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
        CAST(AVG(total_sales) AS DECIMAL(10,1)) AS average_sales,
         COUNT(*) AS no_of_items
        
FROM blinkit_data
GROUP BY outlet_establishment_year
ORDER BY outlet_establishment_year ASC;

SELECT 
    Outlet_Size, 
    CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
    CAST((SUM(Total_Sales) * 100.0 / SUM(SUM(Total_Sales)) OVER()) AS DECIMAL(10,2)) AS Sales_Percentage
FROM blinkit_data
GROUP BY Outlet_Size
ORDER BY Total_Sales DESC;
 
 SELECT Outlet_Location_Type, CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales
FROM blinkit_data
GROUP BY Outlet_Location_Type
ORDER BY Total_Sales DESC;
 
  
  
SELECT Outlet_Type, 
CAST(SUM(Total_Sales) AS DECIMAL(10,2)) AS Total_Sales,
		CAST(AVG(Total_Sales) AS DECIMAL(10,0)) AS Avg_Sales,
		COUNT(*) AS No_Of_Items,
		CAST(AVG(Rating) AS DECIMAL(10,2)) AS Avg_Rating,
		CAST(AVG(Item_Visibility) AS DECIMAL(10,2)) AS Item_Visibility
FROM blinkit_data
GROUP BY Outlet_Type
ORDER BY Total_Sales DESC
