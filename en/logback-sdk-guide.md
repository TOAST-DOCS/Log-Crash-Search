## Data & Analytics > Log & Crash Search > Logback SDK Guide

Log & Crash Logback SDK sends logs to a Log & Crash Search collector server. It allows to retrieve or search logs sent from Log & Crash Search and operates under a multi-threading environment. 

## 1. Add Log & Crash Logback SDK

Add logncrash-java-sdk3-3.0.5.jar to dependency. 
Download Log & Crash Logback SDK from  [NHN Cloud Document](http://docs.toast.com/en/Download/).

```
Click [DOCUMENTS] > [Download] > [Data & Analytics > Log & Crash Search] > [Logback SDK]
```


- Log & Crash Logback SDK has dependency on the libraries of`logback-classic 1.2.3+, apache httpclient 4.5+, and json 20171018+`.
- It is recommended to apply the highest version to prevent any potential issues from redundant libraries.

## 2. Add Dependency for Log & Crash Logback SDK

### 2.1 Install Maven 

Add dependency to pom.xml.  

```xml
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20171018</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```
### 2.2 Install Gradle 

```gradle
dependencies {
    compile 'org.json:json:20171018'
    compile 'org.apache.httpcomponents:httpclient:4.5'
    compile 'ch.qos.logback:logback-classic:1.2.3'
}
```

## 3. Logger Setting and Options  

Following description is based on logback.xml. 

- Collect logs asynchronously with AsyncAppender of Logback as default and send them to a log collector server.  
- Set detail items for sending logs with LogNCrashHttpAppender of Log & Crash Logback SDK. 

```xml
<!-- Declare LogNCrashHttpAppender -->
<appender name="logNCrashHttp" class="com.toast.java.logncrash.logback.LogNCrashHttpAppender">
    <appKey value="appkey"/>
    <logSource value="operation"/>
    <version value="1.0.0"/>
    <logType value="audit log"/>
    <debug value="true"/>
    <category value="log service"/>
    <errorCodeType value="action"/>
</appender>
<!-- Declare AsyncAppender including LogNCrashHttpAppender -->
<appender name="LNCS-APPENDER" class="ch.qos.logback.classic.AsyncAppender">
    <!-- AsyncAppender of Logback Option -->
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <level>INFO</level>
    </filter>
    <includeCallerData value="false"/>
    <queueSize value="2048"/>
    <neverBlock value="true"/>
    <maxFlushTime value="60000"/>
    <appender-ref ref="logNCrashHttp"/>
</appender>

<logger name="user-logger" additivity="false">
    <appender-ref ref="LNCS-APPENDER"/>
</logger>
```

### 3.1 AsyncAppender Option of Logback

- For more details, see [Official Documents](https://logback.qos.ch/manual/appenders.html#AsyncAppender).

| Key               | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| includeCallerData | Sender information (e.g class name, or line number) is added, to decide whether to send to collector server. When it is set for True, performance may be degraded. |
| queueSize         | The highest allowed number allowed at blocking queue, and default is 256. |
| neverBlock        | If it is set for False, while queues are full, the appender blocks application to prevent loss of messages. For True, messages are dropped in order not to stop application. |
| maxFlushTime      | When LoggerContext is suspended, the stop method of AsyncAppender queues until an operation thread is timed out. With maxFlushTime, timeout can be set by the millisecond. Any events that fail to be processed within time set shall be deleted. |

### 3.2 LogNCrashHttpAppender Option of Log & Crash Logback SDK

For other values, except appKey, default information shall be entered, unless param is written as optional.   

| Key           | Description                                                  | Default      |
| ------------- | ------------------------------------------------------------ | ------------ |
| appKey        | Refers to a projectkey enabled in the logncrash console.     | `Required`   |
| logSource     | Information of executed environment setting, such as alpha, beta, and real. If you use Spring Profile, apply ${spring.profiles.active}. | http-logback |
| version       | Specify sender's project version.                            | 1.0.0        |
| logType       | Specify the type of collected logs.                          | log          |
| debug         | Determine by boolean for the output needs of SDK log information in console. | true         |
| category      | Specify the category of collected logs.                      |              |
| errorCodeType | Set the type of error information collected when error occurs: default, action, message, or mdc. <br />- default: Use throwable information.<br />- action: Return errors including URL path information.  <br />- message: Return messages set for logger only.  <br />- mdc: Set value of errorCode item of MDC. | default      |

### 3.3 User-defined Option 

Items not defined in LogNCrashHttpAppender of Log & Crash may be added by using MDC of slf4j. (However,`category` may be changed.) 

```java
MDC.put("userid", "nhnent-userId");
MDC.put("userIp", "127.0.0.1");
...
MDC.clear();
```

#### Reserved words which cannot be changed with MDC (no difference between upper and lower cases)

 

| projectName | clientIp | projectVersion | url     | logSource | headers  |
| ----------- | -------- | -------------- | ------- | --------- | -------- |
| form        | logType  | cookie         | body    | agent     | logLevel |
| host        | referer  | sendTime       | dmpData | dmpFormat |          |

## 4. Example of LogNCrash SDK 

Applied as follows in Java: 

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;

public class LogNCrash {
    private static final Logger USER_LOG = LoggerFactory.getLogger("user-logger");

    public void logging() {
        logger.debug("LogNCrash Debug.") ;

        // Apply MDC to use user-defined items, other than Log & Crash scheduled itmes
        MDC.put("userid", "nhnent-userId");
        logger.warn("Customize items...") ;
        MDC.clear();

        try {
            String logncrash = null;
            if(true) {
                System.out.print(logncrash.toString());
            }    
        } catch(Exception e) {
            logger.error("LogNCrash Exception.", e)
        }   
    }
}
```

## FAQs

### Q. Can I send logs only with LogNCrashHttpAppender of Log & Crash Logback SDK?

It is possible, since LogNCrashHttpAppender of Log & Crash Logback SDK sends logs to a collector server, by using the sync method. Nevertheless, although logs may be lost to the minimum, performance degradation may occur to Application when the Log & Crash Logback system fails, it is recommended to apply Async Appender. 

### Q. How can I use logncrash client in a batch program (project)?

Add a code that allows you to wait for seconds at the end of a batch program. 

```java
try {
    Thread.sleep(3000L);
} catch (InterruptedException ignore){}
```

In a Java batch program, the main thread is immediately closed, closing batch application before a demonstration thread of LogbackAsyncAppender is created to send logs. 
When there is no remaining general thread, regardless of demonstration threads, JVM is immediately closed. Therefore, a code needs to be added that allows waiting at the end of a program, so that a program can be closed after all logs are delivered. 

### Q. How can a Java stack trace be logged to a logback (including Log & Crash Search)?

To get an output of stack trace by using logback, use the log.error (e.getMessage(), e); type. SLF4J Logger, as a method parameter, does not support the logging method which receives Throwables only.  

```java
String[] aa = null;
try {
    ...
} catch (NullPointerException e) {
    log.error(e.getMessage(), e);
}
```

### Q. How can I safely close WAS?

When closing WAS (such as Tomcat) while error logs are sent, following exception may occur and WAS may not be closed properly.  

To prevent it and safely close WAS, call stop() method for LoggerContext instances by the time WAS is closed, and close LogNCrashHttpAppendet. 

For Spring, org.springframework.web.util.Log4jConfigListener is provided for Log4J, but no listener is provided for Logback in support of stable closure. 

Log & Crash Logback SDK provides its own LogbackShutdownListener. By adding the following setting to web.xml, WAS can be safely closed even while error logs are delivered.  

```xml
<listener>
    <listener-class>
        com.toast.java.logncrash.logback.LogbackShutdownListener
    </listener-class>
</listener>
```
