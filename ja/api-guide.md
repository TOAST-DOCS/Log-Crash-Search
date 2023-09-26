## Data & Analytics > Log & Crash Search > APIガイド

## ログ収集API
HTTPプロトコルを使用してLog & Crash収集サーバーにログを転送できます。

> - JSON/HTTPでLog & Crash収集サーバーにログを転送する時は、次のアドレスを使用する必要があります。
>     - Log & Crash: api-logncrash.cloud.toast.com
>     - 転送方式：POST
>     - URI: /v2/log
>     - Content-Type: "application/json"
> - ログを転送する前に、Log & Crashにプロジェクトを登録したか確認します。
> - "logTime"は、Log & Crashシステムで使用します。該当キーを使用した時、Log & Crashでは無視します。
> - キー名にスペースが入らないように注意します。例えば"UserID"と"UserID "は別々のキーとして認識されます。
> - HTTPリクエスト1つの最大サイズは52MBです。
> - ログ(JSON)1つの最大サイズは8MB(8388608バイト)です。

下記のようなJSON形式を使用します。

```
{
	"projectName": "__アプリケーションキー__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "This log message come from HTTP client.",
	"logSource": "http",
	"logType": "nelo2-log",
	"host": "localhost"
}
```

[基本パラメータ]

```
Log Searchのためのパラメータ

projectName: string、必須
	[in]アプリケーションキー。

projectVersion：string、必須
	[in]バージョン。ユーザー指定可能。"A~Z, a~z, 0~9,-._"のみ使用できる。

body：string、オプション
	[in]ログメッセージ。

logVersion：string、必須
	[in]ログフォーマットバージョン。 "v2"。

logSource：string、オプション
	[in]ログソース。Log Searchでフィルタリングのために使用。定義されていなければ"http"。

logType：string、オプション
	[in]ログタイプ。Log Searchでフィルタリングのために使用。定義されていなければ"log"。

host：string、オプション
	[in]ログを送る端末のアドレス。定義されていなければ収集サーバーでpeer-addressを使用して自動的に埋める。
```

[その他のパラメータ]

```
sendTime;string、オプション
	[in]端末が送った時間。Unix timestampで入力。

logLevel; string、オプション
	[in] Syslogイベント用。

UserBinaryData; string、オプション
	[in]ログ検索画面で[ダウンロード|参照]リンク表示、base64エンコードされた値を入れて転送。

UserTxtData; string、オプション
    [in]ログ検索画面で[ダウンロード|表示]リンク表示、 base64エンコードされた値を入れて転送。

txt*; string、オプション
	[in]フィールド名がtxtで始まるフィールド(txtMessage, txt_descriptionなど)はtextフィールドに保存。ログ検索画面でフィールド値の一部の文字列で検索(full text search)可能。フィールドのサイズは1MBに制限される。

long*; long、オプション
    [in]フィールド名がlongで始まるフィールド(longElapsedTime、long_elapsed_timeなど)はlongタイプフィールドに保存される。ログ検索画面でlongタイプrange検索可能。

double*; double、オプション
    [in]フィールド名がdoubleで始まるフィールド(doubleAvgScore, double_avg_scoreなど)はdoubleタイプフィールドに保存される。ログ検索画面でdoubleタイプrange検索可能。
```

[カスタムフィールド]

```
カスタムフィールド名は"A-Z, a-z"で始まり、"A-Z, a-z, 0-9, -, _"を使用できます。

上の基本パラメータ、 Crashパラメータと名前が重複してはいけません。

カスタムフィールドはフィールド文字列と完全に一致する検索のみ可能です(exact match)。

カスタムフィールドの長さは1KBに制限されます。1KB以上転送したり、フィールド値の一部の文字列を検索する必要がある時はtxt* prefixをつけてフィールドを作成する必要があります。
```

[戻り値]  
収集サーバーから次のように返します。

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
	[out]成功時はtrue、失敗時はfalse

resultCode: int
	[out]成功時は0、失敗時はエラーコード

resultMessage: string
	[out]成功時は"Success"、失敗時はエラーメッセージ
```

[Bulk転送]
Bulkで転送するにはJSON array形式で転送します。

```
[
    {
        "projectName": "__アプリケーションキー__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client. (1/2)",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost"
    },
    {
        "projectName": "__アプリケーションキー__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client. (2/2)",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost"
    }
]
```

* 参考
    * webでは受信時間基準でログをソートして表示しますが、bulk転送の場合、同じ時間に受信したとみなされてユーザーが転送した順序が維持されません。
    * Bulkで転送するログの順序を維持するには、各ログにlncBulkIndexフィールドを追加してInteger値を指定した後に転送します。サーバーではこの値を基準に降順で表示します。

```
[
    {
        "projectName": "__アプリケーションキー__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "first message",
        "logSource": "http",
        "logType": "nelo2-log",
        "host": "localhost",
        "lncBulkIndex":1
    },
    {
        "projectName": "__アプリケーションキー__",
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
	* 上の例のように転送した場合、サーバーではsecond message -> first messageの順序で表示します。

収集サーバーでは転送された順序にしたがって、それぞれの結果値をJSON array形式で再び返します。

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
    [out]転送された全体のログ数

errors: int
    [out]転送されたログ中のエラー数

resultList: array
    [out]転送された各ログの結果値
```

### サンプル

[curlを使用して正常にログを転送した場合]

```
//POSTメソッドを使用してログ転送
$ curl -H "content-type:application/json" -XPOST 'https://api-logncrash.cloud.toast.com/v2/log' -d '{
	"projectName": "__アプリケーションキー__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http"
}'
```

[ログの転送に失敗する場合]

```
//URLが無効な場合(log -> loggg)
$ curl -v -H 'content-type:application/json' -XPOST "api-logncrash.cloud.toast.com/v2/loggg" -d '{
	"projectName": "__アプリケーションキー__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http"
}'


//無効なフィールドキーを使用した場合(_xxx)
$ curl -v -H 'content-type:application/json' -XPOST "api-logncrash.cloud.toast.com/v2/log" -d '{
	"projectName": "__アプリケーションキー__",
	"projectVersion": "1.0.0",
	"logVersion": "v2",
	"body": "this log message come from http client, and it is a simple sample.",
	"logSource": "http",
	"logType": "nelo2-http",
	"_xxx": "this is a invalid key"
	}'
カスタムキーは"A～Z、a～z、0～9、-_"を含め、アルファベットで始まる必要があります。
カスタムキーは"A～Z、a～z、0～9、-_"を含め、アルファベットで始まる必要があります。
```

[curlを使用してログをBulk転送した場合]

```
//POSTメソッドを使用してログ転送
$ curl -H "content-type:application/json" -XPOST 'https://api-logncrash.cloud.toast.com/v2/log' -d '[
    {
        "projectName": "__アプリケーションキー__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client, and it is a simple bulk sample. (1/2)",
        "logSource": "http",
        "logType": "nelo2-log"
    },
    {
        "projectName": "__アプリケーションキー__",
        "projectVersion": "1.0.0",
        "logVersion": "v2",
        "body": "This log message come from HTTP client, and it is a simple bulk sample. (2/2)",
        "logSource": "http",
        "logType": "nelo2-log"
    }
]'
```

## ログ検索API
保存されたログをLuceneクエリを使用して検索できます。</br>
ログ検索APIは、使用パターンによって1時間あたりにリクエストできる量を制限します。検索に使用可能なリソースはトークンで表現し、検索APIを呼び出すたびに内部基準に基づいて一定量が差し引かれます。トークンの残量が正の場合、検索APIを使用できます。</br>
検索時に差し引かれるトークン数は検索期間や容量、クエリの複雑さによって異なり、トークンは時間が経過するにつれて自動的にチャージされます。</br>
APIリクエストの際、プロジェクトで有効化されたsecretkeyをヘッダーに含める必要があります。

![lncs-api-01-20230925](https://static.toastoven.net/prod_logncrash/lncs-api-01-20230925.png)

### 基本情報
```
API Endpoint: https://api-lncs-search.nhncloudservice.com
```
```
検索は最近90日以内のログのみ可能で、開始時間と終了時間の範囲は31日を超えることはできません。
```

### Search API
Luceneクエリを使用して指定した時間範囲のログを照会します。ページングを適用して照会することができ、最大100,000件のログまで検索が可能です。
```
POST /api/v2/search/{appkey}
Content-Type: application/json
```

#### リクエストパラメータ
| 名前 | 形式 | 説明 | 必須 |
| --- | --- | --- | --- |
| appkey | String | プロジェクトアプリケーションキー | O | 

#### リクエストヘッダ
| 名前 | 形式 | 説明            | 必須 |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | プロジェクトsecretkey | O |

#### リクエスト本文
| 名前 | 形式 | 説明 | 必須 | 備考 |
| --- | --- | --- | --- | --- |
| query | String | Luceneクエリ | O |  |
| from | String | 開始時間 | O | ISO8601形式日付(YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | 終了時間 | O | ISO8601形式日付(YYYY-MM-DDThh:mm:ss.sTZD) |
| pageNumber | Number | ページ番号 |  | デフォルト値0 |
| pageSize | Number | ページサイズ |  | デフォルト値10、最大値100 |
| sort | Object | ソート基準 |  | フィールドごとの昇順(ASC)および降順(DESC)設定 |

<details>
<summary>例</summary>

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

#### レスポンス
| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| totalItems | Body | Number | ログ数 |
| pageNumber | Body | Number | ページ番号 |
| pageSize | Body | Number | ページサイズ |
| data | Body | List | ログリスト |

<details>
<summary>例</summary>

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
Luceneクエリを使って指定した時間範囲のログをページ指定なしで全て照会します。Scroll Continue APIと一緒に使って複数回に渡って照会できます。
```
POST /api/v2/search/scroll/{appkey}
Content-Type: application/json
```

#### リクエストパラメータ
| 名前 | 形式 | 説明 | 必須 |
| --- | --- | --- | --- |
| appkey | String | プロジェクトアプリケーションキー | O | 

#### リクエストヘッダ
| 名前 | 形式 | 説明            | 必須 |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | プロジェクトsecretkey | O |

#### リクエスト本文
| 名前 | 形式 | 説明 | 必須 | 備考 |
| --- | --- | --- | --- | --- |
| query | String | Luceneクエリ | O |  |
| from | String | 開始時間 | O | ISO8601形式日付(YYYY-MM-DDThh:mm:ss.sTZD) |
| to | String | 終了時間 | O | ISO8601形式日付(YYYY-MM-DDThh:mm:ss.sTZD) |
| pageSize | Number | ページサイズ |  | デフォルト値10、最大値100 |
| sort | Object | ソート基準 |  | フィールドごとの昇順(ASC)および降順(DESC)設定 |

<details>
<summary>例</summary>

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

#### レスポンス
| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | ログ数 |
| pageSize | Body | Number | ページサイズ |
| data | Body | List | ログリスト |

<details>
<summary>例</summary>

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
Scroll Start API または直前に呼び出した Scroll Continue API から取得した Scroll Key を指定してログ照会を継続します。</br>
Scroll Keyは1分間有効です。
```
POST /api/v2/search/scroll/{appkey}/{scrollKey}
Content-Type: application/json
```

#### リクエストパラメータ
| 名前 | 形式 | 説明 | 必須 |
| --- | --- | --- | --- |
| appkey | String | プロジェクトアプリケーションキー | O |
| scrollKey | String | Scroll Key | O |

#### リクエストヘッダ
| 名前 | 形式 | 説明            | 必須 |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | プロジェクトsecretkey | O |

#### リクエスト本文
Scroll Continue APIはリクエスト本文が必要しません。

#### レスポンス
| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| scrollKey | Body | String | Scroll Key |
| totalItems | Body | Number | ログ数 |
| data | Body | List | ログリスト |

<details>
<summary>例</summary>

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
使用可能なトークン数を照会します。
```
GET /api/v2/search/available-tokens/{appkey}
```

#### リクエストパラメータ
| 名前 | 形式 | 説明 | 必須 |
| --- | --- | --- | --- |
| appkey | String | プロジェクトアプリケーションキー | O |

#### リクエストヘッダ
| 名前 | 形式 | 説明            | 必須 |
| --- | --- |----------------| --- |
| X-LNCS-SECRET | String | プロジェクトsecretkey | O |

#### レスポンス
| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| availableToken | Body | Number | 使用可能なトークン |

<details>
<summary>例</summary>

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
