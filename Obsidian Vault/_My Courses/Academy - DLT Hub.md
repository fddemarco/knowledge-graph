[Homepage](https://dlthub.learnworlds.com/courses)
[[Library - dlt]]
# dlt Fundamentals

## Quickstart
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

## Resources and Sources
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
```

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
@dlt.resource(table_name="pokemon")
def my_pokemons() -> TDataItems:
    pokemons = [
        {"id": "1", "name": "bulbasaur", "size": {"weight": 6.9, "height": 0.7}},
        {"id": "4", "name": "charmander", "size": {"weight": 8.5, "height": 0.6}},
        {"id": "25", "name": "pikachu", "size": {"weight": 6, "height": 0.4}},
    ]
    yield pokemons

# Define a transformer to enrich pokemon data with additional details
# NOTE: Transformer receives all items at once
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

# NOTE: Transformer receives one item at a time
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

## Nesting levels

You can limit how deep dlt goes when generating nested tables and flattening dicts into columns. By default, the library will descend and generate nested tables for all nested lists, without limit. You can set nesting level for all resources on the source level or for each resource separately:

```python
@dlt.source(max_table_nesting=1)
def all_data():
	return my_df, get_genome_data, get_pokemon

@dlt.resource(table_name='pokemon_new', max_table_nesting=1)
def my_dict_list():
	yield data
```

In the example above, we want only 1 level of nested tables to be generated (so there are no nested tables of a nested table). Typical settings:

- `max_table_nesting=0` will not generate nested tables and will not flatten dicts into columns at all. All nested data will be represented as JSON.
- `max_table_nesting=1` will generate nested tables of root tables and nothing more. All nested data in nested tables will be represented as JSON.