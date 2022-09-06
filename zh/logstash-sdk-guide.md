## Data & Analytics > Log & Crash Search > User Guide for Logstash SDK

This document describes how to process different types of inputs and outputs by using Logstash. 

## Download

- Download Logstash.
- $ wget      http://download.elastic.co/logstash/logstash/logstash-1.5.6.tar.gz
- Unzip the files.
- $ tar zxvf      logstash-1.5.6.tar.gz

## Install and Execute 

Refer to Configuring Logstash. 

- Create configuration files for Logstash. 
- Execute with bin/logstash -f <Configuration Files>

## Configure Logstash 

Collecting and delivering logs by using Logstash are described as below: 

### **Collect Log & Crash Collector Logs** 

Below shows how Log & Crash Collector Logs are collected with Logstash. 

#### \- Define path for the input, file: an absolute route is required for the path.  

```
input {
...
  file {
	path => [ "/root/apps/nelo2/collector/logs/*" ]
  }
}
```

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

### **Deliver Logs to Log & Crash Collector**

Below shows how logs are sent to Log & Crash Collector with Logstash. 

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

#### - Send to Log & Crash Collector by using output and http.

```
output {
...
  http {
	url => "https://api-logncrash.cloud.toast.com/v2/log"
	http_method => "post"
	format => "json"
	verify_ssl => false
  }
}
- Modify URL to the address of Log & Crash Collector to send 
- Address of NHN Cloud Log & Crash Collector: https://api-logncrash.cloud.toast.com/v2/log
- The URI must be /v2/log.
```

### **Collect Apache Access/Error Logs** 

Below shows how Apache Access/Error Logs are collected with Logstash. 

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

### **Collect Other Logs** 

Collect other logs in reference of the following URL:

- [A bit of logstash cooking](https://home.regit.org/2014/01/a-bit-of-logstash-cooking/)

### **Environment Variables**

Logstash supports the following environment variables. The memory volume of Logstash can be configured via LS_HEAP_SIZE. 

- LS_HEAP_SIZE="xxx" size for the -Xmx${LS_HEAP_SIZE} maximum Java heap size option, default is      "500m"
- LS_JAVA_OPTS="xxx" to append extra options to the defaults JAVA_OPTS provided by logstash
- JAVA_OPTS="xxx" to completely override the defauls set of JAVA_OPTS provided by logstash    

> Note  
> Logstash Website 
> Logstash Reference
> A bit of logstash cooking
