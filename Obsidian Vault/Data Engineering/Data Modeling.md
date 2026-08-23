Ralph Kimball introduced the data warehouse/business intelligence industry to dimensional modeling in 1996 with his seminal book, _The Data Warehouse Toolkit_.  Since then, the Kimball Group has extended the portfolio of best practices.

Drawn from [_The Data Warehouse Toolkit, Third Edition_](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/books/data-warehouse-dw-toolkit/), the “official” Kimball dimensional modeling techniques are described on the following links and [attached .pdf](http://www.kimballgroup.com/wp-content/uploads/2013/08/2013.09-Kimball-Dimensional-Modeling-Techniques11.pdf):

## Fundamental Concepts

- [Gather business requirements and data realities](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/business-requirements-data-realities "Gather Business Requirements and Data Realities")
- [Collaborative dimensional modeling workshops](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/collaborative-modeling-workshop "Collaborative Dimensional Modeling Workshops")
- [Four step dimensional design process](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/four-4-step-design-process "Four-Step Dimensional Design Process")
- [Business processes](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/business-process "Business Processes")
- [Grain](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/grain/ "Grain")
- [Dimensions for descriptive context](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/dimensions-for-context "Dimensions for Descriptive Context")
- [Facts for measurements](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/facts-for-measurement "Facts for Measurements")
- [Star schemas and OLAP cubes](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/star-schema-olap-cube/ "Star Schemas and OLAP cubes")
- [Graceful extensions to dimensional models](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/extensions "Grace Extensions to Dimensional Modeling")    
## Basic Fact Table Techniques

- [Fact table structure](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/fact-table-structure "Fact Table Structure")
- [Additive, semi-additive, and non-additive facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/additive-semi-additive-non-additive-fact "Additive, Semi-Additive, and Non-Additive Facts")
- [Nulls in fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/fact-table-null "Nulls in Fact Tables")
- [Conformed facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/conformed-fact "Conformed Facts")
- [Transaction fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/transaction-fact-table "Transaction Fact Tables")
- [Periodic snapshot fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/periodic-snapshot-fact-table "Periodic Snapshot Fact Tables")
- [Accumulating snapshot fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/accumulating-snapshot-fact-table "Accumulating Snapshot Fact Tables")
- [Factless fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/factless-fact-table "Factless Fact Tables")
- [Aggregated fact tables or cubes](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/aggregate-fact-table-cube "Aggregate Fact Tables or Cubes")
- [Consolidated fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/consolidated-fact-table "Consolidated Fact Tables")

## Basic Dimension Table Techniques

- [Dimension table structure](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/dimension-table-structure "Dimension Table Structure")
- [Dimension surrogate keys](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/dimension-surrogate-key "Dimension Surrogate Keys")
- [Natural, durable and supernatural keys](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/natural-durable-supernatural-key "Natural, Durable, and Supernatural Keys")
- [Drilling down](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/drilling-down "Drilling Down")
- [Degenerate dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/degenerate-dimension "Degenerate Dimensions")
- [Denormalized flattened dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/denormalized-flattened-dimension "Denormalized Flattened Dimensions")
- [Multiple hierarchies in dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/multiple-hierarchies "Multiple Hierarchies in Dimensions")
- [Flags and indicators as dimension attributes](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/flag-indicator-attribute "Flags and Indicators as Textual Dimension Attributes")
- [Null attributes in dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/null-dimension-attribute "Null Attributes in Dimensions")
- [Calendar date dimension](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/calendar-date-dimension "Calendar Date Dimensions")
- [Role playing dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/role-playing-dimension "Role-Playing Dimensions")
- [Junk dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/junk-dimension "Junk Dimensions")
- [Snowflaked dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/snowflake-dimension "Snowflaked Dimensions")
- [Outrigger dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/outrigger-dimension "Outrigger Dimensions")

## Integration via Conformed Dimensions

- [Conformed dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/conformed-dimension "Conformed Dimensions")
- [Shrunken rollup dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/shrunken-rollup-dimension "Shrunken Rollup Dimensions")
- [Drilling across](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/drilling-across "Drilling Across")
- [Value chain](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/value-chain "Value Chain")
- [Enterprise data warehouse bus architecture](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/enterprise-data-warehouse-bus-architecture "Enterprise Data Warehouse Bus Architecture")
- [Enterprise data warehouse bus matrix](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/enterprise-data-warehouse-bus-matrix "Enterprise Data Warehouse Bus Matrix")
- [Opportunity/stakeholder matrix](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/opportunity-stakeholder-matrix "Opportunity/Stakeholder Matrix")

## Slowly Changing Dimension Techniques

- [Type 0: Retain original](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-0 "Type 0: Retain Original")
- [Type 1: Overwrite](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-1 "Type 1: Overwrite")
- [Type 2: Add new row](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-2 "Type 2: Add New Row")
- [Type 3: Add new attribute](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-3 "Type 3: Add New Attribute")
- [Type 4: Add mini-dimension](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-4-mini-dimension "Type 4: Add Mini-Dimension")
- [Type 5: Add mini-dimension and Type 1 outrigger](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-5 "Type 5: Add Mini-Dimension and Type 1 Outrigger")
- [Type 6: Add Type 1 attributes to Type 2 dimension](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-6 "Type 6: Add Type 1 Attributes to Type 2 Dimension")
- [Type 7: Dual Type 1 and Type 2 dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/type-7 "Type 7: Dual Type 1 and Type 2 Dimensions")

## Dimension Hierarchy Techniques

- [Fixed depth positional hierarchies](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/fixed-depth-hierarchy "Fixed Depth Positional Hierarchies")
- [Slightly ragged/variable depth hierarchies](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/slightly-ragged-variable-depth-hierarchy "Slightly Ragged/Variable Depth Hierarchies")
- [Ragged/variable depth hierarchies](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/ragged-variable-depth-hierarchy "Ragged/Variable Depth Hierarchies")

## Advanced Fact Table Techniques

- [Fact table surrogate key](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/fact-surrogate-key "Fact Table Surrogate Keys")
- [Centipede fact table](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/centipede-fact-table "Centipede Fact Tables")
- [Numeric values as attributes or facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/numeric%20-attribute-fact "Numeric Values as Attributes or Facts")
- [Lag/duration facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/lag-duration-fact "Lag/Duration Facts")
- [Header/line fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/header-line-fact-table "Header/Line Fact Tables")
- [Allocated facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/allocated-fact "Allocated Facts")
- [Profit and loss fact tables using allocations](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/profit-loss-fact-table "Profit and Loss Fact Tables Using Allocations")
- [Multiple currency facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/multiple-currencies "Multiple Currency Facts")
- [Multiple units of measure](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/multiple-units-of-measure "Multiple Units of Measure Facts")
- [Year-to-date facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/year-to-date-fact "Year-to-Date Facts")
- [Multipass SQL to avoid fact-to-fact table joins](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/multipass-sql "Multipass SQL to Avoid Fact-to-Fact Table Joins")
- [Timespan tracking in fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/timespan-fact-table "Timespan Tracking in Fact Tables")
- [Late arriving facts](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/late-arriving-fact "Late Arriving Facts")

## Advanced Dimension Table Techniques

- [Dimension-to-dimension table joins](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/dimension-to-dimension-join "Dimension-to-Dimension Table Joins")
- [Multivalued dimensions and bridge tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/multivalued-dimension-bridge-table "Multivalued Dimensions and Bridge Tables")
- [Behavior tag time series](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/behavior-tag-series-attribute "Behavior Tag Time Series")
- [Behavior study group](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/behavior-study-group "Behavior Study Groups")
- [Aggregated facts as dimension attributes](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/aggregated-fact-attribute "Aggregated Facts as Dimension Attributes")
- [Dynamic value banding](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/dynamic-value-banding "Dynamic Value Banding")
- [Text comments](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/text-comment "Text Comments")
- [Multiple time zones](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/multiple-time-zones "Multiple Time Zones")
- [Measure type dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/measure-type-dimension "Measure Type Dimensions")
- [Step dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/step-dimension "Step Dimensions")
- [Hot swappable dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/hot-swappable-dimension "Hot Swappable Dimensions")
- [Abstract generic dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/abstract-generic-dimension "Abstract Generic Dimensions")
- [Audit dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/audit-dimension "Audit Dimensions")
- [Late arriving dimensions](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/late-arriving-dimension "Late Arriving Dimensions")

## Special Purpose Schemas

- [Supertype and subtype schemas for heterogeneous products](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/supertype-subtype-heterogeneous "Supertype and Subtype Schemas for Heterogeneous Products")
- [Real-time fact tables](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/realtime-fact-table "Real-Time Fact Tables")
- [Error event schemas](http://www.kimballgroup.com/data-warehouse-business-intelligence-resources/kimball-techniques/dimensional-modeling-techniques/error-event-schema "Error Event Schemas")

Here’s the .pdf version of [Kimball Dimensional Modeling Techniques](http://www.kimballgroup.com/wp-content/uploads/2013/08/2013.09-Kimball-Dimensional-Modeling-Techniques11.pdf).