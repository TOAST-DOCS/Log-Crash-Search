## Analytics > Log & Crash Search > Log4J SDK Guide

Log & Crash Log4J SDK sends logs to a Log & Crash Search collector server.
Below describe benefits and features of Log & Crash Log4J SDK.

- Send logs to a collector server.
- Retrieve and search logs sent from Log & Crash Search.
- Operate under a multi-threading environment.

## Supporting Environment

- Log4J 1.2.x (1.2.14, 1.2.16, 1.2.17)

## Download

Go to [TOAST Document](http://docs.toast.com/en/Download/) and download **Log4J SDK**.

```
Click [DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Log4J SDK] 
```

## Install

### Configuration

SDK is configured as below.

```
docs/       ; Log4J SDK Document
lib/        ; Log4J Library
sample/     ; Log4J Sample
```

### SDK Sample

Below describe sample/log4j/ that is provided.

1. Run Eclipse, execute **File > Import > Maven > Existing Maven Projects** in the menu and import sample/log4j/.
2. Open the src/test/resources/log4j.xml file and update with issued Appkey and version, and collector server address, if necessary.

```
<param name="collectorUrl" value="https://api-logncrash.nhncloudservice.com" />
<param name="appKey" value="__app_key__" />
<param name="version" value="1.0.0" />
```

3. Go to **Project > Properties > Java Build Path > Libraries** in Eclipse and add toast-logncrash-log4j-sdk-jar.
4. In Eclipse, select **Run > Run As > JUnit Test** and execute.

## Example

1. Add Log4J SDK library to your project.
    For instance, select **Project > Properties > Java Build Path > Libraries** in the Eclipse menu and add toast-logncrash-log4j-sdk-.jar
2. In the case of Maven, add dependency to pom.xml.

```
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>1.2.17</version>
</dependency>
<dependency>
  <groupId>commons-lang</groupId>
  <artifactId>commons-lang</artifactId>
  <version>2.5</version>
</dependency>
<dependency>
  <groupId>org.apache.httpcomponents</groupId>
  <artifactId>httpclient</artifactId>
  <version>4.2.6</version>
</dependency>
<dependency>
  <groupId>org.apache.httpcomponents</groupId>
  <artifactId>httpcore</artifactId>
  <version>4.2.4</version>
</dependency>
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.4</version>
</dependency>
<dependency>
  <groupId>org.json</groupId>
  <artifactId>json</artifactId>
  <version>20090211</version>
</dependency>
```

- For SLF4J, add the following dependency.

```
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.2</version>
</dependency>
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.2</version>
</dependency>
```

3. For non-Maven users, download libraries as below and add to the class path.

```
log4j, 1.2.17
commons-lang, 2.5
httpclient, 4.2.6
httpcore, 4.2.4
servlet-api, 2.4
json, 20090211
```

4. For the setting and configuration of appender, write log4j.xml.

 - Refer to sample/log4j/src/test/resources/log4j.xml for the entire configuration.
 - Make sure to use issued Appkey and collector server address, to appKey and collectorUrl, respectively.

```
<appender name="logncrash-http" class="com.toast.java.logncrash.log4j.LogNCrashHttpAppender">
	 <param name="collectorUrl" value="https://api-logncrash.nhncloudservice.com" />
	 <!-- v2 -->
	 <!--	  -->
	 <param name="appKey" value="__app_key__" />
	 <param name="version" value="1.0.0" />
	 <param name="logSource" value="http-log4j" />
	 <param name="logType" value="log" />
	 <param name="Threshold" value="ALL" />
	 <param name="errorCodeType" value="default" />
	 <param name="enable" value="true" />
	 <param name="debug" value="false" />
</appender>
...
<root>
 <appender-ref ref="logncrash-http" />
</root>
```

5. To set properties, write log4j.properties.

```
log4j.rootLogger=ALL, STDOUT, logncrash-http
log4j.appender.STDOUT=org.apache.log4j.ConsoleAppender
log4j.appender.STDOUT.Threshold=DEBUG
log4j.appender.STDOUT.layout=org.apache.log4j.PatternLayout
log4j.appender.STDOUT.layout.ConversionPattern=%m%n
log4j.appender.logncrash-http=com.toast.java.logncrash.log4j.LogNCrashHttpAppender
log4j.appender.logncrash-http.collectorUrl=https://api-logncrash.nhncloudservice.com
log4j.appender.logncrash-http.appKey=__appkey__
log4j.appender.logncrash-http.version=1.0.0
log4j.appender.logncrash-http.logSource=http-log4j
log4j.appender.logncrash-http.logType=log
log4j.appender.logncrash-http.Threshold=ALL
log4j.appender.logncrash-http.errorCodeType=default
log4j.appender.logncrash-http.enable=true
log4j.appender.logncrash-http.debug=false
```

6. For Java, use as follows.

```
	...
private static Logger logger = Logger.getLogger(LogNCrashLog4jSample.class);
...
// Custom Message
MDC.put("custommessage", "custom message");
logger.debug("Log4j SDK Debug Message");
try {
	String npe = null;
	npe.toString();
} catch(Exception e) {
	logger.error("Log4J SDK Exception", e);
}
```

## API List

### Setting Items for log4j.xml

- collectorUrl: Collector server address
  HTTP : https://api-logncrash.nhncloudservice.com

- appKey: Project Appkey: required
- version: Project version. Default is "1.0.0". 
- logSource: Log source. Default is "http-log4j".
- logType: Log type. Default is "log". 
- Threshold: Specify a log level to send. Default is "ALL". 
- enable: Whether to use Appender or not. Default is "true". 
- debug: Whether to use Debug or not. Default is "false". 
- errorCodeType: Set type of error code. Default is "default". 
  default: Use exception data. 
  mdc: Set and use errorCode of Log4j MDC.

## Constraints

- The current **log4j 2.0** version is not supported. log4j 1.3 supports alpha8 only, but it is recommended to migrate to log4j 1.2. Recommended versions are: log4j 1.2.14, 1.2.16, and 1.2.17. 
- In case too much error data occur all at once, handling of the log4j may be delayed if the bufferSize of logncrash-async appender is small; hence, the bufferSize needs to be adjusted.

## FAQs

### How can I apply false for blocking?

Modify the class name of logncrash-async in log4j.xml, as below. 

```
<!-- define logncrash-async appender -->
<appender name="logncrash-async" class="com.toast.java.logncrash.log4j.LogNCrashAsyncAppender">
    <param name="Threshold" value="ALL" />
    <param name="blocking" value="false" />
    <param name="locationInfo" value="false" />
    <param name="bufferSize" value="2048" />
    <appender-ref ref="logncrash-http" />
</appender>
```

### How can I use logncrash client in a batch program (project)?

It is not applied to a batch project that runs in demonstration-type using quartz. 
 Add a code that allows you to wait for seconds at the end of a batch program.

```
try {
    Thread.sleep(3000L);
} catch (InterruptedException ignore){}
```

For logncrash-async appender, org.apache.log4j.AsyncAppender is used. 

AsyncAppender has an additional demonstration thread that records logs internally, and logs can be asynchronously sent. In a Java batch program, the main thread is immediately closed, closing batch application before a demonstration thread of AsyncAppender is created to send logs. When there is no remaining general thread, regardless of demonstration threads, JVM is immediately closed. 

Another method to apply is to separately use log4j.xml for batch-purposes: modify log4j.xml in logger to make appender logncrash readily available. This may cause a delay when an error occurs, for the purpose of calling an error collector server, as logging is synchronously operated. Therefore, this method is not recommended for a web project.

```
<!-- // define loggers // -->
<logger name="com" additivity="false">
    <level value="INFO"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash"/>
</logger>
<!-- // define root // -->
<root>
    <level value="WARN"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash"/>
</root>
```

### How can a Java stack trace be logged to a log4j (including Log & Crash Search)?

To get an output of stack trace with log4j, use the log.error (e.getMessage()) type: cannot get an output of stack trace for log.error(e);.  

```
String[] aa = null;
try {
    aa[0] = "111";
} catch (NullPointerException e) {
    log.error(e); //stacktrace 출력 안 됨.
    log.error(e.getMessage(), e); ///stacktrace 출력
}
```

### How can I minimize performance degradation due to log4j (including Log & Crash Search) logging?

Maximize filtering by using name and level in the logger setting of logback.xml.
Like below, if com or org is set for DEBUG level for logger configuration, many LoggingEvents(log4js) are unnecessarily created in the logger. As threshold is set with ERROR in appender, logs are not actually sent but LoggingEvent is created in logger and sent to appender. 

[Setting that allows performance degradation (for development use only)]

```
<!-- // define loggers // -->
<logger name="com" additivity="false">
    <level value="DEBUG"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash-async"/>
</logger>

<!-- // define loggers // -->
<logger name="org" additivity="false">
    <level value="DEBUG"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash-async"/>
</logger>

<!-- // define root // -->
<root>
    <level value="WARN"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash-async"/>
</root>
```

[Setting that considers performance (for operational use)]

```
<!-- // define loggers // -->
<logger name="com" additivity="false">
    <level value="ERROR"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash-async"/>
</logger>

<!-- // define root // -->
<root>
    <level value="WARN"/>
    <appender-ref ref="STDOUT"/>
    <appender-ref ref="logncrash-async"/>
</root>
```

### How can I safely close WAS?

When closing WAS (such as Tomcat) while error logs are sent, following exception may occur and WAS may not be closed properly.

```
Exception in thread "pool-12-thread-1" java.lang.NullPointerException
at_external.org.apache.mina.common.AbstractPollingIoProcessor$Worker.run(AbstractPollingIoProcessor.java:740)
at_external.org.apache.mina.util.NamePreservingRunnable.run(NamePreservingRunnable.java:51)
at_java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:886)
at_java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:908)
at_java.lang.Thread.run(Thread.java:619)
```
The exception may occur when WAS is closed while a log delivery is under way. To prevent it and safely close WAS, call LogManager.shutdown() method by the time WAS is closed, and close logncrash appender. 

If you use org.springframework.web.util.Log4jConfigListener, no additional setting is required, as Log4jConfigListener makes a call of the LogManager.shutdown() method, to safely close WAS. 

For non-users of Log4jConfigListener, logncrash-appender provides com.toast.java.logncrash.log4j.Log4jShutdownListener. By adding the following setting to web.xml, WAS can be safely closed even while error logs are delivered.

```
<listener>
    <listener-class>
        com.toast.java.logncrash.log4j.Log4jShutdownListener
    </listener-class>
</listener>
```
