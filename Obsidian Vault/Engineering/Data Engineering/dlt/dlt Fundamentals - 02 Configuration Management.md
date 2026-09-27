## Pagination

When working with APIs, you could implement pagination using only Python and the `requests` library. While this approach works, it often requires writing boilerplate code for tasks like managing authentication, constructing URLs, and handling pagination logic. **But!** We’re going to use dlt's **[RESTClient](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fhttp%2Frest-client)** to handle pagination seamlessly when working with REST APIs like GitHub.

**Why use RESTClient?**

RESTClient is part of dlt's helpers, making it easier to interact with REST APIs by managing repetitive tasks such as:

- Authentication
- Query parameter handling
- Pagination

This reduces boilerplate code and lets you focus on your data pipeline logic. 

1. Import `RESTClient`
2. Create a `RESTClient` instance
3. Use the `paginate` method to iterate through all pages of data

```python
from dlt.sources.helpers.rest_client import RESTClient
from dlt.sources.helpers.rest_client.paginators import HeaderLinkPaginator

# Pattern #1: Let DLT figure it out
client = RESTClient(
    base_url="https://api.github.com",
)
  
# Pattern #2: Specify paginator
client = RESTClient(
    base_url="https://api.github.com",
    paginator=HeaderLinkPaginator(),
)

for page in client.paginate("orgs/dlt-hub/events"):
    print(page)
```

The events endpoint doesn’t contain as much data, especially compared to the issue comments endpoint of the dlt repository. If you run the pipeline for the issue comments endpoint, there's a high chance that you'll face a **rate limit error**.

To avoid the **rate limit error** you can use [GitHub API Authentication](https://www.google.com/url?q=https%3A%2F%2Fdocs.github.com%2Fen%2Frest%2Fauthentication%2Fauthenticating-to-the-rest-api%3FapiVersion%3D2022-11-28):

1. Login to your GitHub account.
2. Generate an [API token](https://www.google.com/url?q=https%3A%2F%2Fdocs.github.com%2Fen%2Fauthentication%2Fkeeping-your-account-and-data-secure%2Fcreating-a-personal-access-token) (classic).
3. Use it as an access token for the GitHub API.

```python
from dlt.sources.helpers.rest_client.auth import BearerTokenAuth

client = RESTClient(
    base_url="https://api.github.com",
    auth=BearerTokenAuth(token=access_token),
)

for page in client.paginate("repos/dlt-hub/dlt/issues/comments"):
    print(page)
    break
```

In dlt, [configurations and secrets](https://www.google.com/url?q=https%3A%2F%2Fdlthub.com%2Fdocs%2Fgeneral-usage%2Fcredentials%2F) are essential for setting up data pipelines. **Configurations** are **non-sensitive** settings that define the behavior of a data pipeline, including file paths, database hosts, timeouts, API URLs, and performance settings. On the other hand, **secrets** are **sensitive** data like passwords, API keys, and private keys, which should never be hard-coded to avoid security risks. Both can be set up in various ways:

- As environment variables
- Within code using `dlt.secrets` and `dlt.config`
- Via configuration files (`secrets.toml` and `config.toml`)

To define the `access_token` secret value, we can use

1. `dlt.secrets` in code (recommended for secret vaults or dynamic creds)
2. Environment variables (recommended for prod)
3. `secrets.toml` file (recommended for local dev)

## **Use `dlt.secrets` in code**

You can easily set or update your secrets directly in Python code. This is especially convenient when retrieving credentials from third-party secret managers or when you need to update secrets and configurations dynamically.

```python
from google.colab import userdata

dlt.secrets["access_token"] = userdata.get("SECRET_KEY")
dlt.secrets["sources.access_token"] = userdata.get('SECRET_KEY')
dlt.secrets["sources.____main____.access_token"] = userdata.get('SECRET_KEY')
dlt.secrets["sources.____main____.github_source.access_token"] = userdata.get('SECRET_KEY')
```

- `sources` is a special word;
- `__main__` is a python module name;
- `github_source` is the resource name;
- `access_token` is the secret variable name.

To keep the **naming convention** flexible, dlt looks for a lot of **possible combinations** of key names, starting from the most specific possible path. Then, if the value is not found, it removes the right-most section and tries again, according to this hierarchy:

```md
pipeline_name
    |
    |-sources
        |
        |-<module name>
            |  
            |-<source function 1 name>
                |
                |- secret variable 1
                |- secret variable 2
```

## Configuration and secrets files
This example uses the [Notion](https://dlthub.com/docs/dlt-ecosystem/verified-sources/notion) source and [filesystem](https://dlthub.com/docs/dlt-ecosystem/destinations/filesystem) destination to demonstrate how to organize configuration in TOML files using the [recommended section layout](https://dlthub.com/docs/general-usage/credentials/setup#recommended-section-layout).

The Notion source is defined in a file named `notion.py`, so we use that module name in the configuration. We configure the `api_key` in our configuration while passing the list of database IDs explicitly in code. For the filesystem destination, we split configuration between `config.toml` (for `bucket_url`) and `secrets.toml` (for AWS credentials).

```python
import dlt

@dlt.source
def notion_databases(
	database_ids = None,
	api_key: str = dlt.secrets.value,  # mark argument to be injected as secret
):
	...
	# Pass database_id in code, let `dlt` inject api_key
	sales_database = notion_databases(  # type: ignore
		database_ids=[{
			"id": "a94223535c674d33a24e313e7921ce15",
			"use_name": "sales_alias"
			}])
```

**config.toml**
```toml
[runtime]
log_level="INFO"

# Do not compress files sent to the filesystem bucket
[normalize.data_writer]
disable_compression=true

# Recommended sections for the destination (destination.module)
[destination.filesystem]
bucket_url = "s3://[your_bucket_name]"
```

**secrets.toml**

```toml
# Recommended sections for sources (sources.module)
[sources.notion]
api_key = "your-notion-api-key"  # Will be injected to api_key argument

# Recommended sections for destination credentials
[destination.filesystem.credentials]
aws_access_key_id = "ABCDEFGHIJKLMNOPQRST" 
aws_secret_access_key = "1234567890_access_key" 
```

While `dlt` handles credentials automatically, you can also access them directly in your code. The `dlt.secrets` and `dlt.config` objects provide dictionary-like access to configuration values and secrets, enabling custom preprocessing if required. You can also store custom settings in the same configuration files.

```python
# Use `dlt.secrets` and `dlt.config` to explicitly retrieve values from providers
source_instance = google_sheets(
	dlt.config["sheet_id"],
	dlt.config["my_section.tabs"],
	dlt.secrets["my_section.gcp_credentials"]
)
source_instance.run(destination="bigquery")
```

`dlt.config` and `dlt.secrets` function as dictionaries. `dlt` examines all [config providers](https://dlthub.com/docs/general-usage/credentials/setup) - environment variables, TOML files, etc. - to populate these dictionaries. You can also use `dlt.config.get()` or `dlt.secrets.get()` to retrieve a value and convert it to a specific type:

```python
credentials = dlt.secrets.get(
	"my_section.gcp_credentials",
	GcpServiceAccountCredentials
)
```

This creates a `GcpServiceAccountCredentials` instance from the values stored under the `my_section.gcp_credentials` key.

## Environment variables

dlt uses a specific naming hierarchy to search for the secrets and config values. This makes configurations and secrets easy to manage. The naming convention for **environment variables** in dlt follows a specific pattern. All names are **capitalized** and sections are separated with **double underscores** __ , e.g. `SOURCES____MAIN____GITHUB_SOURCE__SECRET_KEY`.