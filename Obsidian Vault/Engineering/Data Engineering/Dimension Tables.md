[[Data Modeling]]
[[Book - The Data Warehouse Toolkit]] - Chapter 8 - CRM
## Conformed Dimensions

The essential requirement for two dimensions to be conformed is they share one or more specially administered attributes that have the same **column** **names** and **data values**. 

### Partial Conformity of Multiple Customer Dimensions

Enterprises today build customer knowledge stores that collect all the internal and external customer-facing data sources they can find. A large organization could have as many as 20 internal data sources and 50 or more external data sources, all of which relate in some way to the customer. These sources can vary wildly in granularity and consistency. Of course, there is no guaranteed high-quality customer key defined across all these data sources and no consistent attributes. You don’t have any control over these sources. It seems like a hopeless mess.

Instead of requiring dozens of customer-related dimensions to be identical, you only require they share the specially administered conformed attributes. Not only have you taken the pressure off the data warehouse by relaxing the requirement that all the customer dimensions in your environment be equal from top to bottom, but in addition you can proceed in an incremental and agile way to plant the specially administered conformed attributes in each of the customer-related dimensions.

For example, suppose you start by defining a fairly high-level categorization of customers. You can proceed methodically across all the customer-related dimensions, planting this attribute in each dimension without changing the grain of any target dimension and without invalidating any existing applications that depend on those dimensions. Over a period of time, you gradually increase the scope of integration as you add the special attributes to the separate customer dimensions attached to different sources. At any point in time, you can stop and perform drill-across reports using the dimensions where you have inserted the customer category attribute.