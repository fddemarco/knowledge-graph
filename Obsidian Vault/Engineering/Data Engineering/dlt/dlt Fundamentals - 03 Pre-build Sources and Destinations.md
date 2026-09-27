
The main difference between the [core sources](https://dlthub.com/docs/dlt-ecosystem/verified-sources#core-sources) and [verified sources](https://dlthub.com/docs/dlt-ecosystem/verified-sources#verified-sources) lies in their structure. Core sources are generic collections, meaning they can connect to a variety of systems. For example, the [SQL Database source](https://dlthub.com/docs/dlt-ecosystem/verified-sources/sql_database) can connect to any database that supports SQLAlchemy.

It's also important to note that core sources are integrated into the `dlt` core library, whereas verified sources are maintained in a separate [repository](https://github.com/dlt-hub/verified-sources). To use a verified source, you need to run the `dlt init` command, which will download the verified source code to your working directory. Available dlt core sources:

- **filesystem**: Reads files in s3, gs or azure buckets using fsspec and provides convenience resources for chunked reading of various file formats
- **rest_api**: Generic API Source
- **sql_database**: Source that loads tables form any SQLAlchemy supported database, supports batching requests and incremental loads.

This command shows all available verified sources and their short descriptions. For each source, it checks if your local `dlt` version requires an update and prints the relevant warning.

```sh
dlt init --list-sources;
dlt init --list-destinations;
```

This command will initialize the pipeline example with the GitHub API as the source and DuckDB as the destination:

```sh
dlt init github_api duckdb
```

What you would normally do with the project:

- Add your credentials and define configurations
- Adjust the pipeline script as needed
- Run the pipeline script

## RestAPI Sources

`rest_api` is a generic source that lets you create a `dlt` source from any REST API using a declarative configuration. Since most REST APIs follow similar patterns, this source provides a convenient way to define your integration declaratively.

Using a [declarative configuration](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fdlt-ecosystem%2Fverified-sources%2Frest_api%2Fbasic%23source-configuration), you can specify:

- the API endpoints to pull data from,
- their relationships,
- how to handle pagination,
- authentication.

`dlt` handles the rest for you: **unnesting the data, inferring the schema**, and **writing it to the destination**.

In the previous lesson, you already used the REST API Client. `dlt`’s **[RESTClient](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fhttp%2Frest-client)** is the **low-level abstraction** that powers the RestAPI source.

```python
@dlt.source(name="github")
def github_source(access_token: Optional[str] = dlt.secrets.value) -> Any:
    # Create a REST API configuration for the GitHub API
    # Use RESTAPIConfig to get autocompletion and type checking
    print("Token configured:", bool(access_token))
    print("Token prefix:", access_token[:4] + "..." if access_token else None)

    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.github.com/repos/dlt-hub/dlt/",
            "paginator": {
                "type": "json_link",
                "next_url_path": "paging.next",
            },
            # we add an auth config if the auth token is present
            "auth": (
                {
                    "type": "bearer",
                    "token": access_token,
                }
                if access_token
                else None
            ),
        },
        # The default configuration for all resources and their endpoints
        "resource_defaults": {
            "primary_key": "id",
            "write_disposition": "merge",
            "endpoint": {
                "params": {
                    "per_page": 100,
                },
            },
        },
        "resources": [
            # This is a simple resource definition,
            # that uses the endpoint path as a resource name:
            # "pulls",
            # Alternatively, you can define the endpoint as a dictionary
            # {
            #     "name": "pulls", # <- Name of the resource
            #     "endpoint": "pulls",  # <- This is the endpoint path
            # }
            # Or use a more detailed configuration:
            {
                "name": "issues",
                "endpoint": {
                    "path": "issues",
                    "response_actions": [rate_limit],
                    # Query parameters for the endpoint
                    "params": {
                        "sort": "updated",
                        "direction": "desc",
                        "state": "open",
                        # Define `since` as a special parameter
                        # to incrementally load data from the API.
                        # This works by getting the updated_at value
                        # from the previous response data and using this value
                        # for the `since` query parameter in the next request.
                        "since": "{incremental.start_value}",
                    },
                    # For incremental to work, we need to define the cursor_path
                    # (the field that will be used to get the incremental value)
                    # and the initial value
                    "incremental": {
                        "cursor_path": "updated_at",
                        "initial_value": pendulum.today().subtract(days=30).to_iso8601_string(),
                    },
                },
            },
            # The following is an example of a resource that uses
            # a parent resource (`issues`) to get the `issue_number`
            # and include it in the endpoint path:
            {
                "name": "issue_comments",
                "endpoint": {
                    # The placeholder `{resources.issues.number}`
                    # will be replaced with the value of `number` field
                    # in the `issues` resource data
                    "path": "issues/{resources.issues.number}/comments",
                    "response_actions": [rate_limit],
                },
                # Include data from `id` field of the parent resource
                # in the child data. The field name in the child data
                # will be called `_issues_id` (_{resource_name}_{field_name})
                "include_from_parent": ["id"],
            },
            {
                "name": "contributors",
                "endpoint": {
                    # The placeholder `{resources.issues.number}`
                    # will be replaced with the value of `number` field
                    # in the `issues` resource data
                    "path": "contributors",
                    "response_actions": [rate_limit],
                },
            },
        ],
    }

    yield from rest_api_resources(config)
```

## SQL Database Source

SQL databases are management systems (DBMS) that store data in a structured format, commonly used for efficient and reliable data retrieval.

The `sql_database` verified source loads data to your specified destination using one of the following backends:

- SQLAlchemy,
- PyArrow,
- pandas,
- ConnectorX.

Before running the pipeline, make sure to install all the necessary dependencies:

1. **General dependencies**: These are the general dependencies needed by the `sql_database` source.
2. **Database-specific dependencies**: In addition to the general dependencies, you will also need to install `pymysql` to connect to the MySQL database in this tutorial.

```
pip install pymysql
```
  
Explanation: dlt uses SQLAlchemy to connect to the source database and hence, also requires the database-specific SQLAlchemy dialect, such as `pymysql` (MySQL), `psycopg2` (Postgres), `pymssql` (MSSQL), `snowflake-sqlalchemy` (Snowflake), etc. See the [SQLAlchemy docs](https://docs.sqlalchemy.org/en/20/dialects/#external-dialects) for a full list of available dialects.

```python
from dlt.sources.sql_database import sql_database

sql_source = sql_database(
    "mysql+pymysql://rfamro@mysql-rfam-public.ebi.ac.uk:4497/Rfam",
    table_names=[
        "family",
    ],
)

sql_db_pipeline = dlt.pipeline(
    pipeline_name="sql_database_example",
    destination="duckdb",
    dataset_name="sql_data",
    dev_mode=True,
)

load_info = sql_db_pipeline.run(sql_source)
print(load_info)
```

To successfully connect to your SQL database, you will need to pass credentials into your pipeline. dlt automatically looks for this information inside the generated TOML files. Simply paste the [connection details](https://docs.rfam.org/en/latest/database.html) inside `secrets.toml` as follows:

```toml
[sources.sql_database.credentials]
drivername = "mysql+pymysql" # database+dialect
database = "Rfam"
password = ""
username = "rfamro"
host = "mysql-rfam-public.ebi.ac.uk"
port = 4497
```

Alternatively, you can also paste the credentials as a connection string:

```toml
sources.sql_database.credentials="mysql+pymysql://rfamro@mysql-rfam-public.ebi.ac.uk:4497/Rfam"
```

Note that, when you pass a connection string to DLT that includes parameters such as the database, dialect, host, and port, DLT **may still attempt to retrieve any missing parameters** from your `secrets.toml` file to complete the connection configuration. Mixing these two configuration methods can **lead to unexpected behavior**, such as DLT supplying a password that was not included in the connection string, so we recommend using a single approach. Provide all connection details in the connection string or configure them entirely in `secrets.toml`. This way, you ensure predictable credential resolution.

## Filesystem Source

The filesystem source allows seamless loading of files from the following locations:

- AWS S3
- Google Cloud Storage
- Google Drive
- Azure Blob Storage
- remote filesystem (via SFTP)
- local filesystem

The filesystem source natively supports CSV, Parquet, and JSONL files and allows customization for loading any type of structured file. The Filesystem source doesn't just give you an easy way to load data from both remote and local files — it also comes with a powerful set of tools that let you customize the loading process to fit your specific needs. Filesystem source loads data in two steps:

1. It accesses the files in your remote or local file storage **without** actually **reading** the content yet. At this point, you can filter files by metadata or name. You can also set up incremental loading to load only new files.
2. The **transformer** **reads** the files' content and yields the records. At this step, you can filter out the actual data, enrich records with metadata from files, or perform incremental loading based on the file content.

## Destinations

Most likely, the destination where you want to load data is already a `dlt` integration that undergoes several hundred automated tests every day. If not, you can define a custom destination and still benefit from most `dlt`-specific features. Switching between destinations in `dlt` is incredibly straightforward. Simply modify the `destination` parameter in your pipeline configuration.

```python
data_pipeline = dlt.pipeline(
    pipeline_name="data_pipeline",
    destination="duckdb",
    dataset_name="data",
)
print(data_pipeline.destination.destination_type)

data_pipeline = dlt.pipeline(
    pipeline_name="data_pipeline",
    destination="bigquery",
    dataset_name="data",
)
print(data_pipeline.destination.destination_type)
```
