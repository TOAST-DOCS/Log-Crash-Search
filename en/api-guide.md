## Data & Analytics > Log & Crash Search > API Guide
### Appkey and SecretKey

AppKey and SecretKey are required to use the Log & Crash Search API.

An Appkey is a unique authentication key issued for each NHN Cloud service, used to identify the service and validate API requests. A SecretKey is a private key used to control access to the API. For more information on checking and using Appkeys, please refer to the [Appkey](/nhncloud/en/public-api/appkey).

## Collect Log API

Logs can be sent to Log & Crash collector server via HTTP protocol. 

> - Use the following address to send logs to the Log & Crash collector server with JSON/HTTP.
>     - Log & Crash: api-logncrash.nhncloudservice.com
>     - Method of Delivery: POST
>     - URI: /v2/log
>     - Content-Type: "application/json"
> - Check, before log delivery, if a project has been registered at Log & Crash.
> - "logTime" is applied in the Log & Crash system; the key is ignored at Log & Crash.
> - Take caution for not including a space character in the key name. For instance, "UserID" is considered a different key from "UserID ".
> - One HTTP request can be no larger than 52MB. 
> - One log (JSON) can be no larger than 8MB (8,388,608 bytes).

Use the JSON format as below: 

```
{
	"projectName": "__Appkey__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "This log message come from HTTP client.",
	"logSource": "http",
	"logType": "nelo2-log",
	"host": "localhost"
}
```

[Default Parameters]

```
Parameter for Log Search 

projectName: string, required
	[in] Appkey

projectVersion: string, required
	[in] Version. Allows user-specifics. Includes "A~Z, a~z, 0~9, -._" only.

body: string, optional
	[in] Log messages.

logVersion: string, required
	[in] Log format version. "v2".

logSource: string, optional
	[in] Log source. Used for filtering at Log Search. "http", if not defined.

logType: string, optional
	[in] Log type. Used for filtering at Log Search. "log", if not defined. 

host: string, optional
	[in] Address of a log-sending device. Automatically filled by using peer-address at the collector server, if not defined.
```

[Other Parameters]

```
sendTime: string, optional
	[in] Time sent by device. Enter Unix timestamp for input.

logLevel: string, optional
	[in] For Syslog event.

UserBinaryData: string, optional
	[in] Display [Download|Show] link on the log search screen, and send with values encoded with base64.

UserTxtData: string, optional
	[in] Show [Download|View] link on the log search page, to be sent with base64 encoded value. 

txt*: string, optional
	[in] Save fields starting with txt (e.g. txtMessage or txt_description) as text fields. Allows search by partial character strings of a field value (full text search) on the log search page. Field size can be no larger than 1MB.  

long*: long, optional
    [in] Save fields starting with long (e.g. longElapsedTime, long_elapsed_time) as long-type fields. Allows search of long-type range on the log search page. 

double*: double, optional
    [in] Save fields starting with double (e.g. doubleAvgScore, double_avg_score) as double-type fields. Allows search of double-type range on the log search page.   
```

[Custom Fields]

```
A custom field name must start with "A-Z, a-z", allowing "A-Z, a-z, 0-9, -, _". 

Redundancy is not allowed for a name with basic or crash parameters. 

Search for a custom field is available only for an exact match.

A custom field can be no longer than 1 KB. If you need to send data exceeding 1 KB, or search for a partial string within a field value, you must create the field with a txt* prefix.
```

[Return Value]
Returned like follows, at the collector server: 

```
Content-Type: application/json

{
	"header":{
		"isSuccessful":true,
		"resultCode":0,
		"resultMessage":"Success"
	}
}

isSuccessful: boolean
	[out] True for success; false for failure 
resultCode: int
	[out] 0 for success; error code for failure 

resultMessage: string
	[out] "Success" for success; error message for failure 
```

[Bulk Delivery]
Sent in the JSON array format, for bulk delivery. 

```
[
    {
        "projectName": "__Appkey__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client. (1/2)",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost"
    },
    {
        "projectName": "__Appkey__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client. (2/2)",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost"
    }
]
```

* Note
    * On the web, logs are aligned for display in the receiving time order; but bulk delivery is considered to have been received on same time, and user delivery order is not maintained. 
        * To maintain the order of bulk-delivery logs, add the `lncBulkIndex` field to each log and specify Integer before delivery; and, the server shows the descending order of the value. 

```
[
    {
        "projectName": "__Appkey__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "first message",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost",
        "lncBulkIndex":1
    },
    {
        "projectName": "__Appkey__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "second message",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost",
        "lncBulkIndex":2
    }
]
```
        * If it has been delivered like the above, the server shows in the order of second message -> first message. 

At the collector server, each result value is returned in the JSON array type, in the order of delivery time. 

```
Content-Type: application/json

{
    "header":{
        "isSuccessful":true,
        "resultCode":0,
        "resultMessage":"Success"
    },
    "body":{
        "data":{
            "total":5,
            "errors":2,
            "resultList":[
                {"isSuccessful":true, "resultMessage":"Success"},
                {"isSuccessful":true, "resultMessage":"Success"},
                {"isSuccessful":false, "resultMessage":"LogVersion Mismatch: v1, /v2/log"},
                {"isSuccessful":false, "resultMessage":"The project(invalidProject) is not registered"},
                {"isSuccessful":true, "resultMessage":"Success"}
            ]
        }
    }
}

total: int
    [out] Total number of delivered logs

errors: int
    [out] Number of errors in delivered logs

resultList: array
    [out] Result value of each delivered log
```

### Samples

[When log is normally sent with curl]

```
//Send logs with POST method 
$ curl -H "content-type:application/json" -XPOST 'https://api-logncrash.nhncloudservice.com/v2/log' -d '{
	"projectName": "__Appkey__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http"
}'
```

[When it fails in log delivery]

```
//When URL is incorrect (log -> loggg)
$ curl -v -H 'content-type:application/json' -XPOST "api-logncrash.nhncloudservice.com/v2/loggg" -d '{
	"projectName": "__Appkey__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http"
}'


//When a wrong field key (_xxx) is used
$ curl -v -H 'content-type:application/json' -XPOST "api-logncrash.nhncloudservice.com/v2/log" -d '{
	"projectName": "__Appkey__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http",
	"_xxx": "this is a invalid key"
	}'
A custom key, starting with an alphabet, must include "A~Z, a~z, 0~9, -_".
A custom key, starting with an alphabet, must include "A~Z, a~z, 0~9, -_".
```

[Bulk log delivery using curl]

```
//Send logs with POST method 
$ curl -H "content-type:application/json" -XPOST 'https://api-logncrash.nhncloudservice.com/v2/log' -d '[
    {
        "projectName": "__Appkey__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client, and it is a simple bulk sample. (1/2)",
        "logSource": "http",
        "logType": "nelo2-log"
    },
    {
        "projectName": "__Appkey__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client, and it is a simple bulk sample. (2/2)",
        "logSource": "http",
        "logType": "nelo2-log"
    }
]'
```


## Log Search API

> [Caution] This API is scheduled for deprecation. For new development, we recommend using the [v3 Log Search API](#v3-log-search-api) below.

Saved logs can be searched using Lucene queries.</br>
The log search API limits the amount of requests per hour according to user pattern. The resources available while searching are represented as tokens, and some of them are deducted whenever the search API is called. The API is available for use as long as the number of remaining tokens is a positive number.</br>
The number of tokens deducted when searching an item varies depending on the search duration, size, and the complexity of a query. Tokens are automatically replenished over time.</br>

![lncs-api-01-20230925](https://static.toastoven.net/prod_logncrash/lncs-api-01-20230925.png)

### Basic Information
```
API Endpoint: https://api-lncs-search.nhncloudservice.com
```
```
Only logs created in the past 90 days can be searched. The range of start time and end time cannot exceed 31 days.
```

### Search API
Retrieves logs within the specified time range using a Lucene query. There is no limit on the search results (`totalItems`), but the range that can be retrieved through paging is limited to a maximum of 100,000 (`pageNumber × pageSize ≤ 100,000`). To retrieve more logs than this, use the Search API (cursor pagination) or the Scroll API.
```
POST /api/v2/search/{appkey}

Content-Type: application/json
```

#### Request Parameter
| Name | Format | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project appkey | O |

#### Request Header
| Name | Format | Description             | Required |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | Project SecretKey | O |

#### Request Body
| Name | Format | Description | Required | Note |
| --- | --- | --- | --- | --- |
| query | String | Lucene query | O |  |
| from | String | Start time | O | ISO8601 format date (YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | End time | O | ISO8601 format date (YYYY-MM-DDThh:mm:ss.sTZD) |
| pageNumber | Number | Page number |  | Default value 0 |
| pageSize | Number | Page size |  | Min 10, Max 100. |
| sort | Object | Sort by |  | Set ascending (ASC) and descending (DESC) by field |

<details>
<summary>Example</summary>

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2021-01-01T10:00:00+09:00",
  "to": "2021-01-01T11:00:00+09:00",
  "pageSize": 10,
  "pageNumber": 1,
  "sort": {
      "projectVersion": "asc"
  }
}
```
</details>

#### Response
| Name | Type | Format | Description |
| --- | --- | --- | --- |
| totalItems | Body | Number | Number of logs |
| pageNumber | Body | Number | Page number |
| pageSize | Body | Number | Page size |
| data | Body | List | Log list |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "totalItems": 50,
        "pageNumber": 1,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>


### Search API (Cursor Pagination)
By specifying the URL query parameter `?cursor` at the same endpoint as the Search API to opt in, you can use cursor (search_after)-based pagination. Even when moving to deep pages, you can retrieve subsequent pages sequentially without being affected by the result window limit of `pageNumber × pageSize` (100,000 for the basic Search API).

```
POST /api/v2/search/{appkey}?cursor

Content-Type: application/json
```

> - Cursor pagination is activated when the URL query parameter `?cursor` or `?cursor=true` is specified. If you do not opt in, the existing Search API behavior remains unchanged.
> - When opting in to cursor pagination, you cannot send `pageNumber` together with it (specifying both results in a 400 response). Leave `cursor` empty for the first page request, and for subsequent pages, pass the `nextCursor` value from the previous response as-is in the `cursor` field of the next request.
> - The `cursor` value is an opaque string that encodes the server's internal sort state. Do not parse or modify it on the client side.
> - The page size limit per call (`pageSize` maximum value of 100) applies the same as in the standard Search API.
> - When you reach the last page, the response body does not include the `nextCursor` field.

#### Request Parameters
| Name | Category | Type | Description | Required |
| --- | --- | --- | --- | --- |
| appkey | Path | String | Project app key | O |
| cursor | Query | - | Cursor-based pagination opt-in flag. Activated when `?cursor` or `?cursor=true` is specified | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-LNCS-SECRET | String | Project Secret Key | O |

#### Request Body
| Name | Type | Description | Required | Note |
| --- | --- | --- | --- | --- |
| query | String | Lucene query | O |  |
| from | String | Start time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | End time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| pageSize | Number | Page size |  | Default 10, maximum 100 |
| sort | Object | Sort criteria |  | Sets ascending (ASC) or descending (DESC) order per field |
| cursor | String | Cursor for retrieving the next page |  | Omitted for the first page. For subsequent pages, pass the `nextCursor` value from the previous response as-is. Cannot be used together with `pageNumber` (400) |

<details>
<summary>Example</summary>

First page request (`cursor` not specified):

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2021-01-01T10:00:00+09:00",
  "to": "2021-01-01T11:00:00+09:00",
  "pageSize": 10,
  "sort": {
      "logTime": "desc"
  }
}
```

Next page request (passing the previous response's `nextCursor` as-is):

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2021-01-01T10:00:00+09:00",
  "to": "2021-01-01T11:00:00+09:00",
  "pageSize": 10,
  "sort": {
      "logTime": "desc"
  },
  "cursor": "g2VleUlkLi4u"
}
```
</details>

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| totalItems | Body | Number | Number of logs |
| pageSize | Body | Number | Page size |
| data | Body | List | List of logs |
| nextCursor | Body | String | Cursor for retrieving the next page. Included only when a next result exists; not included on the last page |

<details>
<summary>Example</summary>

When a next page exists (response includes `nextCursor`):

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "totalItems": 50,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ],
        "nextCursor": "g2VleUlkLi4u"
    }
}
```

When it is the last page (response does not include `nextCursor`):

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "totalItems": 50,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>


### Scroll Start API
Searches all the logs within the specified time frame using the Lucene query without pages specified. It can be used with Scroll Continue API to search logs multiple times.
```
POST /api/v2/search/scroll/{appkey}

Content-Type: application/json
```

#### Request Parameter
| Name | Format | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project appkey | O |

#### Request Header
| Name | Format | Description             | Required |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | Project SecretKey | O |

#### Request Body
| Name | Format | Description | Required | Note |
| --- | --- | --- | --- | --- |
| query | String | Lucene query | O |  |
| from | String | Start time | O | ISO8601 format date (YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | End time | O | ISO8601 format date (YYYY-MM-DDThh:mm:ss.sTZD) |
| pageSize | Number | Page size |  | Min 10, Max 100. |
| sort | Object | Sort by |  | Set ascending (ASC) and descending (DESC) by field |

<details>
<summary>Example</summary>

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2021-01-01T10:00:00+09:00",
  "to": "2021-01-01T11:00:00+09:00",
  "pageSize": 10,
  "sort": {
      "projectVersion": "asc"
  }
}
```
</details>

#### Response
| Name | Type | Format | Description |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | Number of logs |
| pageSize | Body | Number | Page size |
| data | Body | List | Log list |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "scrollKey": "51482f39-d499-394d-adca-462585a477e9",
        "totalItems": 60,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>


### Scroll Continue API
Continues searching logs by specifying the Scroll Key obtained from Scroll Start API or the previously called Scroll Continue API.<br>
Scroll Key is valid for 1 minute.
```
POST /api/v2/search/scroll/{appkey}/{scrollKey}

Content-Type: application/json
```

#### Request Parameter
| Name | Format | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project appkey | O |
| scrollKey | String | Scroll Key | O |

#### Request Header
| Name | Format | Description             | Required |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | Project SecretKey | O |

#### Request Body
Scroll Continue API does not require the request body.

#### Response
| Name | Type | Format | Description |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | Number of logs |
| data | Body | List | Log list |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "scrollKey": "51482f39-d499-394d-adca-462585a477e9",
        "totalItems": 60,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>

### Available Token API
Retrieves the number of available tokens.
```
GET /api/v2/search/available-tokens/{appkey}
```

#### Request Parameter
| Name | Format | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project appkey | O |

#### Request Header
| Name | Format | Description             | Required |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | Project SecretKey | O |

#### Response
| Name | Type | Format | Description |
| --- | --- | --- | --- |
| availableToken | Body | Number | Available tokens |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "availableToken": 9875
    }
}
```
</details>


## v3 Log Search API

You can search stored logs using a Lucene query, and it provides features to upload, retrieve, and delete Symbol files for crash analysis.<br>
The log search API limits the amount you can request per hour, depending on your usage pattern. The resources available for search are represented as tokens, and a certain amount is deducted according to internal criteria each time you call the search API. You can use the search API as long as your remaining token balance is positive.<br>
The number of tokens deducted per search varies depending on the search period, data volume, and query complexity, and tokens are automatically replenished over time.<br>

### Authentication

The User Access Key token is supported as a method for API calls and authentication.<br>
For information on how to issue a token, see the link below.

[User Access Key Token](https://docs.nhncloud.com/en/nhncloud/en/public-api/user-access-key-token/)

#### Example HTTP Header for an API Request
```
X-NHN-Authorization: Bearer {Access Token}
```

### Search API
Retrieves logs within the specified time range using a Lucene query. There is no limit on the search results (`totalItems`), but the range that can be retrieved through paging is limited to a maximum of 100,000 (`pageNumber × pageSize ≤ 100,000`). To retrieve more logs than this, use the Cursor Search API or the Scroll API.
```
POST /v3/{appkey}/logs/search

Content-Type: application/json
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Request Body
| Name | Type | Description | Required | Note |
| --- | --- | --- | --- | --- |
| query | String | Lucene query | O |  |
| from | String | Start time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | End time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| pageNumber | Number | Page number |  | Default 0 |
| pageSize | Number | Page size |  | Default 10, maximum 100 |
| sort | Object | Sort criteria |  | Sets ascending (ASC) or descending (DESC) order per field |

<details>
<summary>Example</summary>

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2026-03-24T00:00:00+09:00",
  "to": "2026-03-24T23:59:59.999+09:00",
  "pageSize": 10,
  "pageNumber": 0,
  "sort": {
      "logTime": "DESC"
  }
}
```
</details>

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| totalItems | Body | Number | Number of logs |
| pageNumber | Body | Number | Page number |
| pageSize | Body | Number | Page size |
| data | Body | List | List of logs |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "totalItems": 20927,
        "pageNumber": 0,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>


### Cursor Search API
Searches logs using cursor (opaque)-based pagination.<br>
Even when moving to deep pages, you can retrieve results sequentially without being affected by the result window limit of `pageNumber × pageSize`.

- Omit `cursor` in the body for the first page request.
- For subsequent page requests, pass the `nextCursor` value from the previous response as-is in the `cursor` field of the body.
- When you reach the last page, the response body does not include `nextCursor`.
- The `cursor` value is an opaque string that encodes the backend's internal sort state. Do not parse or modify it on the client side.
- `pageNumber` is not used; including it in the body returns a 400 response.

```
POST /v3/{appkey}/logs/cursor

Content-Type: application/json
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Request Body
| Name | Type | Description | Required | Note |
| --- | --- | --- | --- | --- |
| query | String | Lucene query | O |  |
| from | String | Start time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | End time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| pageSize | Number | Page size |  | Default 10, maximum 100 |
| sort | Object | Sort criteria |  | Sets ascending (ASC) or descending (DESC) order per field |
| cursor | String | The `nextCursor` value from the previous response |  | Omitted for the first page request |

<details>
<summary>Example</summary>

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2026-03-24T00:00:00+09:00",
  "to": "2026-03-24T23:59:59.999+09:00",
  "pageSize": 10,
  "sort": {
      "logTime": "DESC"
  },
  "cursor": "g6JpdGVtc4123WsBYWKhYWOhYWQ"
}
```
</details>

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| totalItems | Body | Number | Number of logs |
| pageNumber | Body | Number | Page number (always fixed at `0` in cursor mode; not meaningful) |
| pageSize | Body | Number | Page size |
| data | Body | List | List of logs |
| nextCursor | Body | String | Opaque cursor for retrieving the next page (not included on the last page) |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "totalItems": 20907,
        "pageNumber": 0,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ],
        "nextCursor": "ghsAAAGePyNW0XZpRnFtZm42Q31231pRcHJ2UC9MMGpR"
    }
}
```
</details>


### Scroll Start API
Retrieves all logs within the specified time range using a Lucene query, without specifying a page. Use this together with the Scroll Continue API to retrieve results across multiple calls.
```
POST /v3/{appkey}/logs/scroll

Content-Type: application/json
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Request Body
| Name | Type | Description | Required | Note |
| --- | --- | --- | --- | --- |
| query | String | Lucene query | O |  |
| from | String | Start time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | End time | O | Date in ISO8601 format (YYYY-MM-DDThh:mm:ss.sTZD) |
| pageSize | Number | Page size |  | Default 10, maximum 100 |
| sort | Object | Sort criteria |  | Sets ascending (ASC) or descending (DESC) order per field |

<details>
<summary>Example</summary>

```json
{
  "query": "logType:\"NORMAL\"",
  "from": "2026-03-24T00:00:00+09:00",
  "to": "2026-03-24T23:59:59.999+09:00",
  "pageSize": 10,
  "sort": {
      "logTime": "DESC"
  }
}
```
</details>

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | Number of logs |
| pageSize | Body | Number | Page size |
| data | Body | List | List of logs |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "scrollKey": "12345bd8-d5a3-3d42-8711-16bc225b0e59",
        "totalItems": 20943,
        "pageSize": 10,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>


### Scroll Continue API
Continues retrieving logs by specifying the Scroll Key obtained from the Scroll Start API or the most recently called Scroll Continue API.<br>
The Scroll Key is valid for 1 minute.
```
POST /v3/{appkey}/logs/scroll/{scrollKey}

Content-Type: application/json
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |
| scrollKey | String | Scroll Key | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Request Body
The Scroll Continue API does not require a request body.

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | Number of logs |
| data | Body | List | List of logs |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "scrollKey": "12345bd8-d5a3-3d42-8711-16bc225b0e59",
        "totalItems": 20943,
        "data": [
            {
                "logTime": 1609463102265,
                "logType": "NORMAL",
                "projectVersion": "1.0.0",
                ...
            },
            ...
        ]
    }
}
```
</details>


### Available Token API
Retrieves the number of available tokens.
```
GET /v3/{appkey}/logs/available-token
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| availableToken | Body | Number | Available tokens |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "body": {
        "availableToken": 9975
    }
}
```
</details>


### Symbol Upload API
Uploads a Symbol file for crash analysis.
```
POST /v3/{appkey}/symbols?platform={platform}&version={version}&description={description}

Content-Type: multipart/form-data
```

#### Request Parameters
| Name | Category | Type | Description | Required |
| --- | --- | --- | --- | -- |
| appkey | Path | String | Project app key | O |
| platform | Query | String | Symbol target platform (one of `iOS`, `Android`, `Android-NDK`, `Windows`) | O |
| version | Query | String | Symbol version | O |
| description | Query | String | Symbol description (special characters such as spaces require URL encoding) |  |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Request Body
| Name | Type | Description | Required | Note |
| --- | --- | --- | --- | --- |
| symbolfile | Binary | Symbol file | O | Sent in multipart/form-data format |

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| result.data.id | Body | List | List of identifiers for the uploaded Symbol files |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "result": {
        "data": {
            "id": [
                "1239aaba9c74f678c6df8b8"
            ]
        }
    }
}
```
</details>


### Symbol List API
Retrieves the list of uploaded Symbol files. Filters by the `platform`/`version` values; to retrieve all, call with both values set to `all`.
```
GET /v3/{appkey}/symbols/{platform}/{version}
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |
| platform | String | Symbol platform filter (`all` to retrieve all) | O |
| version | String | Symbol version filter (`all` to retrieve all) | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| result.data | Body | List | List of Symbol files |

<details>
<summary>Example</summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultMessage": "success",
        "resultCode": 0
    },
    "result": {
        "data": [
            {
                ...
            }
        ]
    }
}
```
</details>


### Symbol Delete API
Deletes a single Symbol file.
```
DELETE /v3/{appkey}/symbols/{sid}
```

#### Request Parameters
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| appkey | String | Project app key | O |
| sid | String | Symbol file ID | O |

#### Request Header
| Name | Type | Description | Required |
| --- | --- | --- | --- |
| X-NHN-Authorization | String | User Access Key token in the format `Bearer {Access Token}` | O |

#### Response
| Name | Category | Type | Description |
| --- | --- | --- | --- |
| header.isSuccessful | Body | Boolean | Whether the request was successful |
| header.resultCode | Body | Number | Result code |
| header.resultMessage | Body | String | Result message |

<details>
<summary>Example</summary>

```json
{
  "header": {
    "isSuccessful": true,
    "resultMessage": "success",
    "resultCode": 0
  }
}
```
</details>
