[[Software Platform - Power BI]] [[DAX]]

https://www.daxpatterns.com/patterns/

# [Time patterns](https://www.daxpatterns.com/time-patterns/)

This post introduces the four time-related calculations patterns presented in this website. The goal here is to help you choose the right pattern based on your specific needs. Indeed, when it comes to time-related calculations, the choice of the pattern … [Read more](https://www.daxpatterns.com/time-patterns/)

# [Standard time-related calculations](https://www.daxpatterns.com/standard-time-related-calculations/)

In this pattern, we show you how to compute time-related calculations, like year-to-date, same period last year, and percentage growth using a standard calendar. The great advantage of working with a standard calendar is that you can rely on several … [Read more](https://www.daxpatterns.com/standard-time-related-calculations/)

# [Month-related calculations](https://www.daxpatterns.com/month-related-calculations/)

This pattern describes how to compute month-related calculations such as year-to-date, same period last year, and percentage growth using a month granularity. This pattern does not rely on DAX built-in time intelligence functions.

# [Week-related calculations](https://www.daxpatterns.com/week-related-calculations/)

This pattern describes how to compute week-related calculations, such as year-to-date, same period last year, and percentage growth using a week granularity. This pattern does not rely on DAX built-in time intelligence functions. All the measures refer to the fiscal … [Read more](https://www.daxpatterns.com/week-related-calculations/)

# [Custom time-related calculations](https://www.daxpatterns.com/custom-time-related-calculations/)

This pattern shows how to compute time-related calculations like year-to-date, same period last year, and percentage growth using a custom calendar. This pattern does not rely on DAX built-in time intelligence functions. All the measures refer to the fiscal calendar … [Read more](https://www.daxpatterns.com/custom-time-related-calculations/)

# [Comparing different time periods](https://www.daxpatterns.com/comparing-different-time-periods/)

This pattern is a useful technique to compare the value of a measure in different time periods. For example, we can compare the sales of the last month against a user-defined period. The two time periods might have a different … [Read more](https://www.daxpatterns.com/comparing-different-time-periods/)

# [Semi-additive calculations](https://www.daxpatterns.com/semi-additive-calculations/)

Calculations reporting values at the start or the end of a time period are quite the challenge for any BI developer, and DAX is no exception. These measures are not hard to compute; the complicated part is understanding the desired … [Read more](https://www.daxpatterns.com/semi-additive-calculations/)

# [Cumulative total](https://www.daxpatterns.com/cumulative-total/)

The cumulative total pattern allows you to perform calculations such as running totals. You can use it to implement warehouse stock and balance sheet calculations using the original transactions instead of using snapshots of data over time.

# [Related distinct count](https://www.daxpatterns.com/related-distinct-count/)

The Related distinct count pattern is useful whenever you have one or more fact tables related to a dimension, and you need to perform the distinct count of column values in a dimension table only considering items related to transactions … [Read more](https://www.daxpatterns.com/related-distinct-count/)

# [Parameter table](https://www.daxpatterns.com/parameter-table/)

The parameter table pattern is used to create parameters in a report, so that users can interact with slicers and dynamically change the behavior of the report itself. For example, a report can show the top N products by category, … [Read more](https://www.daxpatterns.com/parameter-table/)

# [Static segmentation](https://www.daxpatterns.com/static-segmentation/)

The static segmentation pattern classifies numerical values into ranges. A typical example is the analysis of sales by price range. You do not want to slice the data by individual price; instead you want to simplify the analysis by grouping … [Read more](https://www.daxpatterns.com/static-segmentation/)

# [Dynamic segmentation](https://www.daxpatterns.com/dynamic-segmentation/)

The Dynamic segmentation pattern is useful to perform the classification of entities based on measures. A typical example is to cluster customers based on spending volume. The clustering is dynamic, so that the categorization considers the filters active in the … [Read more](https://www.daxpatterns.com/dynamic-segmentation/)

# [ABC classification](https://www.daxpatterns.com/abc-classification/)

The ABC classification pattern classifies entities based on values, grouping entities together that contribute to a certain percentage of the total. A typical example of ABC classification is the segmentation of products (entity) based on sales (value). The best-selling products … [Read more](https://www.daxpatterns.com/abc-classification/)

# [Budget](https://www.daxpatterns.com/budget/)

This pattern includes several coding techniques you may find useful for budgeting scenarios. The techniques do not apply only to budgeting. We use the budget as an example to show how to reallocate a measure at a different granularity, and … [Read more](https://www.daxpatterns.com/budget/)

# [Survey](https://www.daxpatterns.com/survey/)

The Survey pattern uses a data model to analyze correlations between different events related to the same entity, such as customer answers to survey questions. For example, in healthcare organizations the Survey pattern can be used to analyze data about … [Read more](https://www.daxpatterns.com/survey/)

# [Basket analysis](https://www.daxpatterns.com/basket-analysis/)

The Basket analysis pattern builds on a specific application of the Survey pattern. The goal of Basket analysis is to analyze relationships between events. A typical example is to analyze which products are frequently purchased together. This means they are … [Read more](https://www.daxpatterns.com/basket-analysis/)

# [New and returning customers](https://www.daxpatterns.com/new-and-returning-customers/)

The New and returning customers pattern helps in understanding how many customers in a period are new, returning, lost, or recovered. There are several variations to this pattern, each with different performance and results depending on the requirements. Moreover, it … [Read more](https://www.daxpatterns.com/new-and-returning-customers/)

# [Currency conversion](https://www.daxpatterns.com/currency-conversion/)

Currency conversion is a complex scenario where both the data model and the quality of the DAX code play an important role. There are two kinds of currencies: the currency used to collect orders and the currency used to produce … [Read more](https://www.daxpatterns.com/currency-conversion/)

# [Hierarchies](https://www.daxpatterns.com/hierarchies/)

Hierarchies are often created in data models to simplify the browsing of the model by providing users with suggested paths of navigation through attributes. The definition of the hierarchies follows the requirements of the model. For example, the Date table … [Read more](https://www.daxpatterns.com/hierarchies/)

# [Parent-child hierarchies](https://www.daxpatterns.com/parent-child-hierarchies/)

Parent-child hierarchies are often used to represent charts of accounts, stores, salespersons and such. Parent-child hierarchies have a peculiar way of storing the hierarchy in the sense that they have a variable depth. In this pattern we show how to … [Read more](https://www.daxpatterns.com/parent-child-hierarchies/)

# [Events in progress](https://www.daxpatterns.com/events-in-progress/)

The Events in Progress pattern has a broad field of application. It is useful whenever dealing with events with a duration – events that have a start date and an end date. The event is considered to be in progress … [Read more](https://www.daxpatterns.com/events-in-progress/)

# [Like-for-like comparison](https://www.daxpatterns.com/like-for-like-comparison/)

The like-for-like sales comparison is an adjusted metric that compares two time periods, restricting the comparison to products or stores with the same characteristics. In this example, we use the like-for-like technique to compare the sales of Contoso stores that … [Read more](https://www.daxpatterns.com/like-for-like-comparison/)

# [Transition matrix](https://www.daxpatterns.com/transition-matrix/)

The Transition matrix pattern analyzes changes in an attribute assigned to an entity at regular intervals. For example, customers might receive a ranking evaluation every month, or products might have a rating score measured every week. Measuring the changes in … [Read more](https://www.daxpatterns.com/transition-matrix/)

# [Ranking](https://www.daxpatterns.com/ranking/)

The ability to rank things is a very common requirement. Finding the best customers, computing the ranking position of products, or detecting the countries with the best sales volumes are among the questions most frequently asked by management.
