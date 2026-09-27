## Inspecting a Schema
The **schema** describes the structure of normalized data (e.g. tables, columns, data types, etc.). `dlt` generates schemas from the data during the normalization process. The instruction to export a schema should be provided at the beginning when creating a pipeline:


```python
pipeline = dlt.pipeline(
    pipeline_name="github_pipeline2",
    destination="duckdb",
    dataset_name="github_data",
    export_schema_path="schemas/export", # schema export
)
```

A schema (in YAML format) looks something like this:

```yaml
version: 2
version_hash: wdIt+pExjT8Mj1ygQEMhq3E3SXtNBuIbHg0fDz9xD9I=
engine_version: 11
name: github_source
tables:
  _dlt_version:
    ...
  _dlt_loads:
    ...
  github_pulls:
    ...
settings:
  detections:
  - iso_timestamp
  default_hints:
    not_null:
    - _dlt_id
    - _dlt_root_id
    - _dlt_parent_id
    - _dlt_list_idx
    - _dlt_load_id
    parent_key:
    - _dlt_parent_id
    root_key:
    - _dlt_root_id
    unique:
    - _dlt_id
    row_key:
    - _dlt_id
normalizers:
  names: snake_case
  json:
    module: dlt.common.normalizers.json.relational
previous_hashes:
- 0WLnuf3Jh1J1XsbVrV2eB824Z6heOlf5o912i1v3tho=
- 0d1z0RFV2O0OvfEWkebtSjxrCjjiyv1lOeNiF0V8Lws=
```
### **Schema version hash**

The schema hash, denoted by `version_hash`, is generated from the actual schema content, excluding the hash values and version of the schema. Each time the schema is changed, a new hash is produced.

Note that during the initial run (the first pipeline run), the version will be 2, and there will be two previous hashes because the schema is updated during both the extract and normalize stages. You can rely on the version number to determine how many times the schema has been changed, but keep in mind that it stops being reliable when parallelization is introduced. Each version hash is then stored in the `_dlt_version` table.

On subsequent runs, `dlt` checks if the generated schema hash is stored in this table. If it is not, `dlt` concludes that the schema has changed and migrates the destination accordingly.

- If multiple pipelines are sending data to the same dataset and there is a clash in table names, a single table with the union of the columns will be created.
- If columns clash and have different types or other incompatible characteristics, the load may fail if the data cannot be coerced.

### Naming convention

Each schema contains a naming convention that is denoted in the following way when the schema is exported:

```yaml
...
normalizers:  names: snake_case # naming convention...
```

The naming convention is particularly useful if the identifiers of the data to be loaded (e.g., keys in JSON files) need to match the namespace of the destination (such as Redshift, which accepts case-insensitive alphanumeric identifiers with a maximum of 127 characters). This convention is used by `dlt` to translate between these identifiers and namespaces.

The standard behavior of `dlt` is to use the same naming convention for all destinations, ensuring that users always see the same tables and columns in their databases. The default naming convention is `snake_case`:

- Removes all ASCII characters except alphanumerics and underscores.
- Adds an underscore (`_`) if the name starts with a number.
- Multiple underscores (`_`) are reduced to a single underscore.
- The parent-child relationship is expressed as a double underscore (`__`) in names.
- The identifier is shortened if it exceeds the length allowed at the destination.

If you provide any schema elements that contain identifiers via decorators or arguments (e.g., `table_name` or `columns`), all the names used will be converted according to the naming convention when added to the schema. For example, if you execute `dlt.run(..., table_name="CamelCaseTableName")`, the data will be loaded into `camel_case_table_name`.

To retain the original naming convention, you can define the following in your `config.toml`:

```toml
[schema]
naming="direct"
```

or use an environment variable as:

```
SCHEMA__NAMING=direct
```

### Schema settings

The `settings` section of the schema file allows you to define various global rules that impact how tables and columns are inferred from data.

```yaml
settings:
	detections:
		...
	default_hints:
		...
```

#### Detections

You can define a set of functions that will be used to infer the data type of the column from a value. These functions are executed sequentially from top to bottom on the list.

```yaml
settings:
	detections:
		- timestamp # detects int and float values that can be interpreted
		# as timestamps within a 5-year range and converts them
		- iso_timestamp # detects ISO 8601 strings and converts them
		# to timestamp
		- iso_date # detects strings representing an ISO like date
		# (excluding timestamps) and, if so, converts to date
		- large_integer # detects integers too large for 64-bit and classifies
		  # as "wei" or converts to text if extremely large
		- hexbytes_to_text # detects HexBytes objects and converts them to text
		- wei_to_double # detects Wei values and converts them to double for
		  # aggregate non-financial reporting
```

`iso_timestamp` detector is enabled by default. Detectors can be removed or added directly in code:

```python
  source = source()
  source.schema.remove_type_detection("iso_timestamp")
  source.schema.add_type_detection("timestamp")
```
#### Column hint rules

The `default_hints` section in the schema file is used to define global rules that apply to newly inferred columns. These rules are applied **after normalization**, meaning after the naming convention is applied! By default, schema adopts column hint rules from the JSON (relational) normalizer to support correct hinting of columns added by the normalizer:

```yaml
settings:
	default_hints:
		foreign_key:
			- _dlt_parent_id
		not_null:
			- _dlt_id
			- _dlt_root_id
			- _dlt_parent_id
			- _dlt_list_idx
			- _dlt_load_id
		unique:
			- _dlt_id
		root_key:
			- _dlt_root_id
		partition:
			- re:_timestamp$ 
			#  add partition hint to all columns ending with _timestamp
```

Column hints can be added directly in code:

```python
  source = data_source()
  # this will update existing hints with the hints passed
  source.schema.merge_hints({"partition": ["re:_timestamp$"]})
```

#### Preferred data types

In the `preferred_types` section, you can define rules that will set the data type for newly created columns. On the left side, you specify a rule for a column name, and on the right side, you define the corresponding data type. You can use column names directly or with regular expressions to match them.

```yaml
settings:
  preferred_types:
    re:_timestamp$: timestamp
    inserted_at: timestamp
    created_at: timestamp
    updated_at: timestamp
```

Above, we prefer `timestamp` data type for all columns containing timestamp substring and define a exact matches for certain columns. Preferred data types can be added directly in code as well:

```python
source = data_source()
source.schema.update_preferred_types(
  {
    "re:timestamp": "timestamp",
    "inserted_at": "timestamp",
    "created_at": "timestamp",
    "updated_at": "timestamp",
  }
)
```

## Modifying a Schema

You can directly apply data types and hints to your resources, bypassing the need for importing and adjusting schemas. This approach is ideal for rapid prototyping and handling data sources with dynamic schema requirements.

The two main approaches are:

- Using the `columns` argument in the `dlt.resource` decorator.
- Using the `apply_hints` method.

```python
@dlt.resource(name='my_table', columns={"my_column": {"data_type": "bool", "nullable": True}})
def my_resource():
    for i in range(10):
        yield {'my_column': i % 2 == 0}
```

### Hints

When dealing with dynamically generated resources or needing to programmatically set hints, `apply_hints` is your go-to tool.

The `apply_hints` method in dlt is used to programmatically **set** or **adjust** various aspects of your data resources or pipeline. It can be used in several ways:

- You can use `apply_hints` to **directly define data types** and their properties, such as nullability, within the `@dlt.resource` decorator. This eliminates the dependency on external schema files.
- When **dealing with dynamically generated resources** or needing to programmatically set hints, `apply_hints` is your tool. It's especially useful for applying hints across various collections or tables at once.
- `apply_hints` can be used to **load your data incrementally**. For example, you can load only files that have been updated since the last time dlt processed them, or load only the new or updated records by looking at a specific column.
- You can **set or update the table name, columns, and other schema elements** when your resource is executed, and you already yield data. Such changes will be merged with the existing schema in the same way the `apply_hints` method works.
    
It’s especially useful for applying hints across multiple collections or tables at once. For example, to apply a complex data type across all collections from a MongoDB source:

```python
all_collections = ["collection1", "collection2", "collection3"]  # replace with your actual collection names
source_data = mongodb().with_resources(*all_collections)

for col in all_collections:
    source_data.resources[col].apply_hints(columns={"column_name": {"data_type": "complex"}})

pipeline = dlt.pipeline(
    pipeline_name="mongodb_pipeline",
    destination="duckdb",
    dataset_name="mongodb_data"
)
load_info = pipeline.run(source_data)
```

### Adjusting schema settings

Detectors can be removed or added directly in code:

```python
  source = source()
  source.schema.remove_type_detection("iso_timestamp")
  source.schema.add_type_detection("timestamp")
```

Column hints can be added directly in code:

```python
  source = data_source()
  # this will update existing hints with the hints passed
  source.schema.merge_hints({"partition": ["re:_timestamp$"]})
```

Preferred data types can be added directly in code as well:

```python
source = data_source()
source.schema.update_preferred_types(
  {
    "re:_timestamp$": "timestamp",
    "inserted_at": "timestamp",
    "created_at": "timestamp",
    "updated_at": "timestamp",
  }
)
```

### Import a Schema

The usual approach to use this functionality is to export the schema first, make the adjustments and put the adjusted schema into the corresponding import folder. The instruction to import a schema should be provided at the beginning when creating a pipeline:

```python
pipeline = dlt.pipeline(
    pipeline_name="github_pipeline3",
    destination="duckdb",
    dataset_name="github_data",
    export_schema_path="schemas/export",
    import_schema_path="schemas/import", # Import a schema
)
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