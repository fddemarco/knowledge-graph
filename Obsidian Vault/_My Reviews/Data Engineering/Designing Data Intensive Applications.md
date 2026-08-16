---
base: "[[Reading List.base]]"
Rating: ⭐️⭐️⭐️⭐️⭐️
Category:
  - System Design
  - Data Architecture
  - Data Engineering
  - Data Modeling
Author: Martin Kleppman
Status: Completed
---
Este es un buen libro de Bases de Datos. Es muy teorico, poco practico.

## Chapter 1 - Reliable, Scalable, and Maintainable Applications

A data-intensive application is typically built from standard building blocks that pro‐
vide commonly needed functionality. For example, many applications need to:
• Store data so that they, or another application, can find it again later (databases)
• Remember the result of an expensive operation, to speed up reads (caches)
• Allow users to search data by keyword or filter it in various ways (search indexes)
• Send a message to another process, to be handled asynchronously (stream pro‐
cessing)
• Periodically crunch a large amount of accumulated data (batch processing)

There are many factors that may influence the design of a data system, including the
**skills and experience **of the people involved, **legacy system **dependencies, the **time**‐
scale for delivery, your organization’s tolerance of different kinds of **risk**, **regulatory**
**constraints**, etc. Those factors depend very much on the situation.

### **Reliability**

**The system should continue to work correctly (performing the correct function at
the desired level of performance) even in the face of adversity (hardware or soft‐
ware faults, and even human error).**

The things that can go wrong are called **faults**, and systems that anticipate faults and
can cope with them are called fault-tolerant or **resilient**. The former term is slightly
misleading: it suggests that we could make a system tolerant of every possible kind of
fault, which in reality is not feasible.

Note that a fault is not the same as a **failure**. A fault is usually defined as one component of the system deviating from its spec, whereas a failure is when the system as a whole stops providing the required service to the user. It is impossible to reduce the probability of a fault to zero; therefore it is usually best to design fault-tolerance mechanisms that prevent faults from causing failures.

### **Scalability**

Even if a system is working reliably today, that doesn’t mean it will necessarily work reliably in the future. One common reason for degradation is increased load: perhaps the system has grown from 10,000 concurrent users to 100,000 concurrent users, or from 1 million to 10 million. Perhaps it is processing much larger volumes of data
than it did before.

**Scalability **is the term we use to describe **a system’s ability to cope with increased load**. Note, however, that it is not a one-dimensional label that we can attach to a system: it is meaningless to say “X is scalable” or “Y doesn’t scale.” Rather, discussing scalability means considering questions like “If the system grows in a particular way, what are our options for coping with the growth?” and “How can we add computing resources to handle the additional load?” As the system grows (in data volume, traffic volume, or complexity), there should be reasonable ways of dealing with that growth.

Once you have described the load on your system, you can investigate what happens
when the load increases. You can look at it in two ways:
• When you increase a load parameter and keep the system resources (CPU, mem‐
ory, network bandwidth, etc.) unchanged, how is the performance of your system
affected?
• When you increase a load parameter, how much do you need to increase the
resources if you want to keep performance unchanged?

People often talk of a dichotomy between scaling up (vertical scaling, moving to a
more powerful machine) and scaling out (horizontal scaling, distributing the load
across multiple smaller machines). Distributing load across multiple machines is also
known as a shared-nothing architecture. A system that can run on a single machine is
often simpler, but high-end machines can become very expensive, so very intensive
workloads often can’t avoid scaling out. In reality, good architectures usually involve
a pragmatic mixture of approaches: for example, using several fairly powerful
machines can still be simpler and cheaper than a large number of small virtual
machines.
Some systems are elastic, meaning that they can automatically add computing resources when they detect a load increase, whereas other systems are scaled manually. An elastic system can be useful if load is highly unpredictable, but manually scaled systems are simpler and may have fewer operational surprises

While distributing stateless services across multiple machines is fairly straightfor‐
ward, taking stateful data systems from a single node to a distributed setup can intro‐
duce a lot of additional complexity. For this reason, common wisdom until recently
was to keep your database on a single node (scale up) until scaling cost or high-
availability requirements forced you to make it distributed.

### **Maintainability**

Over time, many different people will work on the system (engineering and operations, both maintaining current behavior and adapting the system to new use
cases), and they should all be able to work on it productively.

we can and should design software in such a way that it will hopefully minimize pain during maintenance, and thus avoid creating legacy software ourselves. To this end, we will pay particular attention to three design principles for software
systems:

- **Operability**. Make it easy for operations teams to keep the system running smoothly.
- **Simplicity**. Make it easy for new engineers to understand the system, by removing as much complexity as possible from the system. (Note this is not the same as simplicity of the user interface.)
- **Evolvability**. Make it easy for engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change. Also known as extensibility, modifiability, or plasticity.

## Chapter 2 - Data Models and Query Languages

The best-known data model today is probably that of SQL, based on the relational
model proposed by Edgar Codd in 1970 [1]: data is organized into relations (called
tables in SQL), where each relation is an unordered collection of tuples (rows in SQL).

As computers became vastly more powerful and networked, they started being used
for increasingly diverse purposes. And remarkably, relational databases turned out to
generalize very well, beyond their original scope of business data processing, to a
broad variety of use cases.

There are several driving forces behind the adoption of NoSQL databases, including:
• A need for **greater scalability **than relational databases can easily achieve, includ‐
ing very large datasets or very high write throughput
• A widespread preference for **free and open source software **over commercial
database products
• **Specialized query operations **that are not well supported by the relational model
• Frustration with the restrictiveness of relational schemas, and a desire for a **more
dynamic and expressive data model **[5]

Most application development today is done in object-oriented programming lan‐
guages, which leads to a common criticism of the SQL data model: if data is stored in
relational tables, an awkward translation layer is required between the objects in the application code and the database model of tables, rows, and columns. The disconnect between the models is sometimes called an **impedance mismatch**.

For a data structure like a résumé, which is mostly a self-contained document, a JSON
representation can be quite appropriate: see Example 2-1. JSON has the appeal of
being much simpler than XML. Document-oriented databases like MongoDB [9],
RethinkDB [10], CouchDB [11], and Espresso [12] support this data model.

Some developers feel that the JSON model reduces the impedance mismatch between the application code and the storage layer. However, as we shall see in Chapter 4, There are also problems with JSON as a data encoding format. The lack of a schema is often cited as an advantage

The JSON representation has better locality than the multi-table schema in
Figure 2-1. If you want to fetch a profile in the relational example, you need to either
perform multiple queries (query each table by user_id) or perform a messy multi-
way join between the users table and its subordinate tables. In the JSON representa‐
tion, all the relevant information is in one place, and one query is sufficient.

### Schema flexibility

The advantage of using an ID is that because it has no meaning to humans, it never
needs to change: the ID can remain the same, even if the information it identifies
changes. Anything that is meaningful to humans may need to change sometime in
the future—and if that information is duplicated, all the redundant copies need to be
updated. That incurs write overheads, and risks inconsistencies (where some copies
of the information are updated but others aren’t). Removing such duplication is the
key idea behind **normalization in databases**.

Unfortunately, normalizing this data requires many-to-one relationships (many peo‐
ple live in one particular region, many people work in one particular industry), which
**don’t fit nicely into the document model**. In relational databases, it’s normal to refer
to rows in other tables by ID, because joins are easy. In document databases, joins are
not needed for one-to-many tree structures, and support for joins is often weak.

**The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the data structures used by the application. The relational model counters by providing better support for joins, and many-to-one and many-to-many relationships.**

However, if your application does use many-to-many relationships, the document
model becomes **less appealing**. It’s possible to reduce the need for joins by denormalizing, but then the application code needs to do additional work to keep the denormalized data **consistent**. Joins can be emulated in application code by making multiple requests to the database, but that also moves complexity into the application and is usually slower than a join performed by specialized code inside the database. In such cases, using a document model can lead to significantly more **complex application code and worse performance**

Document databases are sometimes called **schemaless**, **but that’s misleading**, as the
code that reads the data usually assumes some kind of structure—i.e., there is an
implicit schema, but it is not enforced by the database [20]. A more accurate term is
**schema-on-read **(the structure of the data is implicit, and only interpreted when the
data is read), in contrast with **schema-on-write **(the traditional approach of relational databases, where the schema is explicit and the database ensures all written data conforms to it)

The schema-on-read approach is advantageous if the items in the collection don’t all
have the same structure for some reason (i.e., the data is heterogeneous)—for exam‐
ple, because:
• There are many different types of objects, and it is not practical to put each type
of object in its own table

The structure of the data is determined by external systems over which you have
no control and which may change at any time.
In situations like these, a schema may hurt more than it helps, and schemaless docu‐
ments can be a much more natural data model. But in cases where all records are
expected to have the same structure, schemas are a useful mechanism for documenting and enforcing that structure.

### Locality

A document is usually stored as a single continuous string, encoded as JSON, XML,
or a binary variant thereof (such as MongoDB’s BSON). If your application often
needs to access the entire document (for example, to render it on a web page), there is a performance advantage to this storage locality. If data is split across multiple tables, like in Figure 2-1, multiple index lookups are required to retrieve it all, which may require more disk seeks and take more time.
The locality advantage only applies if you need large parts of the document at the
same time. The database typically needs to load the entire document, even if you
access only a small portion of it, which can be wasteful on large documents. On
updates to a document, the entire document usually needs to be rewritten—only
modifications that don’t change the encoded size of a document can easily be per‐
formed in place [19]. For these reasons, it is generally recommended that you keep
documents fairly small and avoid writes that increase the size of a document [9].
These performance limitations significantly reduce the set of situations in which
document databases are useful.

### Graph data

We saw earlier that many-to-many relationships are an important distinguishing feature between different data models. If your application has mostly one-to-many relationships (tree-structured data) or no relationships between records, the documentmodel is appropriate.

But what if many-to-many relationships are very common in your data? The relational model can handle simple cases of many-to-many relationships, but as the connections within your data become more complex, it becomes more natural to start modeling your data as a graph.

Graph data can be represented in a relational database. But if we put graph data in a relational structure, can we also query it using SQL? The answer is yes, but with some difficulty. In a relational database, you usually know in advance which joins you need in your query. In a graph query, you may need to traverse a variable number of edges before you find the vertex you’re looking for— that is, the number of joins is not fixed in advance.

In our example, that happens in the () -[:WITHIN*0..]-> () rule in the Cypher
query. A person’s LIVES_IN edge may point at any kind of location: a street, a city, a
district, a region, a state, etc. A city may be WITHIN a region, a region WITHIN a state, a state WITHIN a country, etc. The LIVES_IN edge may point directly at the location vertex you’re looking for, or it may be several levels removed in the location hierarchy. In Cypher, :WITHIN*0.. expresses that fact very concisely: it means “follow a WITHIN edge, zero or more times.” It is like the * operator in a regular expression.

## Chapter 3 - Storage and Retrieval

You’re probably not going to implement your own storage engine from scratch, but you do need to select a storage engine that is appropriate for your application, from the many that are available. In order to tune a storage engine to perform well on your kind of workload, you need to have a rough idea of what the storage engine is doing under the hood. In particular, there is a big difference between storage engines that are optimized for transactional workloads and those that are optimized for analytics.

### Data Structures

We will examine two families of storage engines: log-structured storage engines, and page-oriented storage engines such as B-trees.

**Logs**

Many databases internally use a log, which is an append-only data file. Real databases have more issues to deal with (such as concurrency control, reclaiming disk space so that the log doesn’t grow forever, and handling errors and partially written records), but the basic principle is the same. log is used in the more general sense: an append-only sequence of records.

As described so far, we only ever append to a file—so how do we avoid eventually
running out of disk space? A good solution is to break the log into segments of a certain size by closing a segment file when it reaches a certain size, and making subsequent writes to a new segment file. We can then perform **compaction **on these
segments, that is, throwing away duplicate keys in the log, and keeping only the most recent update for each key.

We can also **merge **several segments together at the same time as performing the compaction. Segments are never modified after they have been written, so the
merged segment is written to a new file.

An append-only log seems wasteful at first glance: why don’t you update the file in place, overwriting the old value with the new value? But an append-only design turns out to be good for several reasons:

**SSTables**

Each log-structured storage segment is a sequence of key-value pairs. These pairs appear in the order that they were written, and values later in the log take precedence over values for the same key earlier in the log. Apart from that, the order of key-value pairs in the file does not matter.

Now we can make a simple change to the format of our segment files: we require that the sequence of key-value pairs is **sorted by key**. We call this format Sorted String Table, or SSTable for short. We also require that **each key only appears once within each merged segment file. **SSTables have several big advantages over log segments with hash indexes:

- Merging segments is simple and efficient, even if the files are bigger than the
available memory.
- In order to find a particular key in the file, you no longer need to keep an index
of all the keys in memory.

**B-Tree**

Introduced in 1970 [17] and called “ubiquitous” less than 10 years later [18], B-trees
have stood the test of time very well. They remain the standard index implementation
in almost all relational databases, and many nonrelational databases use them too.

**Indexes**

In order to efficiently find the value for a particular key in the database, we need a
different data structure: an **index**.

An index is an additional structure that is derived from the primary data. Many databases allow you to add and remove indexes, and this doesn’t affect the contents of the database; it only affects the **performance of queries**. Maintaining additional structures incurs overhead, especially on writes.

This is an important trade-off in storage systems: **well-chosen indexes speed up read
queries, but every index slows down writes.** For this reason, databases don’t usually
index everything by default, but require you to choose indexes manually, using your knowledge of the application’s typical query patterns.

- **Hash index**. Uses in-memory hash map to improve read performance (Bitcask is an example database). The underlying data is stored in a log.
    - The hash table must fit in memory, so if you have a very large number of keys,
you’re out of luck.
    - Range queries are not efficient. For example, you cannot easily scan over all keys between kitty00000 and kitty99999—you’d have to look up each key individually in the hash maps.
- **SSTables. **Similar storage engines are used in Cassandra and HBase [8], both of which were inspired by Google’s Bigtable paper [9].
• When a write comes in, add it to an in-memory balanced tree data structure (for
example, a red-black tree). This in-memory tree is sometimes called a memtable.
• When the memtable gets bigger than some threshold write it out to disk as an SSTable file. The new SSTable file becomes the most recent segment of the database
• In order to serve a read request, first try to find the key in the memtable, then in
the most recent on-disk segment, then in the next-older segment, etc.
• From time to time, run a merging and compaction process in the background to
combine segment files and to discard overwritten or deleted values.
This scheme works very well. It only suffers from one problem: if the database
crashes, the most recent writes (which are in the memtable but not yet written out to disk) are lost. In order to avoid that problem, we can keep a separate log on disk to which every write is immediately appended, just like in the previous section.

Even though B-tree implementations are generally more mature than LSM-tree
implementations, LSM-trees are also interesting due to their performance character‐
istics. As a rule of thumb, LSM-trees are typically faster for writes, whereas B-trees
are thought to be faster for reads [23]. Reads are typically slower on LSM-trees
because they have to check several different data structures and SSTables at different
stages of compaction.

LSM-trees can be compressed better, and thus often produce smaller files on disk
than B-trees. B-tree storage engines leave some disk space unused due to fragmenta‐
tion: when a page is split or when a row cannot fit into an existing page, some space
in a page remains unused. Since LSM-trees are not page-oriented and periodically
rewrite SSTables to remove fragmentation, they have lower storage overheads, espe‐
cially when using leveled compaction

An advantage of B-trees is that each key exists in exactly one place in the index,
whereas a log-structured storage engine may have multiple copies of the same key in
different segments. This aspect makes B-trees attractive in databases that want to
offer strong transactional semantics: in many relational databases, transaction isola‐
tion is implemented using locks on ranges of keys, and in a B-tree index, those locks
can be directly attached to the tree

### Types of Indexes

The key in an index is the thing that queries search for, but the value can be one of
two things: it could be **the actual row **(document, vertex) in question, or it could be **a
reference to the row **stored elsewhere. In the latter case, the place where rows are
stored is known as a **heap file**, and it stores data in no particular order (it may be
append-only, or it may keep track of deleted rows in order to overwrite them with
new data later). The heap file approach is common because it avoids duplicating data
when multiple secondary indexes are present: each index just references a location in
the heap file, and the actual data is kept in one place.

In some situations, the extra hop from the index to the heap file is too much of a per‐
formance penalty for reads, so it can be desirable to store the indexed row directly
within an index. This is known as a **clustered index**. For example, in MySQL’s
InnoDB storage engine, the primary key of a table is always a clustered index, and
secondary indexes refer to the primary key (rather than a heap file location) [31]. In
SQL Server, you can specify one clustered index per table [32].

### In-memory databases

As RAM becomes cheaper, the cost-per-gigabyte argument is eroded. Many datasets
are simply not that big, so it’s quite feasible to keep them entirely in memory, poten‐

tially distributed across several machines. This has led to the development of in-
memory databases.
Some in-memory key-value stores, such as Memcached, are intended for caching use
only, where it’s acceptable for data to be lost if a machine is restarted. But other in-
memory databases aim for durability, which can be achieved with special hardware
(such as battery-powered RAM), by writing a log of changes to disk, by writing peri‐
odic snapshots to disk, or by replicating the in-memory state to other machines.

Counterintuitively, the performance advantage of in-memory databases is not due to
the fact that they don’t need to read from disk. Rather, they can be faster
because they can avoid the overheads of encoding in-memory data structures in a
form that can be written to disk [44].
Besides performance, another interesting area for in-memory databases is providing
data models that are difficult to implement with disk-based indexes. For example,
Redis offers a database-like interface to various data structures such as priority
queues and sets. Because it keeps all data in memory, its implementation is compara‐
tively simple.

### OLTP vs OLAP

In the early days of business data processing, a write to the database typically corre‐
sponded to a commercial transaction taking place: making a sale, placing an order
with a supplier, paying an employee’s salary, etc. The basic access pattern remained similar to processing business transactions. An application typically looks up a small number of records by some key, using an index. Records are inserted or updated based on the user’s input. Because these applications are interactive, the access pattern became known as online transaction processing (OLTP).

However, databases also started being increasingly used for data analytics, which has
very different access patterns. Usually an analytic query needs to scan over a huge
number of records, only reading a few columns per record, and calculates aggregate
statistics (such as count, sum, or average) rather than returning the raw data to the
user. These queries are often written by business analysts, and feed into reports that help the management of a company make better decisions (business intelligence), and has been called online analytic processing (OLAP)

At first, the same databases were used for both transaction processing and analytic
queries. SQL turned out to be quite flexible in this regard: it works well for OLTP-
type queries as well as OLAP-type queries. Nevertheless, in the late 1980s and early
1990s, there was a trend for companies to stop using their OLTP systems for analytics
purposes, and to run the analytics on a separate database instead. This separate data‐
base was called a data warehouse.

An enterprise may have **dozens of different transaction processing systems**: systems
powering the customer-facing website, controlling point of sale (checkout) systems in
physical stores, tracking inventory in warehouses, planning routes for vehicles, man‐
aging suppliers, administering employees, etc. Each of these systems is complex and
needs a team of people to maintain it, so the systems end up operating mostly auton‐
omously from each other.
These OLTP systems are usually expected to be highly available and to process trans‐
actions with low latency, since they are often critical to the operation of the business.
Database administrators therefore closely guard their OLTP databases. **They are usu‐
ally reluctant to let business analysts run ad hoc analytic queries on an OLTP data‐
base, since those queries are often expensive, scanning large parts of the dataset,
which can harm the performance of concurrently executing transactions.**

A data warehouse, by contrast, is a **separate database **that analysts can query to their
hearts’ content, without affecting OLTP operations [48]. The data warehouse con‐
tains a **read-only copy **of the data in all the various OLTP systems in the company.
Data is extracted from OLTP databases (using either a periodic data dump or a con‐
tinuous stream of updates), transformed into an analysis-friendly schema, cleaned
up, and then loaded into the data warehouse. This process of getting data into the
warehouse is known as **Extract–Transform–Load **(ETL)

A big advantage of using a separate data warehouse, rather than querying OLTP sys‐
tems directly for analytics, is that the **data warehouse can be optimized for analytic
access patterns**. It turns out that the indexing algorithms discussed in the first half of
this chapter work well for OLTP, but are not very good at answering analytic queries.

On the surface, a data warehouse and a relational OLTP database look similar,
because they both have a SQL query interface. However, the internals of the systems
can look quite different, because they are optimized for very different query patterns.
Many database vendors now focus on supporting either transaction processing or
analytics workloads, but not both.

a wide range of different data models are used in the realm
of transaction processing, depending on the needs of the application. On the other
hand, in analytics, there is much less diversity of data models. Many data warehouses
are used in a fairly formulaic style, known as a star schema (also known as dimen‐
sional modeling

At the center of the schema is a so-called fact table (in this example,
it is called fact_sales). Each row of the fact table represents an event that occurred
at a particular time (here, each row represents a customer’s purchase of a product). 

Some of the columns in the fact table are attributes, such as the price at which the
product was sold and the cost of buying it from the supplier (allowing the profit mar‐
gin to be calculated). Other columns in the fact table are foreign key references to
other tables, called dimension tables. As each row in the fact table represents an event,
the dimensions represent the who, what, where, when, how, and why of the event.

A variation of this template is known as the snowflake schema, where dimensions are
further broken down into subdimensions. For example, there could be separate tables
for brands and product categories, and each row in the dim_product table could ref‐
erence the brand and category as foreign keys, rather than storing them as strings in
the dim_product table. Snowflake schemas are more normalized than star schemas,
but star schemas are often preferred because they are simpler for analysts to work
with

### Column-oriented Storage

In a typical data warehouse, tables are often very wide: fact tables often have over 100
columns, sometimes several hundred [51]. Dimension tables can also be very wide, as
they include all the metadata that may be relevant for analysis—for example, the
dim_store table may include details of which services are offered at each store,
whether it has an in-store bakery, the square footage, the date when the store was first
opened, when it was last remodeled, how far it is from the nearest highway, etc.

Although fact tables are often over 100 columns wide, a typical data warehouse query
only accesses 4 or 5 of them at one time ("SELECT *" queries are rarely needed for
analytics) [51]. Take the query in Example 3-1: it accesses a large number of rows
(every occurrence of someone buying fruit or candy during the 2013 calendar year),
but it only needs to access three columns of the fact_sales table: date_key,
product_sk, and quantity. The query ignores all other columns.

In most OLTP databases, storage is laid out in a row-oriented fashion: all the values
from one row of a table are stored next to each other. Document databases are simi‐
lar: an entire document is typically stored as one contiguous sequence of bytes.

The idea behind column-oriented storage is simple: don’t store all the values from one
row together, but store all the values from each column together instead. If each col‐
umn is stored in a separate file, a query only needs to read and parse those columns
that are used in that query, which can save a lot of work.

**Optimizations**

Besides only loading those columns from disk that are required for a query, we can
further reduce the demands on disk throughput by compressing data. Fortunately,
column-oriented storage often lends itself very well to compression. One technique that is particu‐
larly effective in data warehouses is bitmap encoding, 

Often, the number of distinct values in a column is small compared to the number of
rows (for example, a retailer may have billions of sales transactions, but only 100,000
distinct products). We can now take a column with n distinct values and turn it into
**n separate bitmaps**: one bitmap for each distinct value, with one bit for each row. The
bit is 1 if the row has that value, and 0 if not. the bitmaps are sparse and can additionally be run-length encoded. This can make the encoding of a column remarkably compact.

**Sort Order**

A clever extension of this idea was introduced in C-Store and adopted in the com‐
mercial data warehouse Vertica [61, 62]. Different queries benefit from different sort
orders, so why not store the same data sorted in several different ways? Data needs to
be replicated to multiple machines anyway, so that you don’t lose data if one machine
fails. You might as well store that redundant data sorted in different ways so that
when you’re processing a query, you can use the version that best fits the query
pattern.
Having multiple sort orders in a column-oriented store is a bit similar to having mul‐
tiple secondary indexes in a row-oriented store. But the big difference is that the row-
oriented store keeps every row in one place (in the heap file or a clustered index), and
secondary indexes just contain pointers to the matching rows. In a column store,
there normally aren’t any pointers to data elsewhere, only columns containing values.

**Writing to disk**

An update-in-place approach, like B-trees use, is not possible with compressed col‐
umns. If you wanted to insert a row in the middle of a sorted table, you would most
likely have to rewrite all the column files. As rows are identified by their position
within a column, the insertion has to update all columns consistently.
Fortunately, we have already seen a good solution earlier in this chapter: LSM-trees.
All writes first go to an in-memory store, where they are added to a sorted structure
and prepared for writing to disk. It doesn’t matter whether the in-memory store is
row-oriented or column-oriented. When enough writes have accumulated, they are
merged with the column files on disk and written to new files in bulk.

## Chapter 4 - Encoding and Evolution

In most cases, a change to an application’s features also requires a change to data that
it stores: perhaps a new field or record type needs to be captured, or perhaps existing
data needs to be presented in a new way.

The data models we discussed in Chapter 2 have different ways of coping with such
change. Relational databases generally assume that all data in the database conforms
to one schema: although that schema can be changed (through schema migrations;
i.e., ALTER statements), there is exactly one schema in force at any one point in time.
By contrast, schema-on-read (“schemaless”) databases don’t enforce a schema, so the
database can contain a mixture of older and newer data formats written at different
times

old and new versions of the code, and old and new data formats,
may potentially all coexist in the system at the same time. In order for the system to
continue running smoothly, we need to maintain compatibility in both directions:
**Backward compatibility
**Newer code can read data that was written by older code.
**Forward compatibility
**Older code can read data that was written by newer code.
Backward compatibility is normally not hard to achieve: as author of the newer code,
you know the format of data written by older code, and so you can explicitly handle it
(if necessary by simply keeping the old code to read the old data). Forward compati‐
bility can be trickier, because it requires older code to ignore additions made by a
newer version of the code.

Programs usually work with data in (at least) two different representations:

1. In memory, data is kept in objects, structs, lists, arrays, hash tables, trees, and so
on. These data structures are optimized for efficient access and manipulation by
the CPU (typically using pointers).
2. When you want to write data to a file or send it over the network, you have to
encode it as some kind of self-contained sequence of bytes (for example, a JSON
document). Since a pointer wouldn’t make sense to any other process, this sequence-of-bytes representation looks quite different from the data structures that are normally used in memory.

Thus, we need some kind of translation between the two representations. The trans‐
lation from the in-memory representation to a byte sequence is called encoding (also
known as serialization or marshalling), and the reverse is called decoding (parsing,
deserialization, unmarshalling).

With Avro, when an application wants to encode some data (to write it to a file or
database, to send it over the network, etc.), it encodes the data using whatever version
of the schema it knows about—for example, that schema may be compiled into the
application. This is known as the writer’s schema.
When an application wants to decode some data (read it from a file or database,
receive it from the network, etc.), it is expecting the data to be in some schema, which
is known as the reader’s schema. That is the schema the application code is relying on
—code may have been generated from that schema during the application’s build
process.
The key idea with Avro is that the writer’s schema and the reader’s schema don’t have
to be the same—they only need to be compatible.

With Avro, forward compatibility means that you can have a new version of the
schema as writer and an old version of the schema as reader. Conversely, backward
compatibility means that you can have a new version of the schema as reader and an
old version as writer.
To maintain compatibility, you may only add or remove a field that has a default
value. (The field favoriteNumber in our Avro schema has a default value of null.)
For example, say you add a field with a default value, so this new field exists in the
new schema but not the old one. When a reader using the new schema reads a record
written with the old schema, the default value is filled in for the missing field.
If you were to add a field that has no default value, new readers wouldn’t be able to
read data written by old writers, so you would break backward compatibility. If you
were to remove a field that has no default value, old readers wouldn’t be able to read
data written by new writers, so you would break forward compatibility.

### Message-Passing Data flow

asynchronous message-passing systems,
which are somewhere between RPC and databases. They are similar to RPC in that a
client’s request (usually called a message) is delivered to another process with low
latency. They are similar to databases in that the message is not sent via a direct net‐
work connection, but goes via an intermediary called a message broker (also called a
message queue or message-oriented middleware), which stores the message temporar‐
ily.
Using a message broker has several advantages compared to direct RPC:
• It can act as a buffer if the recipient is unavailable or overloaded, and thus
improve system reliability.
• It can automatically redeliver messages to a process that has crashed, and thus
prevent messages from being lost.
• It avoids the sender needing to know the IP address and port number of the
recipient (which is particularly useful in a cloud deployment where virtual
machines often come and go).
• It allows one message to be sent to several recipients.
• It logically decouples the sender from the recipient (the sender just publishes
messages and doesn’t care who consumes them).

The detailed delivery semantics vary by implementation and configuration, but in
general, message brokers are used as follows: one process sends a message to a named
queue or topic, and the broker ensures that the message is delivered to one or more
consumers of or subscribers to that queue or topic. There can be many producers and
many consumers on the same topic.

# Chapter 5 - Replication

Eeplication means keeping a copy of the same data on multiple machines that are
connected via a network. As discussed in the introduction to Part II, there are several
reasons why you might want to replicate data:
• To keep data geographically close to your users (and thus reduce latency)
• To allow the system to continue working even if some of its parts have failed
(and thus increase availability)
• To scale out the number of machines that can serve read queries (and thus
increase read throughput)

Each node that stores a copy of the database is called a replica. With multiple replicas,
a question inevitably arises: how do we ensure that all the data ends up on all the rep‐
licas?
Every write to the database needs to be processed by every replica; otherwise, the rep‐
licas would no longer contain the same data. The most common solution for this is
called leader-based replication (also known as active/passive or master–slave replica‐
tion) and is illustrated in Figure 5-1. It works as follows:

3. One of the replicas is designated the leader (also known as master or primary).
When clients want to write to the database, they must send their requests to the
leader, which first writes the new data to its local storage.
4. The other replicas are known as followers (read replicas, slaves, secondaries, or hot
standbys).i Whenever the leader writes new data to its local storage, it also sends
the data change to all of its followers as part of a replication log or change stream.
Each follower takes the log from the leader and updates its local copy of the data‐
base accordingly, by applying all writes in the same order as they were processed
on the leader.
5. When a client wants to read from the database, it can query either the leader or
any of the followers. However, writes are only accepted on the leader (the follow‐
ers are read-only from the client’s point of view).

An important detail of a replicated system is whether the replication happens syn‐
chronously or asynchronously. (In relational databases, this is often a configurable
option; other systems are often hardcoded to be either one or the other.)

- **synchronous:** the leader waits until follower 1 has confirmed that it received the write before reporting success to the user, and before making the write visible to other clients.
- **asynchronous**: the leader sends the message, but doesn’t wait for a response from the follower.

The advantage of synchronous replication is that the follower is guaranteed to have
an up-to-date copy of the data that is consistent with the leader. If the leader sud‐
denly fails, we can be sure that the data is still available on the follower. The disad‐
vantage is that if the synchronous follower doesn’t respond (because it has crashed,
or there is a network fault, or for any other reason), the write cannot be processed.
The leader must block all writes and wait until the synchronous replica is available
again.
For that reason, it is impractical for all followers to be synchronous: any one node
outage would cause the whole system to grind to a halt. In practice, if you enable syn‐
chronous replication on a database, it usually means that one of the followers is syn‐
chronous, and the others are asynchronous. If the synchronous follower becomes
unavailable or slow, one of the asynchronous followers is made synchronous. This
guarantees that you have an up-to-date copy of the data on at least two nodes: the 

leader and one synchronous follower. This configuration is sometimes also called
semi-synchronous [7].
Often, leader-based replication is configured to be completely asynchronous. In this
case, if the leader fails and is not recoverable, any writes that have not yet been repli‐
cated to followers are lost. This means that a write is not guaranteed to be durable,
even if it has been confirmed to the client. However, a fully asynchronous configura‐
tion has the advantage that the leader can continue processing writes, even if all of its
followers have fallen behind.
Weakening durability may sound like a bad trade-off, but asynchronous replication is
nevertheless widely used, especially if there are many followers or if they are geo‐
graphically distributed.

From time to time, you need to set up new followers—perhaps to increase the num‐
ber of replicas, or to replace failed nodes. How do you ensure that the new follower
has an accurate copy of the leader’s data?
Simply copying data files from one node to another is typically not sufficient: clients
are constantly writing to the database, and the data is always in flux, so a standard file
copy would see different parts of the database at different points in time. The result
might not make any sense.
You could make the files on disk consistent by locking the database (making it
unavailable for writes), but that would go against our goal of high availability. Fortu‐
nately, setting up a follower can usually be done without downtime. Conceptually,
the process looks like this:

Any node in the system can go down, perhaps unexpectedly due to a fault, but just as
likely due to planned maintenance (for example, rebooting a machine to install a ker‐
nel security patch). Being able to reboot individual nodes without downtime is a big
advantage for operations and maintenance. Thus, our goal is to keep the system as a
whole running despite individual node failures, and to keep the impact of a node out‐
age as small as possible.
How do you achieve high availability with leader-based replication?

Handling a failure of the leader is trickier: one of the followers needs to be promoted
to be the new leader, clients need to be reconfigured to send their writes to the new
leader, and the other followers need to start consuming data changes from the new
leader. This process is called failover.

n the simplest case, the leader logs every write request (statement) that it executes
and sends that statement log to its followers. For a relational database, this means
that every INSERT, UPDATE, or DELETE statement is forwarded to followers, and each 

follower parses and executes that SQL statement as if it had been received from a
client.

lthough this may sound reasonable, there are various ways in which this approach
to replication can break down:
• Any statement that calls a nondeterministic function, such as NOW() to get the
current date and time or RAND() to get a random number, is likely to generate a
different value on each replica.
• If statements use an autoincrementing column, or if they depend on the existing
data in the database (e.g., UPDATE … WHERE <some condition>), they must be
executed in exactly the same order on each replica, or else they may have a differ‐
ent effect. This can be limiting when there are multiple concurrently executing
transactions.
• Statements that have side effects (e.g., triggers, stored procedures, user-defined
functions) may result in different side effects occurring on each replica, unless
the side effects are absolutely deterministic.

In either case, the log is an append-only sequence of bytes containing all writes to the
database. We can use the exact same log to build a replica on another node: besides
writing the log to disk, the leader also sends it across the network to its followers.

An alternative is to use different log formats for replication and for the storage
engine, which allows the replication log to be decoupled from the storage engine
internals. This kind of replication log is called a logical log, to distinguish it from the
storage engine’s (physical) data representation.
A logical log for a relational database is usually a sequence of records describing
writes to database tables at the granularity of a row:
• For an inserted row, the log contains the new values of all columns.
• For a deleted row, the log contains enough information to uniquely identify the
row that was deleted. Typically this would be the primary key, but if there is no
primary key on the table, the old values of all columns need to be logged.
• For an updated row, the log contains enough information to uniquely identify
the updated row, and the new values of all columns (or at least the new values of
all columns that changed).

A logical log format is also easier for external applications to parse. This aspect is use‐
ful if you want to send the contents of a database to an external system, such as a data 

warehouse for offline analysis, or for building custom indexes and caches [18]. This
technique is called change data capture, and we will return to it in Chapter 11.

### Read Scalability

Being able to tolerate node failures is just one reason for wanting replication. As
mentioned in the introduction to Part II, other reasons are scalability (processing
more requests than a single machine can handle) and latency (placing replicas geo‐
graphically closer to users).
Leader-based replication requires all writes to go through a single node, but read-
only queries can go to any replica. For workloads that consist of mostly reads and
only a small percentage of writes (a common pattern on the web), there is an attrac‐
tive option: create many followers, and distribute the read requests across those fol‐
lowers. This removes load from the leader and allows read requests to be served by
nearby replicas.
In this read-scaling architecture, you can increase the capacity for serving read-only
requests simply by adding more followers. However, this approach only realistically
works with asynchronous replication—if you tried to synchronously replicate to all
followers, a single node failure or network outage would make the entire system 

unavailable for writing. And the more nodes you have, the likelier it is that one will
be down, so a fully synchronous configuration would be very unreliable.
Unfortunately, if an application reads from an asynchronous follower, it may see out‐
dated information if the follower has fallen behind. This leads to apparent inconsis‐
tencies in the database: if you run the same query on the leader and a follower at the
same time, you may get different results, because not all writes have been reflected in
the follower. This inconsistency is just a temporary state—if you stop writing to the
database and wait a while, the followers will eventually catch up and become consis‐
tent with the leader. For that reason, this effect is known as eventual consistency. 

The term eventual consistency was coined by Douglas Terry et al. [24], popularized by Werner Vogels
[22], and became the battle cry of many NoSQL projects. However, not only NoSQL databases are eventually
consistent: followers in an asynchronously replicated relational database have the same characteristics.

Monotonic reads [23] is a guarantee that this kind of anomaly does not happen. It’s a
lesser guarantee than strong consistency, but a stronger guarantee than eventual con‐
sistency. When you read data, you may see an old value; monotonic reads only means
that if one user makes several reads in sequence, they will not see time go backward—
i.e., they will not read older data after having previously read newer data.

Preventing this kind of anomaly requires another type of guarantee: consistent prefix
reads [23]. This guarantee says that if a sequence of writes happens in a certain order,
then anyone reading those writes will see them appear in the same order.

When working with an eventually consistent system, it is worth thinking about how
the application behaves if the replication lag increases to several minutes or even
hours. If the answer is “no problem,” that’s great. However, if the result is a bad expe‐
rience for users, it’s important to design the system to provide a stronger guarantee,
such as read-after-write. Pretending that replication is synchronous when in fact it is
asynchronous is a recipe for problems down the line.

Single-node transactions have existed for a long time. **However, in the move to dis‐
tributed (replicated and partitioned) databases, many systems have abandoned them,
claiming that transactions are too expensive in terms of performance and availability,
and asserting that eventual consistency is inevitable in a scalable system. **There is
some truth in that statement, but it is overly simplistic, and we will develop a more
nuanced view over the course of the rest of this book.

## Multi-leader replication

**A natural extension of the leader-based replication model is to allow more than one
node to accept writes**. Replication still happens in the same way: each node that pro‐
cesses a write must forward that data change to all the other nodes. We call this a
multi-leader configuration (also known as master–master or active/active replication).
In this setup, each leader simultaneously acts as a follower to the other leaders.

Imagine you have a database with replicas in several different datacenters (perhaps so
that you can tolerate failure of an entire datacenter, or perhaps in order to be closer
to your users). With a normal leader-based replication setup, the leader has to be in
one of the datacenters, and all writes must go through that datacenter.
In a multi-leader configuration, you can have a leader in each datacenter. Figure 5-6
shows what this architecture might look like. Within each datacenter, regular leader–
follower replication is used; between datacenters, each datacenter’s leader replicates
its changes to the leaders in other datacenters.

**Although multi-leader replication has advantages, it also has a big downside: the
**same data may be concurrently modified in two different datacenters, and those write
conflicts must be resolved (indicated as “conflict resolution” in Figure 5-6).
As multi-leader replication is a somewhat retrofitted feature in many databases, there
are often subtle configuration pitfalls and surprising interactions with other database
features. For example, autoincrementing keys, triggers, and integrity constraints can
be problematic. For this reason, multi-leader replication is often considered danger‐
ous territory that should be avoided if possible [28].

The simplest strategy for dealing with conflicts is to **avoid **them: if the application can
ensure that all writes for a particular record go through the same leader, then con‐
flicts cannot occur. Since many implementations of multi-leader replication handle
conflicts quite poorly, avoiding conflicts is a frequently recommended approach [34].

**There are various ways of achieving convergent conflict resolution:
**• Give each write a unique ID (e.g., a timestamp, a long random number, a UUID,
or a hash of the key and value), pick the write with the highest ID as the winner,
and throw away the other writes. If a timestamp is used, this technique is known
as last write wins (LWW). Although this approach is popular, it is dangerously
prone to data loss [35]. We will discuss LWW in more detail at the end of this
chapter (“Detecting Concurrent Writes” on page 184).
• Give each replica a unique ID, and let writes that originated at a higher-
numbered replica always take precedence over writes that originated at a lower-
numbered replica. This approach also implies data loss.
• Somehow merge the values together—e.g., order them alphabetically and then
concatenate them (in Figure 5-7, the merged title might be something like
“B/C”).
• Record the conflict in an explicit data structure that preserves all information,
and write application code that resolves the conflict at some later time (perhaps
by prompting the user).

As the most appropriate way of resolving a conflict may depend on the application,
most multi-leader replication tools let you write conflict resolution logic using appli‐
cation code. That code may be executed on write or on read:
**On write
**As soon as the database system detects a conflict in the log of replicated changes,
it calls the conflict handler. For example, Bucardo allows you to write a snippet of
Perl for this purpose. This handler typically cannot prompt a user—it runs in a
background process and it must execute quickly.
**On read
**When a conflict is detected, all the conflicting writes are stored. The next time
the data is read, these multiple versions of the data are returned to the applica‐
tion. The application may prompt the user or automatically resolve the conflict,
and write the result back to the database. CouchDB works this way, for example.

### Topology

The most general topology is all-to-all (Figure 5-8 [c]), in which every leader sends its
writes to every other leader. However, more restricted topologies are also used: for
example, MySQL by default supports only a circular topology [34], in which each
node receives writes from one node and forwards those writes (plus any writes of its
own) to one other node. Another popular topology has the shape of a star: one desig‐
nated root node forwards writes to all of the other nodes. The star topology can be
generalized to a tree.

In circular and star topologies, a write may need to pass through several nodes before
it reaches all replicas. Therefore, nodes need to forward data changes they receive
from other nodes. To prevent infinite replication loops, each node is given a unique
identifier, and in the replication log, each write is tagged with the identifiers of all the
nodes it has passed through

A problem with circular and star topologies is that if just one node fails, it can inter‐
rupt the flow of replication messages between other nodes, causing them to be unable
to communicate until the node is fixed. The topology could be reconfigured to work
around the failed node, but in most deployments such reconfiguration would have to
be done manually. The fault tolerance of a more densely connected topology (such as
all-to-all) is better because it allows messages to travel along different paths, avoiding
a single point of failure.
On the other hand, all-to-all topologies can have issues too. In particular, some net‐
work links may be faster than others (e.g., due to network congestion), with the result
that some replication messages may “overtake” others

To order these events correctly, a technique called version vectors can be used, which
we will discuss later in this chapter (see “Detecting Concurrent Writes” on page 184).
However, conflict detection techniques are poorly implemented in many multi-leader
replication systems. For example, at the time of writing, PostgreSQL BDR does not
provide causal ordering of writes [27], and Tungsten Replicator for MySQL doesn’t
even try to detect conflicts

## Leaderless Replication

Some data storage systems take a different approach, abandoning the concept of a
leader and allowing any replica to directly accept writes from clients. Some of the ear‐
liest replicated data systems were leaderless [1, 44], but the idea was mostly forgotten
during the era of dominance of relational databases. It once again became a fashiona‐
ble architecture for databases after Amazon used it for its in-house Dynamo system
[37].vi Riak, Cassandra, and Voldemort are open source datastores with leaderless
replication models inspired by Dynamo, so this kind of database is also known as
Dynamo-style.

On the other hand, in a leaderless configuration, failover does not exist. Figure 5-10
shows what happens: the client (user 1234) sends the write to all three replicas in par‐
allel, and the two available replicas accept the write but the unavailable replica misses
it. Let’s say that it’s sufficient for two out of three replicas to acknowledge the write:
after user 1234 has received two ok responses, we consider the write to be successful.
The client simply ignores the fact that one of the replicas missed the write.

Now imagine that the unavailable node comes back online, and clients start reading
from it. Any writes that happened while the node was down are missing from that
node. Thus, if you read from that node, you may get stale (outdated) values as
responses.
To solve that problem, when a client reads from the database, it doesn’t just send its
request to one replica: read requests are also sent to several nodes in parallel. The cli‐
ent may get different responses from different nodes; i.e., the up-to-date value from
one node and a stale value from another. Version numbers are used to determine
which value is newer

Read repair
When a client makes a read from several nodes in parallel, it can detect any stale
responses. For example, in Figure 5-10, user 2345 gets a version 6 value from rep‐
lica 3 and a version 7 value from replicas 1 and 2. The client sees that replica 3
has a stale value and writes the newer value back to that replica. This approach
works well for values that are frequently read.
Anti-entropy process
In addition, some datastores have a background process that constantly looks for
differences in the data between replicas and copies any missing data from one
replica to another. Unlike the replication log in leader-based replication, this
anti-entropy process does not copy writes in any particular order, and there may
be a significant delay before data is copied.

If we know that every successful write is guaranteed to be present on at least two out
of three replicas, that means at most one replica can be stale. Thus, if we read from at
least two replicas, we can be sure that at least one of the two is up to date. If the third
replica is down or slow to respond, reads can nevertheless continue returning an up-
to-date value.
More generally, if there are n replicas, every write must be confirmed by w nodes to
be considered successful, and we must query at least r nodes for each read. (In our
example, n = 3, w = 2, r = 2.) As long as w + r > n, we expect to get an up-to-date
value when reading, because at least one of the r nodes we’re reading from must be
up to date. Reads and writes that obey these r and w values are called quorum reads
and writes [44].vii You can think of r and w as the minimum number of votes required
for the read or write to be valid. it only matters that the sets of nodes used by the read and
write operations overlap in at least one node.

Normally, reads and writes are always sent to all n replicas in parallel. The
parameters w and r determine how many nodes we wait for—i.e., how many of
the n nodes need to report success before we consider the read or write to be suc‐
cessful.

With a smaller w and r you are more likely to read stale values, because it’s more
likely that your read didn’t include the node with the latest value. On the upside, this
configuration allows lower latency and higher availability: if there is a network inter‐
ruption and many replicas become unreachable, there’s a higher chance that you can
continue processing reads and writes. Only after the number of reachable replicas
falls below w or r does the database become unavailable for writing or reading,
respectively.

Thus, although quorums appear to guarantee that a read returns the latest written
value, in practice it is not so simple. Dynamo-style databases are generally optimized
for use cases that can tolerate eventual consistency. Stronger guarantees generally require transactions or consensus

### Sloppy Quorums

In a large cluster (with significantly more than n nodes) it’s likely that the client can
connect to some database nodes during the network interruption, just not to the
nodes that it needs to assemble a quorum for a particular value. In that case, database
designers face a trade-off:
• Is it better to return errors to all requests for which we cannot reach a quorum of
w or r nodes?
• Or should we accept writes anyway, and write them to some nodes that are
reachable but aren’t among the n nodes on which the value usually lives?
The latter is known as a sloppy quorum [37]: writes and reads still require w and r
successful responses, but those may include nodes that are not among the designated
n “home” nodes for a value. 

Once the network interruption is fixed, any writes that one node temporarily
accepted on behalf of another node are sent to the appropriate “home” nodes. This is called hinted handoff

Thus, a sloppy quorum actually isn’t a quorum at all in the traditional sense. It’s only
an assurance of durability, namely that the data is stored on w nodes somewhere.
There is no guarantee that a read of r nodes will see it until the hinted handoff has
completed.

# Chapter 6 - Partitioning

In Chapter 5 we discussed replication—that is, having multiple copies of the same
data on different nodes. For very large datasets, or very high query throughput, that is
not sufficient: we need to break the data up into partitions, also known as sharding. In effect, each partition is a small database of its own, although the database may support operations that touch multiple partitions at the same time.

What we call a partition here is called a shard in MongoDB, Elasticsearch, and SolrCloud; it’s known as a region in HBase, a tablet in Bigtable, a vnode in Cassandra and Riak, and a vBucket in Couchbase. However, partitioning is the most established term, so we’ll stick with that.

The main reason for wanting to partition data is scalability. Different partitions can be placed on different nodes in a shared-nothing cluster. Thus, a large dataset can be distributed across many disks, and the query load can be distributed across many processors.

Partitioning is usually combined with replication so that copies of each partition are
stored on multiple nodes. This means that, even though each record belongs to
exactly one partition, it may still be stored on several different nodes for fault toler‐
ance.

Our goal with partitioning is to spread the data and the query load evenly across
nodes. If every node takes a fair share, then—in theory—**10 nodes should be able to
handle 10 times as much data and 10 times the read and write throughput of a single
node (ignoring replication for now).**

Because of this risk of skew and hot spots, many distributed datastores use a hash
function to determine the partition for a given key. Unfortunately however, by using the hash of the key for partitioning we lose a nice property of key-range partitioning: the ability to do efficient range queries. Keys that were once adjacent are now scattered across all the partitions, so their sort order is lost.

The situation becomes more complicated if secondary indexes are involved (see also
“Other Indexing Structures” on page 85). A secondary index usually doesn’t identify
a record uniquely but rather is a way of searching for occurrences of a particular
value: find all actions by user 123, find all articles containing the word hogwash, find
all cars whose color is red, and so on.
Secondary indexes are the bread and butter of relational databases, and they are com‐
mon in document databases too. Many key-value stores (such as HBase and Volde‐
mort) have avoided secondary indexes because of their added implementation
complexity, but some (such as Riak) have started adding them because they are so
useful for data modeling. And finally, secondary indexes are the raison d’être of
search servers such as Solr and Elasticsearch.

In this indexing approach, each partition is completely separate: each partition main‐
tains its own secondary indexes, covering only the documents in that partition. It
doesn’t care what data is stored in other partitions. Whenever you need to write to
the database—to add, remove, or update a document—you only need to deal with the
partition that contains the document ID that you are writing. For that reason, a
document-partitioned index is also known as a local index (as opposed to a global
index, described in the next section).

Rather than each partition having its own secondary index (a local index), we can
construct a global index that covers data in all partitions. However, we can’t just store
that index on one node, since it would likely become a bottleneck and defeat the pur‐
pose of partitioning. A global index must also be partitioned, but it can be partitioned
differently from the primary key index.

So far we have focused on very simple queries that read or write a single key (plus
scatter/gather queries in the case of document-partitioned secondary indexes). This is
about the level of access supported by most NoSQL distributed datastores.
However, massively parallel processing (MPP) relational database products, often
used for analytics, are much more sophisticated in the types of queries they support.
A typical data warehouse query contains several join, filtering, grouping, and aggre‐
gation operations. The MPP query optimizer breaks this complex query into a num‐
ber of execution stages and partitions, many of which can be executed in parallel on
different nodes of the database cluster. Queries that involve scanning over large parts
of the dataset particularly benefit from such parallel execution.

# Appendice

## Dynamo: AWS Highly Scalable Key-value Store

Reliability at massive scale is one of the biggest challenges we face at [Amazon.com](http://amazon.com/), one of the largest e-commerce operations in the world; even the slightest outage has significant financial
consequences and impacts customer trust. The [Amazon.com](http://amazon.com/) platform, which provides services for many web sites worldwide, is implemented on top of an infrastructure of tens of thousands of servers and network components located in many datacenters around the world. At this scale, small and large components fail continuously and the way persistent state is managed in the face of these failures drives the reliability and scalability of the software systems.

One of the lessons our organization has learned from operating Amazon’s platform is that the **reliability and scalability of a system is dependent on how its application state is managed**. Amazon uses a highly decentralized, loosely coupled, service oriented architecture consisting of hundreds of services. In this environment **there is a particular need for storage technologies that are always available**. For example, customers should be able to view and add items to their shopping cart even if disks are failing, network routes are flapping, or data centers are being destroyed by tornados. Therefore, the service responsible for managing shopping carts requires that it can always write to and read from its data store, and that its data needs to be available across multiple data centers.

Dynamo is used to manage the state of services that have **very high reliability requirements **and need tight control over the tradeoffs between availability, consistency, cost-effectiveness and
performance.

There are many services on Amazon’s platform that only need primary-key access to a data store. For many services, such as those that provide best seller lists, shopping carts, customer preferences, session management, sales rank, and product catalog, **the common pattern of using a relational database would lead to inefficiencies and limit scale and availability. **Dynamo provides a simple primary-key only interface to meet the requirements of these applications. Dynamo uses a synthesis of well known techniques to achieve scalability and availability: Data is partitioned and replicated
using consistent hashing [10], and consistency is facilitated by object versioning [12]. The consistency among replicas during updates is maintained by a quorum-like technique and a
decentralized replica synchronization protocol. Dynamo is a completely decentralized system with
minimal need for manual administration.

**Background**

Traditionally production systems store their state in relational databases. For many of the more common usage patterns of state persistence, however, a relational database is a solution that is far
from ideal. **Most of these services only store and retrieve data by primary key and do not require the complex querying and management functionality offered by an RDBMS. **This excess functionality requires **expensive hardware **and **highly skilled **personnel for its operation, making it a very inefficient solution.

In addition, the available replication technologies are limited and **typically choose consistency over availability**. Although many advances have been made in the recent years, it is still not easy to scale-out databases or use smart partitioning schemes for load balancing.

**Assumptions**

Query Model:** simple read and write operations to a data item that is uniquely identified by a key**. State is stored as binary objects (i.e., blobs) identified by unique keys. No operations span
multiple data items and there is no need for relational schema. This requirement is based on the observation that a significant portion of Amazon’s services can work with this simple query
model and do not need any relational schema. Dynamo targets applications that need to store objects that are relatively small (usually less than 1 MB).

ACID Properties: ACID (Atomicity, Consistency, Isolation, Durability) is a set of properties that guarantee that database transactions are processed reliably. In the context of databases, a single logical operation on the data is called a transaction. Experience at Amazon has shown that data stores that provide ACID guarantees tend to have poor availability. This has been widely acknowledged by both the industry and academia [5]. **Dynamo targets applications that operate with weaker consistency (the “C” in ACID) if this results in high availability. **Dynamo does not provide any isolation guarantees and permits only single key updates.

**Design considerations**

**Traditional replicated relational database systems **focus on the problem of guaranteeing strong consistency to replicated data. Although strong consistency provides the application writer a
convenient programming model, **these systems are limited in scalability and availability **[7]. These systems are not capable of handling network partitions because they typically provide strong
consistency guarantees. For instance, rather than dealing with the uncertainty of the correctness of an answer, the data is made unavailable until it is absolutely certain that it is correct. From the very early replicated database works, it is well known that when dealing with the possibility of network failures, strong consistency and high data availability cannot be achieved simultaneously [2, 11]. As such systems and applications need to be aware which properties can be achieved under which conditions.

For systems prone to server and network failures, availability can be increased by using **optimistic replication techniques**, where changes are allowed to propagate to replicas in the background, and concurrent, disconnected work is tolerated. The challenge with this approach is that it can lead to conflicting changes which must be detected and resolved. This process of conflict resolution introduces two problems: when to resolve them and who resolves them. Dynamo is designed to be an eventually consistent data store; that is all updates reach all replicas eventually

An important design consideration is to decide when to perform the process of resolving update conflicts, i.e., whether conflicts should be resolved during reads or writes. Many traditional data stores execute conflict resolution during writes and keep the read complexity simple [7]. In such systems, writes may be rejected if the data store cannot reach all (or a majority of) the replicas at a given time. On the other hand, Dynamo targets the design space of an “**always writeable**” data store (i.e., a data store that is highly available for writes).

**System Architecture**

| Problem | Technique | Advantage |
| --- | --- | --- |
| Partitioning | Consistent Hashing | Incremental scalability |
| High Availability for writes | Vector clocks with reconciliation during reads | Version size is decoupled from update rates |
| Handling temporary failures | Sloppy Quorum and hinted handoff | Provides high availability and durability guarantee when some replicas are unavailable |
| Recovering from permanent failures | Anti-entropy using Merkle trees | Synchronizes divergent replicas in the background |
| Membership and failure detection | Gossip-based membership protocol and failure detection | Preserves symmetry and avoids a centralized registry for membership and liveness info |

A **gossip protocol** is a way for nodes in a distributed system to:

- Exchange information about **membership** (who is in the cluster),
- Share **health status** (who is up/down),
- Spread **configuration updates**, etc.

Each node periodically:

- Picks a **random peer**,
- **Shares what it knows** (like a rumor),
- Over time, **all nodes converge** on the same information.

===

**Consistent hashing** is a way to distribute keys (data items) across a dynamic set of nodes (servers) such that **when nodes are added or removed**, only a **small number of keys need to be remapped**.

Imagine all possible hash values laid out on a **circle** (also called a *hash ring*).

- The **hash function** outputs values from `0` to `MAX` (e.g., 0 to 2³²−1).
- These hash values form a **ring** — meaning the end wraps back to the beginning.
- Every **node** (server) and **key** (data item) is placed on this ring by hashing its identifier.

Now let’s hash some data (e.g., user IDs):

- `hash("user1") = 25` → falls between A (20) and B (50) → goes to **Node B**
- `hash("user2") = 77` → falls between B (50) and C (80) → goes to **Node C**
- `hash("user3") = 10` → falls before A (20) → goes to **Node A**

🧠 Rule: Each key is stored at the **first node clockwise** from its hash value.

Suppose **Node B** fails.

- All keys that mapped to Node B now go to the **next node clockwise**, which is **C**.
- Only keys between **20 and 50** (i.e., B’s share) get remapped — the rest stay put.

The principle advantage of consistent hashing is that departure or arrival of a node only affects its immediate neighbors and other nodes remain unaffected.

**System Interface**

Dynamo stores objects associated with a key through a simple interface; it exposes two operations: get() and put(). The get(key) operation locates the object replicas associated with the key in the storage system and returns a single object or a list of objects with conflicting versions along with a context. The put(key, context, object) operation determines where the replicas of the object should be placed based on the associated key, and writes the replicas to disk. The context encodes system metadata about the object that is opaque to the caller and includes information such as the version of the object. The context information is stored along with the object so that the system can verify the validity of the context object supplied in the put request.

Dynamo treats both the key and the object supplied by the caller as an opaque array of bytes. It applies a MD5 hash on the key to generate a 128-bit identifier, which is used to determine the storage nodes that are responsible for serving the key.

The basic consistent hashing algorithm presents some challenges. First, the random position assignment of each node on the ring leads to non-uniform data and load distribution. Second, the basic algorithm is oblivious to the heterogeneity in the performance of nodes. To address these issues, Dynamo uses a variant of consistent hashing (similar to the one used in [10, 20]): instead of
mapping a node to a single point in the circle, each node gets assigned to multiple points in the ring. To this end, Dynamo uses the concept of “virtual nodes”. A virtual node looks like a single node in the system, but each node can be responsible for more than one virtual node.

To achieve high availability and durability, Dynamo replicates its data on multiple hosts. Each data item is replicated at N hosts, where N is a parameter configured “per-instance”. Each key, k, is assigned to a coordinator node (described in the previous section). The coordinator is in charge of the replication of the data items that fall within its range. In addition to locally storing each key within its range, the coordinator replicates these keys at the N-1 clockwise successor nodes in the ring.

The list of nodes that is responsible for storing a particular key is called the preference list. The system is designed, as will be explained in Section 4.8, so that every node in the system can
determine which nodes should be in this list for any particular key.

Both get and put operations are invoked using Amazon’s infrastructure-specific request processing framework over HTTP. There are two strategies that a client can use to select a node: (1) route its request through a generic load balancer that will select a node based on load information, or (2) use a partition-aware client library that routes requests directly to the appropriate coordinator nodes. The advantage of the first approach is that the client does not have to link any code specific to Dynamo in its application, whereas the second strategy can achieve lower latency because it skips a potential forwarding step.

A node handling a read or write operation is known as the coordinator. Typically, this is the first among the top N nodes in the preference list. If the requests are received through a load balancer, requests to access a key may be routed to any random node in the ring. In this scenario, the node that receives the request will not coordinate it if the node is not in the top N of the requested key’s preference list. Instead, that node will forward the request to the first among the top N nodes in the preference list.

If Dynamo used a traditional quorum approach it would be unavailable during server failures and network partitions, and would have reduced durability even under the simplest of failure conditions. To remedy this it does not enforce strict quorum membership and instead it uses a “sloppy quorum”; all read and write operations are performed on the first N healthy nodes from the preference list, which may not always be the first N nodes encountered while walking the consistent hashing ring.

It is imperative that a highly available storage system be capable of handling the failure of an entire data center(s). Data center failures happen due to power outages, cooling failures, network failures, and natural disasters. Dynamo is configured such that each object is replicated across multiple data centers. In essence, the preference list of a key is constructed such that the storage nodes are spread across multiple data centers. These datacenters are connected through high speed network links. This scheme of replicating across multiple datacenters allows us to handle entire data center failures without a data outage.

Dynamo’s local persistence component allows for different storage engines to be plugged in. Engines that are in use are Berkeley Database (BDB) Transactional Data Store2, BDB Java Edition, MySQL, and an in-memory buffer with persistent backing store. The main reason for designing a pluggable persistence component is to choose the storage engine best suited for an application’s access patterns. For instance, BDB can handle objects typically in the order of tens of kilobytes whereas MySQL can handle objects of larger sizes. Applications choose Dynamo’s local persistence engine based on their object size distribution. The majority of Dynamo’s production instances use BDB Transactional Data Store.

## Bigtable: a Distributed Storage System for Structured Data

Bigtable is a distributed storage system for managing structured data that is designed to scale to a very large size: petabytes of data across thousands of commodity servers. Many projects at Google store data in Bigtable, including web indexing, Google Earth, and Google Finance. These applications place very different demands on Bigtable, both in terms of data size (from URLs to web pages to satellite imagery) and latency requirements (from backend bulk processing to realtime data serving).

Bigtable is used by more than sixty Google products and projects, including Google Analytics, Google Finance, Orkut, Personalized Search, Writely, and Google Earth. These products use Bigtable for a variety of demanding workloads, which range from throughput-oriented batch-processing jobs to latency-sensitive serving of data to end users. The Bigtable clusters used by these products span a wide range of configurations, from a handful to thousands of servers, and store up to several hundred terabytes of data.

Bigtable does not support a full relational data model; instead, it provides clients with a simple data model that supports dynamic control over data layout and format, and allows clients to reason about the locality properties of the data represented in the underlying storage. Data is indexed using row and column names that can be arbitrary strings. Bigtable also treats data as uninterpreted strings, although clients often serialize various forms of structured and semi-structured data into these strings. Clients can control the locality of their data through careful choices in their schemas. Finally, Bigtable schema parameters let clients dynamically control whether to serve data out of memory or from disk.

A Bigtable cluster is a set of processes that run the Bigtable software. Each cluster serves a set of tables. A table in Bigtable is a sparse, distributed, persistent multidimensional sorted map. Data is organized into three dimensions: rows, columns, and timestamps.

$(row:string, column:string, time:int64) → string$

We refer to the storage referenced by a particular row key, column key, and timestamp as a cell.
Rows are grouped together to form the unit of load balancing, and columns are grouped together to form the unit of access control and resource accounting.

**Rows**
Bigtable maintains data in lexicographic order by row key. The row keys in a table are arbitrary strings (currently up to 64KB in size, although 10-100 bytes is a typical size for most of our users). Every read or write of data under a single row key is serializable (regardless of the number of different columns being read or written in the row), a design decision that makes it easier for clients to reason about the system’s behavior in the presence of concurrent updates to the same row. In other words, **the row is the unit of transactional consistency in Bigtable**, which does not currently support transactions across rows.

Rows with consecutive keys are grouped into **tablets**, which form **the unit of distribution and load balancing. **As a result, reads of short row ranges are efficient and typically require communication with only a small number of machines. Clients can exploit this property by selecting their row keys so that they get good locality for their data accesses. For example, in Webtable, pages in the same domain are grouped together into contiguous rows by reversing the hostname components of the URLs. We would store data for [maps.google.com/index.html](http://maps.google.com/index.html) under the key com.google.maps/index.html. Storing pages from the same domain near each other makes some host and domain analyses more efficient.

**Columns**
Column keys are grouped into sets called **column families**, which form the **unit of access control**. All data stored in a column family is usually **of the same type **(we compress data in the same column family together). A column family must be created explicitly before data can be stored under any column key in that family. **After a family has been created, any column key within the family can be used: data can be stored under such a column key without affecting a table’s schema. Bigtable’s design choice here is intentionally schemaless at the column level, which has powerful implications for flexibility, scalability, and application design. **Column qualifiers (column keys): Are not predefined — any string can be used at write time within a family. Once a family is defined, you can write any number of arbitrary columns within it.

A column key is named using the following syntax: family:qualifier. Column family names must be printable, but qualifiers may be arbitrary strings. We use only one column key with an empty qualifier in the language family to store each web page’s language ID.  **Access control **and both disk and memory accounting are performed at the column-family level.

**Timestamps**
Different cells in a table can contain multiple versions of the same data, where the versions are indexed by timestamp. Bigtable timestamps are 64-bit integers. They can be assigned implicitly by Bigtable, in which case they represent “real time” in microseconds, or they can be assigned explicitly
by client applications.

The Bigtable API provides functions for creating and deleting tables and column families. It also provides functions for changing cluster, table, and column family metadata, such as access control rights. Client applications can write or delete values in Bigtable, look up values from individual rows, or iterate over a subset of the data in a table.

### Details

The **Google SSTable immutable-file format **is used internally to store Bigtable data files. An SSTable provides a persistent, ordered immutable map from keys to values, where both keys and values are arbitrary byte strings. Operations are provided to look up the value associated with a specified key, and to iterate over all key/value pairs in a specified key range.

Bigtable relies on a highly-available and persistent distributed lock service called Chubby [Burrows 2006]. A Chubby service consists of five active replicas, one of which is elected to be the master and actively serve requests. The service is live when a majority of the replicas are running and can communicate with each other.

The master is responsible for assigning tablets to tablet servers, detecting the addition and expiration of tablet servers, balancing tablet-server load, and garbage collecting files in GFS. In addition, it handles schema changes such as table and column family creations and deletions.

Each tablet server manages a set of tablets (typically we have somewhere between ten to a thousand tablets per tablet server). The tablet server handles read and write requests to the tablets that it has loaded, and also splits tablets that have grown too large.

A Bigtable cluster stores a number of tables. Each table consists of a set of tablets, and each tablet contains all of the data associated with a row range. Tablets are a **higher-level construct** that use multiple **SSTables** (and memtables) internally to store row data. They define **what data is stored**, SSTables define **how it’s stored on disk**.

Initially, each table consists of just one tablet. As a table grows, it is automatically split into multiple tablets, each approximately 1 GB in size by default. Although our model supports arbitrary-sized data, the current Bigtable implementation does not support extremely large values well. Because a tablet cannot be split in the middle of a row, we recommend to users that a row contain no more than a few hundred GB of data.

**Locality Groups.
**Each column family is assigned to a client-defined locality group, which is an abstraction that enables clients to control their data’s storage layout. **A separate SSTable is generated for each locality group in each tablet during compaction**. Segregating column families that are not typically accessed together into separate locality groups enables more efficient reads.

In addition, some useful tuning parameters can be specified on a per-locality group basis. For example, a locality group can be declared to be in-memory. SSTables for in-memory locality groups are loaded lazily into the memory of the tablet server. Because SSTables are immutable, there are no consistency issues. Once loaded, column families that belong to such locality groups can be read without accessing the disk. This feature is useful for small pieces of data that are accessed frequently; we use it internally for the tablet-location column family in the METADATA table.

**Compression.**
Clients can control whether or not the SSTables for a locality group are compressed, and if so, which compression format is used. The user-specified compression format is applied to each SSTable block (whose size is controllable via a locality-group-specific tuning parameter). Although we lose some disk space by compressing each block separately, we benefit in that small portions of an SSTable can be read without decompressing the entire file. Many Bigtable clients use a two-pass custom compression scheme.

### Summary

Column keys are grouped into sets called **column families**, which form the **unit of access control**. All data stored in a column family is usually **of the same type **(we compress data in the same column family together). A column family must be created explicitly before data can be stored under any column key in that family. **After a family has been created, any column key within the family can be used: data can be stored under such a column key without affecting a table’s schema. Bigtable’s design choice here is intentionally schemaless at the column level, which has powerful implications for flexibility, scalability, and application design. **Column qualifiers (column keys): Are not predefined — any string can be used at write time within a family. Once a family is defined, you can write any number of arbitrary columns within it.

A column key is named using the following syntax: family:qualifier. Column family names must be printable, but qualifiers may be arbitrary strings. We use only one column key with an empty qualifier in the language family to store each web page’s language ID.  **Access control **and both disk and memory accounting are performed at the column-family level.

| **Component** | **Description** |
| --- | --- |
| **SSTable** | Immutable, ordered map of key → value pairs (keys/values = byte strings). Used as the **on-disk storage format**. Efficient for range scans and lookups. |
| **Chubby** | Distributed lock service (5 replicas, 1 master). Used for **coordination and metadata** (e.g., master election, tablet assignments). |
| **Master Server** | Assigns tablets to tablet servers, detects tablet server status, balances load, handles schema changes, and garbage collects SSTable files. |
| **Tablet Server** | Manages 10–1000+ tablets each. Handles **reads/writes**, splits large tablets (~1 GB+), and serves them. |
| **Table** | Top-level data container. Made of **tablets**, each covering a row key range. Starts as 1 tablet, splits as it grows. |
| **Tablet** | **Row-range partition** of a table (~1 GB). Cannot split a row across tablets, so **row size should be < a few hundred GB**. |
| **Row** | Atomic unit for operations. Contains data in multiple columns, across one or more column families. |
| **Column Family** | Logical group of columns with shared storage configuration (e.g., TTL, compression). **Stored together** for improved performance and locality. |

Bigtable’s design is centralized around **Chubby**, which acts as a **distributed lock service and reliable metadata store**. The hierarchy works like this:

- **Chubby** manages **master election** and stores critical configuration data.
- The **master** server, elected via Chubby, oversees Bigtable operations.
- The master manages the **METADATA** table, which keeps track of tablet locations.
- **Tablet servers** use the METADATA table to find and serve data tablets.

Bigtable uses a **three-level hierarchy **analogous to that of a B-tree to store tablet location information. The first level is a file stored in **Chubby **that contains the location of the **root tablet**. The root tablet contains the locations of all of the tablets of a special **METADATA table**. Each METADATA tablet contains the location of a set of **user tablets**. The root tablet is treated specially—it is never split—to ensure that the tablet location hierarchy has no more than three levels.

The METADATA table stores the location of a tablet under a row key that is an encoding of the tablet’s table identifier and its end row. The client library traverses the location hierarchy to locate tablets, and caches the locations that it finds. If the client does not know the location of a tablet, or if it discovers that cached location information is incorrect, then it recursively moves up the tablet location hierarchy.

## Cassandra - A Decentralized Structured Storage System

Cassandra is a distributed storage system for managing very large amounts of structured data spread out across many commodity servers, while providing highly available service with no single point of failure. Cassandra aims to run on top of an infrastructure of hundreds of nodes (possibly spread across different data centers). At this scale, small and large components fail continuously.
The way Cassandra manages the persistent state in the face of these failures drives the reliability and scalability of the software systems relying on this service. While in many ways Cassandra resembles a database and shares many design and implementation strategies therewith, Cassandra does not support a full relational data model; instead, it provides clients with a simple data model that supports dynamic control over data layout and format. Cassandra system was designed to run on
cheap commodity hardware and handle high write throughput while not sacrificing read efficiency.

**A table in Cassandra is a distributed multi dimensional map indexed by a key. **The value is an object which is highly structured. The row key in a table is a string with no size restrictions, although typically 16 to 36 bytes long. **Every operation under a single row key is atomic per replica **no matter how many columns are being read or written into. Columns are grouped together into sets called **column families **very much similar to what happens in the Bigtable[4] system. Cassandra exposes two kinds of columns families, **Simple and Super column families**. Super column families can be visualized as a column family within a column family.

Typically applications use a dedicated Cassandra cluster and manage them as part of their service. Although the system supports the notion of multiple tables **all deployments have only one table in their schema**.

The Cassandra API consists of the following three simple
methods.

- insert(table, key, rowMutation)
- get(table, key, columnName)
- delete(table, key, columnName)

Typically a read/write request for a key gets routed to any node in the Cassandra cluster. The node then determines the replicas for this particular key. For writes, the system routes the requests to the replicas and waits for a quorum of replicas to acknowledge the completion of the writes. For reads, based on the consistency guarantees required by the client, the system either routes the requests to the closest replica or routes the requests to all replicas and waits for a quorum of responses.

Cassandra partitions data across the cluster using **consistent hashing **[11] but uses an order pre-
serving hash function to do so. Typically there exist two ways to address this issue: One is for nodes to get assigned to multiple positions in the circle (like in Dynamo), and the second is to analyze load information on the ring and have lightly loaded nodes move on the ring to alleviate heavily loaded
nodes as described in [17]. Cassandra opts for the latter as it makes the design and implementation very tractable and helps to make very deterministic choices about load balancing.

Cassandra morphs all writes to disk into sequential writes thus maximizing disk write throughput.
Since the files dumped to disk are never mutated no locks need to be taken while reading them. The server instance of Cassandra is practically lockless for read/write operations. Hence we do not need to deal with or handle the concurrency issues that exist in B-Tree based database implementations.

In Cassandra, **SSTables **(Sorted Strings Table) are the **immutable data files that store data persistently on disk**. They are the core storage mechanism for Cassandra, acting as the on-disk representation of data that was initially in [memtables](https://www.google.com/search?rlz=1C1VDKB_enCA1037CA1037&cs=0&sca_esv=69b583eab287adc9&sxsrf=AE3TifPzaRFZ4ymGYM5vtlOGyhrGY7ztrQ%3A1753843986330&q=memtables&sa=X&ved=2ahUKEwjfkob-yeOOAxXFGLkGHachKOkQxccNegQIBRAB&mstk=AUtExfCvSS6aYvUM5pbtCmHZT1AufrGeByE4ftDlH6sY8arH0zYTZedP0BNBjJMfPbifqsUSSffkXXobvKysbKVl7xzOrV3n8n2gRhtJU_UvEX0C5VK0j07tpHfkHU7KF5BOPEO9Vp54JUwJyT4pRHb_WSwBd9IIcvp-u1TFsFON2ihYqPrS_hP4WqTIQwS8xC2xjig3p0vUS8ZntJM3mCXfHvzViyNJtPi2RBKjAvMKQS8O2zFypD1KMqCW1bZjV6hEZYpA4_vz03RQsxFfWKomf70d&csui=3). SSTables are not modified after creation; instead, when data changes, new SSTables are written with the updated information. Compaction is the process that merges and compacts SSTables, discarding outdated data and tombstones, to maintain database health and performance.

### C-Store: A Column-oriented DBMS

This paper presents the design of a read-optimized relational DBMS that contrasts sharply with most
current systems, which are write-optimized. Among the many differences in its design are:
storage of data by column rather than by row, careful coding and packing of objects into storage
including main memory during query processing, storing an overlapping collection of column-oriented projections, rather than the current fare of tables and indexes, a non-traditional implementation of transactions which includes high availability and snapshot isolation for read-only
transactions, and the extensive use of bitmap indexes to complement B-tree structures.

Most major DBMS vendors implement **record-oriented storage systems**, where the attributes of a record (or tuple) are placed contiguously in storage. With this row store architecture, a single disk write suffices to push all of the fields of a single record out to disk. Hence, high performance writes are achieved, and we call a DBMS with a row store architecture a **write-optimized system.** These are especially effective on OLTP-style applications.

In contrast, systems oriented toward ad-hoc querying of large amounts of data should be **read-optimized**. Data warehouses represent one class of read-optimized system, in which periodically a bulk load of new data is performed, followed by a relatively long period of ad-hoc queries. In such environments, a column store architecture, in which the values for each single column (or attribute) are stored contiguously, should be more efficient.

With a column store architecture, a DBMS need only read the values of columns required for processing a given query, and can avoid bringing into memory irrelevant attributes. In warehouse environments where typical queries involve aggregates performed over large numbers of data items, a column store has a sizeable performance advantage.

CPUs are getting faster at a much greater rate than disk bandwidth is increasing. Hence, it makes sense to trade CPU cycles, which are abundant, for disk bandwidth, which is not. This tradeoff appears especially profitable in a read-mostly environment.

Commercial relational DBMSs store complete tuples of tabular data along with auxiliary B-tree indexes on attributes in the table. Such indexes can be primary, whereby the rows of the table are stored in as close to sorted order on the specified attribute as possible, or secondary, in which case no attempt is made to keep the underlying records in order on the indexed attribute. Such indexes are effective in an OLTP write-optimized environment but do not perform well in a read-optimized world. In the latter case, other data structures are advantageous, including bit map indexes [ONEI97], cross table indexes [ORAC04], and materialized views [CERI91]. In a read-optimized DBMS one can explore storing data using only these read-optimized structures, and not support write-optimized ones at all.

C-Store physically stores a collection of columns, each sorted on some attribute(s). Groups of
columns sorted on the same attribute are referred to as “projections”; the same column may exist in multiple projections, possibly sorted on a different attribute in each. C-Store horizontally partitions data across the disks of the various nodes in a “shared nothing” architecture, is essential to allocate data structures to grid/cluster nodes automatically . In addition, intra-query parallelism is facilitated by horizontal partitioning of stored data structures.

C-Store allows redundant objects to be stored in different sort orders providing higher retrieval performance in addition to high availability. In general, storing overlapping projections further improves performance, as long as redundancy is crafted so that all data can be accessed even if one of the G sites fails.

At the top level, there is a small Writeable Store (WS) component, which is architected to support high performance inserts and updates. There is also a much larger component called the Read-optimized Store (RS), which is capable of supporting very large amounts of information. RS, as the name implies, is optimized for read and supports only a very restricted form of insert, namely the batch movement of records from WS to RS

In order to support a high-speed tuple mover, C-Store uses a variant of the LSM-tree concept [ONEI96], which supports a merge out process that moves tuples from WS to RS in bulk by an efficient method of merging ordered WS data objects with large RS blocks, resulting in a new copy of RS that is installed when the operation completes.
