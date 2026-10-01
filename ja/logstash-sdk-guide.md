<!-- pre-align:aligned sig=130414ff6b2d -->

<a id="data-analytics-log-crash-search-user-guide-for-logstash-sdk"></a>
## Data & Analytics > Log & Crash Search > Logstash SDK使用ガイド { #data-analytics-log-crash-search-user-guide-for-logstash-sdk }

Logstashを利用して多様なInputとOutputを処理する方法を説明します。

<a id="download"></a>
## ダウンロード { #download }

- Logstashをダウンロードします。
- $ wget http://download.elastic.co/logstash/logstash/logstash-1.5.6.tar.gz
- 圧縮を解凍します。
- $ tar zxvf logstash-1.5.6.tar.gz

<a id="install-and-execute"></a>
## インストールおよび実行 { #install-and-execute }

Configuring Logstashを参照してください。

- Logstash設定ファイルを作成します。
- bin/logstash -f <設定ファイル>で実行します。

<a id="configure-logstash"></a>
## Logstash設定 { #configure-logstash }

Logstashを使用してLogを収集、転送する方法を説明します。

<a id="collect-log-crash-collector-logs"></a>
### Log & Crash Collector Logの収集 { #collect-log-crash-collector-logs }

Logstashを利用して、Log & Crash Collector Logを収集する方法を説明します。

<a id="collect-log-crash-collector-logs-define-path-for-the-input-file-an-absolute-route-is-required-for-the-path"></a>
#### - input, fileもpathを定義します。 Pathには絶対パスを使用する必要があります。

```
input {
...
  file {
	path => [ "/root/apps/nelo2/collector/logs/*" ]
  }
}
```

<a id="collect-log-crash-collector-logs-use-filter-and-multiline-to-combine-logs-in-many-lines"></a>
#### - filter, multilineを使用して、複数行に出力されるLogをマージする処理をします。

```
filter {
...
  ## Collector's Multi-line Log
  multiline {
	pattern => "=+Show Running Statistic=+"
	what => "previous"
  }
  multiline {
	pattern => "OwfsSink:.*\(total=.*increase=.*speed=.*\)"
	what => "previous"
  }
  multiline {
	pattern => "KafkaSink:.*\(total=.*increase=.*speed=.*\)"
	what => "previous"
  }
...
```

<a id="deliver-logs-to-log-crash-collector"></a>
### Log & Crash Collectorに転送 { #deliver-logs-to-log-crash-collector }

Logstashを使用して、Log & Crash CollectorにLogを転送する方法を説明します。

<a id="deliver-logs-to-log-crash-collector-convert-logstash-logs-to-the-log-crash-http-rest-api-format-by-using-filter-and-mutate"></a>
#### - filter、mutateを使用してLogstash LogをLog & Crash HTTP REST API形式に変換します。

```
filter {
...
  ## Convert Logstash event to Log & Crash HTTP REST event
  mutate {
	remove_field => [ "@version", "@timestamp", "path", "tags" ]
	rename => {
	  "message" => "body"
	  "host" => "host"
	}
	add_field => {
	  ## TODO:: modify below fields. see> nelo2-http-rest-api-manual-kr.md
	  "projectName" => "nelo2-webapp"
	  "projectVersion" => "0.0.1"
	  "logVersion" => "v2"
	  "logType" => "logstash"
	  "logSource" => "collector"
	  "logLevel" => "INFO"
	}
  }
}
- remove_fieldを使用して、必要ないfieldを除去します。転送されるログサイズを減らすために使用できます。
- renameを使用して、Log & Crash HTTP REST APIに合わせてfield名を変更します。
- add_fieldを使用して、Log & Crash HTTP REST APIに必要なfieldを追加します。
    - "projectName"：必須。プロジェクト名/アプリケーションキー
    - "projectVersion"：必須。プロジェクトバージョン
    - "logVersion"：必須。ログフォーマットバージョン
    - "logType"：オプション。ログタイプ
    - "logSource"：オプション。ログソース
    - "logLevel"：オプション。ログレベル
```

<a id="deliver-logs-to-log-crash-collector-send-to-log-crash-collector-by-using-output-and-http"></a>
#### - output、httpを使用してLog & Crash Collectorに転送します。

```
output {
...
  http {
	url => "https://api-logncrash.nhncloudservice.com/v2/log"
	http_method => "post"
	format => "json"
	verify_ssl => false
  }
}
- urlに転送するLog & Crash Collectorアドレスに修正する必要があります。
- NHN Cloud Log & Crash Collectorアドレス：https://api-logncrash.nhncloudservice.com/v2/log
- URIは/v2/logである必要があります。
```

<a id="collect-apache-accesserror-logs"></a>
### Apache Access/Error Log収集 { #collect-apache-accesserror-logs }

Logstashを利用して、Apache Access/Error Logを収集する方法を説明します。

<a id="collect-apache-accesserror-logs-define-path-for-input-and-file-define-type-to-tell-the-difference-between-access-and-error"></a>
#### - input、fileにpathを定義します。 typeを定義してaccess/errorを区分します。

```
input {
...
  file {
	path => [ "/root/logs/apache/access.log.*" ]
	type => "apache-access"
  }
  file {
	path => [ "/root/logs/apache/error.log.*" ]
	type => "apache-error"
  }
 ...
}
-上のpathは、CAB DEV Webサーバーで使用される値です。Logの位置が合っていなければ修正する必要があります。
```

<a id="collect-apache-accesserror-logs-analyze-logs-by-using-filter-grok"></a>
#### - filter, grokを使用してlogを分析します。

```
filter {
	  if [type] == "apache-access" {
	grok {
	  match => { "message" => "%{COMBINEDAPACHELOG}" }
	}
  }
  if [type] == "apache-error" {
	grok {
	  match => { "message" => "%{APACHEERRORLOG}" }
	  #patterns_dir => [ "/root/kwonshin/logstash-1.5.6/my-pat.grok" ]
	  patterns_dir => [ "./my-pat.grok" ]
	}
  }
}
- typeが"apache-access"の場合、grokでデフォルトで提供される"%{COMBINEDAPACHELG}"を使用します。
- typeが"apache-error"の場合、"./my-pat.grok"に定義されている"%{APACHEERRORLOG}"を使用します。
- my-pat.grok
```

```
 HTTPERRORDATE %{DAY} %{MONTH} %{MONTHDAY} %{TIME} %{YEAR}
#APACHEERRORLOG \[%{HTTPERRORDATE:timestamp}\] \[%{WORD:severity}\] \[client %{IPORHOST:clientip}\] %{GREEDYDATA:message_remainder}
APACHEERRORLOG \[%{HTTPERRORDATE:timestamp}\] \[%{WORD:severity}\] %{GREEDYDATA:message_remainder}

- A bit of logstash cookingで使用されたgrok patternを修正しました。
```

<a id="collect-other-logs"></a>
### 他のLogを収集 { #collect-other-logs }

他のログを収集するには、次のURLを参照してください。

- [A bit of logstash cooking](https://home.regit.org/2014/01/a-bit-of-logstash-cooking/)

<a id="environment-variables"></a>
### 環境変数 { #environment-variables }
logstashは、次の環境変数をサポートします。logstashが使用するメモリ量は、LS_HEAP_SIZE環境変数を通して設定できます。

 - LS_HEAP_SIZE="xxx" size for the -Xmx${LS_HEAP_SIZE} maximum Java heap size option, default is "500m"
 - LS_JAVA_OPTS="xxx" to append extra options to the defaults JAVA_OPTS provided by logstash
 - JAVA_OPTS="xxx" to completely override the defauls set of JAVA_OPTS provided by logstash

> 参考
> Logstash Webサイト
> Logstash Reference
> A bit of logstash cooking
