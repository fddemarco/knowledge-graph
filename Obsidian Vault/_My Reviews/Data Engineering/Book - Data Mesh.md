---
base: "[[Reading List.base]]"
Category:
  - Data Engineering
  - Management
  - Data Architecture
Author: Zhamak Dehghani
Status: Not started
---
[[Domain Driven Design (DDD)]]
[[Book - Inspired]]
[[Book - Thinking in Systems]]
[[Book - Fundamentals of Software Architecture]]
[[Book - The Design of Everyday Things]]

## What is Data Mesh?

**Data mesh** is a sociotechnical paradigm: an approach that recognizes the interactions between people and the technical architecture and solutions in complex organizations. Data mesh can be utilized as an element of an enterprise data strategy, articulating the target state of both the enterprise architecture and an organizational operating model with an iterative execution model.
In the simplest form it can be described through four interacting principles. 

Data mesh calls for a fundamental shift in the assumptions, architecture, technical
solutions, and social structure of our organizations, in how we manage, use, and own
analytical data:

- **Organizationally**, it shifts from centralized ownership of data by specialists who run the data platform technologies to a decentralized data ownership model pushing ownership and accountability of the data back to the business domains where data is produced from or is used.
- **Architecturally**, it shifts from collecting data in monolithic warehouses and lakes to connecting data through a distributed mesh of data products accessed through standardized protocols.
- **Operationally**, it shifts data governance from a top-down centralized operational model with human interventions to a federated model with computational policies embedded in the nodes on the mesh.
- **Infrastructurally**, it shifts from two sets of fragmented and point-to-point integrated infrastructure services—one for data and analytics and the other for applications and operational systems to a well-integrated set of infrastructure for both operational and data systems.

Four simple **principles** can capture what underpins data mesh’s logical architecture and operating model. These principles are designed to progress us toward the objectives of data mesh: increase value from data at scale, sustain agility as an organization grows, and embrace change in a complex and volatile business context.

- **Domain Ownership**. Decentralize the ownership of analytical data to business domains closest to the data—either the source of the data or its main consumers. Decompose the (analytical) data logically and based on the business domain it represents, and manage the life cycle of domain-oriented data independently.
- **Data as product**. Data as a product introduces a new unit of logical architecture called data quantum, controlling and encapsulating all the structural components needed to share  data as a product—data, metadata, code, policy, and declaration of infrastructure dependencies—autonomously. Data as a product adheres to a set of usability characteristics:
	- Discoverable
	- Addressable
	- Understandable
	- Trustworthy and truthful
	- Natively accessible
	- Interoperable and composable
	- Valuable on its own
	- Secure
- **Self-serve Data Platform**. This principle leads to a new generation of self-serve data platform services that empower domains’ cross-functional teams to share data. The motivations of the self-serve data platform are to:
	- Reduce the total cost of decentralized ownership of data.
	- Abstract data management complexity and reduce the cognitive load of domain teams in managing the end-to-end life cycle of their data products.
	- Mobilize a larger population of developers—technology generalists—to embark on data product development and reduce the need for specialization.
	- Automate governance policies to create security and compliance standards for all data products.
- **Federated Computational Governance**. This principle creates a data governance operating model based on a federated decision-making and accountability structure, with a team composed of domain representatives, data platform, and subject matter experts—legal, compliance, security, etc. The governance execution model heavily relies on codifying and automating the policies at a fine-grained level, for every data product, via the platform services.

### Domain Ownership

Data mesh, at its core, is founded in decentralization and distribution of data responsibility to people who are closest to the data. This is to support a scale-out structure and continuous and rapid change cycles.

Domain-driven design is an approach to decomposition of software design (models) and team allocation, based on the seams of a business. It decomposes software based on how a business decomposes into domains, and models the software based on the language used by each business domain.

Eric Evans introduced the concept in his book **Domain-Driven Design** in 2003. In the time since, DDD has deeply influenced modern architectural thinking and, consequently, organizational modeling. DDD was a response to the rapid growth of software design complexity that stemmed from the digitization of businesses. DDD defines a domain as “a sphere of knowledge, influence, or activity.” In the Daff example, the listener subscription domain has the knowledge of what events happen during the subscription or unsubscription, what the rules governing the subscription are, what data is generated during the subscription events, and so on.

The closest application of DDD in data platform architecture I have seen is for source operational systems to emit their business **domain events** and for the monolithic data platform to ingest them. However, beyond the point of ingestion the domain team’s responsibility ends, and data responsibilities are transferred to the data team.

Eric Evans introduces a set of complementary strategies to scale modeling at the enterprise level called DDD’s Strategic Design. These strategies are designed for organizations with complex domains and many teams. DDD’s Strategic Design techniques move away from the previously used **modes of modeling and ownership**:

- **Organizational-level central modeling**. Eric Evans observed that total unification of the domain models of the organization into one is neither feasible nor cost-effective. This is similar to the data  warehouse approach to data modeling, with tightly dependent shared schemas. Centralized modeling leads to organizational bottlenecks for change.
- **Silos of internal models with limited integration**. This mode introduces cumbersome interteam communications. This is similar to data silos in different applications, connected via brittle extract, transform, load procedures (ETLs).
- **No intentional modeling**. This is similar to a data lake, dumping raw data into blob storage.

DDD’s Strategic Design embraces modeling based on multiple models each contextualized to a particular domain, called a **bounded context**. A bounded context is *“the delimited applicability of a particular model that gives team members a clear and shared understanding of what has to be consistent and what can develop independently.*”
Additionally, DDD introduces **context mapping**, which explicitly defines the relationship between bounded contexts. Data mesh data products are inspired on bounded contexts— data, its models, and its ownership.

When we map the data mesh to an organization and its domains, we discover a few different **archetypes** of domain-oriented analytical data. There are three archetypes of domain-oriented data:
- **Source-aligned domain data**. Analytical data reflecting the business facts generated by the operational systems. This is also called a native data product. The business facts are best presented as business **domain events** and can be stored and served as distributed logs of time-stamped events. In addition to timed events, source-aligned domain data often needs to be provided in easily consumable historical slices, aggregated over a time interval that closely reflects the interval of change in the business domain. For example, in the listener domain, a daily aggregate of the listener profiles is a reasonable model for analytical usage.
- **Aggregate domain data**. Analytical data that is an aggregate of multiple upstream domains. There is never a one-to-one mapping between a core concept of a business and a source system at an enterprise scale. There are often many systems that can serve parts of the data that belong to a shared business concept. Hence there might be a lot of source-aligned data that ultimately needs to be aggregated into a more aggregate form of a concept.
- I strongly caution you against creating ambitious aggregate domain data—aggregate domain data that attempts to capture all facets of a particular concept, like listener 360, and serve many organization-wide data users. Such aggregates can become too complex and unwieldy to manage, difficult to understand and use for any particular use case, and hard to keep up-to-date. In the past, the implementation of **Master Data Management** (MDM) has attempted to aggregate all facets of shared data assets in one place and in one model. This is a move back to single monolithic schema modeling  that doesn’t scale. Data mesh proposes that end consumers compose their own fit-for-purpose data aggregates and resist the temptation of highly reusable and ambitious aggregates.
- **Consumer-aligned domain data.** Consumer-aligned domain data, and the teams that own it, aim to satisfy **one or a small group of closely related use cases**. For example, recommendations are created as fit-for-purpose data that is presented to listeners while they interact with the player app. Engineered features to train machine learning models often fall into this category.

In traditional data architectures, **data pipelines** are first-class architectural concerns that compose more complex data transformation and movement. In data mesh, a data pipeline is simply an internal implementation of the data domain and is handled **internally within the domain**. As a result, when transitioning to data mesh, you will be redistributing different pipelines and their jobs to different domains.

Don’t try to design the **domains** of a business up front and allocate and model analytical data according to that. Instead, start working with the seams of your business as they are. If your business is already organized based on domains, start there. If not, perhaps data mesh is not the right solution just yet.

### Data as Product

One long-standing challenge of existing analytical data architectures is the high friction and cost of using data: discovering, understanding, trusting, exploring, and ultimately consuming quality data. If not addressed, this problem only **exacerbates** with data mesh, as the number of places and teams who provide data, i.e., domains, increases. Further **data siloing** and regression of data usability are potential undesirable consequences of data mesh’s first principle, domain-oriented ownership. The principle of **data as a product** addresses these concerns.
Data as a product expects that the analytical data provided by the domains is treated as a product, and the consumers of that data should be treated as customers— happy and pleased.

In his book INSPIRED, Marty Cagan, a prominent thought leader in product development and management, provides convincing evidence on how successful products have three common characteristics: they are **feasible, valuable**, and **usable**. Data as a product embodies standardized characteristics to make data valuable and usable.

Compared to past paradigms, data as a product inverts the model of responsibility. In data lake or data warehousing architectures the accountability of creating data with quality and integrity resides downstream from the source and remains with the centralized data team. Data mesh shifts this responsibility close to the source of the data. This transition is not unique to data mesh; in fact, over the last decade we have seen the trend of shift left with testing and operations, on the basis that addressing problems is cheaper and more effective when done close to the source.

Over the last decade, high-performing organizations have embraced the idea of treating their internal operational technology like a product, similarly to their external technology. They treat their internal developers as customers and their satisfaction a sign of success. Curiously, the magical ingredient of empathy, treating data as a product and its users as customers, has been missing in big data solutions. Operational teams still perceive their data as a byproduct of running the business, leaving it to someone else, e.g., the data team, to pick it up and recycle it into products.

- **Discoverable**. The very first step that data users take in their journey is to discover the world of data available to them and explore and search to find “the one.” Hence, one of the first usability attributes of data is to be easily discoverable.
	- A traditional implementation of discoverability is a centralized registry or catalog listing available datasets with some additional information about each dataset, the owners, the location, sample data, etc.
	- Data product discoverability on data mesh embraces a shift-left solution where the data product itself intentionally provides discoverability information. Each data product continuously shares its source of origin, owners, runtime information such as timeliness, quality metrics, sample datasets, and most importantly information contributed by their consumers such as the top use cases and applications enabled by their data.
- **Addressable**. A data product offers a permanent and unique address to the data user to programmatically or manually access it.
	- It must recognize that many aspects of data products will continue to change, while it assures continuity of usage.
		- **Semantic and syntax changes in data products**. Schema evolution
		- **Continuous release of new data over time (window)**. Partitioning strategy and grouping of data tuples associated with a particular time (or time window).
		- **Newly supported modes of access to data**. New ways of serializing, presenting, and querying the data
		- **Changing runtime behavioral information**. For example, service-level objectives, access logs, debug logs.
	- The data product must have an **addressable aggregate root** that serves as an entry to all information about a data product, including its documentation, service-level objectives, and the data it serves.
- **Understandable**. Each data product provides semantically coherent data: data with a specific meaning. A data user needs to understand this meaning: what kind of entities the data product encapsulates, what the relationships among the entities are, and their adjacent data products.
	- In addition to understanding the semantics, data users need to understand how exactly the data is presented to them.
		- They need to understand the schema of the underlying syntax of data.
		- Sample datasets and example consumer codes ideally accompany this information.
		- Formalized description of the data improve data users’ understanding. 
	- Understanding a usable data product requires no hand-holding. A self-serve method of understanding is a baseline usability characteristic.
	- Lastly, understanding is a social process. We learn from each other. Data products facilitate communication across their users to share their experience and how they take advantage of the data product.
- **Trustworthy and truthful**. No one will use a product that they can’t trust. So what does it mean to trust a data product, and more importantly what does it take to trust? To unpack this, I like to use the concept of trust offered by Rachel Botsman: the bridge between the known and the unknown.
	- A piece of closing the trust gap is to guarantee and communicate data products’ service-level objectives (SLOs)—objective measures that remove uncertainty surrounding the data.
		- **Interval of change**. How often changes in the data are reflected
		- **Timeliness**. The skew between the time that a business fact occurs and becomes available to the data users
		- **Completeness**. Degree of availability of all the necessary information
		- **Statistical shape of data** Its distribution, range, volume, etc.
		- **Lineage** The data transformation journey from source to here
		- **Precision and accuracy** over time. Degree of business truthfulness as time passes
		- **Operational qualities**. Freshness, general availability, performance
- **Natively accessible**. Depending on the data maturity of the organization there is a wide spectrum of data user personas in need of access to data products. A data product needs to make it possible for various data users to access and read its data in their native mode of access. This can be implemented as a polyglot storage of data or by building multiple read adapters on the same data.
- **Interoperable**. One of the main concerns in a distributed data architecture is the ability to correlate data across domains and stitch them together in wonderful and insightful ways: join, filter, aggregate. The key for an effective composability of data across domains is following standards and harmonization rules that allow linking data across domains easily.
	- **Field type**. A common explicitly defined type system
	- **Polysemes identifiers**. Universally identifying entities that cross boundaries of data products
	- **Common metadata fields**. Such as representation of time when data occurs and when data is recorded
	- **Schema linking**. Ability to link and reuse schemas—types—defined by other data products
	- **Data linking**. Ability to link or map to data in other data products 
	- **Schema stability**. Approach to evolving schemas that respects backward compatibility
- **Valuable on its own**. There is a common antipattern when migrating from a warehouse architecture to data mesh: directly mapping warehouse tables to data products can create data products with no value. In the data warehouse, there are glue (aka facts) tables that optimize correlation between entities. These are identity tables that map identifiers of one kind of entity to another. Such identity tables are not meaningful or valuable on their own—without being joined to other tables. They are simply mechanical implementations to facilitate joins.
	- Machine optimizations such as indices or fact tables must be automatically created by the platform and hidden from the product products.
- **Secure**. Data products follow the practice of security policy as code. This means to write security policies in a way that they can be versioned, automatically tested, deployed and observed, and computationally evaluated and enforced.
	- Access control
	- Encryption
	- Confidentiality levels
	- Data retention
	- Regulations and agreements

### Data Platform

The data mesh platform must close the gap between analytical and operational technologies. It must find ways to get them to work seamlessly together, in a way that is natural to a cross-functional domain-oriented data and application team.

Data mesh creates a clear delineation of responsibility between domain teams—who focus on creating business-oriented products, services that are ideally data-driven, and data products—and the platform teams who focus on technical enablers for the domains. This is different from the existing delineation of responsibility where the data team is often responsible for amalgamation of domain-specific data for analytical usage, as well as the underlying technical infrastructure.

Provisioning and managing the underlying infrastructure for life cycle management of a data product requires specialized knowledge of today’s tooling and is difficult to replicate in each domain. Hence, the data mesh platform must implement all necessary capabilities allowing a data product developer to build, test, deploy, secure, and maintain a data product without worrying about the underlying infrastructure resource provisioning. It must enable all domain-agnostic and cross-functional capabilities.

Ultimately, the platform must enable the data product developer to just focus on the
domain-specific aspects of data product development:
- Transformation code, the domain-specific logic that generates and maintains the data
- Build-time tests to verify and maintain the domain’s data integrity
- Runtime tests to continuously monitor that the data product meets its quality guarantees
- Developing a data product’s metadata such as its schema, documentation, etc.
- Declaration of the required infrastructure resources

### Data Governance

Governance is the mechanism that assures that the mesh of independent data products, as a whole, is secure, trusted, and most importantly delivers value through the interconnection of its nodes. In the past, governance has relied heavily on manual interventions, complex central processes of data validation and certification, and establishing global canonical modeling of data with minimal support for change, often engaged too late after the fact.

**Data mesh governance** embeds the computational policies in each and **every domain** and **data product** with autonomy and **domain-local decision-making power**, while creating and adhering to a set of global rules. The **global** **rules** are informed and enabled by global specializations such as legal and security to ensure a trustworthy, secure, and interoperable ecosystem. Data mesh calls this model of governance a **federated computational governance**.

One of the common concerns I hear from the existing governance teams is around “preventing **data product duplication and redundant work**,” basically controlling the chaos that may arise from each domain team making independent decisions and **creating duplicate data products**. This of course stems from scars they have incurred over the years, seeing teams copying data into many isolated and abandoned databases, each for a single use. Traditionally, this problem has been solved by injecting governance control structures that qualify and certify that the data is not a duplicate before it can be used. Data mesh introduces **feedback loops** to get the same outcome without creating bottlenecks.
The platform “search and discovery” feature can give lower visibility to the duplicate data products that don’t have high ratings; as a result, they get gradually degraded on the mesh and hence less used. The platform can inform the data product owners of the state of their data products and nudge them to prune out unused and duplicate data products in favor of others. This mechanism is called a **negative or balancing feedback loop**. Vice versa, there is also a **positive feedback loop** for highly used, highly rated data products.

Organizationally, by design, data mesh is a federation. It has an organizational structure with smaller divisions, the domains, where each has a fair amount of internal autonomy. The domains control and own their data products. They control how their data products are modeled and served. Despite the autonomy of the domains, there are a set of standards and global policies that all domains must adhere to as a prerequisite to be a member of the mesh. Data mesh proposes a governance operating model that benefits from federated decision making.

- **Federated team**. Composed of domain product owners, subject matter experts such as legal and security
- **Guiding values**. Managing the scope and guiding what good looks like
- **Policies**. Security, conformance, legal, and interoperability guidelines and standards governing the mesh
- **Incentives**. Leverage points that balance local and global optimization
- **Platform automations**. Protocols, standards, policies as code, automated testing, monitoring and recovery of the mesh governance

Data mesh leaves the modeling of data to the domains, the people closest to the data. However, in order to get interoperability and linkage between data across domains, there are data entities in each domain that need to be modeled in a **consistent fashion across all domains**. Such entities are called **polysemes**. Standardizing how polysemes are modeled, identified, and mapped across domains is a **global governance function**.

## Why Data Mesh?

Data mesh assumes a new default starting state: proliferation of data origins within and beyond organizations’ boundaries, on one or across multiple cloud platforms. It assumes a diverse range of use cases for analytical data. It works with the grain of a highly complex and volatile organizational environment, and not against it.

Today, we keep **copying data** around because we need the data for yet another mode of access, or yet another model of computation. We copy data from operational systems to a landing zone and then to the data lake and after that to feature stores for data scientists. We copy the data again from the lake into lakeshore marts for data analyst access and then into the downstream dashboard or reporting databases for the last mile. We build complex and brittle pipelines to do the copying.

Data mesh addresses this problem by creating a new architectural unit that encapsulates a domain-oriented data semantic while also providing **multiple modes of access** to the data suitable for different use cases and users. This architectural unit is called the **data product quantum** (data quantum for short). A data quantum has an explicit set of **contracts and guarantees** for each of its native access modes—SQL, files, events, etc. It can be accessed anywhere across the internet, in case it chooses to provide data to external data users. It provides **access control and policy enforcement** on each of its interfaces at the time of access. A data quantum encapsulates the code that transforms and maintains its data. Data pipelines break down and become internal implementations of data quantum logic. A data quantum shares data without the need for intermediary pipelines.

Data mesh looks critically at the existing technology landscape and reimagines the technology solutions as a **data-product-developer** (or user)-centric platform. It intends to remove the need for **data specialists** and enable **generalist experts** to develop data products. Additionally, data mesh defines a set of **open and standard interfaces** for different affordances that all data products share—discovering, requesting access, querying, serving data, securing data, etc.—to enable a more collaborative ecosystem of technologies. This is to reduce the cost of integration across vendors.

### Evolution of Data Architectures

**Data warehousing architecture** today is influenced by early concepts such as facts and dimensions. The architecture intends to move data from operational systems to business intelligence (BI) systems. BI systems have traditionally served management with operational planning of an organization. While data warehousing solutions have greatly evolved, many of the original characteristics and assumptions of their architectural model remain the same. Data is:

- Extracted from many operational databases and sources
- Transformed into a universal schema—represented in a multidimensional and time-variant tabular format
- Loaded into the warehouse tables
- Accessed through SQL-like queries
- Mainly serving data analysts for reporting and analytical visualization use cases

Data lake architecture was introduced in 2010 in response to the challenges of data warehousing architecture in satisfying the new uses of data: access to data by data scientists in the machine learning model training process. **Data lake architecture**, similar to a data warehouse, assumes that data gets extracted from operational systems and loaded into a central repository often in the format of an object store—storage of any type of data. However, unlike data warehousing, a data lake assumes very little or no transformation and modeling of the data up front; it attempts to retain the data close to its original form. Once the data becomes available in the lake, the architecture gets extended with elaborate transformation pipelines to model the higher value data and store it in lakeshore marts or feature stores at the edge of the lake. There are refinements to this approach, with allocated “zones” in the lake, storing data with varying degrees of cleansing and transformation, to organize the lake a bit better.

Data lake architecture suffers from complexity and deterioration. It creates complex and unwieldy pipelines of batch or streaming jobs operated by a central team of hyper-specialized data engineers. It deteriorates over time. Its unmanaged datasets, which are often untrusted and inaccessible, provide little value. The data lineage and dependencies are obscured and hard to track.

Despite the undeniable innovation, there are fundamental assumptions that have remained unchallenged for the last few decades and must be closely evaluated:

- Data must be centralized to be useful—managed by a centralized organization, with an intention to have an enterprise-wide taxonomy.
- Data management architecture, technology, and organization are monolithic.
- The enabling technologies drive the paradigm—architecture and organization.

At 30,000 feet, the data platform architecture is a monolithic architecture whose goal is to:

- Ingest data from all corners of the enterprise and beyond, ranging from operational and transactional systems in domains that run the business to external data providers that augment the knowledge of the enterprise.
- Cleanse, enrich, and transform the source data into trustworthy data that can address the needs of a diverse set of consumers.
- Serve datasets to a variety of consumers with a diverse set of needs. This ranges from data exploration and machine learning training to business intelligence reports.

Over the last decade we have successfully applied **domain-driven design** and bounded context to the design of our operational systems to manage software complexity at scale, we have largely disregarded domain-driven design paradigm for data. DDD’s strategic design introduces a set of principles to manage software modeling at scale in a large and complex organization. It encourages moving away from a **single canonical model** to many **bounded context models**. It assumes multiple models each owned and managed by a unit of organization. And it explicitly articulates the relationships between these models.

Looking back at different generations of analytical data architectures, from warehouse to lake and all on the cloud, we have heavily leaned on a **technology-driven architecture**. A typical solution architecture of a data management system merely wires together various technologies, each performing a technical function, a piece of the end-to-end storage and processing flow.

The core technologies listed in the figure are powerful and helpful in enabling a **data platform**. However, the proposed solution architecture decomposes and then integrates the components of the architecture based on their technical function and the solution supporting the function. For example, first we encounter the ingestion function supported by Cloud Pub/Sub and then publishing data to Cloud Storage, which then serves data through BigQuery. This approach leads to a **technically partitioned architecture** and consequently an **activity-oriented team decomposition**.

**Top-level technical partitioning** decomposes the system into its components based on their technical capabilities and concerns; it’s a decomposition that is closer to the implementation concerns than business domain concerns. Architects and leaders of monolithic data platforms have decomposed monolithic solutions based on a pipeline architecture into its technical functions such as ingestion, cleansing, aggregation, enrichment, and serving. An alternative approach is a **top-level domain-oriented partitioning**, where these technical functions are embedded in every domain, where the change to the domain can be managed locally without top-level synchronization.

The consequence of **top-level technical decomposition** is decomposing teams into activity-oriented groups, each focused on a particular activity required by a stage of the pipeline, for example, a team focusing on ingestion of data from various sources or a team responsible for serving the lakeshore marts. Each team attempts to **optimize their activity**, for example find patterns of ingestion. Though this model provides some level of scale, by assigning teams to different activities of the flow, it has an inherent limitation that does not scale what matters: delivery of outcome. **Delivering an outcome** demands synchronization between teams and aligning changes to the activities. Such decomposition is **orthogonal** to the axis of change or outcome, slows down the delivery of value, and introduces organizational friction. Conversely, an **outcome-oriented team decomposition** optimizes for an end-to-end outcome, delivered rapidly and with low synchronization overhead.

## How to Design the Data Mesh Architecture

To promote a domain-oriented decomposition of data ownership, we need to model an architecture that arranges the analytical data1 by domains. In this architecture, a domain’s interface to the rest of the organization not only includes the **operational capabilities** but also shares the **analytical data** that a domain generates and owns.

**Analytical interfaces** are the APIs that data products expose to get discovered, understood, and observed and to share their data. For analytical data sharing, the APIs may choose to redirect the clients to the underlying storage of the data, once the access policies are enforced. Depending on the data product’s implementation, the data sharing interface can redirect access to either a blob storage (e.g., Parquet files from AWS S3) or an event stream (e.g., Kafka Topic), or a table (e.g., BigQuery Table) or something completely different.

To implement such interfaces there is code involved in providing the necessary
information and serving them:

- **Output data APIs**. There is a set of collaborating APIs that share the output data of a data product in a way that can be understood and trusted. Output data APIs can receive and run remote queries on their data, for example an interface to run SQL queries on the underlying tables holding data. Output APIs share the data in multiple formats. For example, APIs read semi-structured files from blob storage or subscribe to an event stream.
- **Input data APIs**. These APIs are used internally by the data product to configure and read data from the upstream sources. They can be implemented as a subscription to upstream output data APIs and asynchronously receive data as it becomes available. They can be implemented as queries executed on an upstream source and read as data frames. The arrival of input data triggers the transformation code.
- **Discovery and observability APIs**. These APIs provide additional information about the data product to help with its discoverability and debugging. For example, a discovery API can provide information about the team maintaining the data products, tags that help to search for it, description of its semantic, etc.

The governance standardizes the definition of these interfaces across all data prod‐ ucts, and the platform provides the mechanisms to implement them.

In data mesh architecture, the **data dependency** is defined and controlled by a data product to its upstream source. A **data product** is in full control of what its sources are and what data it consumes from them and how.

An **architectural quantum**, defined in [[Book - Building Evolutionary Architectures]] and
later expanded in the [[Book - Software Architecture The Hard Parts]], is the smallest unit of architecture that can be independently deployed, has high functional cohesion, and includes all the “structural elements required for its function.” In the case of data mesh, a data product is an architectural quantum. It is the smallest unit of architecture that can be independently deployed and managed. It has high functional cohesion, i.e., performing a specific analytical transformation and securely sharing the result as domain-oriented analytical data. It has all the structural components that it requires to do its function: the transformation code, the data, the metadata, the policies that govern the data, and its dependencies to infrastructure.

At a high level, data products have three types of **structural components**: code, data (and metadata and configuration), and the specifications of its infrastructure dependencies.

- For a data product to independently control the life cycle of analytical data—maintain the business logic that generates it, control its revisions, manage access to it, and share its content—it must include **code** and run it within its computational context. This is a fundamental difference between what data mesh calls a data product and other forms of data artifacts. A data product is active while other data artifacts like files or tables are passive.
- In traditional architectures, this code lives outside of a data product, as a **data pipeline**. A pipeline is managed as an **independent artifact**, perhaps a DAG configuration. Its output data is managed independently, perhaps as a table in a warehouse. Data mesh removes the concept of an **external pipeline** and introduces the **internal transformation code**. This code may be implemented as a pipeline, or not. The important differentiating point is that the code is encapsulated as an **internal implementation of a data product**. Its life cycle is controlled by the data product, and it runs in the execution context allocated to the data product.

The last category of code encapsulated by a data product implementation is the code responsible for configuring and executing various behavioral and **structural policies**, such as encryption, access control, quality, and compliance. This code is provided by the platform but invoked in the flow to data product read and write functions.

For data to be usable, data products maintain and serve an associated set of information about the data—often called metadata. For example, this includes the data’s documentation, semantic and syntax declarations, and SLOs. The life cycle of metadata and how often it changes depends on its type. For example, data models—semantic or syntax—change less often and change at build time. However, most metadata changes as a data product generates new data, including statistical properties of the data, and its current SLO metric values.

There are two general approaches in designing such a platform: adopt a **single and tightly integrated platform**—often bought from a main vendor—or use a **set of loosely integrated services** that expose standard and open interfaces, often implemented as a combination of build and buy from different vendors. Data mesh platform design falls into the second camp. It favors breaking a complex platform into its capabilities, served through APIs, with open interfaces to integrate with other collaborating capabilities.
## How to Design the Data Product Architecture

In his book [[Book - The Design of Everyday Things]], Don Norman, the father of cognitive engineering, calls out affordances as one of the five fundamental principles of interactions. He describes:

>*The term affordance refers to the relationship between a physical object and a person (or for that matter, any interacting agent, whether animal or human, or even machines and robots). An affordance is a relationship between the properties of an object and the capabilities of the agent that determine just how the object could possibly be used. A chair affords (“is for”) support and, therefore, affords sitting. Most chairs can also be carried by a single person (they afford lifting), but some can only be lifted by a strong person or by a team of people. If young or relatively weak people cannot lift a chair, then for these people, the chair does not have that affordance, it does not afford lifting.*
> 
> *The presence of an affordance is jointly determined by the qualities of the object and the abilities of the agent that is interacting. This relational definition of affordance gives considerable difficulty to many people. We are used to thinking that properties are associated with objects. But affordance is not a property. An affordance is a relationship. Whether an affordance exists depends upon the properties of both the object and the agent.* 


Common mechanisms for implementing the input port for consuming data from collaborating operational systems include **asynchronous event-driven data sharing** in the case of modern systems, and **change data capture** for legacy systems that are difficult to change. Modern operational systems sharing their domain events is increasingly becoming a common practice and is a great model for feeding data to a collaborative data product. Change data capture is a set of integration patterns to discover and track changes to an application database, which then can be sourced as the input to the data product. This is the least desirable way to receive data from a collaborating operational system. It exposes the internal implementation of a database transaction and doesn’t correspond to a business domain. But sometimes it is the only available option in the case of legacy systems.

Data mesh leaves many of the implementation decisions to the underlying technology. Data mesh architecture is **agnostic of the underlying technology and infrastructure**—as much as possible. For example, where the data is physically located and whether a consuming data product physically copies data from one location to another are implementation details built by the platform. 

However, data mesh architecture components such as **input** and **output ports** are great interfaces to abstract the internal implementation of data or processing movement across physical boundaries. A simple set of APIs that **connect a data product’s input port to another’s output port** can hide the physical movement of data from one physical storage and trust boundary to another. Similarly, executing the data consumption request can execute a remote query issued from one computational environment to another.

The implication of this design is that a mesh can **span one or multiple physical infrastructures, multiple clouds, and on-prem hosting environments.** This means that when a data product consumes data from its source, in that process, it can physically move data from one underlying hosting environment to another. This seemingly simple capability can have a profound impact on data migration to the cloud and a multicloud data platform.

## How to Get Started

**Kent Beck’s “3X” model: Explore → Expand → Extract** ([The Product Development Triathlon](https://medium.com/@kentbeck_7670/the-product-development-triathlon-6464e2763c46)). You’ll often see it written with alternate labels like the ones you gave (bootstrap / scale / sustain), and it’s commonly overlaid on the technology adoption curve.

**The three phases**

- **Explore (bootstrap):** You don’t know yet what works. The goal is to find something valuable with as many cheap, fast experiments as possible. Most bets fail, and that’s fine. Speed of learning matters more than quality, efficiency or scalability. Heavy process, long-term architecture and big plans are waste here.
- **Expand (scale):** Something has taken off, and demand is growing faster than you can handle. The goal is to remove whatever bottleneck is about to break: infrastructure, onboarding, support, hiring. This phase is short, chaotic and reactive. You fix the next thing that’s breaking.
- **Extract (sustain):** Growth is predictable. The goal is profitability and efficiency: lower costs, higher margins, reliability, incremental improvements. Economies of scale, careful planning and solid engineering pay off here. This is where most traditional management practice comes from.

Beck’s key point is that each phase needs **different behaviors, metrics and even people**. A team running “Extract” habits (ROI cases, roadmaps, polish) in the Explore phase will kill ideas too early. A team running “Explore” habits in the Extract phase will be wasteful and unstable.

**How it maps to the adoption curve**

The early adopters / early majority / laggards terms come from Everett Rogers’ _Diffusion of Innovations_, which Geoffrey Moore built on in [[Book - Crossing the Chasm]]. The rough overlay:

- **Explore** → innovators and early adopters. You’re looking for anyone who’ll put up with something rough because it solves a real problem.
- **Expand** → crossing the chasm into the early majority. Adoption accelerates, and the product has to become usable by pragmatists.
- **Extract** → late majority and laggards. The market is mature, so the game is efficiency, retention and squeezing value from an established position.

A fitness function is an objective function used to summarize how close a prospective design solution is to achieving its set aims. In evolutionary computing, the fitness function determines whether an algorithm has improved over time. In [[_My Reviews/Software Design/Book - Building Evolutionary Architectures|Book - Building Evolutionary Architectures]], fitness functions objectively guide the iterations of an architecture toward its set objectives across multiple dimensions such as performance, security, extensibility, etc.
