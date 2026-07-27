---
base: "[[Reading List.base]]"
Rating: ⭐️
Category:
  - Data Warehousing
  - Data Governance
Author: Bill Inmon
Status: Completed
---
## Chapter 8 - External Data

One problem of external data is the **frequency of availability**. Unlike internally appearing data, there is no real fixed pattern of appearance for external data.

Attempting to use the data model for any serious reshaping of the external data is a **mistake**. The most that can be done is to create subsets of the data that are compatible with the existing internal data.

## Chapter 9 - Migration to the EDW

One observation worthwhile at this point relates to the frequency of refreshment of data into the data warehouse. As a rule, data warehouse data should be refreshed no more frequently than every 24 hours. By making sure that there is at least a 24-hour time delay in the loading of data, the data warehouse developer minimizes the temptation to turn the data warehouse into an operational environment.

But there are cases where rapidly placing data in the warehouse may be what the requirements are. In this case, it helps to have technology suited for what is termed active data warehousing. Active data warehousing refers to the technology of being able to support some small amount of online access processing in the data warehouse.

One approach — which is on a track independent of the migration to the data warehouse environment — is to use the data model as a guideline and make a case to management that major changes need to be made to the operational environment. The industry track record of this approach is dismal. The amount of effort, the amount of resources, and the disruption to the end user in undertaking a massive rewrite and restructuring of operational data and systems is such that management seldom supports such an effort with the needed level of commitment and resources.

A better ploy is to coordinate the effort to rebuild operational systems with what are termed the “agents of change”:

■■ The aging of systems
■■ The radical changing of technology
■■ Organizational upheaval
■■ Massive business changes

When management faces the effects of the agents of change, there is no question that changes will have to be made — the only question is how soon and at what expense. The data architect allies the agents of change with the notion of an architecture and presents management with an irresistible argument for the purpose of restructuring operational processing.

## Chapter 10 - DW and the Web

### Web to the DW

The Web interacts with corporate systems another way as well — through the collection of Web activity in a log. The Web log contains what is typically called clickstream data. Each time the Internet user clicks to move to a different location, a clickstream record is created. As the user looks at different corporate products, a record of what the user has looked at, what the user has purchased, and what the user has thought about purchasing is compiled.

Web-generated data is at a very low level of detail — in fact, so low that it is not fit for either analysis or entry into the data warehouse. To make the clickstream data useful for analysis and the warehouse, the log data must be read and refined.

Web log clickstream data is passed through software that is called a Granularity Manager (GM) before entry into the data warehouse environment. A lot of processing occurs in the Granularity Manager, which reads clickstream data and does the following:
■■ Edits out extraneous data

■■ Creates a single record out of multiple, related clickstream log records

■■ Edits out incorrect data

■■ Converts data that is unique to the Web environment, especially key data that needs to be used in the integration with other corporate data

■■ Summarizes data

■■ Aggregates data

As a rule of thumb, about 90 percent of raw clickstream data is discarded or summarized as it passes through the Granularity Manager.

### DW to the Web

Suppose the Web site is dedicated to selling cars. The
analyst would really like to know who has purchased the brand of car the com-
pany is selling. Where is the historical information of this variety found? In the
data warehouse, of course.

The data warehouse then provides a foundation of integrated historical
information that is available to the business analyst. Data passes out of the data warehouse into the corporate operational data store (ODS), where it is then available for direct access from the Web.

At first glance, it may seem odd that the ODS sits between the data warehouse and the Web. There are some very good reasons for this positioning. The ODS is a hybrid structure that has some aspects of a data warehouse and other aspects of an operational system. The ODS contains integrated data and can support DSS processing. But the ODS can also support high-performance transaction processing. It is this last characteristic of the ODS that makes it so valuable to the Web. When a Web site accesses the ODS, the Web environment knows that it will receive a reply in a matter of milliseconds. This speedy response time makes it possible for the Web to perform true transaction processing. If the Web were to directly access the data warehouse, it could take minutes to receive a reply from the warehouse.

### Operational Data Store (ODS)

At first glance, it may seem that there is a lot of redundant data between the
data warehouse and the ODS. After all, the ODS is fed from the data ware-
house.

The ODS is full of interpretive data. Data has been read in the data warehouse, analyzed, and turned into “profile” data, or profile records. The profile records reside in the ODS.

The profile record contains all sorts of information that is created as a result of reading and interpreting the transaction data.

The detailed integrated historical transaction data is read and analyzed in order to produce the profile record. The analysis is done periodically. The analytical program is both interpretive and predictive. Based on the past activity of the customer and any other information that the analytical program can get, the analytical program assimilates the information to produce a very personal projection of the customer. The projection is predictive as well as factual.

## Chapter 11 - Unstructured Data

*Text *forms the most basic substance of what is found in the **unstructured environment**. The world of **structured data** is one that is dominated by *numbers*. The unstructured environment represents *documents *and *communications*. The structured environment represents *transactions*.

In a **probabilistic match**, as much data that might be used to indicate the “Bob Smith” that you’re looking for is gathered and is used as a basis for a match against similar data found where other “Bob Smiths” are located. Then, all the data that intersects is used to determine if a match on the name is valid.

Close identifiers are identifiers where there is a good probability that a solid
identification has been made. Close identifiers include names, addresses, and
other identifying data. The difference between an identifier and a close identi-
fier is the sureness of the identification. When a person has been identified by
Social Security number, there is a very high probability that the person is who
he or she really is. But close identifiers are not the same.

The unstructured environment is divided into two basic categories — documents and communications. In the documents category are found document-identifying information such as title, author, data of document, and location of document. Also found are the first n bytes of the document. Context, prefix, word, and suffix are found. There are also keywords. The keyword can be related to the document by means of being a part of a theme or by coming from an industrially recognized list of words.
The communications part of unstructured data is similar to the document part except that the communications part contains identifiers and close identifiers. But other than that difference, the information about communications is the same.

Usually, identifiers from the structured environment can be matched with identifiers from the unstructured environment. Close identifiers from the structured environment can probabilistically be matched with close identifiers from the unstructured environment. Keywords can be matched from the unstructured environment with either metadata or repository data from the structured environment.

### Themes

One way to organize the unstructured data is by **industrially recognized themes**.
In this approach, the unstructured data is analyzed according to the existence
of words that relate to industrialized themes. A set of documents that has a strong orientation against accounting will have many “hits” against the industrially recognized list of words, as opposed to a document that does not have such strong orientation to accounting.

One way to relate the themed data found in the unstructured environment to the data found in the structured environment is through a raw match of data. In a raw match of data, if a word is found anywhere in the structured environment and the word is part of the theme of a document, the unstructured document is linked to the structured record. But such a matching is not very meaningful and may actually be misleading.

Another way to link the two environments is by the **metadata **found in the structured environment.

### Two-tiered DW

There are two basic approaches to the usage of unstructured data in the data warehouse environment. One approach is to access the unstructured environment and pull data over into the structured environment. This approach works well for certain kinds of unstructured data. Another approach to unstructured data and the data warehouse environment is to create a two-tiered data warehouse. One tier of the data warehouse is for unstructured data and another tier of the data warehouse is for structured data.

There may be either a tight or a casual relationship of data
between the two environments, or there may be no relationship at all. There is
nothing implied about the data in that regard.

Data in the unstructured data warehouse is
divided into one of the two following categories:
■■ Unstructured communications
■■ Documents and libraries

As a rule, documents are much larger than communications. And documents are intended for a much wider audience than the communications. A third difference between documents and communications is that documents have a much longer useful life than communications. Documents are grouped into libraries. A library is merely a collection of documents, all of which relate to some subject.

Depending on quite a few variables, it may be desirable to store the actual document in the unstructured data warehouse, or it may make sense to store only references to the location of the document in the data warehouse. An intermediate solution between having the document in storage or out of storage is storing the sentence before and after where the themed word lies.

### Visualization

Visualization can also be done for textual-based data. Textual-based data forms the foundation of unstructured technology. A commercial example of unstructured visualization is Compudigm. To create a textual visualization, documents and words are collected. Then the words are edited and prepared for display. The words are then fed to a display engine where they are analyzed, clustered, and otherwise prepared for visualization.

The result is a self-organizing map (SOM). The SOM produces a display that
appears to be a topographical map. The SOM shows how different words and
the documents are clustered, and displayed according to themes.

## Chapter 12 - Big DWH

*Historical data * Detailed data * Diverse data = Lots of data*

The cost of storage is not the megabyte cost. Instead, the cost of storage is
about the infrastructure surrounding the data. There are lots of components to disk storage aside from the storage device itself. There is the disk controller. There are the communications lines. There is the processor (or processors) that are required to control the usage of the data. Then there is the software that is required to make the processor operate properly. There is data base software, operating system software, and business intelligence software, to name a few types of software. All of these components go up in cost when your volume of data increases.

When an organization has only 50GB of storage in its data warehouse, it is a
good bet that all or most of all of the data found in the data warehouse is being
used. Most queries can afford to access all the data in the data warehouse as
needed. But, as the volume of data in the data warehouse grows, that basic
practice ceases to be a possibility.

In a large data warehouse, data is either frequently used or infrequently used. Infrequently used data is often called **dormant data **or **inactive data**. Frequently used data is often called **actively used data**.

OLTP data is accessed randomly and there is roughly an equal probability of access for each unit of data in the OLTP environment. For this pattern of random access, disk storage is ideal. In the data warehouse DSS environment, there is not a
random pattern of access, as was seen in the OLTP environment. In truth, most
DSS processing is qualified by date. The fresher the data, the more likely it is
to be used. **The older the data, the less likely it is that it will be accessed.**

Because of this fact, it makes sense to split the data in a data warehouse over multiple forms of storage. Actively used data goes on high-performance storage and inactively used data goes on bulk-storage media. Additionally, placing all of your data in a data warehouse where the data warehouse is large on disk storage **is slower **than placing your data on split-storage media.

Despite the similarities between the near-line environment and the archival
environment, there are some significant differences as well. One significant
difference is that of the notion of the data being extended from the data ware-
house.

Logically speaking, the **near-line storage **environment is seen to be merely an **extension **of the data warehouse. Indeed, in some cases the location of the data may be transparent to the end user. In this case, when the end user makes a query, he or she does not know whether the data is located in high-performance storage or the data is located in near-line storage. However when the data is in **archival storage**, the end user always knows that the data is not in high performance storage. This is what is meant by the location of the data being **transparent **in near-line storage and **not **in archival storage.

When alternate forms of storage are entered into the equation, it is possible to “invert the data warehouse.” So what is an **inverted data warehouse**? With a data warehouse inversion, there is the possibility of managing any amount of data. The alternative is to first enter data into near-line storage, not disk storage. Then, when a query is done, the data is “staged” from the near-line environment to the disk environment. Once in the disk environment, the data is accessed and analyzed as if the data resided there permanently. Once the analysis is over, the data is returned to near-line storage.

There is another way to look at the management of very large amounts of data: as the data grows, so grows the budget required for the data warehouse. But with the introduction of near-line and archival storage, the growing costs of a data warehouse can be mitigated.

## Chapter 13 - Relational and Dimensional Design

There are two basic models for database design that are widely considered — the **relational model **and the **multidimensional model**. The relational model is widely considered to be the “Inmon” approach, while the multidimensional model is considered to be the “Kimball” approach to design for the data warehouse. 

The relational foundation for database design is the best long-term approach for the building of the data warehouse and for the case where a true enterprise approach is needed. The multidimensional model is good for short-term data warehouses, where there is a limited scope for the data warehouse.

### The Multidimensional Model

The multidimensional approach is also sometimes called the **star join approach**. The multidimensional approach has been championed by Dr. Ralph **Kimball**.

The star join data structure is so-called because its representation depicts a “star” with a center and several outlying structures of data. A **fact table **is a structure that contains many occurrences of data. Surrounding the fact table are **dimensions**, which describe one important aspect of the fact table. There are fewer number of occurrences of a dimension table than the number
of occurrences of fact tables.

As a rule, in a star join there is one fact table. But more than one fact table can be combined in a database design to create a composite structure called a **snowflake structure**. In a snowflake structure, different fact tables are connected by means of sharing one or more common dimensions. Sometimes these shared dimensions are called *conformed dimensions*.
The great advantage of the multidimensional design is its efficiency of access. When designed properly, the star join is very efficient in delivering data to the end user. To make the delivery of information efficient, the end-user requirements must be gathered and assimilated. The processes the end **user has that will use the data **are **at the** **heart of defining what the multidimensional structure will look like**. Once the end-user requirements are understood, the end-user requirements are used to shape the star join into its final, most optimal structure.

### Differences between Models

The single most important difference is in terms of **flexibility **and **performance**. The relational model is highly flexible, but is not optimized for performance for any user. The multidimensional model is highly efficient at servicing the needs of one user community, but it is not good at flexibility.

Another important difference in the two approaches to database design is in the scope of the design. Of necessity, the multidimensional design has a limited scope. Since processing requirements are used to shape the model, the design starts to break down when many processing requirements are gathered. In other words, the database design can be optimized for one and only one set of processing requirements. 
When the relational model is used, there is no particular optimization for performance one way or the other. Since the relational model calls for data to be stored at the lowest level of granularity, new elements of data can be added ad infinitum. 

For this reason **the relational model is appropriate to a very large scope of data **(such as an enterprise model), while **the multidimensional model is appropriate to a small scope of data **(such as a department or even a sub-department).

The roots of the differences between the multidimensional model and the relational model go back to the original shaping of the models themselves. The relational environment is shaped by the corporate or enterprise data model. The star join or multidimensional model is shaped by the end-user requirements.

Another advantage of using the relational model as the foundation for a data warehouse is its ability to **handle change gracefully**. The relational model is designed to be accessed **indirectly**. In other words, users don’t interact directly with the underlying relational tables. Instead, they access data **derived from** the relational model — such as through views, reports, or data marts. This separation means that when changes are made to the underlying data model, the impact on users is minimal. Different users often access **different tables **of the data, so changes in one part of the system don’t necessarily disrupt others.

The ability to change gracefully is not one of the characteristics of the star join, multidimensional approach to database design. The design of a multidimensional database is fragile and is a result of many processing requirements taken together. Once the data has been set in the form of a multidimensional database, change is not easy.

**The relational model forms an ideal basis for the data warehouse, while the star join forms the ideal basis for the data mart**.

### Data Marts

A **data mart **is a data structure that is dedicated to serving the analytical needs of one group of people, such as the accounting department or the finance department.

Another aspect of the multidimensional model is the alignment of the multidimensional model with what is referred to as the **independent data mart approach**. The independent data mart is a data mart that is built directly from the legacy applications. The architectural counterpoint to the independent data mart is the **dependent data mart**.

Independent data marts are appealing because they appear to be a direct solution to solving the information problem. An independent data mart can be created by a single department with no consideration to other departments or without any consideration to a centralized IT organization. There is no need to “think globally” when building an independent data mart. The independent data represents a subset of the entire DSS requirements for an organization. An independent data mart is a relatively inexpensive thing to build, and it allows an organization to take its own information destiny in its own hands.

The opposite of an independent data mart is a dependent data mart. A dependent data mart is one that is built from data coming from the data warehouse. The dependent data mart does not depend on legacy or operational data for its source. It depends on only the data warehouse for its source data. The dependent data mart requires forethought and investment. It requires that someone “think globally.” The dependent data mart requires multiple users to pool their information needs for the creation of the data warehouse. In other words, the dependent data mart requires advance planning, a long-term perspective, global analysis, and cooperation and coordination of the definition of requirements among different departments of an organization.

After a number of independent data marts have been built, the issues of what is wrong with the independent data mart approach becomes apparent. They result in growing redundancy of detailed data, and inconsistencies across departments. There is no foundation on which to build and there is no historical data to access. All integration must be redone (one more time), and there is no coordination with any previous efforts at integration.  Independent data marts are good for short, fast solutions, but when the longer-term perspective is taken, independent data marts are simply inadequate to solve the problems of information across the organization.

## Chapter 14 - Advanced Topics

### Resource Contention

What happens to a machine when a heavy amount of statistical analysis hits it (such as when an **explorer **makes a request)? What happens to normal data warehouse processing (**farmer**) when such a request is received? Under normal conditions, normal processing comes to a dead halt. And what happens when normal processing comes to a dead halt? End users become dissatisfied quickly. In the cases where contention for resources becomes an issue, it is wise to investigate a special form of a data warehouse. That form of a data warehouse is called an **exploration warehouse **or a *data mining warehouse.*

The purpose of an exploration warehouse is to provide a foundation for heavy statistical analysis. Once the exploration warehouse has been built, there will be no more contention problems with the data warehouse. Statistical analyses can be run all day long and there will be no contention issues because the processing that is occurring is happening on separate machines. The statistical processing occurs one place and the regular data warehouse processing occurs elsewhere.

The exploration warehouse is seldom a direct copy of the data found in the data warehouse. Instead, the exploration warehouse starts with a subset of the data found in the data warehouse. Then the data that is taken from the data warehouse is recast. A typical recast of the data warehouse data is the creation of what can be termed “convenience” fields. A convenience field is one that is created for the purpose of streamlining statistical analysis.

Exploration warehouses are usually focused on projects. This means that when the results of the project are obtained, the exploration warehouse goes away.

### Data Mining Warehouse

One difference between the exploration warehouse and the data mining warehouse then is this: The exploration warehouse is optimized on breadth while the data mining warehouse is optimized on depth. The exploration warehouse must have a wide diversity of data types to provide a breadth of information. The data mining warehouse needs to have depth. It needs to have on-hand as many occurrences as possible of whatever is being analyzed.

The primary purpose for an exploration warehouse is the creation of assertions, hypotheses, and observations. A data mining warehouse has the purpose of proving the strength of the truth of the hypotheses.

Because the differences between an exploration warehouse and a data mining warehouse are so subtle, it is only very sophisticated companies that make this distinction. In most companies, the exploration warehouse serves both exploration and mining purposes.

### Freezing the Exploration Warehouse

There is one characteristic of an exploration warehouse that is entirely different from a data warehouse: The exploration warehouse on occasion cannot be refreshed with the most current detailed data.

To achieve a precise analysis, it is necessary to hold the data that is being analyzed constant, while the algorithms being tested change when doing heuristic analysis. If the data changes, there can be no certainty about the changes in results that are obtained. Therefore, on occasion it is important to not refresh new and current data into the exploration warehouse.

On Day 1, an analysis is done showing that women spend $25.00 per month on shoes. The next day, fresh data is fed into the exploration warehouse. Another analysis is done showing that women under 40 spend $20.00 per month on new shoes. Now the question is asked: Is the difference in the analysis caused by a change in the calculations and selection of data, or is the change in analysis somehow a byproduct of the data that has just been fed into the exploration warehouse? The answer is — no one knows.

### External Data

One other major difference exists between the data warehouse and the explo-
ration warehouse: External data fits nicely into the exploration warehouse.

Often it makes sense to use external data in the exploration warehouse. In many cases, comparing external results versus internal results yields very interesting insights. For example, if you see that corporate revenue has fallen, then that may be taken as a bad omen. But if it happens that industry revenue has fallen in the same period at a steeper rate, then things don’t look nearly so bleak. So, contrasting internally generated numbers against externally generated numbers is often very enlightening.

## Chapter 16 - The ODS

A data warehouse can never be accessed on a millisecond basis. Because of the nature of the data, the volume of the data, and the mixed workload that uses data from the data warehouse, the data warehouse is not geared to support OLTP types of processes. Guaranteed subsecond response for an access to the data warehouse is architecturally not a viable option. But subsecond response time is, in fact, very valuable in many operations.
Many businesses require very fast response time, and those businesses cannot have access to the data warehouse. When subsecond response time is required and integrated DSS data must be accessed, there is a structure known as an operational data store (ODS) that is the place to go to when high-performance processing must be done.

Often the ODS stores records in what are termed “profile records.” A profile
record is a record made from many observations about a customer. A profile
record is made from looking at and creating a synopsis of lots of occurrences
of data.  The profile record in the ODS is constructed by looking at many, many detailed historical records contained in the data warehouse. The value of the ODS profile record is that it can be accessed very quickly. There is no need to go and look at hundreds of detailed historical records

The ODS is designed in a hybrid manner. A part of an ODS design is relational and another part of the ODS design is multidimensional. The relational portion of the ODS database design is the portion where flexibility is the most important parameter. The multidimensional portion of the ODS design is the part where performance is the most important parameter.
