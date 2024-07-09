# Sales Performance Analysis

<div align="justify">This project aims to analyze and monitor the sales performance of a retail store using DAX queries and a Power BI dashboard. The project provides insights into sales trends, the profits of the products sold, who bought it and in which market.</div></br>

The repository contains a Power BI dashboard for visualizing and analyzing several metrics and also the data used in it.

## Table of Contents
1. [Project Statement](#project-statement)
2. [Objectives](#objectives)
3. [Dataset](#dataset)
4. [Data Modeling](#data-modeling)
5. [PowerBI Dashboard](#powerbi-dashboard)
6. [Recommendations](#recommendations)

## Project Statement

<div align="justify"> The objective of this project is to conduct a comprehensive analysis of the Global Superstore dataset, focusing on sales, products, markets and customers. To achieve this goal, i will employ data modeling and data transformation techniques to refine the dataset, ensuring that the most relevant columns are considered on the analysis, while using an eficient data model. </div></br>

<div align="justify">Using DAX, i will extract meaningful insights and patterns that are important to understand which costumers and markets are the most important to maximize sales and profitability. This approach not only provides a more insightfull analysis but it also facilitates the understanding of the factors that influence sales and profit.</div></br>

## Objectives

With this dashboard i want to answer several question to help understand the business performance and how it can be improved in the future.

- Which are the sales tendency over time for the different categories of products?
- Find out the total sales per market over time.
- Which products are more or less profitable?
- <div align="justify">Show the category of products and the respective sub-categories that are more profitable, depending on the market and segment of products.</div>
- Find out which countries have more orders placed and which ones are more profitable.
- Show the average number of days that takes to ship an order on the countries of a certain market.
- Classify the costumer by the number of orders to understand which costumers are the most profitable.
- <div align="justify">Identify customers who have placed a high volume of orders but have stopped ordering for a certain period of time, in order to reduce the churn rate.</div></br>

## Dataset

<div align="justify">The dataset used is the Global Superstore dataset with 23 features and 51291 different orders, in a time period of four years. The orders where placed by 1590 customers, from 146 countries and distributed between seven markets. The store has 10292 different products, divided into 3 different categories and 17 sub-categories, that are placed in 3 distinct segments.</div></br>

## Data Modeling

<div align="justify">To query and work with the data faster and easily, the dataset was divided into four tables using a star schema. This schema has three dimension tables, called City, Customers and Products, and one fact table called Orders, that was normalized avoiding the repetition of the data of the other three tables.</div></br>

![image](https://github.com/victor-malheiro/PowerBI/assets/142715844/2e4f6f67-bb27-4484-a0dd-2177f5b38a04)

## PowerBI Dashboard

<div align="justify">The PowerBI Dashboard provides visualizations and oportunities to analyze different metrics that were defined on the [Objectives](#objectives) section. The dashboard presents several information, using visualization and DAX programming, divided into four different pages that can be accessed by the navigation buttons and with a time slicer to analyze all the resullts over time.</div></br>

<div align="justify">On the first page i created two charts representing on the first one the sales over time by category and on the second, the trend of the sales by market, using a tooltip to present the top 5 countries in each market as an additional representation, to understand who contributed the most for the results presented.</div></br>

![image](https://github.com/victor-malheiro/PowerBI/assets/142715844/43eb137c-5fb1-48d2-a58e-f67fb1338ae4)

<div align="justify">The second page was created to analyze which products contribute the most or not to the profits of the store. A pie chart was created to know how the categories, and sub-categories using a tooltip, contribute to the global profit.</div></br>

<div align="justify">Two tables were also inserted to analyze which are the five products that are more profitable and another one to analyze which products contribute less to the profits, with the respective values.</div></br>

<div align="justify">All this information can be further filtered using two additional slicers, one for the market and another for the segment of products.</div></br>

![image](https://github.com/victor-malheiro/PowerBI/assets/142715844/6086022c-af60-488a-b75b-dbd217a298a5)

<div align="justify">To analyze the markets in more detail, a third page was created where the top 10 countries that are more important to the profits are presented, and also the number of orders per country.</div></br>

<div align="justify">A table was also added to show the average number of days that is need to ship an order in each country. This was done creating a calculated table where the number of days from the order date to the ship date was computed, using the DAX code below.</div></br>

```
ShipTimeinDays = DATEDIFF(Orders[Order Date], Orders[Ship Date],DAY)
```

These visualizations can be filtered using a slicer where the different markets can be chosen.

![image](https://github.com/victor-malheiro/PowerBI/assets/142715844/d153e4b3-35ab-407a-80b8-dfc82d6e275e)

<div align="justify">Finally, a fourth page was created to analyze the costumers that bought items on the store, a chart was created to show the 10 clients that created more orders.</div></br>

<div align="justify">Two pie charts were also created, the first one is to show the total profit divided by a client classification created by me, to divide the clients by the volume purchased. This chart was based on a calculated column called "ClientClassification" that divided the clients on three classes, the clients with low, medium and high volumes of orders.</div></br>

```
ClientClassification = SWITCH(TRUE(),
        [numOrdersClient] >= 70, "High Volume Client",
        [numOrdersClient] >= 20, "Medium Volume Client",
        "Low Volume Client")
```

To create the client classification, a measure with name "numOrdersClient" was computed to know the number of orders each client has.

```
numOrdersClient = CALCULATE(count(Orders[Order ID]))
```

<div align="justify">The second pie chart represents the number of customers that need a certain action action to be taken. This action is based on the number of orders the client has, but for some reason the client stopped ordering for a certain period of time, this is done in order to reduce the churn rate.</div></br>

<div align="justify">Three different actions are advised, the first one is to contact the client immediatly, then it is to contact the client ASAP and the last one is to identify the number of clients that do not need to be contacted.</div></br>

<div align="justify">To know the action to be taken for each client, three calculated columns were created. The first one is to know when the last order of each client was created.</div></br>

```
LastOrder = CALCULATE(
                MAX(Orders[Order Date]),
                FILTER(
                    ALL(Orders),
                    Orders[Customer ID] = Customers[Customer ID]
                )
            )
```

The second column was created to know how many days passed since the last order a certain client made.

```
DaysFromLastOrder = DATEDIFF(Customers[LastOrder], TODAY(),DAY)
```

And finally, the action to be taken for each client was decided.

```
Action = 
    IF(Customers[DaysFromLastOrder] > 3500 && (Customers[ClientClassification] = "High Volume Client" || Customers[ClientClassification] = "Medium Volume Client"), "Contact Client Immediatly",
    IF(Customers[DaysFromLastOrder] >= 2500 && (Customers[ClientClassification] = "High Volume Client" || Customers[ClientClassification] = "Medium Volume Client"), "Contact Client ASAP",
    "Do Not Contact Client"))
```

<div align="justify">All the visualizations could be filtered by the market with the help of a slicer, that enables the drill down of the market by the country and the city.</div></br>

![image](https://github.com/victor-malheiro/PowerBI/assets/142715844/4791c512-0f11-4211-989e-2940694b49a6)

## Recommendations

<div align="justify">To access the dashboard, open the StoreGlobalOrders.pbix file in Power BI Desktop, interact with the visualizations on the different pages, that can be navigated using the navigation buttons on the top of each page, apply filters and slicers, to analyze the all the data over time.</div></br>

<div align="justify">The dashboard is usefull to gather knowledge about the sales information and about the products sold, the costumers that bought them and the respective markets, to help the management take more informed decisions.</div></br>

## Copyright

© 2024 Victor Malheiro
