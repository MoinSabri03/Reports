
# Adventure works Dashboard


## Pdf link for the final product:
https://drive.google.com/file/d/10yts1j2n8qHQbxknCxqa4sIlKtMaz_7U/view?usp=sharing

## Problem Statement
 Adventure Works Company a global  manufacturing company that produces cycling equipment and accessories
 The management team needs a way to track KPIs (sales, revenue, profit, returns), compare regional performance, analyze product-level trends, and identify high-value customers.
This dashboard helps the company to understand their customers better. It helps the company know if their customers are satisfied with their services. Through analyzing trends and pattern they get to know their improvement area, & thus they can improve their services by identifying these area. It also lets them know the which product is high in demand  & product which causing loss to the company, thus since by using this dashboard they have identified this problem, they can further work on factors responsible for these unwanted issue and the number one customer  .








### Steps followed 

- Step 1 : Load data into Power BI Desktop, dataset is a csv file.
- Step 2: change the data type of product cost and product price  into Currency (Fixed decimal number) and Removed Columns Product size as the data in it is inconsistent i.e number and text and duplicated the product sku type and fetch the data of second delimeter.
- Step 3 : Open power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options. Also since by default, profile will be opened only for 1000 rows so you need to select "column profiling based on entire dataset".
- Step 4 : It was observed that in 5 of the columns errors &  empty values were showing in customer lookup table so removed 5 errors by checking all the data  by keep errors and removed 1 empty.
- Step 5 :Capitalizing each word of Prefix,First_name,Last_name. and merging it into a full name by add column menu.
- Step 6- Created a domain name column by adding column from email column by text before and after delimeters and replacing values of hyphen to space and adding discount price by multiplying 0.9 to product price .
- Step 7:in calender lookup table adding column day name,start of week,start of month,start of quarter,month,month name,start of year,year.
- Step 8 :Define primary key of each table in model view and create relationship between Facts and dimension table and also create the hierarchy of country and year. 

![Screenshot (28)](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/96de097a-338b-4545-b30f-3c7eb939cc04)

- Step 9-Calculated columns created in sales table
        Quantity Type = IF(
    'Sales Data'[OrderQuantity] >1,
    "Multiple",
    "Single")
           

- Step 10-Created  Measure table and creating measure one by one which we will use in visualization in report view For e.x Total Order,Total profit,Total Revenue e.t.c .
  * Total Customer = 
        DISTINCTCOUNT(
        'Sales Data'[CustomerKey]
        )
  * Total Order = 
        DISTINCTCOUNT('Sales Data'[OrderNumber]
        )
  * Total Profit = [Total Revenue]-[Total Cost]
  * Total Returns = 
   COUNT(
    'Returns Data'[ReturnQuantity]
                )
  * Return  rate = 
        DIVIDE(
        [Quantity Returned],
        [Quantity sold],
        "No Sales"
        )
  * Revenue Target = [Previous Month Revenue] * 1.1
  * Revenue Target Gap = [Total Revenue]-      [Revenue  Target]

  * Total Revenue = 
     SUMX(
        'Sales Data',
        'Sales Data'[OrderQuantity] * 
                RELATED(
                'Product Lookup'[ProductPrice]
                ))

  * Total Revenue(Customer Detail) = 
    if(
        HASONEVALUE(
        'Customer Lookup'[CustomerKey]
    ),
        [Total Revenue],
         "-"
        )

   * Weekend Orders = 
     CALCULATE(
    [Total Order],
    'Calendar Lookup'[weekend]="weekend"
     )
   *  YTD Revenue = 
   CALCULATE(
    [Total Revenue],
    DATESYTD(
        'Calendar Lookup'[Date]
    )
    )


  * Quantity Returned = SUM('Returns Data'[ReturnQuantity])
  * Quantity sold = (
    sum(
        'Sales Data'[OrderQuantity]))
  * Return  rate = 
        DIVIDE(
        [Quantity Returned],
        [Quantity sold],
        "No Sales"
        )
  
  * Total Cost = 
        SUMX(
        'Sales Data',
        'Sales Data'[OrderQuantity] *
                 related(
        'Product Lookup'[ProductCost]
        )
        )
 
  * Total Order(Customer Detail) = 
     if(
        HASONEVALUE(
                'Customer Lookup'[CustomerKey]
        ),
        [Total Order],
                "-"
        ) 

  * Order Quantity =
      SUM('Sales Data' [OrderQuantity])
  * Average Retail Price = 
    AVERAGE('Product      Lookup'[ProductPrice])
  * % of all Order = 
      DIVIDE(
    [Total Order],
    [All Orders]
    )
 * % Of All Return = 
     DIVIDE(
    [Total Returns],
    [All Return])
 *  10-Day Rolling Revenue = CALCULATE(
    [Total Revenue],
    DATESINPERIOD('Calendar Lookup'[Date],
    MAX(
        'Calendar Lookup'[Date]
    ),
    -10,
    DAY
    )
* 90-Day Rolling Profit = 
     CALCULATE(
    [Total Profit],
    DATESINPERIOD('Calendar Lookup'[Date],
    MAX(
        'Calendar Lookup'[Date]
    ),
    -90,
    DAY
    )
    )
* 
  Adjusted Price = 
     [Average Retail Price] * (1+'Price Adjustment(%)'[Price Adjustment(%) Value]) 
* Adjusted Profit = [Adjusted Revenue]-[Total Cost]
* Adjusted Revenue = 
  SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] * 
   [Adjusted Price]
  )
*  All Orders = 
   CALCULATE(
    [Total Order],
    ALL(
        'Sales Data'
    )
   )
* All Return = 
   CALCULATE(
    [Total Returns],
    ALL(
        'Returns Data'
    )
  )
* All Return = 
  CALCULATE(
    [Total Returns],
    ALL(
        'Returns Data'
    )
  )
* Average revenue per customer = 
   DIVIDE(
    [Total Revenue],
    [Total Customer]
   )
* Bikes Sold = 
  CALCULATE(
    [Quantity sold],
    'Product Categories Lookup'[CategoryName]   ="Bikes"
    )
* Bulk orders = 
        CALCULATE(
    [Total Order],
    'Sales Data'[OrderQuantity]>1
    )  
  
* Bike Return Rate = 
  CALCULATE(
   
    [Return  rate],
    'Product Categories Lookup'[CategoryName]     ="Bikes"
   )
* BIke Returned = 
   CALCULATE(
    [Total Returns],
    'Product Categories Lookup'[CategoryName]
    ="Bikes"
    )
* High Ticket Orders = 
   CALCULATE(
    [Total Order],
    FILTER('Product Lookup',
    'Product Lookup'[ProductPrice]>[Overall average price]
    )
)
* Order Target = [Previous Month order] * 1.1
* Order Target Gap = [Total Order]-[Order Target]
* Overall average price = 
  CALCULATE(
    [Average Retail Price],
    ALL(
        'Product Lookup'
    )
   )
* Previous Month order = 
   CALCULATE(
    [Total Order],
    DATEADD(
        'Calendar Lookup'[Date],
    -1,
    MONTH
    )
 )
* Previous Month Profit = 
 CALCULATE(
    [Total Profit],
    DATEADD(
        'Calendar Lookup'[Date],
    -1,
    MONTH
    )
   )
*Previous Month Return = 
   CALCULATE(
    [Total Returns],
    DATEADD(
        'Calendar Lookup'[Date],
    -1,
    MONTH
    )
   )
* Previous Month Revenue = 
   CALCULATE(
    [Total Revenue],
    DATEADD('Calendar Lookup'[Date],
    -1,
    MONTH
    )
   )
* Profit Target = [Previous Month Profit] * 1.1
* Profit Target Gap = [Total Profit]-[Profit Target]
-Creating visualization by adding charts,KPI,Maps amd metrices to the 6 different tabs Exec dashboard,Map,Product detail,Decomposition tree,Key influencers  and customer Tab

## Exec Dashboard-

- Step 1-adding KPI Cards,Bookmarks,filters on visual ,clustered bar chart,matrix and company logo  to the exec dashboard layout and adding 4 kpi cards i.e total order,total revenue,total profit and return rate in the field section.
- Step 2-Inserting matrix to the visual through insert menu and adding product name,Order<,Revenue and return rate and applying filters top 10 product name.
 Step 3 -Inserting Line chart on x axis start of month and y axis total revenue and metrix visualto the layout
- Step4- Inserting  order by category  clustered bar chart in the layout and adding matrix from insert menu.
-Step 5 -Inserting 2 cards and adding filter top 1  n item for most ordered product and most returned product type
- Step 6-Inserting 3 cards i.e  Monthly Revenue, Monthly Ordered and  Monthly Returned and adding start of month trend axis and adding previous month in tartet tab to get more appropiate visual for the cards
- Step7- Adding filter to the visual
  - Previous month  order
  - Start of Month
  - Total Order

  Final layout

  Exec Dashboard-
![Exec Dashboard](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/c2ed2d50-29b6-4292-88da-26b7309009dd)

## Map Layout
Adding second layout naming maps by adding maps   - adding country to the location value.
- adding total order to the bubble.
-  adding slicer to the layouts by adding continent.
![Screenshot (23)](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/c4584453-f24a-4f86-ae18-81e3d2189a13)

## Product detail layout
Adding third layout name product Detail layout
 - Step 1-Adding card to the layout by product name which is top product.
 -  Step 2-Adding 3 gauge chart to the layout 
  and adding value 
   - Total order 
   - Total revenue 
   - Total profit
  - Step 3- adding 3 target value in maximum section to see the target acheived or not for targeted revenue,targeted order and targeted profit.

  - Step 4- Adding area and line chart to the layout.

  - Step 5- Adding slicer and adding Price Adjustment(%)In the value. and also adding second slicer and putting Product metric selection n the field option

  - Step 6 -Summary also added to the layout to get the final insights.

  Product detail layout
  ![Screenshot (24)](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/d422b2a1-3343-442a-aa2e-d615e0d53daf)



## Customer layout
- Step 1-Adding 2 cards one by putting total customer in the field and revenue per customer in the field.
- Step 2- Adding line chart to the layout by putting date hierarchy on x axis  and customer metric in the y axis.
- Step 3 -Adding   2 donut chart to the layout 
        - Order By income level.
        - order by occupation.
- Step 4 -inserting table and adding customer key,full name,order and revenue.

- Step 5 - Adding 3 cards 
        * Top Customer (By Revenue)
        * Orders
        * Revenue
 ![Screenshot (25)](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/1a8ed966-c4c3-4a5e-b8c4-0e247465808f)

 ## Decomposition tree Layout

- Inserting Decomposition tree by putting total order  in analyze and category name,Sub category name and product name in the Explain by  to understand the product hierarchy.
![Screenshot (26)](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/0e7ac978-fec5-4193-ad0f-8a186113773e)

## Key Influencers

Adding 2 Key Influencers
one by putting Home owner into analyze and Sum of annual income,parent ,marital status,education level and occupation explain by.
Second by putting Average retail prce analyze section, sub-category name in expand by and sum of product cost in explain by.
 ![Screenshot (27)](https://github.com/MoinSabri03/PowerBI_Dashboard_Report/assets/152681629/6efd0c06-51bf-407c-9a0d-99ba4f7346ec)



# Insights after final product
- Total orders for ﻿Water Bottle - 30 oz.﻿   were  ﻿404﻿



- Total Profit (380.77% increase) and Adjusted Profit (380.77% increase) both trended up between Sunday, June 27, 2021 and Sunday, June 26, 2022.﻿﻿
﻿﻿

-  Return% was unexpectedly high on Sunday, November 7, 2021. It had a value of 6.03%, which is outside the expected range of 0.04-0.05.﻿﻿

-  The most recent anomaly was on Sunday, November 7, 2021, when Return% had a high value of 6.03%.

- Return% had the most significant anomaly, a high of 6.03% on Sunday, November 7, 2021.

- Return% experienced the longest period of decline (-2.19%) between Sunday, November 28, 2021 and Sunday, March 6, 2022.

- Among customers in skilled manual roles in 2022.Ruben suarez Drove  the  most revenue at $4683
