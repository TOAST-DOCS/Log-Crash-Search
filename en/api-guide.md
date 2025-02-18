## Data & Analytics > Log & Crash Search > API Guide

## Collect Log API
Logs can be sent to Log & Crash collector server via HTTP protocol. 

> - Use the following address to send logs to the Log & Crash collector server with JSON/HTTP. 
>     - Log & Crash: api-logncrash.nhncloudservice.com
>     - Method of Delivery: POST
>     - URI: /v2/log
>     - Content-Type: "application/json"
> - Check, before log delivery, if a project has been registered at Log & Crash. 
> - "logTime" is applied in the Log & Crash system; the key is ignored at Log & Crash.     
> -  Take caution for not including a space character in the key name. For instance, "UserID" is considered a different key from "UserID ". 
> -  One HTTP request can be no larger than 52MB. 
> -  One log (JSON) can be no larger than 8MB (8388608 bytes).

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
	[in] Version. Allows user-specifics. Includes "A~Z, a~z, 0~9,-._" only.

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
sendTime; string, optional
	[in] Time sent by device. Enter Unix timestamp for input.

logLevel; string, optional
	[in] For Syslog event.

UserBinaryData; string, optional
	[in] Display [Download|Show] link on the log search screen, and send with values encoded with base64.

UserTxtData; string, optional
	[in] Show [Download|View] link on the log search page, to be sent with base64 encdoed value. 

txt*; string, optional
	[in] Save fields starting with txt (e.g. txtMessage or txt_description) as text fields. Allows search by partial character strings of a field value (full text search) on the log search page. Field size can be no larger than 1MB.  

long*; long, optional
    [in] Save fields starting with long (e.g. longElapsedTime, long_elapsed_time) as long-type fields. Allows search of long-type range on the log search page. 

double*; double, optional
    [in] Save fields starting with double (e.g. doubleAvgScore, double_avg_score) as double-type fields. Allows search of double-type range on the log search page.   
```

[Custom Fields]

```
A custom field name must start with "A-Z, a-z", allowing "A-Z, a-z, 0-9, -, _". 

Redundancy is not allowed for a name with basic or crash parameters. 

Search for a custom field is available only for an exact match.

A custom field can be no longer than 1KB. To send larger than 1KB field or search only a part of a value, attach txt*prefix to create a field. 
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
        * To maintain the order of bulk-delivery logs, add the lncBulkIndex field to each log and specify Integer before delivery; and, the server shows the descending order of the value. 

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
            ]}
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
Saved logs can be searched using Lucene queries.</br>
The log search API limits the amount of requests per hour according to user pattern. The resources available while searching are represented as tokens, and some of them are deducted whenever the search API is called. The API is available for use as long as the number of remaining tokens is a positive number.</br>
The number of tokens deducted when searching an item varies depending on the search duration, size, and the complexity of a query. Tokens are automatically replenished over time.</br>
API requests must include the secretkey enabled in a project in the header.

![lncs-api-01-20230925](https://static.toastoven.net/prod_logncrash/lncs-api-01-20230925.png)

### Basic Information
```
API Endpoint: https://api-lncs-search.nhncloudservice.com
```
```
Only logs created in the past 90 days can be searched. The range of start time and end time cannot exceed 31 days.
```

### Search API
You can view logs within the specified time frame using the Lucene query. Phasing is used to view them, which allows you to search for up to 100,000 logs.
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
| X-LNCS-SECRET | String | Project secretkey | O |

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
| X-LNCS-SECRET | String | Project secretkey | O |

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
Continues searching logs by specifying the Scroll Key obtained from Scroll Start API or the previously called Scroll Continue API.</br>
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
| X-LNCS-SECRET | String | Project secretkey | O |

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
| X-LNCS-SECRET | String | Project secretkey | O |

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
