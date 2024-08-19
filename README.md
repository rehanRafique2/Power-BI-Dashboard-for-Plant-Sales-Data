# Unlocking Business Insights: Creating a Power BI Dashboard for Plant Sales Data
![Dashboard](https://github.com/user-attachments/assets/c1fd00d6-a02d-472a-847a-8df92ad4c0a9)

In the era of data-driven decision-making, the ability to visualize and analyze data effectively can provide businesses with a significant edge. In this blog post, I’ll walk you through the process of building a Power BI dashboard that analyzes plant sales data, from setting up the data model to extracting actionable insights. This project is a part of my portfolio, showcasing my skills in data visualization and analysis.

## Understanding the Dataset
### Data Sources and Preparation
The dataset used in this dashboard includes sales data, customer information, and date dimensions spanning from 2022 to 2024. The primary data sources were Excel files, which I cleaned and prepared before importing into Power BI. I created a ‘Dim_Date’ table using the following DAX measure to cover the analysis period:

Dim_Date =

CALENDAR(

DATE(2022, 1, 1),

DATE(2024, 12, 31)

)

This date table was crucial for performing time-based calculations, such as Year-to-Date (YTD) metrics, and ensuring consistency across all time-based analyses.

## Creating Relationships
Establishing relationships between tables was a critical step in the data modeling process. I linked the ‘Fact_Sales’ table to the ‘Dim_Date’ table via the ‘Date_Time’ field, enabling accurate time-based calculations and filtering. Proper relationships ensure that the data is aggregated correctly, providing reliable insights.

### Key Metrics and DAX Calculations
#### 1. COGs (Cost of Goods Sold):
 ```dax

COGs = SUM(Fact_Sales[COGS_USD])

```
![cogs](https://github.com/user-attachments/assets/fa9135be-0911-46e9-9c8d-c129f459939a)

#### Explanation: This measure sums up the `COGS_USD` column to calculate the total cost of goods sold.

#### 2. Sales:
```dax

Sales = SUM(Fact_Sales[Sales_USD])

```

#### Explanation: This measure sums the `Sales_USD` column to calculate the total revenue.

#### 3. Quantity:
```dax

Quantity = SUM(Fact_Sales[quantity])

```

#### Explanation: This measure sums the `quantity` column to get the total number of items sold.

#### 4. Gross Profit:
```dax

Gross Profit = [Sales] — [COGs]

```

#### Explanation: This measure calculates the profit by subtracting COGS from Sales.

#### 5. PYTD_Gross_Profit (Prior Year-to-Date Gross Profit):
```dax

PYTD_Gross_Profit = CALCULATE(

[Gross Profit],

SAMEPERIODLASTYEAR(Dim_Date[Date]),

Dim_Date[Inpast] = TRUE()

)

```

#### Explanation: This measure calculates gross profit for the same period last year.

#### 6. YTD_Gross_Profit (Year-to-Date Gross Profit):
```dax

YTD_Gross_Profit = TOTALYTD([Gross Profit], Fact_Sales[Date_Time])

```

#### Explanation: This measure calculates gross profit from the start of the year to the current date.

#### 7. PYTD_Quantity (Prior Year-to-Date Quantity):
```dax

PYTD_Quantity = CALCULATE(

[Quantity],

SAMEPERIODLASTYEAR(Dim_Date[Date]),

Dim_Date[Inpast] = TRUE()

)

```

#### Explanation: This measure calculates the quantity sold during the same period last year.

#### 8. YTD_Quantity (Year-to-Date Quantity):
```dax

YTD_Quantity = TOTALYTD([Quantity], Fact_Sales[Date_Time])

```

#### Explanation: This measure calculates the quantity sold from the start of the year to the current date.

#### 9. PYTD_Sales (Prior Year-to-Date Sales):
```dax

PYTD_Sales = CALCULATE(

[Sales],

SAMEPERIODLASTYEAR(Dim_Date[Date]),

Dim_Date[Inpast] = TRUE()

)

```

#### Explanation: This measure calculates sales for the same period last year.

#### 10. YTD_Sales (Year-to-Date Sales):
```dax

YTD_Sales = TOTALYTD([Sales], Fact_Sales[Date_Time])

```

#### Explanation: This measure calculates sales from the start of the year to the current date.

#### 11. S_PYTD (Dynamic Prior Year-to-Date Selector):
```dax

S_PYTD =

VAR selected_value = SELECTEDVALUE(Slc_Values[Values])

VAR result = SWITCH(selected_value,

“Sales”, [PYTD_Sales],

“Quantity”, [PYTD_Quantity],

“Gross Profit”, [PYTD_Gross_Profit],

BLANK()

)

RETURN result

```
![s_pytd](https://github.com/user-attachments/assets/c452800b-c5b8-40b1-85e1-0c461423ec2a)

#### Explanation: This measure dynamically returns the prior year’s value (Sales, Quantity, or Gross Profit) based on user selection.


#### 12. S_YTD (Dynamic Year-to-Date Selector):
```dax

S_YTD =

VAR selected_value = SELECTEDVALUE(Slc_Values[Values])

VAR result = SWITCH(selected_value,

“Sales”, [YTD_Sales],

“Quantity”, [YTD_Quantity],

“Gross Profit”, [YTD_Gross_Profit],

BLANK()

)

RETURN result

```

#### Explanation: This measure dynamically returns the current year’s value (Sales, Quantity, or Gross Profit) based on user selection.

#### 13. YTD vs PYTD (Year-to-Date vs Prior Year-to-Date):
```dax

YTD VS PYTD = [S_YTD] — [S_PYTD]

```

#### Explanation: This measure calculates the difference between the current year’s and prior year’s values for the selected metric.


### GP% (Gross Profit Percentage): Calculated as Gross Profit / Sales, this measure provides insight into profitability by indicating what percentage of sales is retained as profit after accounting for the cost of goods sold.

## Designing the Dashboard
### Slicers for Dynamic Filtering:
**Metric Selector**: The first slicer allows users to switch between different key metrics — Gross Profit, Quantity, and Sales. This provides flexibility to focus on the specific data that matters most at any given time.
**Year Filter**: The second slicer enables filtering by year (2022, 2023, 2024). Users can easily compare performance across different years.
**Multi-Row Card**:
I created a multi-row card that displays key metrics such as S_PYTD, S_YTD, YTD vs. PYTD, and GP% (Gross Profit Percentage). This card provides a quick snapshot of essential financial figures, making it easier to track and compare current performance with the previous year.
#### Visualizations:
**Treemap:** A Bottom 10 YTD vs. PYTD (Year-to-Date vs. Prior Year-to-Date) treemap visualizes the performance of the bottom 10 countries. This visualization highlights areas where improvement is needed by showing which countries are underperforming compared to the previous year.
![treemap](https://github.com/user-attachments/assets/c7b42e19-9515-4a1a-b77d-5d0f1a0b5c8f)

**Waterfall Chart:** A waterfall chart displays the difference between YTD and PYTD for the selected metric. This visual effectively breaks down how each component contributes to the overall change, making it easier to understand the drivers behind the performance.
![waterfall chart](https://github.com/user-attachments/assets/d5e34ae8-7641-4338-bddd-1db7e746188f)

**Line and Stacked Column Chart:** This chart shows the S_YTD on the Y-axis with the Date on the X-axis. The column legend is segmented by product type, and the line Y-axis displays the S_PYTD. This combined visual provides a clear comparison between current and prior year performance across different product types.
![stacked column chart](https://github.com/user-attachments/assets/86005017-8076-4415-8b23-44a7bc201897)

**Scatterplot for Account Profitability Segmentation:** The scatterplot visual is used for segmenting account profitability. The X-axis represents S_YTD, the Y-axis represents GP%, and the values represent accounts. This visualization helps in identifying accounts that are most profitable and those that need attention.
![scatterplot](https://github.com/user-attachments/assets/8656bf2c-4d66-44ef-a518-5d8542ce5db6)

## Insights and Recommendations
### Business Insights
The dashboard reveals several key insights, such as the strong performance of specific plant categories and seasonal sales trends. For example, there was a noticeable increase in sales during the spring months, likely due to increased consumer interest in gardening.

The Treemap showing the bottom 10 countries in YTD vs PYTD sales highlights areas where the company needs to focus more attention. These underperforming regions may benefit from targeted marketing campaigns or strategic adjustments.

The Waterfall Chart provides a clear breakdown of the differences between YTD and PYTD Gross Profit, allowing the company to understand which factors contributed most to the overall change. This insight can guide strategic decisions to enhance profitability.

## Actionable Recommendations
Based on the insights, I recommend increasing marketing efforts during peak sales seasons and exploring strategies to boost sales in underperforming regions. These insights can help the business focus its resources more effectively and drive better results.

## Conclusion
Reflection and Future Work
This project allowed me to deepen my understanding of Power BI’s capabilities, particularly in creating interactive and dynamic dashboards. The ability to visualize complex data and derive actionable insights is invaluable in today’s data-centric business environment. Moving forward, I plan to incorporate predictive analytics into the dashboard to forecast future sales trends, providing even more value to business decision-makers.

## Call to Action
I invite readers to share their thoughts on the dashboard and any suggestions for improvement. Feel free to reach out if you have any questions or if you’re interested in collaborating on similar projects.

# LINKEDIN: https://www.linkedin.com/in/muhammad-rehan-rafique-76a72b267/
