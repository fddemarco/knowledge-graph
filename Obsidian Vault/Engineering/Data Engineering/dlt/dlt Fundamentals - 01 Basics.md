## QuickStart

**What is a `dlt` Pipeline?**

A [pipeline](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fpipeline) is a connection that moves data from your Python code to a destination. The pipeline accepts dlt sources or resources, as well as generators, async generators, lists, and any iterables. Once the pipeline runs, all resources are evaluated and the data is loaded at the destination.

```python
another_pipeline = dlt.pipeline(
    pipeline_name="resource_source",
    destination="duckdb",
    dataset_name="mydata",
    dev_mode=True,
)
```

You instantiate a pipeline by calling the `dlt.pipeline` function with the following arguments:

- **`pipeline_name`**: This is the name you give to your pipeline. It helps you track and monitor your pipeline, and also helps to bring back its state and data structures for future runs. If you don't provide a name, dlt will use the name of the Python file you're running as the pipeline name.
- **`destination`**: a name of the destination to which dlt will load the data. It may also be provided to the run method of the pipeline.
- **`dataset_name`**: This is the name of the group of tables (or dataset) where your data will be sent. You can think of a dataset like a folder that holds many files, or a schema in a relational database. You can also specify this later when you run or load the pipeline. If you don't provide a name, it will default to the name of your pipeline.
- **`dev_mode`**: If you set this to True, dlt will add a timestamp to your dataset name every time you create a pipeline. This means a new dataset will be created each time you create a pipeline.

To load the data, you call the `run()` method and pass your data in the data argument.

```python
# Run the pipeline and print load info
load_info = another_pipeline.run(data, table_name="pokemon")
print(load_info)
```

Commonly used arguments:

- **`data`** (the first argument) may be a dlt source, resource, generator function, or any Iterator or Iterable (i.e., a list or the result of the map function).
- **`write_disposition`** controls how to write data to a table. Defaults to the value "append".
    - `append` will always add new data at the end of the table.
    - `replace` will replace existing data with new data.
    - `skip` will prevent data from loading.
    - `merge` will deduplicate and merge data based on `primary_key` and `merge_key` hints.
- **`table_name`**: specified in cases when the table name cannot be inferred, i.e., from the resources or name of the generator function.

**`dlt`'s sql_client**

Most dlt destinations (even filesystem) use an implementation of the `SqlClientBase` class to connect to the physical destination to which your data is loaded. You can access the SQL client of your destination via the `sql_client` method on your pipeline.

Start a connection to your database with `pipeline.sql_client()` and execute a query to get all data from the `pokemon` table:

```python
# Query data from 'pokemon' using the SQL client
with another_pipeline.sql_client() as client:
    with client.execute_query("SELECT * FROM pokemon") as cursor:
        data = cursor.df()
data
```

**dlt datasets**

Here's an example of how to retrieve data from a pipeline and load it into a Pandas DataFrame or a PyArrow Table.

```python
dataset = another_pipeline.dataset()
dataset.pokemon.df()
```

**DuckDB Connection**
Start a connection to your database using a native `duckdb` connection and see which tables were generated:

```python
import duckdb

with duckdb.connect(f"{pipeline.pipeline_name}.duckdb") as conn:
    conn.sql(f"SELECT * FROM {pipeline.dataset_name}.<table_name>").pl() # fetch_arrow_table(), show()
```
## Resources and Sources

### dlt resources
A better way to represent the *pokemon* table is to wrap it in the `@dlt.resource` decorator which denotes a logical grouping of data within a data source, typically holding data of similar structure and origin:

```python
import dlt
from dlt.common.typing import TDataItems, TDataItem

pipeline = dlt.pipeline(
    pipeline_name="resource_source",
    destination="duckdb",
    dataset_name="mydata",
    dev_mode=True,
)

data = [
    {"id": "1", "name": "bulbasaur", "size": {"weight": 6.9, "height": 0.7}},
    {"id": "4", "name": "charmander", "size": {"weight": 8.5, "height": 0.6}},
    {"id": "25", "name": "pikachu", "size": {"weight": 6, "height": 0.4}},
]

# Create a dlt resource from the data
@dlt.resource(table_name="pokemon_new")
def my_dict_list() -> TDataItems:
    yield data

# Define a resource to load data from a CSV
@dlt.resource(table_name="df_data")
def my_df() -> TDataItems:
    sample_df = pd.read_csv(
	    "https://people.sc.fsu.edu/~jburkardt/data/csv/hw_200.csv"
    )
    yield sample_df

# Define a resource to fetch genome data from the database
@dlt.resource(table_name="genome_data")
def get_genome_data() -> TDataItems:
	from sqlalchemy import create_engine
    engine = create_engine(
        "mysql+pymysql://rfamro@mysql-rfam-public.ebi.ac.uk:4497/Rfam"
    )
    with engine.connect() as conn:
        query = "SELECT * FROM genome LIMIT 1000"
        rows = conn.execution_options(yield_per=100).exec_driver_sql(query)
        yield from map(lambda row: dict(row._mapping), rows)

# Define a resource to fetch pokemons from PokeAPI
@dlt.resource(table_name="pokemon_api")
def get_pokemon() -> TDataItems:
	from dlt.sources.helpers import requests
    url = "https://pokeapi.co/api/v2/pokemon"
    response = requests.get(url)
    yield response.json()["results"]


# Run the pipeline and print load info
pipeline.run(my_dict_list)
pipeline.run(my_df)
pipeline.run(get_genome_data)
```

Commonly used arguments:

- **`name`**: The resource name and the name of the table generated by this resource. Defaults to the decorated function name.
- **`table_name`**: The name of the table, if different from the resource name.
- **`write_disposition`**: Controls how to write data to a table. Defaults to the value "append".

Instead of a dict list, the data could also be a/an:

- dataframe
- database query response
- API request response
- Anything you can transform into JSON/dict format

**Why is it a better way?** This allows you to use `dlt` functionalities to the fullest that follow Data Engineering best practices, including incremental loading and data contracts.

### **`dlt` sources**

Now that there are multiple `dlt` resources, each corresponding to a separate table, we can group them into a **`dlt` source**. A source is a logical grouping of resources, e.g., endpoints of a single API. The most common approach is to define it in a separate Python module. Read more about [sources](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fsource) and [resources](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fresource).

- A source is a function decorated with `@dlt.source` that returns one or more resources.
- A source can optionally define a schema with tables, columns, performance hints, and more.
- The source Python module typically contains optional customizations and data transformations.
- The source Python module typically contains the authentication and pagination code for a particular API.

You declare a source by decorating a function that returns or yields one or more resources with `@dlt.source`. Only using the source above, load everything into a separate database using a new pipeline:

```python
from typing import Iterable
from dlt.extract import DltResource
  

@dlt.source
def all_data() -> Iterable[DltResource]:
    return my_df, get_genome_data, get_pokemon

# Create a pipeline

new_pipeline = dlt.pipeline(
    pipeline_name="resource_source_new",
    destination="duckdb",
    dataset_name="all_data"
)

# Run the pipeline
load_info = new_pipeline.run(all_data())
print(load_info)

# Using dataset API
data = pipeline.dataset().table("dlt_hub_repos").arrow()

# Using sql_client API
with pipeline.sql_client() as sql_client:
    with sql_client.execute_query(
	    f"SELECT * FROM {pipeline.dataset_name}.dlt_hub_repos"
	) as cursor:
        data = cursor.arrow()

# Using Database connection
import duckdb

with duckdb.connect(f"{pipeline.pipeline_name}.duckdb") as conn:
    conn.sql(f"SELECT * FROM {pipeline.dataset_name}.<table_name>").pl() # fetch_arrow_table(), show()
```

- Pipeline name (`resource_source_new`): Identifies the pipeline and, for DuckDB, determines the database filename or catalog.
- Destination (`duckdb`): Specifies DuckDB as the destination where the extracted data will be stored.
- Dataset name (`all_data`): Specifies the schema in DuckDB where the tables will be created. If omitted, DLT defaults to `<pipeline_name>_dataset`.
- Resource name: Determines the table name within the dataset. For example, a resource named `family` will generally be loaded into the `family` table.

**Why does this matter?**:

- It is more efficient than running your resources separately.
- It organizes both your schema and your code.
- It enables the option for parallelization.

### **`dlt` transformers**

We now know that `dlt` resources can be grouped into a `dlt` source, represented as:

```
                  Source
               /          \
          Resource 1  ...  Resource N
```

However, imagine a scenario where you need an additional step in between, for example, in a situation where Resource 1 returns a list of pokemons IDs, and you need to use each of those IDs to retrieve detailed information about the pokemons from a separate API endpoint. In such cases, you would use `dlt` transformers — special `dlt` resources that can be fed data from another resource:

```

                  Source
                 /     \
          Transformer    \
             /             \
        Resource 1  ...  Resource N
```

Given the *pokemon* resource, we need to get detailed information about pokemons from [PokeAPI](https://www.google.com/url?q=https%3A%2F%2Fpokeapi.co%2F) `"https://pokeapi.co/api/v2/pokemon/{id}"` based on their IDs.

```python
# Pattern #1: Transformer receives all items at once
@dlt.resource(table_name="pokemon")
def my_pokemons() -> TDataItems:
    pokemons = [
        {"id": "1", "name": "bulbasaur", "size": {"weight": 6.9, "height": 0.7}},
        {"id": "4", "name": "charmander", "size": {"weight": 8.5, "height": 0.6}},
        {"id": "25", "name": "pikachu", "size": {"weight": 6, "height": 0.4}},
    ]
    yield pokemons

# Define a transformer to enrich pokemon data with additional details
@dlt.transformer(data_from=my_pokemons, table_name="detailed_info")
	def poke_details(
    items: TDataItems,
) -> TDataItems:
    for item in items:
        print(f"Item: {item}\n")
        item_id = item["id"]
        url = f"https://pokeapi.co/api/v2/pokemon/{item_id}"
        response = requests.get(url)
        details = response.json()
        print(f"Details: {details}\n")
        yield details

# ===

# Pattern #2: Transformer receives one item at a time
@dlt.resource(table_name="pokemon")
def my_other_pokemons() -> TDataItems:
    pokemons = [
        {"id": "1", "name": "bulbasaur", "size": {"weight": 6.9, "height": 0.7}},
        {"id": "4", "name": "charmander", "size": {"weight": 8.5, "height": 0.6}},
        {"id": "25", "name": "pikachu", "size": {"weight": 6, "height": 0.4}},
    ]

    yield from pokemons

@dlt.transformer(data_from=my_other_pokemons, table_name="detailed_info")
def other_poke_details(
    data_item: TDataItem,
) -> TDataItems:
    item_id = data_item["id"]
    url = f"https://pokeapi.co/api/v2/pokemon/{item_id}"
    response = requests.get(url)
    details = response.json()
    yield details

# Set pipeline name, destination, and dataset name

pipeline = dlt.pipeline(
    pipeline_name="quick_start",
    destination="duckdb",
    dataset_name="pokedata",
    dev_mode=True,
)

# Run the pipeline
load_info = pipeline.run(poke_details())
print(load_info)
```

You can also use pipe instead of `data_from`, this is useful when you want to apply `dlt.transformer` to multiple `dlt.resources`:

```python
load_info = another_pipeline.run(my_pokemons | poke_details)
print(load_info)
```

## How dlt works

The `pipeline.run()` method executes the entire pipeline, encompassing the `extract`, `normalize`, and `load` stages.

```python
import dlt

pipeline = dlt.pipeline(
    pipeline_name="my_pipeline", destination="duckdb", progress="log"
)

load_info = pipeline.run(
    [
        {"id": 1},
        {"id": 2},
        {"id": 3, "nested": [{"id": 1}, {"id": 2}]},
    ],
    table_name="items",
)
print(load_info)
```

```sh
---------------------------------- Extract my ----------------------------------
Resources: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 220.06 MB (41.00%) | CPU usage: 0.00%

---------------------------------- Extract my ----------------------------------
Resources: 1/1 (100.0%) | Time: 0.01s | Rate: 75.60/s
items: 3  | Time: 0.01s | Rate: 261.93/s
Memory usage: 220.57 MB (41.00%) | CPU usage: 0.00%

---------------------- Normalize my in 1790467842.5402555 ----------------------
Files: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 220.57 MB (41.00%) | CPU usage: 0.00%

---------------------- Normalize my in 1790467842.5402555 ----------------------
Files: 1/1 (100.0%) | Time: 0.01s | Rate: 84.49/s
items: 3  | Time: 0.00s | Rate: 888.37/s
items__nested: 2  | Time: 0.00s | Rate: 1207.17/s
Memory usage: 220.57 MB (41.00%) | CPU usage: 0.00%

------------------------ Load my in 1790467842.5402555 -------------------------
Jobs: 0/2 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 220.57 MB (41.00%) | CPU usage: 0.00%

------------------------ Load my in 1790467842.5402555 -------------------------
Jobs: 2/2 (100.0%) | Time: 0.10s | Rate: 19.74/s
Memory usage: 226.51 MB (41.20%) | CPU usage: 0.00%

Pipeline my_pipeline load step finished in 0.10 seconds
1 load package(s) were loaded to destination duckdb and into dataset my_pipeline_dataset
The duckdb destination used duckdb:////home/fddemarco/data-eng/dlt/my_pipeline.duckdb location to store data
Load package 1790467842.5402555 is LOADED and contains no failed jobs
```

1. **Extract** - Fully extracts the data from your source to your hard drive. In the example above, an implicit source with one resource with 3 items is created and extracted.
2. **Normalize** - Inspects and normalizes your data and computes a schema compatible with your destination. For the example above, the normalizer will detect one column `id` of type `int` in one table named `items`, it will furthermore detect a nested list in table items and unnest it into a child table named `items__nested`.
3. **Load** - Runs schema migrations if necessary on your destination and loads your data into the destination. For the example above, a new dataset on a local duckdb database is created that contains the two tables discovered in the previous steps.

The `progress="log"` argument in the `dlt.pipeline` configuration enables detailed logging of the pipeline’s progress during execution. These logs provide visibility into the pipeline’s operations, showing how data flows through the **Extract**, **Normalize**, and **Load** phases. The logs include real-time metrics such as resource or file counts, time elapsed, processing rates, memory usage, and CPU utilization. `dlt` supports 4 progress monitors out of the box:

- `enlighten` - a status bar with progress bars that also allows for logging.
- `tqdm` - the most popular Python progress bar lib, proven to work in Notebooks.
- `alive_progress` - with the most fancy animations.
- `log` — dumps progress information to a log, console, or text stream; **most useful in production**, and can optionally include memory and CPU usage stats.

### Extract

Extract can be run individually with the `extract` method on the pipeline:

```python
pipeline.extract(data)
```

```sh
---------------------------------- Extract my ----------------------------------
Resources: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 358.66 MB (44.00%) | CPU usage: 0.00%

---------------------------------- Extract my ----------------------------------
Resources: 1/1 (100.0%) | Time: 0.01s | Rate: 75.22/s
items: 3  | Time: 0.01s | Rate: 266.77/s
Memory usage: 359.24 MB (44.00%) | CPU usage: 0.00%


Load package 1790468672.793539 is EXTRACTED and NOT YET LOADED to the destination and contains no failed jobs

```

When the `pipeline.run()` method is executed, it first performs the `extract` stage, during which the following occurs:

1. Data is fetched and stored in an in-memory buffer.
2. When the buffer reaches its capacity, the data inside it is written to an intermediary file, and the buffer is cleared for the next set of data items.
3. If a size is specified for intermediary files and an the intermediary file in question reaches this size, a new intermediary file is opened for further data.

```markdown
               API Data
                  | (extract)
                Buffer
(resources) /  | ... |   \
     extracted data in local storage
```

The **number** of intermediate **files** depends on the number of **resources** and whether **file rotation** is enabled.

- The in-memory buffer is set to `5000` items.
- By default, **intermediary files are not rotated**. If you do not explicitly set a size for an intermediary file with `file_max_items=100000`, `dlt` will create a **single file** for a resource, regardless of the number of records it contains, even if it reaches millions.
- By default, intermediary files at the extract stage use a custom version of the JSONL format.
### Normalize

Normalize can be run individually with the `normalize` command on the pipeline. Normalize is dependent on having a completed extract phase and will not do anything if there is no extracted data.

```python
pipeline.extract(source_data)
pipeline.normalize()
```

```sh
---------------------------------- Extract my ----------------------------------
Resources: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 368.05 MB (44.20%) | CPU usage: 0.00%

---------------------------------- Extract my ----------------------------------
Resources: 1/1 (100.0%) | Time: 0.01s | Rate: 74.96/s
items: 3  | Time: 0.01s | Rate: 265.68/s
Memory usage: 368.05 MB (44.20%) | CPU usage: 0.00%

---------------------- Normalize my in 1790468672.793539 -----------------------
Files: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 368.08 MB (44.20%) | CPU usage: 0.00%

---------------------- Normalize my in 1790468672.793539 -----------------------
Files: 1/1 (100.0%) | Time: 0.03s | Rate: 35.76/s
items: 3  | Time: 0.01s | Rate: 357.05/s
items__nested: 2  | Time: 0.01s | Rate: 384.25/s
Memory usage: 368.08 MB (44.20%) | CPU usage: 0.00%

---------------------- Normalize my in 1790468910.2553408 ----------------------
Files: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 368.08 MB (44.20%) | CPU usage: 0.00%

---------------------- Normalize my in 1790468910.2553408 ----------------------
Files: 1/1 (100.0%) | Time: 0.01s | Rate: 75.87/s
items: 3  | Time: 0.00s | Rate: 652.30/s
items__nested: 2  | Time: 0.00s | Rate: 1167.52/s
Memory usage: 368.08 MB (44.20%) | CPU usage: 0.00%

Normalized data for the following tables:
- items: 6 row(s)
- items__nested: 4 row(s)

Load package 1790468672.793539 is NORMALIZED and NOT YET LOADED to the destination and contains no failed jobs
Load package 1790468910.2553408 is NORMALIZED and NOT YET LOADED to the destination and contains no failed jobs
```

In the `normalize` stage, `dlt` first transforms the structure of the input data. This transformed data is then converted into a relational structure that can be easily loaded into the destination.

1. Intermediary files are sent from the `extract` stage to the `normalize` stage.
2. During normalization step it processes one intermediate file at a time within its own in-memory buffer.
3. When the buffer reaches its capacity, the normalized data inside it is written to an intermediary file, and the buffer is cleared for the next set of data items.
4. If a size is specified for intermediary files in the normalize stage and the intermediary file in question reaches this size, a new intermediary file is opened.

```markdown
       (extract)
API Data --> extracted files in local storage
                /     |      \     (normalize)
          one file  ...  one file
          /  |  \          / | \   
      normalized files   normalized files        

```

The **number** of intermediate **files** depends on the number of **resources** and whether **file rotation** is enabled.
- The in-memory buffer is set to `5000`, just like at the extraction stage.
- By default, **intermediary files are not rotated** as well. If you do not explicitly set a size for an intermediary file with `file_max_items=100000`, dlt will create a **single file** for a resource, regardless of the number of records it contains, even if it reaches millions.
### Load
Load can be run individually with the `load` command on the pipeline. Load is dependent on having a completed normalize phase and will not do anything if there is no normalized data.

```python
pipeline.extract(source_data)
pipeline.normalize()
pipeline.load(worksers=20)
```

```sh
---------------------------------- Extract my ----------------------------------
Resources: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 372.08 MB (44.30%) | CPU usage: 0.00%

---------------------------------- Extract my ----------------------------------
Resources: 1/1 (100.0%) | Time: 0.01s | Rate: 67.74/s
items: 3  | Time: 0.01s | Rate: 263.44/s
Memory usage: 372.08 MB (44.30%) | CPU usage: 0.00%

---------------------- Normalize my in 1790469266.030951 -----------------------
Files: 0/1 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 372.11 MB (44.30%) | CPU usage: 0.00%

---------------------- Normalize my in 1790469266.030951 -----------------------
Files: 1/1 (100.0%) | Time: 0.01s | Rate: 86.38/s
items: 3  | Time: 0.00s | Rate: 660.14/s
items__nested: 2  | Time: 0.00s | Rate: 827.93/s
Memory usage: 372.12 MB (44.30%) | CPU usage: 0.00%

------------------------- Load my in 1790468672.793539 -------------------------
Jobs: 0/2 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 372.14 MB (44.30%) | CPU usage: 0.00%

------------------------- Load my in 1790468672.793539 -------------------------
Jobs: 2/2 (100.0%) | Time: 0.12s | Rate: 16.67/s
Memory usage: 373.92 MB (44.60%) | CPU usage: 0.00%

------------------------ Load my in 1790468910.2553408 -------------------------
Jobs: 0/2 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 373.92 MB (44.60%) | CPU usage: 0.00%

------------------------ Load my in 1790468910.2553408 -------------------------
Jobs: 2/2 (100.0%) | Time: 0.50s | Rate: 4.02/s
Memory usage: 371.85 MB (43.80%) | CPU usage: 0.00%

------------------------- Load my in 1790469266.030951 -------------------------
Jobs: 0/2 (0.0%) | Time: 0.00s | Rate: 0.00/s
Memory usage: 371.85 MB (43.80%) | CPU usage: 0.00%

------------------------- Load my in 1790469266.030951 -------------------------
Jobs: 2/2 (100.0%) | Time: 0.11s | Rate: 17.74/s
Memory usage: 366.04 MB (43.80%) | CPU usage: 0.00%

Pipeline my_pipeline load step finished in 0.94 seconds
3 load package(s) were loaded to destination duckdb and into dataset my_pipeline_dataset
The duckdb destination used duckdb:////home/fddemarco/data-eng/dlt/my_pipeline.duckdb location to store data
Load package 1790468672.793539 is LOADED and contains no failed jobs
Load package 1790468910.2553408 is LOADED and contains no failed jobs
Load package 1790469266.030951 is LOADED and contains no failed jobs
```
The `load` stage is responsible for taking the normalized data and loading it into your chosen destination:

1. All intermediary files from a **single source** are combined into a single load package.
2. All load packages are then loaded into the destination.
3. Loading happens in `20` threads (`workers=20`), each loading a single file.

```markdown
    (extract)             (normalize)
API Data --> extracted files --> normalized files     
                                  /  |  ... |  \   (load)
                    one normalized file ... one file
                                 \   |  ... |   /
                                    destination
                                           
```

#### Intermediary Load files
Intermediary files at the extract stage use a custom version of the JSONL format, while the loader files - files  created at the normalize stage - can take 4 different formats.

**Configuration**:

- Directly in the `pipeline.run()`:

```python
  info = pipeline.run(some_source(), loader_file_format="jsonl")
```

- In `config.toml` or `secrets.toml`:

```toml
  [normalize]
  loader_file_format="jsonl"
```

- Via environment variables:

```sh
  export NORMALIZE__LOADER_FILE_FORMAT="jsonl"
```

- Specify directly in the resource decorator:

```python
  @dlt.resource(file_format="jsonl")
  def generate_rows():
    ...
```

Available file formats:
- `jsonl`
- `insert_values` (SQL)
- `parquet`
- `csv`
#### JSONL
JSON Delimited is a file format that stores several JSON documents in one file. The JSON documents are separated by a new line.

- **Compression:** enabled by default.
- **Data type handling:**
	- `datetime` and `date` are stored as ISO strings;
	- `decimal` is stored as a text representation of a decimal number;
	- `binary` is stored as a base64 encoded string;
	- `HexBytes` is stored as a hex encoded string;
	- `complex` is serialized as a string.
- **By default used by:**
	- Bigquery
	- Snowflake
	- Filesystem
#### SQL INSERT

This file format contains an INSERT...VALUES statement to be executed on the destination during the `load` stage.

- Additional data types are stored as follows:
	- `datetime` and date are stored as ISO strings;
	- `decimal` is stored as a text representation of a decimal number;
	- `binary` storage depends on the format accepted by the destination;
	- `complex` storage also depends on the format accepted by the destination.
- This file format is compressed by default.
- **Default for:**
	1. DuckDB
	2. PostgreSQL
	3. Redshift
-  **Supported by:**
	1. Filesystem
#### Parquet

Apache Parquet is a free and open-source column-oriented data storage format in the Apache Hadoop ecosystem. To use this format, you need a pyarrow package. You can get this package as a dlt extra as well:

```sh
pip install "dlt[parquet]"
```

**Default version**: 2.4, which coerces timestamps to microseconds and silently truncates nanoseconds for better compatibility with databases and pandas.

**Supported by:**

- Bigquery
- DuckDB
- Snowflake
- Filesystem
- Athena
- Databricks
- Synapse

**Destination AutoConfig**:
`dlt` automatically configures the Parquet writer based on the destination's capabilities:

- Selects the appropriate decimal type and sets the correct precision and scale for accurate numeric data storage, including handling very small units like Wei.
- Adjusts the timestamp resolution (seconds, microseconds, or nanoseconds) to match what the destination supports

**Writer settings:**

`dlt` uses the pyarrow Parquet writer for file creation. You can adjust the writer's behavior with the following options:

- `flavor` adjusts schema and compatibility settings for different target systems. Defaults to None (pyarrow default).
- `version` selects Parquet logical types based on the Parquet format version. Defaults to "2.6".
- `data_page_size` sets the target size for data pages within a column chunk (in bytes). Defaults to None.
- `timestamp_timezone` specifies the timezone; defaults to UTC.
- `coerce_timestamps` sets the timestamp resolution (s, ms, us, ns).
- `allow_truncated_timestamps` raises an error if precision is lost on truncated timestamps.

  **Example configurations:**

  - In `configs.toml` or `secrets.toml`:
```toml
    [normalize.data_writer]
    # the default values
    flavor="spark"
    version="2.4"
    data_page_size=1048576
    timestamp_timezone="Europe/Berlin"
```

  - Via environment variables:
```sh
export  NORMALIZE__DATA_WRITER__FLAVOR="spark"
```

**Timestamps and timezones**

`dlt` adds UTC adjustments to all timestamps, creating timezone-aware timestamp columns in destinations (except DuckDB).

**Disable timezone/UTC adjustments:**

- Set `flavor` to `spark` to use the deprecated `int96` timestamp type without logical adjustments.
- Set `timestamp_timezone` to an empty string (`DATA_WRITER__TIMESTAMP_TIMEZONE=""`) to generate logical timestamps without UTC adjustment.

By default, pyarrow converts timezone-aware DateTime objects to UTC and stores them in Parquet without timezone information.
#### CSV

**Supported by:**

- PostgreSQL
- Filesystem
- Snowflake

**Two implementation**:

1. `pyarrow` csv writer - very fast, multithreaded writer for the arrow tables
	  - complex (nested, struct) types are **not supported**
2. `python stdlib writer` - a csv writer included in the Python standard library for Python objects
	  - complex columns dumped with json.dumps
	  - None values are always quoted

**Default settings:**

- separators are commas
- quotes are " and are escaped as ""
- NULL values both are empty strings and empty tokens as in the example below
- UNIX new lines are used
- dates are represented as ISO 8601  quoting style is "when needed"

**Adjustable setting:**

- `delimiter`: change the delimiting character (default: ',')
- `include_header`: include the header row (default: True)
- `quoting`: `quote_all` - all values are quoted, `quote_needed` - quote only values that need quoting (default: `quote_needed`)

```TOML
[normalize.data_writer]
delimiter="|"
include_header=false
quoting="quote_all"
```

  or

```sh
NORMALIZE__DATA_WRITER__DELIMITER=|
NORMALIZE__DATA_WRITER__INCLUDE_HEADER=False
NORMALIZE__DATA_WRITER__QUOTING=quote_all
```

## Pipeline Metadata

**Pipeline metadata** is data about your data pipeline. This is useful when you want to know things like:

- When your pipeline first ran
- When your pipeline last ran
- Information about your source or destination
- Processing time
- Custom metadata you add yourself
- And much more!

`dlt` allows you to view all this metadata through various options!

- Load info
- Trace
- State
### Load info

`Load Info:` This is a collection of useful information about the recently loaded data. It includes details like the pipeline and dataset name, destination information, and a list of loaded packages with their statuses, file sizes, types, and error messages (if any).

`Load Package:` A load package is a collection of jobs with data for specific tables, generated during each execution of the pipeline. Each package is uniquely identified by a `load_id`.

```sh
$ dlt pipeline -v <pipeline_name> load-package

Found pipeline my_pipeline in /home/fddemarco/.dlt/pipelines
Package 1790517781.6166503 found in /home/fddemarco/.dlt/pipelines/my_pipeline/load/loaded/1790517781.6166503
The package with load id 1790517781.6166503 for schema my is in LOADED state. It updated schema for 0 tables. The package was LOADED at 2026-09-27 14:03:01.784476+00:00.
Jobs details:
Job: copy.af7a2ba9c1.insert_values.gz, table: copy in completed_jobs. File type: insert_values, size: 186B. Started on: 2026-09-27 14:03:01.663841+00:00 and completed in 0.12 seconds.
Job: copy__nested.40117e8fa6.insert_values.gz, table: copy__nested in completed_jobs. File type: insert_values, size: 182B. Started on: 2026-09-27 14:03:01.664026+00:00 and completed in 0.12 seconds.
```
The `load_id` of a particular package is added to the top data tables (parent tables) and to the special `_dlt_loads` table with a status of `0` when the load process is fully completed. The `_dlt_loads` table tracks completed loads and allows chaining transformations on top of them.

We can also view load package info for a specific `load_id` (replace the value with the one output above):

```sh
$ dlt pipeline -v <pipeline_name> load-package <load_id>

Found pipeline my_pipeline in /home/fddemarco/.dlt/pipelines
Package 1790517781.6166503 found in /home/fddemarco/.dlt/pipelines/my_pipeline/load/loaded/1790517781.6166503
The package with load id 1790517781.6166503 for schema my is in LOADED state. It updated schema for 0 tables. The package was LOADED at 2026-09-27 14:03:01.784476+00:00.
Jobs details:
Job: copy.af7a2ba9c1.insert_values.gz, table: copy in completed_jobs. File type: insert_values, size: 186B. Started on: 2026-09-27 14:03:01.663841+00:00 and completed in 0.12 seconds.
Job: copy__nested.40117e8fa6.insert_values.gz, table: copy__nested in completed_jobs. File type: insert_values, size: 182B. Started on: 2026-09-27 14:03:01.664026+00:00 and completed in 0.12 seconds.
```

We can also access the load info from Python:

```python
print(load_info.load_packages[0])
```

### Trace
`Trace`: A trace is a detailed record of the execution of a pipeline. It provides rich information on the pipeline processing steps: **extract**, **normalize**, and **load**. It also shows the last `load_info`. You can access the pipeline trace using the command:

```sh
$ dlt pipeline <pipeline_name> trace

Found pipeline my_pipeline in /home/fddemarco/.dlt/pipelines
Run started at 2026-09-27 14:08:47.056571+00:00 and COMPLETED in 0.24 seconds with 4 steps.
Step extract COMPLETED in 0.04 seconds.

Load package 1790518127.125266 is EXTRACTED and NOT YET LOADED to the destination and contains no failed jobs

Step normalize COMPLETED in 0.03 seconds.
Normalized data for the following tables:
- copy: 3 row(s)
- copy__nested: 2 row(s)

Load package 1790518127.125266 is NORMALIZED and NOT YET LOADED to the destination and contains no failed jobs

Step load COMPLETED in 0.12 seconds.
Pipeline my_pipeline load step finished in 0.10 seconds
1 load package(s) were loaded to destination duckdb and into dataset my_pipeline_dataset
The duckdb destination used duckdb:////home/fddemarco/data-eng/dlt/my_pipeline.duckdb location to store data
Load package 1790518127.125266 is LOADED and contains no failed jobs

Step run COMPLETED in 0.24 seconds.
Pipeline my_pipeline load step finished in 0.10 seconds
1 load package(s) were loaded to destination duckdb and into dataset my_pipeline_dataset
The duckdb destination used duckdb:////home/fddemarco/data-eng/dlt/my_pipeline.duckdb location to store data
Load package 1790518127.125266 is LOADED and contains no failed jobs
```

We can also access the trace using Python:

```python
print(pipeline.last_trace)
```

## Pipeline State

[`The pipeline state`](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fstate) is a Python dictionary that lives alongside your data. You can store values in it during a pipeline run, and then retrieve them in the next pipeline run. It's used for tasks like preserving the "last value" or similar loading checkpoints, and it gets committed atomically with the data. The state is stored locally in the pipeline working directory and is also stored at the destination for future runs.

**When to use pipeline state**

- `dlt` uses the state internally to implement last value incremental loading. This use case should cover around 90% of your needs to use the pipeline state.
- Store a list of already requested entities if the list is not much bigger than 100k elements.
- Store large dictionaries of last values if you are not able to implement it with the standard incremental construct.
- Store the custom fields dictionaries, dynamic configurations and other source-scoped state.

**When not to use pipeline state**

Do not use `dlt` state when it may grow to millions of elements. For example, storing modification timestamps for millions of user records is a bad idea.

```sh
$ dlt pipeline -v <pipeline_name> info

Attaching to pipeline my_pipeline
Found pipeline my_pipeline in /home/fddemarco/.dlt/pipelines
Synchronized state:
_state_version: 1
_state_engine_version: 4
pipeline_name: my_pipeline
dataset_name: my_pipeline_dataset
schema_names: ['my']
default_schema_name: my
destination_type: dlt.destinations.duckdb
destination_name: None
_version_hash: +2Z3B/gKqhXoKMrObpXTXYY4U39HWZRi6liSkToafDk=

Local state:
first_run: False
_dev_mode: False
initial_cwd: /home/fddemarco/data-eng/dlt
last_run_context['uri']: file:///home/fddemarco/data-eng/dlt
_last_extracted_at: 2026-09-27 00:09:21.031498+00:00
_last_extracted_hash: +2Z3B/gKqhXoKMrObpXTXYY4U39HWZRi6liSkToafDk=

Resources in schema: my
items with 2 table(s) and 0 resource state slot(s)
	items table 3 column(s) received data 
	items__nested table 4 column(s) received data 
items_copy with 2 table(s) and 0 resource state slot(s)
	items_copy table 3 column(s) received data 
	items_copy__nested table 4 column(s) received data 
copy with 2 table(s) and 0 resource state slot(s)
	copy table 3 column(s) received data 
	copy__nested table 4 column(s) received data 

Working dir content:
Has 21 completed load packages with following load ids:
1790467760.9829428
1790467792.5685344
1790467842.5402555
1790468278.195622
1790468672.793539
1790468910.2553408
1790469266.030951
1790481037.0530891
1790481131.2949724
1790481358.0492039
1790482398.3358214
1790482477.3352327
1790482477.5989878
1790482532.4827652
1790482857.9550512
1790483178.9672258
1790483179.2426977
1790517781.0514302
1790517781.6166503
1790518126.8753612
1790518127.125266

Pipeline has last run trace. Use 'dlt pipeline my_pipeline trace' to inspect 

```

###  Resource state

You can **read** and **write** the state in your resources using:

```python
dlt.current.resource_state().get()
```
and

```python
dlt.current.resource_state().setdefault(key, value)
```

### Source state

You can also access the source-scoped state with `dlt.current.source_state()` which can be shared across resources of a particular source and is also available read-only in the source-decorated functions. The most common use case for the source-scoped state is to store the mapping of custom fields to their displayable names. Let's read some custom keys from the state with:

```python
source_new_keys = dlt.current.source_state().get("resources", {}).get("github_pulls", {}).get("new_key")
```

### Sync state

What if you run your pipeline on, for example, Airflow, where every task gets a clean filesystem and the pipeline working directory is always deleted? dlt loads your state into the destination together with all other data, and when starting from a clean slate, it will try to restore the state from the destination.

The remote state is identified by the pipeline name, the destination location (as defined by the credentials), and the destination dataset. To reuse the same state, use the same pipeline name and the same destination. The state is stored in the `dlt_pipeline_state` table at the destination and contains information about the pipeline, the pipeline run (to which the state belongs), and the state blob. dlt provides a command that retrieves the state from that table.

```sh
dlt pipeline <pipeline name> sync
```

If you can keep the pipeline working directory across runs, you can disable state sync by setting `restore_from_destination = false` in your `config.toml`.

### Reset state

**To fully reset the state:**

- Drop the destination dataset to fully reset the pipeline.
- Set the `dev_mode` flag when creating the pipeline.
- Use the `dlt pipeline drop --drop-all` command to drop state and tables for a given schema name.

**To partially reset the state:**

- Use the `dlt pipeline drop <resource_name>` command to drop state and tables for a given resource.
- Use the `dlt pipeline drop --state-paths` command to reset the state at a given path without touching the tables or data.