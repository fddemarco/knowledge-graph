### Cursor-based Incremental Loading
In most REST APIs (and other data sources, i.e., database tables), you can request new or updated data by passing a timestamp or ID of the "last" record to a query. The API/database returns just the new/updated records from which you take the maximum/minimum timestamp/ID for the next load.

To do incremental loading this way, we need to:

- Figure out which field is used to track changes (the so-called **cursor field**) (e.g., "inserted_at", "updated_at", etc.);
- Determine how to pass the "last" (maximum/minimum) value of the cursor field to an API to get just new or modified data (how we do this depends on the source API).

Once you've figured that out, `dlt` takes care of finding maximum/minimum cursor field values, removing duplicates, and managing the state with the last values of the cursor.

```python
data = [
    {
        "id": "1",
        "name": "bulbasaur",
        "size": {"weight": 6.9, "height": 0.7},
        "created_at": "2024-12-01",
    },
    {
        "id": "4",
        "name": "charmander",
        "size": {"weight": 8.5, "height": 0.6},
        "created_at": "2024-09-01",
    },
    {
        "id": "25",
        "name": "pikachu",
        "size": {"weight": 6, "height": 0.4},
        "created_at": "2023-06-01",
    },
]
cursor_date = dlt.sources.incremental("created_at", initial_value="2024-01-01")

@dlt.resource(
    name="pokemon",
    write_disposition={"disposition": "append"}, #, "strategy": "delete-insert"},
)
def append_pokemon(cursor_date=cursor_date):
	...
    yield data

@dlt.resource(primary_key="id")
def repo_issues(
    access_token=dlt.secrets.value,
    repository=dlt.config.value,
    updated_at = dlt.sources.incremental(
	    "updated_at", initial_value="1970-01-01T00:00:00Z")
):
    # Get issues since "updated_at" stored in state on previous run (or initial_value on first run)
    for page in _get_issues_page(
	    access_token,
	    repository,
	    since=updated_at.start_value
	):
        yield page
        # Last_value is updated after every page
        print(updated_at.last_value)
```

Here we add an `updated_at` argument that will receive incremental state, initialized to `1970-01-01T00:00:00Z`. It is configured to track the `updated_at` field in issues yielded by the `repo_issues` resource. It will store the newest `updated_at` value in `dlt` [state](https://dlthub.com/docs/general-usage/state) and make it available in `updated_at.start_value` on the next pipeline run. This value is inserted in the `_get_issues_page` function into the request query param **since** to the [GitHub API](https://docs.github.com/en/rest/issues/issues?#list-repository-issues).

In essence, the `dlt.sources.incremental` instance above:

- **updated_at.initial_value** which is always equal to "1970-01-01T00:00:00Z" passed in the constructor
- **updated_at.start_value** a maximum `updated_at` value from the previous run or the **initial_value** on the first run
- **updated_at.last_value** a "real-time" `updated_at` value updated with each yielded item or page. Before the first yield, it equals **start_value**
- **updated_at.end_value** (here not used) [marking the end of the backfill range](https://dlthub.com/docs/general-usage/incremental/cursor#using-end_value-for-backfill)

When paginating, you probably need the **start_value** which does not change during the execution of the resource, however, most paginators will return a **next page** link which you should use. Behind the scenes, dlt will deduplicate the results, i.e., in case the last issue is returned again (`updated_at` filter is inclusive) and skip already loaded ones.

#### Hints
Alternatively, you can use `apply_hints` on a resource to define an incremental field:

```python
resource = resource()
resource.apply_hints(incremental=dlt.sources.incremental("updated_at"))
```

When you apply an incremental hint using `apply_hints`, the source still performs a full extract. The incremental hint is used by `dlt` to filter the data after it has been extracted, before it is loaded into the destination.
#### last_value_func

`dlt.sources.incremental` allows you to choose a function that orders (compares) cursor values to the current `last_value`.

- The default function is the built-in `max`, which returns the larger value of the two.
- Another built-in, `min`, returns the smaller value.

You can also pass your custom function. This lets you define `last_value` on nested types, i.e., dictionaries, and store indexes of last values, not just simple types. The `last_value` argument is a [JSON Path](https://github.com/json-path/JsonPath#operators) and lets you select nested data (including the whole data item when `$` is used). The example below creates a last value which is a dictionary holding a max `created_at` value for each created table name:

```python
def by_event_type(event):
    last_value = None
    if len(event) == 1:
        item, = event
    else:
        item, last_value = event

    if last_value is None:
        last_value = {}
    else:
        last_value = dict(last_value)
    item_type = item["type"]
    last_value[item_type] = max(item["created_at"], last_value.get(item_type, "1970-01-01T00:00:00Z"))
    return last_value

@dlt.resource(primary_key="id", table_name=lambda i: i['type'])
def get_events(last_created_at = dlt.sources.incremental("$", last_value_func=by_event_type)):
    with open("tests/normalize/cases/github.events.load_page_1_duck.json", "r", encoding="utf-8") as f:
        yield json.load(f)
```

#### row_order
\With the `row_order` argument set, dlt will stop retrieving data from the data source (e.g., GitHub API) if it detects that the values of the cursor field are out of the range of **start** and **end** values.

In particular:

- dlt stops processing when the resource yields any item with a cursor value _equal to or greater than_ the `end_value` and `row_order` is set to **asc**. (`end_value` is not included)
- dlt stops processing when the resource yields any item with a cursor value _lower_ than the `last_value` and `row_order` is set to **desc**. (`last_value` is included)



In the example below, we incrementally load the GitHub events, where the API does not let us filter for the newest events - it always returns all of them. Nevertheless, `dlt` will load only the new items, filtering out all the duplicates and past issues.

```python
# Use naming function in table name to generate separate tables for each event
@dlt.resource(primary_key="id", table_name=lambda i: i['type'])  # type: ignore
def repo_events(
    last_created_at = dlt.sources.incremental(
	    "created_at",
	    initial_value="1970-01-01T00:00:00Z",
	    last_value_func=max),
	row_order="desc"
) -> Iterator[TDataItems]:
    repos_path = "/repos/%s/%s/events" % (
	    urllib.parse.quote(owner),
	    urllib.parse.quote(name)
	)
    for page in _get_rest_pages(access_token, repos_path + "?per_page=100"):
        yield page
```

We just yield all the events and `dlt` does the filtering (using the `id` column declared as `primary_key`). GitHub returns events ordered from newest to oldest. So we declare the `rows_order` as **descending** to [stop requesting more pages once the incremental value is out of range](https://dlthub.com/docs/general-usage/incremental/cursor#declare-row-order-to-not-request-unnecessary-data). We stop requesting more data from the API after finding the first event with `created_at` earlier than `initial_value`.

`dlt.sources.incremental` is implemented as a [filter function](https://dlthub.com/docs/general-usage/resource#filter-transform-and-pivot-data) that is executed **after** all other transforms you add with `add_map` or `add_filter`. This means that you can manipulate the data item before the incremental filter sees it. For example:

- You can create a surrogate primary key from other columns
- You can modify the cursor value or create a new field composed of other fields
- Dump Pydantic models to Python dicts to allow incremental to find custom values

Row order is most useful when:

1. The data source does **not** offer start/end filtering of results (e.g., there is no `start_time/end_time` query parameter or similar).
2. The source returns results **ordered by the cursor field**.

The GitHub events example is exactly such a case. The results are ordered on cursor value descending, but there's no way to tell the API to limit returned items to those created before a certain date. Without the `row_order` setting, we'd be getting all events, each time we extract the `github_events` resource.