<!-- pre-align:aligned sig=130414ff6b2d -->

<a id="data-analytics-log-crash-search-user-guide-for-logstash-sdk"></a>
## Data & Analytics > Log & Crash Search > User Guide for Logstash SDK { #data-analytics-log-crash-search-user-guide-for-logstash-sdk }

This document describes how to process different types of inputs and outputs by using Logstash. 

<a id="download"></a>
## Download { #download }

- Download Logstash.
- $ wget      http://download.elastic.co/logstash/logstash/logstash-1.5.6.tar.gz
- Unzip the files.
- $ tar zxvf      logstash-1.5.6.tar.gz

<a id="install-and-execute"></a>
## Install and Execute { #install-and-execute }

Refer to Configuring Logstash. 

- Create configuration files for Logstash. 
- Execute with bin/logstash -f <Configuration Files>

<a id="configure-logstash"></a>
## Configure Logstash { #configure-logstash }

Collecting and delivering logs by using Logstash are described as below: 

<a id="collect-log-crash-collector-logs"></a>
### **Collect Log & Crash Collector Logs** { #collect-log-crash-collector-logs }

Below shows how Log & Crash Collector Logs are collected with Logstash. 

<a id="collect-log-crash-collector-logs-define-path-for-the-input-file-an-absolute-route-is-required-for-the-path"></a>
#### \- Define path for the input, file: an absolute route is required for the path.  

```
input {
...
  file {
	path => [ "/root/apps/nelo2/collector/logs/*" ]
  }
}
```

<a id="collect-log-crash-collector-logs-use-filter-and-multiline-to-combine-logs-in-many-lines"></a>
#### - Use filter and multiline to combine logs in many lines.

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
### **Deliver Logs to Log & Crash Collector** { #deliver-logs-to-log-crash-collector }

Below shows how logs are sent to Log & Crash Collector with Logstash. 

<a id="deliver-logs-to-log-crash-collector-convert-logstash-logs-to-the-log-crash-http-rest-api-format-by-using-filter-and-mutate"></a>
#### \- Convert Logstash logs to the Log & Crash HTTP REST API format, by using filter and mutate. 

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
- Remove unnecessary fields by using remove_field: also available to reduce the size of delivered logs.
- Change the field name to fit for Log & Crash HTTP REST API by using rename.
- Add fields required for Log & Crash HTTP REST API by using add field.
    - "projectName": Required, Project name/Appkey
    - "projectVersion": Required, Project version
    - "logVersion": Required, Log format version
    - "logType": Optional, Log type
    - "logSource": Optional, Log source
    - "logLevel": Optional, Log level
```

<a id="deliver-logs-to-log-crash-collector-send-to-log-crash-collector-by-using-output-and-http"></a>
#### - Send to Log & Crash Collector by using output and http.

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
- Modify URL to the address of Log & Crash Collector to send 
- Address of NHN Cloud Log & Crash Collector: https://api-logncrash.nhncloudservice.com/v2/log
- The URI must be /v2/log.
```

<a id="collect-apache-accesserror-logs"></a>
### **Collect Apache Access/Error Logs** { #collect-apache-accesserror-logs }

Below shows how Apache Access/Error Logs are collected with Logstash. 

<a id="collect-apache-accesserror-logs-define-path-for-input-and-file-define-type-to-tell-the-difference-between-access-and-error"></a>
#### \- Define path for input and file. Define type to tell the difference between access and error.  

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
- The above path is used in the CAB DEV Web server. Correction is required if the log location is not correct.
```

<a id="collect-apache-accesserror-logs-analyze-logs-by-using-filter-grok"></a>
#### - Analyze logs by using filter, grok.

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
- The "apache-access" type adopts "%{COMBINEDAPACHELG}", provided as default by grok.
- The "apache-error" type adopts "%{APACHEERRORLOG}", defined by "./my-pat.grok". 
- my-pat.grok
```

```
 HTTPERRORDATE %{DAY} %{MONTH} %{MONTHDAY} %{TIME} %{YEAR}
#APACHEERRORLOG \[%{HTTPERRORDATE:timestamp}\] \[%{WORD:severity}\] \[client %{IPORHOST:clientip}\] %{GREEDYDATA:message_remainder}
APACHEERRORLOG \[%{HTTPERRORDATE:timestamp}\] \[%{WORD:severity}\] %{GREEDYDATA:message_remainder}

- The grok pattern adopted by a bit of logstash cooking has been modified.
```

<a id="collect-other-logs"></a>
### **Collect Other Logs** { #collect-other-logs }

Collect other logs in reference of the following URL:

- [A bit of logstash cooking](https://home.regit.org/2014/01/a-bit-of-logstash-cooking/)

<a id="environment-variables"></a>
### **Environment Variables** { #environment-variables }

Logstash supports the following environment variables. The memory volume of Logstash can be configured via LS_HEAP_SIZE. 

- LS_HEAP_SIZE="xxx" size for the -Xmx${LS_HEAP_SIZE} maximum Java heap size option, default is      "500m"
- LS_JAVA_OPTS="xxx" to append extra options to the defaults JAVA_OPTS provided by logstash
- JAVA_OPTS="xxx" to completely override the defauls set of JAVA_OPTS provided by logstash    

> Note  
> Logstash Website 
> Logstash Reference
> A bit of logstash cooking
