## Project Title
# Swiggy-food-delivery-sales-and-top-selling-foods-dashboard
### Dashboard Link :
# Problem Statement
The goal of this project is to analyze Swiggy’s food delivery data and develop an interactive Power BI dashboard that provides key business insights for data-driven decision-making. The dashboard should visually represent the overall performance of restaurants, user preferences, and city-wise sales patterns through various KPIs and visualizations.

# Objectives
- Build a comprehensive Power BI dashboard for Swiggy Food Delivery Data Analysis.
- Display Key Performance Indicators (KPIs) such as:
  - **Total Sales**
  - **Average Order Value (AOV)**
  - **Total Restaurants Affiliated**
  - **Active Users**
  - **Percentage of Veg vs Non-Veg Orders**
  
- Provide Visual Insights using:

   - **Bar Chart: Top 10 best-selling food items and bottom 10 least-selling food items.**

   - **Line Chart: Monthly Total Sales trend.**

   - **Map Visualization: City-wise distribution of total sales.**

  - **Matrix Table: Top-rated restaurants with their total sales.**

 - **Donut Chart: Active user segmentation based on Veg and Non-Veg orders.**

- Identify business opportunities by comparing restaurant performance, customer preferences, and sales distribution.

# Tools & Technologies

- Power BI – Data visualization & dashboard creation

- Excel / CSV Dataset – Raw Swiggy delivery data

- Power Query – Data cleaning and transformation

- DAX – KPI calculations and custom measures

## Steps Followed

### 1. Dataset Download
- Downloaded the Swiggy food delivery dataset in **CSV format** from **Kaggle**.

---

### 2. Data Import
- Opened **Power BI Desktop**.  
- Used **Get Data → Excel** (as the CSV file was stored locally).  
- Loaded the dataset in **Import Mode** for faster processing.

**Data Transformation (Power Query):**
- Removed duplicate rows.  
- Handled missing and null values.  
- Verified and corrected data types for all columns (**Date, Numeric, Text**, etc.).  
- Cleaned and formatted the dataset for consistency.

---

### 3. Data Modeling
- Created a **Date Table** for time intelligence calculations.  
- Established relationships between tables using **Primary and Foreign Keys**.  
- Built a **Star Schema Model** to optimize performance and relationships.

---

### 4. DAX Measures
Created important measures for analysis, including:

**Average Order Value:**
```DAX
AverageOrderValue = 
DIVIDE(
    SUM(orders[Total]),  
    COUNT(orders[food_id]),  
    0  -- Default value if division by zero
```
**Customer Satisfaction Rate (Weighted)**
```DAX
CustomerSatisfactionRate_Weighted =
DIVIDE(
    SUMX(
        restaurant,
        SWITCH(
            TRUE(),
            restaurant[rating] >= 4.5, restaurant[rating_count] * 1,    -- Highly satisfied (100%)
            restaurant[rating] >= 4.0, restaurant[rating_count] * 0.8,  -- Satisfied (80%)
            restaurant[rating] >= 3.5, restaurant[rating_count] * 0.5,  -- Neutral (50%)
            restaurant[rating] >= 3.0, restaurant[rating_count] * 0.3,  -- Low satisfaction (30%)
            0                                                           -- Dissatisfied (0%)
        )
    ),
    SUM(restaurant[rating_count]),
    0
) * 100
```

**Max Sales**
```DAX
Max Sales =
VAR _TotalSales =
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]),
            "@TotalSales",[Total Sales]
        ),
        ALLSELECTED()
    )
VAR _MinValue = MINX(_TotalSales,[@TotalSales])
VAR _MaxValue = MAXX(_TotalSales,[@TotalSales])
VAR _TotalOrders = [Total Sales]
RETURN
    SWITCH(
        TRUE(),
        _TotalOrders = _MinValue, 0,
        _TotalOrders = _MaxValue, 1
    )
```

**Max Value Sales**
```DAX
Max value Sales =
VAR _TotalSales =
    CALCULATETABLE(
        ADDCOLUMNS(
            SUMMARIZE('Date','Date'[Month]),
            "@TotalSales",[Total Sales]
        ),
        ALLSELECTED()
    )
VAR _MinValue = MINX(_TotalSales,[@TotalSales])
VAR _MaxValue = MAXX(_TotalSales,[@TotalSales])
VAR _TotalOrders = [Total Sales]
RETURN
    SWITCH(
        TRUE(),
        _TotalOrders = _MinValue,[Total Sales],
        _TotalOrders = _MaxValue,[Total Sales]
    )
```

**Veg vs Non-Veg Measures**
```DAX
NonVegOrderCount =
CALCULATE(
    COUNT(orders[user_id]),
    FILTER(orders, RELATED(Food[Veg_NonVeg]) = "Non-Veg")
)

NonVegUser =
CALCULATE(
    DISTINCTCOUNT(orders[user_id]),
    FILTER(orders, RELATED(Food[Veg_NonVeg]) = "Non-Veg")
)

VegOrderCount =
CALCULATE(
    COUNT(orders[user_id]),
    FILTER(orders, RELATED(Food[Veg_NonVeg]) = "Veg")
)

VegUser =
CALCULATE(
    DISTINCTCOUNT(orders[user_id]),
    FILTER(orders, RELATED(Food[Veg_NonVeg]) = "Veg")
)

VegUsersDishes =
CALCULATE(
    COUNTROWS(Food),
    Food[Veg_NonVeg] = "Veg"
)
```

**Restaurant Ranking**
```DAX
RestaurantRank =
RANKX(
    ALL(restaurant[restaurant_id]),  -- Ignores existing filters
    CALCULATE(SUM(orders[Total])),
    ,
    DESC,
    DENSE
)

SalesRankMeasure =
RANKX(
    ALL(restaurant),
    [TotalOrders],
    ,
    DESC
)
```

**Top 10 Restaurant Sales**
```DAX
Top10RestaurantSales =
VAR RankedRestaurants =
    ADDCOLUMNS(
        SUMMARIZE(
            orders,
            orders[restaurant_id],
            "TotalSales", SUM(orders[Total])
        ),
        "Rank",
        RANKX(
            SUMMARIZE(
                orders,
                orders[restaurant_id],
                "TotalSales", SUM(orders[Total])
            ),
            [TotalSales],
            ,
            DESC,
            DENSE
        )
    )
RETURN
    CALCULATE(
        SUM(orders[Total]),
        FILTER(RankedRestaurants, [Rank] <= 10) -- Keeps only the top 10 restaurants
    )
```

**Basic Measures**
```DAX
Total Sales = SUM(orders[Total])
TotalActiveUsers = DISTINCTCOUNT(orders[user_id])
TotalOrders = COUNT(orders[user_id])
```

**Date Table**
```DAX
Date =
ADDCOLUMNS(
    CALENDARAUTO(),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "mmm"),
    "Weektype",
        IF(
            WEEKDAY([Date])=1, "Weekend",
            IF(WEEKDAY([Date])=7, "Weekend", "Weekdays")
        ),
    "Weekday", FORMAT([Date],"ddd"),
    "Monthnum", MONTH([Date])
)
```


