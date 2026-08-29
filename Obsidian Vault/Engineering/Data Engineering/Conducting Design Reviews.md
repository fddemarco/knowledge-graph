[[Dimensional Modeling]]
[[Book - The Data Warehouse Toolkit]]

## General Considerations

Dimensional models should be designed based on a blended understanding of the business’s needs, along with the operational source system’s data realities. While requirements are collected from the business users, the underlying source data should be profiled. Models driven solely by **requirements** inevitably include data elements that can’t be sourced. Meanwhile, models driven solely by **source system** data analysis inevitably omit data elements that are critical to the business’s analytics.

Dimensional models should be designed to mirror an organization’s primary business process events. Dimensional models should not be designed solely to deliver specific reports or answer specific questions. After the base processes have been built, it may be useful to design complementary schemas, such as summary aggregations, accumulating snapshots that look across a workflow of processes, consolidated fact tables that combine facts from multiple processes to a common granularity, or subset fact tables that provide access to a limited subset of fact data for security or data distribution purposes.

### Granularity

The first question to always ask during a design review is, “**What’s the grain of the fact table**?” Surprisingly, you often get inconsistent answers to this inquiry from a design team. Declaring a clear and concise definition of the grain of the fact table is critical to a productive modeling effort. The project team and business liaisons must share a common understanding of this grain declaration; without this agreement, the design effort will spin in circles. Fact tables should be built at the **lowest level of granularity possible** for maximum flexibility and extensibility, especially given the unpredictable filtering and grouping required by business user queries.

### Single Granularity of Facts

After the fact table granularity has been established, facts should be identified that are **consistent with the grain declaration**. To improve performance or reduce query complexity, aggregated facts such as year-to-date totals sometimes sneak into the fact row. These totals are dangerous because they are not perfectly additive. It is important that once the grain of a fact table is chosen, all the additive facts are presented at a uniform grain.

You should prohibit **text fields**, including cryptic **indicators** and **flags**, from the fact table. They almost always take up more space in the fact table than a surrogate key. More important, business users generally want to query, constrain, and report against these text fields. You can provide quicker responses and more flexible access by handling these textual values in a dimension table, along with descriptive rollup attributes associated with the flags and indicators.

### Dimension Granularity and Hierarchies

Each of the dimensions associated with a fact table should take on a single value with each row of fact table measurements. Likewise, each of the dimension attributes should take on one value for a given dimension row. If the attributes have a many-to-one relationship, this hierarchical relationship can be represented within a single dimension. You should generally look for opportunities to collapse or **denormalize** dimension hierarchies whenever possible.

Sometimes designers attempt to deal with dimension hierarchies within the fact table. For example, rather than having a single foreign key to the product dimension, they include separate foreign keys for the key elements in the product hierarchy, such as brand and category. Before you know it, a compact fact table turns into an unruly centipede fact table joining to dozens of dimension tables. If the fact table has more than 20 or so foreign keys, you should look for opportunities to combine or collapse dimensions.

### Degenerate Dimensions

Rather than treating operational transaction numbers such as the invoice or order number as degenerate dimensions, teams sometimes want to create a separate dimension table for the transaction number. In this case, attributes of the transaction number dimension include elements from the transaction header record, such as the transaction date and customer.

Remember, transaction numbers are best treated as **degenerate dimensions**. The transaction date and customer should be captured as foreign keys on the fact table, not as attributes in a transaction dimension. Be on the lookout for a dimension table that has as many (or nearly as many) rows as the fact table; this is a warning sign that there may be a degenerate dimension lurking within a dimension table.

### Surrogate Keys

Instead of relying on operational keys or identifiers, we recommend the use of surrogate keys as the dimension tables’ primary keys. The only permissible deviation from this guideline applies to the highly predictable and stable date dimension.

### Dimension Decodes and Descriptions

All identifiers and codes in the dimension tables should be accompanied by descriptive decodes. This practice often seems counterintuitive to experienced data modelers who have historically tried to reduce data redundancies by relying on look-up codes. In the dimensional model, dimension attributes should be populated with the values that business users want to see on BI reports and application pull-down menus.

Project teams sometimes opt to embed labeling logic in the BI tool’s semantic layer rather than supporting it via dimension table attributes. Although some BI tools provide the ability to decode within the query or reporting application, we recommend that decodes be stored as data elements instead.

### Conforming Dimensions

Design teams must commit to using shared conformed dimensions across process-centric models. Conformed dimensions are absolutely critical to a robust data architecture that ensures consistency and integration. Without conformed dimensions, you inevitably perpetuate incompatible stovepipe views of performance across the organization.

## Design Review Session Tips

Designate someone to act as **scribe**. The scribe should take copious notes about both
the discussions and decisions being made.

Start with the **big picture**. Just as when you design from a blank slate, begin with the bus matrix. Focus on a single, high-priority business process, defi ne its granularity and then move out to the corresponding dimensions. Follow this same “peeling back the layers of the onion” method with a design review, starting with the fact table and then tackling dimension-related issues. But don’t defer the tough stuff to the afternoon of the second day.

Close the meeting with a **recap**. Don’t let participants leave the room with- out clear expectations about their assignments and due dates, along with an established time for the next follow-up.

Agree on the **review’s scope**. Ancillary topics will inevitably arise during the review, but agreeing in advance on the scope makes it easier to stay focused on the task at hand.

Assign homework. For example, ask everyone involved to make a list of their top five concerns, problem areas, or opportunities for improvement with the existing design. Encourage participants to use complete sentences when making their list so that it’s meaningful to others. These lists should be sent to the facilitator in advance of the design review for consolidation. Soliciting advance input gets people engaged and helps avoid “group think” during the review.

