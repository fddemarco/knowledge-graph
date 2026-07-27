---
base: "[[Reading List.base]]"
Rating: ⭐️
Category:
  - Data Architecture
Author: Bill Inmon
Status: Completed
---
## Chapter 5 - Generic Structure of the Data Pond

Each of the data ponds (other than the raw data pond) has some common components:

- Pond descriptor. The pond descriptor contains a description of the external
contents and manifestation of the pond, and where the data in the pond originated from.
- Pond target. The pond target is a description of the relationship between the business of the corporation and the data inside the pond.
- Pond data. The data in the pond is merely the physical data that resides inside the pond.
- Pond metadata. The metadata describes the physical characteristics of the data contained in the data pond.
- Pond metaprocess. Metaprocess information is information about the transformation / conditioning of the data inside the data pond. In order to be useful, data in the pond must undergo a transformation / conditioning process.
- Pond transformation criteria. Pond transformation criteria are documentation of how the transformation / conditioning of data inside the pond should occur.

POND DESCRIPTOR
The pond descriptor has information such as:

- Frequency of update or refreshment. The update frequency or refreshment refers to the cycle with which data is sent to the data pond and/or the frequency or refreshment cycle of data outside the pond. This can be a regularly scheduled movement of data or update / refreshment can be on an as needed basis.
- Source description. The source description describes the lineage of the data in the data pond. In many cases, the lineage of data will pass through more than one source. This lineage information is useful to the analyst in determining the fitness of data in the data pond for analysis.
- Volume of data. The volume of data is a general description of how much data is in the data pond. Data is measured both in terms of number of records and in number of bytes. The volume of data greatly influences the type and depth of analysis that can be done.
- Selection criteria. The selection criteria are a description of the criteria that were used to select the data for inclusion in the data pond. The selection criteria of data are important to the analyst in determining what data is in the pond and why it is there.
- Summarization criteria. Most of the time, data is summarized or otherwise processed as it passes into the data pond. The summarization is a description of the algorithms employed. In some cases, data is transformed in a different model than summarization. This is a description of the algorithmic processing used in the shaping of the data in the data pond. The summarization criteria are useful to the analyst in determining how to do analysis
- Organization criteria. Once the data is placed in the data pond, it is usually organized along the lines of the target of the pond. The target of the pond is similar to the data model of the business. The organization of data can be rigorous or casual, but in any case there is a description of exactly how the pond is organized. The description of the data organization is useful to the business analyst trying to make sense of the data pond.
- Data relationships. There normally are many data relationships among the data found in the pond. This is a description of those relationships. The data relationships are useful to the business analyst when it comes time to do business analysis.

POND TARGET

The pond target is the basic model that is used to shape the data in the data pond. The pond target can be as formal as a data model or can be as informal as a general description of the data found in the data pond. Typical pond target elements include such things as customer profile, sales record, shipment record, patient record, part number, inventory,

SKU, telephone call record, click stream activity, delivery information, insurance claim, professor name, class name, class schedule, flight schedule, flight manifest, passenger record, reservation record, and so forth. The pond target is the means by which a business relationship is made to the data in the data pond. The pond target is invaluable to the business analyst in planning how to conduct an analysis. There will then be, of necessity, a business relationship between the elements found in the target and the business itself
