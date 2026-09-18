[dbt](https://docs.getdbt.com/best-practices/how-we-style/1-how-we-style-our-dbt-models?version=2)

- Models should be pluralized, for example, `customers`, `orders`, `products`.
- Each model should have a primary key.
- The primary key of a model should be named `<object>_id`, for example, `account_id`. This makes it easier to know what `id` is being referenced in downstream joined models.
- Use underscores for naming dbt models; avoid dots.
    - Most data platforms use dots to separate `database.schema.object`, so using underscores instead of dots reduces your need for [quoting](https://docs.getdbt.com/reference/resource-properties/quoting) as well as the risk of issues in certain parts of dbt. For more background, refer to [this GitHub issue](https://github.com/dbt-labs/dbt/issues/3246).
- Keys should be **string data types**.
- Consistency is key! Use the same field names across models where possible. For example, a key to the `customers` table should be named `customer_id` rather than `user_id` or 'id'.
- Do not use abbreviations or aliases. Emphasize readability over brevity. For example, do not use `cust` for `customer` or `o` for `orders`.
- Avoid reserved words as column names.
- Booleans should be prefixed with `is_` or `has_`.
- Timestamp columns should be named `<event>_at`(for example, `created_at`) and should be in UTC. If a different timezone is used, this should be indicated with a suffix (`created_at_pt`).
- Dates should be named `<event>_date`. For example, `created_date.`
- Events dates and times should be past tense — `created`, `updated`, or `deleted`.
- Price/revenue fields should be in decimal currency (`19.99` for $19.99; many app databases store prices as integers in cents). If a non-decimal currency is used, indicate this with a suffix (`price_in_cents`).
- Schema, table and column names should be in `snake_case`.
- Use names based on the _business_ terminology, rather than the source terminology. For example, if the source database uses `user_id` but the business calls them `customer_id`, use `customer_id` in the model.
- Versions of models should use the suffix `_v1`, `_v2`, etc for consistency (`customers_v1` and `customers_v2`).
- Use a consistent ordering of data types and consider grouping and labeling columns by type, as in the example below. This will minimize join errors and make it easier to read the model, as well as help downstream consumers of the data understand the data types and scan models for the columns they need. We prefer to use the following order: ids, strings, numerics, booleans, dates, and timestamps.

### Naming Conventions 

In working on this project, we established some conventions for naming our models.

- **Sources** (`src`) refer to the raw table data that have been built in the warehouse through a loading process. (We will cover configuring Sources in the Sources module)
- **Staging** (`stg`) refers to models that are built directly on top of sources. These have a one-to-one relationship with sources tables. These are used for very light transformations that shape the data into what you want it to be. These models are used to clean and standardize the data before transforming data downstream. Note: These are typically materialized as views.
- **Intermediate** (`int`) refers to any models that exist between final fact and dimension tables. These should be built on staging models rather than directly on sources to leverage the data cleaning that was done in staging.
- **Fact** (`fct`) refers to any data that represents something that occurred or is occurring. Examples include sessions, transactions, orders, stories, votes. These are typically skinny, long tables.
- **Dimension** (`dim`) refers to data that represents a person, place or thing. Examples include customers, products, candidates, buildings, employees.
- Note: The Fact and Dimension convention is based on previous normalized modeling techniques.

The following framework can be a starting part for designing your own model organization:
- **Marts** folder: All intermediate, fact, and dimension models can be stored here. Further subfolders can be used to separate data by business function (e.g. marketing, finance)
- **Staging** folder: All staging models and source configurations can be stored here. Further subfolders can be used to separate data by data source (e.g. Stripe, Segment, Salesforce). (We will cover configuring Sources in the Sources module)

### File names

Creating a consistent pattern of file naming is [crucial in dbt](https://docs.getdbt.com/blog/on-the-importance-of-naming). File names must be unique and correspond to the name of the model when selected and created in the warehouse. We recommend putting as much clear information into the file name as possible, including a prefix for the layer the model exists in, important grouping information, and specific information about the entity or transformation in the model.
    - ✅ `stg_[source]__[entity]s.sql` - the double underscore between source system and entity helps visually distinguish the separate parts in the case of a source name having multiple words. For instance, `google_analytics__campaigns` is always understandable, whereas to somebody unfamiliar `google_analytics_campaigns` could be `analytics_campaigns` from the `google` source system as easily as `campaigns` from the `google_analytics` source system. Think of it like an [oxford comma](https://www.youtube.com/watch?v=P_i1xk07o4g), the extra clarity is very much worth the extra punctuation. In a single-source project like Jaffle Shop, `stg_orders.sql` and `stg_customers.sql` are clear enough without the source prefix.
    - ❌ `stg_[entity].sql` - might be specific enough at first for a single-source project, but will break down in time as you add sources. Adding the source system into the file name aids in discoverability, and allows understanding where a component model came from even if you aren't looking at the file tree.
    - ✅ **Plural.** SQL, and particularly SQL in dbt, should read as much like prose as we can achieve. We want to lean into the broad clarity and declarative nature of SQL when possible. As such, unless there’s a single order in your `orders` table, plural is the correct way to describe what is in a table with multiple rows.

### Folders

Folder structure is extremely important in dbt. Not only do we need a consistent structure to find our way around the codebase, as with any software project, but our folder structure is also one of the key interfaces for understanding the knowledge graph encoded in our project (alongside the DAG and the data output into our warehouse). It should reflect how the data flows, step-by-step, from a wide variety of source-conformed models into fewer, richer business-conformed models. Moreover, we can use our folder structure as a means of selection in dbt [selector syntax](https://docs.getdbt.com/reference/node-selection/syntax). For example, with the above structure, if we got fresh e-commerce data loaded and wanted to run all the models that build on our staging layer, we can easily run `dbt build --select staging+` and we're all set for building more up-to-date reports.
    - ✅ **Subdirectories based on the source system**. Our internal transactional database is one system, the data we get from Stripe's API is another, and lastly the events from our Snowplow instrumentation. We've found this to be the best grouping for most companies, as source systems tend to share similar loading methods and properties between tables, and this allows us to operate on those similar sets easily. The Jaffle Shop example project uses a single `ecom` source, so its staging models live in a flat `staging/` folder. As you add more source systems, create a subdirectory per source.
    - ❌ **Subdirectories based on loader.** Some people attempt to group by how the data is loaded (Fivetran, Stitch, custom syncs), but this is too broad to be useful on a project of any real size.
    - ❌ **Subdirectories based on business grouping.** Another approach we recommend against is splitting up by business groupings in the staging layer, and creating subdirectories like 'marketing', 'finance', etc. A key goal of any great dbt project should be establishing a single source of truth. By breaking things up too early, we open ourselves up to creating overlap and conflicting definitions (think marketing and financing having different fundamental tables for orders). We want everybody to be building with the same set of atoms, so in our experience, starting our transformations with our staging structure reflecting the source system structures is the best level of grouping for this step.

```sh
models/staging  
├── __sources.yml  
├── stg_customers.sql  
├── stg_customers.yml  
├── stg_locations.sql  
├── stg_locations.yml  
├── stg_order_items.sql  
├── stg_order_items.yml  
├── stg_orders.sql  
├── stg_orders.yml  
├── stg_products.sql  
├── stg_products.yml  
├── stg_supplies.sql  
└── stg_supplies.yml
```

## Transformations

### Sources
Source properties can be declared in any `properties.yml` file in your `models/` directory (as defined by the [`model-paths` config](https://docs.getdbt.com/reference/project-configs/model-paths)). Source properties are "special properties" in that you can't configure them in the `dbt_project.yml` file or using `config()` blocks. Refer to [Configs and properties](https://docs.getdbt.com/reference/define-properties#which-properties-are-not-also-configs) for more info.  

You can name these files `whatever_you_want.yml`, and nest them arbitrarily deeply in subfolders within the `models/` directory:

Sources are defined in `.yml` files nested under a `sources:` key. models/.yml.

```yaml
sources:
- name: jaffle_shop
  database: raw
  schema: jaffle_shop
  tables:
	- name: orders
	- name: customers
- name: stripe
  tables:
	  - name: payments
```

By default, schema will be the same as name. Add` schema` only if you want to use a ource name that differs from the existing schema.

- Use these files to define sources and associated tests on sources (raw data). Consistently name them using the _src_ prefix (e.g., _src_sales.yml).    
- Models can be generated directly from within the sources YAML listing all the available fields by clicking the Generate Model prompt.

You can also:

- Add data tests to sources
- Add descriptions to sources, that get rendered as part of your documentation site

### Staging

The most standard types of staging model transformations are:
- ✅ **Renaming**
- ✅ **Type casting**
- ✅ **Basic computations** (e.g. cents to dollars)
- ✅ **Categorizing** (using conditional logic to group values into buckets or booleans)
- ❌ **Joins** — the goal of staging models is to clean and prepare individual source-conformed concepts for downstream usage. We're creating the most useful version of a source system table, which we can use as a new modular component for our project. In our experience, joins are almost always a bad idea here — they create immediate duplicated computation and confusing relationships that ripple downstream. Sometimes, in order to maintain a clean and DRY staging layer we do need to implement some joins to create a solid concept for our building blocks. In these cases, we recommend creating a sub-directory in the staging directory for the source system in question and building `base` models. These have all the same properties that would normally be in the staging layer, they will directly source the raw data and do the non-joining transformations, then in the staging models we'll join the requisite base models. Common use cases include joining in separate delete tables or union disparate but symmetrical sources
- ❌ **Aggregations** — aggregations entail grouping, and we're not doing that at this stage. Remember - staging models are your place to create the building blocks you’ll use all throughout the rest of your project — if we start changing the grain of our tables by grouping in this layer, we’ll lose access to source data that we’ll likely need at some point. We just want to get our individual concepts cleaned and ready for use, and will handle aggregating values downstream.
- ✅ **Materialized as views.** Looking at a partial view of our `dbt_project.yml` below, we can see that we’ve configured the entire staging directory to be materialized as views. As they’re not intended to be final artifacts themselves, but rather building blocks for later models, staging models should typically be materialized as views for two key reasons:
    
    - Any downstream model (discussed more in [marts](https://docs.getdbt.com/best-practices/how-we-structure/4-marts)) referencing our staging models will always get the freshest data possible from all of the component views it’s pulling together and materializing
        
    - It avoids wasting space in the warehouse on models that are not intended to be queried by data consumers, and thus do not need to perform as quickly or efficiently
        
        ```
        # dbt_project.ymlmodels:  jaffle_shop:    staging:      +materialized: view
        ```
        
- Staging models are the only place we'll use the [`source` macro](https://docs.getdbt.com/docs/build/sources), and our staging models should have a 1-to-1 relationship to our source tables. That means for each source system table we’ll have a single staging model referencing it, acting as its entry point — _staging_ it — for use downstream.

### Intermediate
In a project that uses an intermediate layer, models typically live in an `intermediate/` subdirectory:

```
models/intermediate
└── finance
├── _int_finance__models.yml
└── int_orders_summed_to_customer.sql
```

- **Folders**
    - ✅ **Subdirectories based on business groupings.** Much like the staging layer, we'll house this layer of models inside their own `intermediate` subfolder. Unlike the staging layer, here we shift towards being business-conformed, splitting our models up into subdirectories not by their source system, but by their area of business concern.
- **File names**
    - `✅ int_[entity]s_[verb]s.sql` - the variety of transformations that can happen inside of the intermediate layer makes it harder to dictate strictly how to name them. The best guiding principle is to think about _verbs_ (e.g. `pivoted`, `aggregated_to_user`, `joined`, `fanned_out_by_quantity`, `funnel_created`, etc.) in the intermediate layer. In a larger project, you might use an intermediate model to aggregate order items to the order grain, and name it `int_order_items_summed_to_orders`. It's easy for anybody to quickly understand what's happening in that model, even if they don't know [SQL](https://mode.com/sql-tutorial/). That clarity is worth the long file name. It's important to note that we've dropped the double underscores at this layer. In moving towards business-conformed concepts, we no longer need to separate a system and an entity and simply reference the unified entity if possible. In cases where you need intermediate models to operate at the source system level (e.g. `int_shopify__orders_summed`, `int_core__orders_summed` which you would later union), you'd preserve the double underscores. Some people like to separate the entity and verbs with double underscores as well. That's a matter of preference, but in our experience, there is often an intrinsic connection between entities and verbs in this layer that make that difficult to maintain.

Intermediate models serve a clear, single purpose: preparing staging models for marts. Common patterns include grouping and pivoting to a different grain, fanning out rows, or isolating complex logic so marts stay readable.

- ❌ **Exposed to end users.** Intermediate models should generally not be exposed in the main production schema. They are not intended for output to final targets like dashboards or applications, so it's best to keep them separated from models that are so you can more easily control data governance and discoverability.
- ✅ **Materialized ephemerally.** Considering the above, one popular option is to default to intermediate models being materialized [ephemerally](https://docs.getdbt.com/docs/build/materializations#ephemeral). This is generally the best place to start for simplicity. It will keep unnecessary models out of your warehouse with minimum configuration. Keep in mind though that the simplicity of ephemerals does translate a bit more difficulty in troubleshooting, as they're interpolated into the models that `ref` them, rather than existing on their own in a way that you can view the output of.
- ✅ **Materialized as views in a custom schema with special permissions.** A more robust option is to materialize your intermediate models as views in a specific [custom schema](https://docs.getdbt.com/docs/build/custom-schemas), outside of your main production schema. This gives you added insight into development and easier troubleshooting as the number and complexity of your models grows, while remaining easy to implement and taking up negligible space.

Some of the most common use cases of intermediate models include:

- ✅ **Structural simplification.** Bringing together a reasonable number (typically 4 to 6) of entities or concepts (staging models, or perhaps other intermediate models) that will be joined with another similarly purposed intermediate model to generate a mart — rather than have 10 joins in our mart, we can join two intermediate models that each house a piece of the complexity, giving us increased readability, flexibility, testing surface area, and insight into our components.
- ✅ **Re-graining.** Intermediate models are often used to fan out or collapse models to the right composite grain — if we're building a mart for `order_items` that requires us to fan out our `orders` based on the `quantity` column, creating a new single row for each item, this would be ideal to do in a specific intermediate model to maintain clarity in our mart and more easily view that our grain is correct before we mix it with other components.
- ✅ **Isolating complex operations.** It's helpful to move any particularly complex or difficult to understand pieces of logic into their own intermediate models. This not only makes them easier to refine and troubleshoot, but simplifies later models that can reference this concept in a more clearly readable way. For example, in the `quantity` fan out example above, we benefit by isolating this complex piece of logic so we can quickly debug and thoroughly test that transformation, and downstream models can reference `order_items` in a way that's intuitively easy to grasp.

### Marts

This is the layer where everything comes together and we start to arrange all of our atoms (staging models) into full-fledged cells that have identity and purpose. We sometimes like to call this the _entity_ _layer_ or _concept layer_, to emphasize that all our marts are meant to represent a specific entity or concept at its unique grain. For instance, an order, a customer, a territory, a click event, a payment — each of these would be represented with a distinct mart, and each row would represent a discrete instance of these concepts. Unlike in a traditional Kimball star schema though, in modern data warehousing — where storage is cheap and compute is expensive — we'll happily borrow and add any and all data from other concepts that are relevant to answering questions about the mart's core entity. Building the same data in multiple places, as we do with `orders` in our `customers` mart example below, is more efficient in this paradigm than repeatedly rejoining these concepts (this is a basic definition of denormalization in this context). Let's take a look at how we approach this first layer intended expressly for exposure to end users.

The marts layer in our example project contains one model per core business entity:

```
models/marts
├── customers.sql
├── customers.yml
├── locations.sql
├── locations.yml
├── order_items.sql
├── order_items.yml
├── orders.sql
├── orders.yml
├── products.sql
├── products.yml
├── supplies.sql
└── supplies.yml
```

✅ **Group by department or area of concern.** If you have fewer than 10 or so marts you may not have much need for subfolders, so as with the intermediate layer, don't over-optimize too early. If you do find yourself needing to insert more structure and grouping though, use useful business concepts here. In our marts layer, we're no longer worried about source-conformed data, so grouping by departments (marketing, finance, etc.) is the most common structure at this stage.

✅ **Name by entity.** Use plain English to name the file based on the concept that forms the grain of the mart's `customers`, `orders`. Marts that don't include any time-based rollups (pure marts) should not have a time dimension (`orders_per_day`) here, typically best captured via metrics.

❌ **Build the same concept differently for different teams.** `finance_orders` and `marketing_orders` is typically considered an anti-pattern. There are, as always, exceptions — a common pattern we see is that, finance may have specific needs, for example reporting revenue to the government in a way that diverges from how the company as a whole measures revenue day-to-day. Just make sure that these are clearly designed and understandable as _separate_ concepts, not departmental views on the same concept: `tax_revenue` and `revenue` not `finance_revenue` and `marketing_revenue`.

- ✅ **Materialized as tables or incremental models.** Once we reach the marts layer, it's time to start building not just our logic into the warehouse, but the data itself. This gives end users much faster performance for these later models that are actually designed for their use, and saves us costs recomputing these entire chains of models every time somebody refreshes a dashboard or runs a regression in python. A good general rule of thumb regarding materialization is to always start with a view (as it takes up essentially no storage and always gives you up-to-date results), once that view takes too long to practically _query_, build it into a table, and finally once that table takes too long to _build_ and is slowing down your runs, [configure it as an incremental model](https://docs.getdbt.com/docs/build/incremental-models). As always, start simple and only add complexity as necessary. The models with the most data and compute-intensive transformations should absolutely take advantage of dbt's excellent incremental materialization options, but rushing to make all your marts models incremental by default will introduce superfluous difficulty. We recommend reading this [classic post from Tristan on the limits of incremental modeling](https://discourse.getdbt.com/t/on-the-limits-of-incrementality/303).
- ✅ **Wide and denormalized.** Unlike old school warehousing, in the modern data stack storage is cheap and it's compute that is expensive and must be prioritized as such, packing these into very wide denormalized concepts that can provide everything somebody needs about a concept as a goal.
- ❌ **Too many joins in one mart.** One good rule of thumb when building dbt transformations is to avoid bringing together too many concepts in a single mart. What constitutes 'too many' can vary. If you need to bring 8 staging models together with nothing but simple joins, that might be fine. Conversely, if you have 4 concepts you're weaving together with some complex and computationally heavy window functions, that could be too much. You need to weigh the number of models you're joining against the complexity of the logic within the mart, and if it's too much to read through and build a clear mental model of then look to modularize. While this isn't a hard rule, if you're bringing together more than 4 or 5 concepts to create your mart, you may benefit from adding some [intermediate models](https://docs.getdbt.com/best-practices/how-we-structure/3-intermediate) for added clarity. Two intermediate models that bring together three concepts each, and a mart that brings together those two intermediate models, will typically result in a much more readable chain of logic than a single mart with six joins.
- ✅ **Build on separate marts thoughtfully.** While we strive to preserve a narrowing DAG up to the marts layer, once here things may start to get a little less strict. A common example is passing information between marts at different grains, as we saw above, where we bring our `orders` mart into our `customers` marts to aggregate critical order data into a `customer` grain. Now that we're really 'spending' compute and storage by actually building the data in our outputs, it's sensible to leverage previously built resources to speed up and save costs on outputs that require similar data, versus recomputing the same views and CTEs from scratch. The right approach here is heavily dependent on your unique DAG, models, and goals — it's just important to note that using a mart in building another, later mart is okay, but requires careful consideration to avoid wasted resources or circular dependencies.

Our structural recommendations are impacted quite a bit by whether or not you're using the Semantic Layer. If you're using the Semantic Layer, we recommend a more normalized approach to your marts. If you're not using the Semantic Layer, we recommend a more denormalized approach that has become typical in dbt projects.

### Semantic Layer

Let's look at the differences we can observe in how we might approach this with MetricFlow supercharging dbt versus how we work without a Semantic Layer. These differences can then inform our structure.

- 🍊 In dbt, we tend to create **highly denormalized datasets** that bring **everything you want around a certain entity or process into a single table**.
- 💜 The problem is, this **limits the dimensionality available to MetricFlow**. The more we pre-compute and 'freeze' into place, the less flexible our data is.
- 🚰 In MetricFlow, we ideally want **highly normalized**, star schema-like data that then allows MetricFlow to shine as a **denormalization engine**.
- ∞ Another way to think about this is that instead of moving down a list of requested priorities trying to pre-make as many combinations of our marts as possible — increasing lines of code and complexity — we can **let MetricFlow present every combination possible without specifically coding it**.
- 🏗️ To resolve these approaches optimally, we'll need to shift some **fundamental aspects of our modeling strategy**.

## Surrogate keys

 [DBT Blog](https://docs.getdbt.com/blog/managing-surrogate-keys)

Before the advent of the analytical warehouse tools we use today, the data warehouse architecture had a few key constraints that led to the rise of the Kimball-style warehouse with a snowflake schema. This was because storage was expensive — it was more efficient to store data as few times as possible, and rely on joins to connect data tog ether when a report required it. And to make those joins efficient, it became standard practice to use **monotonically increasing integer surrogate keys (MIISKs)**, a fancy way to say “count each record starting at one” so that your data model would look something like this (you are a cheesemonger):

There are some clear benefits here!

- There are clear, intuitive relationships between these entities!
- The fact that the keys here are small integers, the database can
	- a) not worry about storage costs for this data
	- b) index this field easily, making joins quick and efficient.

However, there are also some clear maintenance issues here. Making updates to, say, your products table will require some careful surgical work to ensure the association of cheddar to id 2 is never accidentally changed. You may have heard of the phrase “load your dims before your facts” — this refers to the careful work required to maintain this referential integrity. Additionally, you need to know about the _exact state of the data_ before making any updates. This data is **stateful**, making it rigid and more difficult to work with should there be any losses to this data. Imagine trying to rebuild these relationships from scratch!

### Hashed keys

An alternative to using the traditional MIISK strategy is to use cryptographic hashing functions to _derive the surrogate key values from the data itself,_ a fancy way of saying “create a random string for every unique combination of values you find in my table”. These hashing functions are **deterministic**, meaning the same set of inputs will always produce the same output. In our SQL models, we can pass the column or columns that represent to the grain to this hashing function and voilà, a consistent, unique identifier is generated automatically! This has been packaged up in the `surrogate_key()` macro in the `dbt_utils` package ([source](https://github.com/dbt-labs/dbt-utils#surrogate_key-source)), and works across warehouse providers! Check out our SQL Magic post that dives deeper into this function [here](https://docs.getdbt.com/blog/sql-surrogate-keys).

An alternative to using the traditional MIISK strategy is to use cryptographic hashing functions to _derive the surrogate key values from the data itself,_ a fancy way of saying “create a random string for every unique combination of values you find in my table”. These hashing functions are **deterministic**, meaning the same set of inputs will always produce the same output. In our SQL models, we can pass the column or columns that represent to the grain to this hashing function and voilà, a consistent, unique identifier is generated automatically! This has been packaged up in the `surrogate_key()` macro in the `dbt_utils` package ([source](https://github.com/dbt-labs/dbt-utils#surrogate_key-source)), and works across warehouse providers! Check out our SQL Magic post that dives deeper into this function [here](https://docs.getdbt.com/blog/sql-surrogate-keys).

This strategy is not without its caveats either!

- **Collisions -** Although it's _exceedingly_ rare, depending on the hashing algorithm you use, it's possible for two different sets of inputs to produce the same outputs, causing erroneous duplicate records in your dataset. Using an MD5 hash (the default for the `dbt_utils.generate_surrogate_key` macro), you have a 50% of a collision when you get up to 2^64 records (1.84 x 10E19 aka a whole lot of data). While [very very very unlikely](https://docs.getdbt.com/terms/surrogate-key#a-note-on-hashing-algorithms), it’s certainly something to consider for truly massive datasets.
- **Datatypes -** If you’re in the process of migrating legacy code to a new warehouse provider, you likely have some constraints on the datatype of your keys from the consumers of your datasets, and may have some issues converting to a string-based key. Luckily, some warehouse providers have hash functions that output integer values (like Snowflake’s `MD5_UPPER/LOWER_64` functions). However, these have fewer bits in the hashing function, so may lead to collision issues on big data sets.
- **Performance -** Hashed keys generally result in long string-type values. On massive datasets on some warehouses, this could cause some performance issues. Unlike MIISKs, string values can’t be easily partitioned to improve query performance. Luckily, as described in the above bullet point, you can choose to utilize hashing functions that output other, more performant datatypes!
- **Storage -** As mentioned above, hash keys will end up with higher storage costs than their MIISK counterparts. Given that the cost of storage in cloud warehouses is extremely cheap, it’s unlikely to be worth the effort to optimize for storage costs.

For my money, the simplicity of using hashed keys far outweighs the potential benefits of having MIISKs in your data model. Building with dbt works best when all parts of your project are idempotent, and hashed keys require close to zero maintenance. The cost of time spent rebuilding your surrogate keys in your data models if you can’t recreate them with a simple `dbt run` usually offsets any modest performance and storage gains you might be able to achieve with MIISKs.