## Analytics > Log & Crash Search > APIガイド

HTTPプロトコルを使用して、Log & Crash収集サーバーにログを転送でき、下記のようなJSON形式で使用します。

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
Log Searchのためのパラメータ。

projectName: string、必須
	[in]アプリケーションキー。

projectVersion：string、必須
	[in]バージョン。ユーザーが指定できる。"A～Z、a～z、0～9、-、_"のみ使用できる。

body：string、オプション
	[in]ログメッセージ。

logVersion：string、必須
	[in]ログフォーマットバージョン。 "v2"。

logSource：string、オプション
	[in]ログソース。Log Searchでフィルタリングのために使用される。定義されていない場合は"http"。

logType：string、オプション
	[in]ログタイプ。 Log Searchでフィルタリングのために使用される。定義されていない場合は"log"

host：string、オプション
	[in]ログを送る端末のアドレス。定義されていない場合は、収集サーバーでpeer-addressを使用して自動的に埋める。
```

[その他のパラメータ]

```
sendTime;string、オプション
	[in]端末が転送した時間。入力時、Unix Timestampで入力

logLevel; string、オプション
	[in] Syslog event用。

UserBinaryData; string、オプション
	[in]ログ検索画面で[ダウンロード|参照]リンク表示、base64エンコードされた値を入れて転送。

UserTxtData; string、オプション
  [in]ログ検索画面で[ダウンロード|参照]リンク表示、base64エンコードされた値を入れて転送。

txt*; string、オプション
	[in]フィールド名がtxtで始まるフィールド(txtMessage、txt_descriptionなど)は、分析(analyzed)フィールドに保存されます。ログ検索画面でフィールド値の一部の文字列で検索ができます。

long*; long、オプション
    [in]フィールド名がlongで始まるフィールド(longElapsedTime、long_elapsed_timeなど)は、longタイプフィールドに保存されます。ログ検索画面でlongタイプのRange検索ができます。

double*; double、オプション
    [in]フィールド名がdoubleで始まるフィールド(doubleAvgScore、double_avg_scoreなど)は、doubleタイプのフィールドに保存されます。ログ検索画面でdoubleタイプのRange検索ができます。
```

[カスタムフィールド]

```
カスタムフィールド名は、"A-Z、a-z"で始まり、"A-Z、a-z、0-9、-、_"を使用できます。

上の基本パラメータ、 Crashパラメータと名前が重複してはいけません。

カスタムフィールドの長さは2kbyteに制限され、2kbyte以上を転送すると、txt* prefixを付けてフィールドを作成する必要があります。
```

[戻り値]  
収集サーバーから次のように返されます。

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
	[out]成功すると"Success"、失敗するとエラーメッセージ
```

[Bulk転送]
Bulk転送のためには、JSON array形式で収集サーバーに転送します。

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

* Note
		* webでは受信時間基準でログをソートして表示しますが、bulk転送の場合、同じ時間に受信したとみなされてユーザーが
		  転送した順序が維持されません。
		* Bulkで転送するログの順序関係を維持するためには、ログにlncBulkIndexフィールドを追加して、integer値を指定して転送すると
		サーバーではこの値を基準に、降順で表示します。

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

> 注意 
> 1. JSON/HTTPでLog & Crash収集サーバーにログを転送する時、次のアドレスを使用する必要があります。
> Log & Crash: api-logncrash.cloud.toast.com  
>
> 転送方式：POST  
>
> URI: /v2/log  
>
> Content-Type: "application/json"  
> 2. ログを転送する前に、Log & Crashにプロジェクトを登録したか確認する。
> 3. "logTime"は、Log & Crashシステムで使用する。該当キーを使用した時、Log & Crashでは無視する。
> 4. キー名にスペースが入らないように注意する必要がある。例えば"UserID"と"UserID "は別々のキーとして認識される。

## サンプル

[curlを使用して正常にログを転送した場合]

```
//POSTメソッドを使用してログを転送
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

[curlを使用してbulkログを転送した場合]

```
//POSTメソッドを使用してログを転送
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
