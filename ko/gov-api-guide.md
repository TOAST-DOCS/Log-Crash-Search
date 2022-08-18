## Data & Analytics > Log & Crash Search > API 가이드

## 로그 수집 API
HTTP 프로토콜을 사용해서 Log & Crash 수집 서버에 로그를 전송할수 있습니다. 

> - JSON/HTTP로 Log & Crash 수집 서버에 로그를 전송할 때는 다음 주소를 사용해야 합니다.
>     - Log & Crash: api-logncrash.gov-nhncloudservice.com
>     - Method of Delivery: POST
>     - URI: /v2/log
>     - Content-Type: "application/json"
> - 로그를 전송하기 전에 Log & Crash에 프로젝트를 등록했는지 확인합니다.  
> - "logTime"은 Log & Crash 시스템에서 사용합니다. 해당 키를 사용하면 Log & Crash에서는 무시합니다.  
> -  키 이름에 공백 문자가 들어가지 않게 주의합니다. 예를 들어 "UserID"와 "UserID "는 서로 다른 키로 인식됩니다. 
> -  HTTP 요청 하나의 최대 크기는 52MB입니다.
> -  로그(JSON) 하나의 최대 크기는 8MB(8388608바이트)입니다.

아래와 같은 JSON 형식을 사용합니다.

```
{
	"projectName": "__앱키__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "This log message come from HTTP client.",
	"logSource": "http",
	"logType": "nelo2-log",
	"host": "localhost"
}
```

[기본 파라미터]

```
Log Search를 위한 파라미터

projectName: string, 필수
	[in] 앱키.

projectVersion: string, 필수
	[in] 버전. 사용자 지정 가능. "A~Z, a~z, 0~9,-._"만 포함.

body: string, 옵션
	[in] 로그 메시지.

logVersion: string, 필수
	[in] 로그 포맷 버전. "v2".

logSource: string, 옵션
	[in] 로그 소스. Log Search에서 필터링을 위해 사용. 정의되지 않으면 "http".

logType: string, 옵션
	[in] 로그 타입. Log Search에서 필터링을 위해 사용. 정의되지 않으면 "log".

host: string, 옵션
	[in] 로그를 보내는 단말의 주소. 정의되지 않으면 수집 서버에서 peer-address를 사용해 자동으로 채움.
```

[기타 파라미터]

```
sendTime; string, 옵션
	[in] 단말이 보낸 시간. 입력 시 Unix timestamp로 입력.

logLevel; string, 옵션
	[in] Syslog 이벤트용.

UserBinaryData; string, 옵션
	[in] 로그 검색 화면에서 [다운로드|보기] 링크 표시, base64 인코딩된 값을 담아 전송.

UserTxtData; string, 옵션
	[in] 로그 검색 화면에서 [다운로드|보기] 링크 표시, base64 인코딩된 값을 담아 전송.

txt*; string, 옵션
	[in] 필드 이름이 txt로 시작하는 필드(txtMessage, txt_description 등)는 text 필드로 저장. 로그 검색 화면에서 필드값의 일부 문자열로 검색(full text search) 가능. 필드의 크기는 1MB로 제한됨.

long*; long, 옵션
    [in] 필드 이름이 long으로 시작하는 필드(longElapsedTime, long_elapsed_time 등)는 long 타입 필드로 저장됨. 로그 검색 화면에서 long 타입 range 검색 가능.

double*; double, 옵션
    [in] 필드 이름이 double로 시작하는 필드(doubleAvgScore, double_avg_score 등)는 double 타입 필드로 저장됨. 로그 검색 화면에서 double 타입 range 검색 가능.
```

[커스텀 필드]

```
커스텀 필드 이름은 "A-Z, a-z"로 시작하고 "A-Z, a-z, 0-9, -, _" 문자를 사용할 수 있습니다.

위의 기본 파라미터, Crash 파라미터와 이름이 중복되면 안 됩니다.

커스텀 필드는 필드 전체 문자열과 일치하는 검색만 가능합니다(exact match).

커스텀 필드의 길이는 1KB로 제한됩니다. 1KB 이상 전송하거나, 필드값의 일부 문자열을 검색해야 할 때는 txt* prefix를 붙여 필드를 생성해야 합니다.
```

[반환값]  
수집 서버에서 다음과 같이 반환합니다.

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
	[out] 성공 시 true, 실패 시 false

resultCode: int
	[out] 성공 시 0, 실패 시 오류 코드

resultMessage: string
	[out] 성공 시 "Success", 실패 시 오류 메시지
```

[Bulk 전송]
Bulk로 전송하려면 JSON array 형태로 전송합니다.

```
[
    {
        "projectName": "__앱키__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client. (1/2)",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost"
    },
    {
        "projectName": "__앱키__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client. (2/2)",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost"
    }
]
```

* 참고
    * 웹에서는 수신 시간 기준으로 로그를 정렬해 표시하는데, Bulk 전송의 경우 동일한 시간에 수신한 것으로 간주되어 사용자가 전송한 순서가 유지되지 않습니다.
        * Bulk로 전송하는 로그들의 순서를 유지하려면 각 로그에 lncBulkIndex 필드를 추가해 Integer값을 지정한 후 전송하면 서버에서는 이 값을 기준으로 내림차순으로 표시합니다.

```
[
    {
        "projectName": "__앱키__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "first message",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost",
        "lncBulkIndex":1
    },
    {
        "projectName": "__앱키__",
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
	* 위 예시와 같이 전송한 경우 서버에서는 second message -> first message 순서로 표시합니다.

수집 서버에서는 전송된 순서에 따라 각각의 결괏값을 JSON array 형태로 다시 반환합니다.

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
    [out] 전송된 전체 로그 수

errors: int
    [out] 전송된 로그 중 오류 수

resultList: array
    [out] 전송된 각 로그들의 결괏값
```

### 샘플

[curl을 사용해 정상적으로 로그를 전송한 경우]

```
//POST 메서드을 사용해 로그 전송
$ curl -H "content-type:application/json" -XPOST 'https://api-logncrash.gov-nhncloudservice.com/v2/log' -d '{
	"projectName": "__앱키__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http"
}'
```

[로그 전송에 실패하는 경우]

```
//URL이 잘못된 경우(log -> loggg)
$ curl -v -H 'content-type:application/json' -XPOST "api-logncrash.gov-nhncloudservice.com/v2/loggg" -d '{
	"projectName": "__앱키__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http"
}'


//잘못된 필드 키를 사용한 경우(_xxx)
$ curl -v -H 'content-type:application/json' -XPOST "api-logncrash.gov-nhncloudservice.com/v2/log" -d '{
	"projectName": "__앱키__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http",
	"_xxx": "this is a invalid key"
	}'
커스텀 키는 "A~Z, a~z, 0~9, -_"를 포함하고 알파벳으로 시작해야 합니다.
커스텀 키는 "A~Z, a~z, 0~9, -_"를 포함하고 알파벳으로 시작해야 합니다.
```

[curl을 사용해  로그를 Bulk 전송한 경우]

```
//POST 메서드을 사용해 로그 전송
$ curl -H "content-type:application/json" -XPOST 'https://api-logncrash.gov-nhncloudservice.com/v2/log' -d '[
    {
        "projectName": "__앱키__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client, and it is a simple bulk sample. (1/2)",
        "logSource": "http",
        "logType": "nelo2-log"
    },
    {
        "projectName": "__앱키__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client, and it is a simple bulk sample. (2/2)",
        "logSource": "http",
        "logType": "nelo2-log"
    }
]'
```

## 로그 검색 API
저장된 로그를 Lucene 쿼리를 사용해 검색할 수 있습니다.</br>
로그 검색 API는 사용 패턴에 따라 시간 당 요청할 수 있는 양을 제한하고 있습니다. 검색에 사용 가능한 리소스는 `토큰`으로 표현하며, 검색 API를 호출할 때마다 내부적인 기준에 따라 일정량을 차감하고 있습니다. 토큰 잔량이 양수일 때 검색 API 사용이 가능합니다.</br>
검색 시 차감되는 토큰 수는 검색 기간 및 용량, 쿼리의 복잡도에 따라 달라지며, 토큰은 시간이 경과함에 따라 자동으로 충전됩니다.

### 기본 정보
```
API Endpoint: https://api-lncs-search.gov-nhncloudservice.com
```
```
검색은 최근 90일 이내의 로그만 가능하며, 시작 시간과 종료 시간의 범위는 31일을 초과할 수 없습니다.
```

### Search API
Lucene 쿼리를 사용하여 지정한 시간 범위의 로그를 조회합니다. 페이징을 적용하여 조회할 수 있고, 최대 100,000건의 로그까지 검색이 가능합니다.
```
POST /api/v1/search/{appkey}

Content-Type: application/json
```

#### 요청 파라미터
| 이름 | 형식 | 설명 | 필수 |
| --- | --- | --- | --- |
| appkey | String | 프로젝트 앱키 | O | 

#### 요청 본문
| 이름 | 형식 | 설명 | 필수 | 비고 |
| --- | --- | --- | --- | --- |
| query | String | Lucene 쿼리 | O |  |
| from | String | 시작 시간 | O | ISO8601 형식 날짜(YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | 종료 시간 | O | ISO8601 형식 날짜(YYYY-MM-DDThh:mm:ss.sTZD) |
| pageNumber | Number | 페이지 번호 |  | 기본값 0 |
| pageSize | Number | 페이지 크기 |  | 기본값 10, 최댓값 100 |
| sort | Object | 정렬 기준 |  | 필드 별 오름차순(ASC) 및 내림차순(DESC) 설정 |

<details>
<summary>예시</summary>

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

#### 응답
| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| totalItems | Body | Number | 로그 개수 |
| pageNumber | Body | Number | 페이지 번호 |
| pageSize | Body | Number | 페이지 크기 |
| data | Body | List | 로그 목록 |

<details>
<summary>예시</summary>

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
                "projectVersion": "1.0.0"
            },
            ...
        ]
    }
}
```
</details>


### Scroll Start API
Lucene 쿼리를 사용하여 지정한 시간 범위의 로그를 페이지를 지정하지 않고 모두 조회합니다. Scroll Continue API와 함께 사용하여 여러 차례에 걸쳐 조회할 수 있습니다.
```
POST /api/v1/search/scroll/{appkey}

Content-Type: application/json
```

#### 요청 파라미터
| 이름 | 형식 | 설명 | 필수 |
| --- | --- | --- | --- |
| appkey | String | 프로젝트 앱키 | O | 

#### 요청 본문
| 이름 | 형식 | 설명 | 필수 | 비고 |
| --- | --- | --- | --- | --- |
| query | String | Lucene 쿼리 | O |  |
| from | String | 시작 시간 | O | ISO8601 형식 날짜(YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | 종료 시간 | O | ISO8601 형식 날짜(YYYY-MM-DDThh:mm:ss.sTZD) |
| pageSize | Number | 페이지 크기 |  | 기본값 10, 최댓값 100 |
| sort | Object | 정렬 기준 |  | 필드 별 오름차순(ASC) 및 내림차순(DESC) 설정 |

<details>
<summary>예시</summary>

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

#### 응답
| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | 로그 개수 |
| data | Body | List | 로그 목록 |

<details>
<summary>예시</summary>

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
                "projectVersion": "1.0.0"
            },
            ...
        ]
    }
}
```
</details>


### Scroll Continue API
Scroll Start API 또는 직전에 호출한 Scroll Continue API 로부터 얻은 Scroll Key를 지정하여 로그 조회를 지속합니다.</br>
Scroll Key는 1분간 유효합니다.
```
POST /api/v1/search/scroll/{appkey}/{scrollKey}

Content-Type: application/json
```

#### 요청 파라미터
| 이름 | 형식 | 설명 | 필수 |
| --- | --- | --- | --- |
| appkey | String | 프로젝트 앱키 | O |
| scrollKey | String | Scroll Key | O |

#### 요청 본문
Scroll Continue API는 요청 본문이 필요하지 않습니다.

#### 응답
| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | 로그 개수 |
| data | Body | List | 로그 목록 |

<details>
<summary>예시</summary>

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
                "projectVersion": "1.0.0"
            },
            ...
        ]
    }
}
```
</details>

### Available Token API
사용 가능한 토큰 수를 조회합니다.
```
GET /api/v1/search/available-tokens/{appkey}
```

#### 요청 파라미터
| 이름 | 형식 | 설명 | 필수 |
| --- | --- | --- | --- |
| appkey | String | 프로젝트 앱키 | O | 

#### 응답
| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| availableToken | Body | Number | 사용 가능한 토큰 |

<details>
<summary>예시</summary>

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
