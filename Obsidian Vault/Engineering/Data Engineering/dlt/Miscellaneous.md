## Advanced Topics
### Retry rules

By default, failing requests are retried up to 5 times with an exponentially increasing delay. That means the first retry will wait 1 second, and the fifth retry will wait 16 seconds.

If all retry attempts fail, the corresponding requests exception is raised. E.g., `requests.HTTPError` or `requests.ConnectionTimeout`.

All standard HTTP server errors trigger a retry. This includes:

- Error status codes. All status codes in the `500` range and `429` (too many requests). Commonly, servers include a `Retry-After` header with `429` and `503` responses. When detected, this value supersedes the standard retry delay.
- Connection and timeout errors. When the remote server is unreachable, the connection is unexpectedly dropped, or when the request takes longer than the configured `timeout`.

Many requests settings can be added to the runtime section in your `config.toml`. For example:

```toml
# config.toml

[runtime]
request_max_attempts = 10  # Stop after 10 retry attempts instead of 5
request_backoff_factor = 1.5  # Multiplier applied to the exponential delays. Default is 1
request_timeout = 120  # Timeout in seconds
request_max_retry_delay = 30  # Cap exponential delay to 30 seconds
```

HTTP 429 errors indicate you've hit API rate limits. The dlt requests client retries these automatically and respects `Retry-After` headers. If rate limits persist, consider additional mitigation strategies.

- **Check authentication**: Properly authenticated requests often have higher rate limits
- **Review API documentation**: Look for rate limit guidelines and `Retry-After` header usage
- **Add delays**: Use `time.sleep()` or a rate-limiting library to space out requests
- **Implement backoff**: Increase wait times after failures (exponential backoff)
- **Reduce calls**: Batch requests or cache results when possible

> 💡 The dlt requests client already handles basic `429` retries with exponential backoff and respects `Retry-After` headers.

For more control, you can create your own instance of `dlt.sources.requests.Client` and use that instead of the global client. This lets you customize which status codes and exceptions to retry on. You may even supply a custom retry condition in the form of a predicate. This is sometimes needed when loading from non-standard APIs which don't use HTTP error codes.

```python
from dlt.sources.helpers import requests

http_client = requests.Client(
	status_codes=(403, 500, 502, 503),
	exceptions=(requests.ConnectionError, requests.ChunkedEncodingError)
)

from dlt.sources.helpers import requests

def retry_if_error_key(
	response: Optional[requests.Response],
	exception: Optional[BaseException]
) -> bool:
	"""Decide whether to retry the request based on whether    the json response contains an `error` key    """
	if response is None:        # Fall back on the default exception predicate
		return False
	data = response.json()
	return 'error' in data

http_client = Client(retry_condition=retry_if_error_key)
```

### Custom hooks

`RESTClient.paginate()` allows you to specify a [custom hook function](https://requests.readthedocs.io/en/latest/user/advanced/#event-hooks) that can be used to modify the response objects. For example, to handle specific HTTP status codes gracefully:

```python
def custom_response_handler(response, *args):
    if response.status_code == 404:
        # Handle not found
        pass

client.paginate("/posts", hooks={"response": [custom_response_handler]})
```

The handler function may raise `IgnoreResponseException` to exit the pagination loop early. This is useful for endpoints that return a 404 status code when there are no items to paginate.

### Custom sessions

You can pass custom `requests` `Session` to `RESTClient`. `dlt` provides its own implementation where you can easily configure retry strategies, timeouts and other factors. For example:

```python
from dlt.sources.helpers import requests
client = RESTClient(
    base_url="https://api.example.com",
    session=requests.Client(request_timeout=(1.0, 1.0), request_max_attempts=0).session
)
```

will set-up the client for a short connect and read timeouts with no retries.

### Response actions

The `response_actions` field in the endpoint configuration allows you to specify how to handle specific responses or all responses from the API. For example, responses with specific status codes or content substrings can be ignored. Additionally, all responses or only responses with specific status codes or content substrings can be transformed with a custom callable, such as a function. This callable is passed on to the requests library as a [response hook](https://requests.readthedocs.io/en/latest/user/advanced/#event-hooks). The callable can modify the response object and must return it for the modifications to take effect.

**Fields:**

- `status_code` (int, optional): The HTTP status code to match.
- `content` (str, optional): A substring to search for in the response content.
- `action` (str or Callable or List[Callable], optional): The action to take when the condition is met. Currently supported actions:
    - `"ignore"`: Ignore the response.
    - a callable accepting and returning the response object.
    - a list of callables, each accepting and returning the response object.

#### Example A

```json
{
    "path": "issues",
    "response_actions": [
        {"status_code": 404, "action": "ignore"},
        {"content": "Not found", "action": "ignore"},
        {"status_code": 200, "content": "some text", "action": "ignore"},
    ],
}
```

In this example, the source will ignore responses with a status code of 404, responses with the content "Not found", and responses with a status code of 200 _and_ content "some text".

#### Example B

```python
from requests.models import Response
from dlt.common import json

def set_encoding(response, *args, **kwargs):
    # Sets the encoding in case it's not correctly detected
    response.encoding = 'windows-1252'
    return response


def add_and_remove_fields(response: Response, *args, **kwargs) -> Response:
    payload = response.json()
    for record in payload["data"]:
        record["custom_field"] = "foobar"
        record.pop("email", None)
    modified_content: bytes = json.dumps(payload).encode("utf-8")
    response._content = modified_content
    return response


source_config = {
    "client": {
        # ...
    },
    "resources": [
        {
            "name": "issues",
            "endpoint": {
                "path": "issues",
                "response_actions": [
                    set_encoding,
                    {
                        "status_code": 200,
                        "content": "some text",
                        "action": add_and_remove_fields,
                    },
                ],
            },
        },
    ],
}
```

In this example, the resource will set the correct encoding for all responses first. Thereafter, for all responses that have the status code 200, we will add a field `custom_field` and remove the field `email`.
