## Project Title
# Swiggy-food-delivery-sales-and-top-selling-foods-dashboard
### Dashboard Link :
# Problem Statement
The goal of this project is to analyze Swiggy’s food delivery data and develop an interactive Power BI dashboard that provides key business insights for data-driven decision-making. The dashboard should visually represent the overall performance of restaurants, user preferences, and city-wise sales patterns through various KPIs and visualizations.

# Objectives
1.Build a comprehensive Power BI dashboard for Swiggy Food Delivery Data Analysis.

2.Display Key Performance Indicators (KPIs) such as:

Total Sales

   Average Order Value (AOV)

   Total Restaurants Affiliated

   Active Users

   Percentage of Veg vs Non-Veg Orders
  
  3. Provide Visual Insights using:

  Bar Chart: Top 10 best-selling food items and bottom 10 least-selling food items.

  Line Chart: Monthly Total Sales trend.

  Map Visualization: City-wise distribution of total sales.

  Matrix Table: Top-rated restaurants with their total sales.

  Donut Chart: Active user segmentation based on Veg and Non-Veg orders.

4. Identify business opportunities by comparing restaurant performance, customer preferences, and sales distribution.

# Tools & Technologies

Power BI – Data visualization & dashboard creation

Excel / CSV Dataset – Raw Swiggy delivery data

Power Query – Data cleaning and transformation

DAX – KPI calculations and custom measures

# Steps followed
Dataset Download:

Downloaded the Swiggy food delivery dataset in CSV format from Kaggle.

Data Import:

Opened Power BI Desktop.

Used Get Data → Excel (as the CSV file was stored locally).

Loaded the dataset in Import Mode for faster processing.

Data Transformation (Power Query):

Removed duplicate rows.

Handled missing and null values.

Verified and corrected data types for all columns (Date, Numeric, Text, etc.).

Cleaned and formatted the dataset for consistency.

Data Modeling:

Created a Date Table for time intelligence calculations.

Established relationships between tables using Primary and Foreign Keys.

Built a Star Schema Model to optimize performance and relationships.

DAX Measures:

Created important measures for analysis, including:

AverageOrderValue = 
DIVIDE(
    SUM(orders[Total]),  
    COUNT(orders[food_id]),  
    0  -- Default value if division by zero
)

Total Sales = SUM(orders[Total])


  
