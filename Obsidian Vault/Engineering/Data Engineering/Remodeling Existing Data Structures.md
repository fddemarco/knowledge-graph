[[Dimensional Modeling]]
[[Book - The Data Warehouse Toolkit]]

## Adding a new attribute

Adding a new attribute to an existing dimension table feels like a minor enhancement. It is nearly pain-free if the business data stewards declare it to be a **slowly changing dimension type 1** attribute. Likewise if the attribute is to be populated starting now with no attempt to **backfill** historically accurate values beyond a Not Available attribute value; note that while this tactic is relatively easy to implement, it presents analytic challenges and may be deemed unacceptable.

If the new attribute is a designated **type 2** attribute with the requirement to capture historical changes, this seemingly simple enhancement just got much more complicated. In this scenario, **rows need to be added** to the dimension table to capture the historical changes in the attribute, along with the other dimension attribute changes. Some **fact table rows** then need to be recast so the appropriate dimension table row is associated with the fact table’s event. This most robust approach consumes surprisingly more effort than you might initially imagine.

