<!-- pre-align:aligned sig=fdc80d476a42 -->

<a id="data-analytics-log-crash-search-logback-sdk-guide"></a>
## Data & Analytics > Log & Crash Search > Logback SDK Guide { #data-analytics-log-crash-search-logback-sdk-guide }

Log & Crash Logback SDK sends logs to a Log & Crash Search collector server. It allows to retrieve or search logs sent from Log & Crash Search and operates under a multi-threading environment. 

<a id="add-log-crash-logback-sdk"></a>
## 1. Add Log & Crash Logback SDK { #add-log-crash-logback-sdk }

Add logncrash-java-sdk3-4.0.0.jar to dependency. 
Download Log & Crash Logback SDK from  [NHN Cloud Document](http://docs.toast.com/en/Download/).

```
Click [DOCUMENTS] > [Download] > [Data & Analytics > Log & Crash Search] > [Logback SDK]
```


- Log & Crash Logback SDK has dependency on the libraries of`logback-classic 1.5.3+, apache httpclient 5.3.1+, json 20240303+`.
- It is recommended to apply the highest version to prevent any potential issues from redundant libraries.

<a id="add-dependency-for-log-crash-logback-sdk"></a>
## 2. Add Dependency for Log & Crash Logback SDK { #add-dependency-for-log-crash-logback-sdk }

<a id="1-install-maven"></a>
### 2.1 Install Maven { #1-install-maven }

Add dependency to pom.xml.  

```xml
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20240303</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient</artifactId>
    <version>5.3.1</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.5.3</version>
</dependency>
```
<a id="2-install-gradle"></a>
### 2.2 Install Gradle { #2-install-gradle }

```gradle
dependencies {  
    compile 'org.json:json:20240303'    
    compile 'org.apache.httpcomponents.client5:httpclient5:5.3.1'    
    compile 'ch.qos.logback:logback-classic:1.5.3'
}
```

<a id="logger-setting-and-options"></a>
## 3. Logger Setting and Options { #logger-setting-and-options }

Following description is based on logback.xml. 

- Collect logs asynchronously with AsyncAppender of Logback as default and send them to a log collector server.  
- Set detail items for sending logs with LogNCrashHttpAppender of Log & Crash Logback SDK. 

```xml
<!-- Declare LogNCrashHttpAppender -->
<appender name="logNCrashHttp" class="com.toast.java.logncrash.logback.LogNCrashHttpAppender">
    <appKey>appkey</appKey>
    <logSource>operation</logSource>
    <version>1.0.0</version>
    <logType>audit log</logType>
    <debug>true</debug>
    <category>log service</category>
    <errorCodeType>action</errorCodeType>
</appender>
<!-- Declare AsyncAppender including LogNCrashHttpAppender -->
<appender name="LNCS-APPENDER" class="ch.qos.logback.classic.AsyncAppender">
    <!-- AsyncAppender of Logback Option -->
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
        <level>INFO</level>
    </filter>
    <includeCallerData>false</includeCallerData>
    <queueSize>2048</queueSize>
    <neverBlock>true</neverBlock>
    <maxFlushTime>60000</maxFlushTime>    
    <appender-ref ref="logNCrashHttp"/>
</appender>

<logger name="user-logger" additivity="false">
    <appender-ref ref="LNCS-APPENDER"/>
</logger>
```

<a id="1-asyncappender-option-of-logback"></a>
### 3.1 AsyncAppender Option of Logback { #1-asyncappender-option-of-logback }

- For more details, see [Official Documents](https://logback.qos.ch/manual/appenders.html#AsyncAppender).

| Key               | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| includeCallerData | Sender information (e.g class name, or line number) is added, to decide whether to send to collector server. When it is set for True, performance may be degraded. |
| queueSize         | The highest allowed number allowed at blocking queue, and default is 256. |
| neverBlock        | If it is set for False, while queues are full, the appender blocks application to prevent loss of messages. For True, messages are dropped in order not to stop application. |
| maxFlushTime      | When LoggerContext is suspended, the stop method of AsyncAppender queues until an operation thread is timed out. With maxFlushTime, timeout can be set by the millisecond. Any events that fail to be processed within time set shall be deleted. |

<a id="2-logncrashhttpappender-option-of-log-crash-logback-sdk"></a>
### 3.2 LogNCrashHttpAppender Option of Log & Crash Logback SDK { #2-logncrashhttpappender-option-of-log-crash-logback-sdk }

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

<a id="3-user-defined-option"></a>
### 3.3 User-defined Option { #3-user-defined-option }

Items not defined in LogNCrashHttpAppender of Log & Crash may be added by using MDC of slf4j. (However,`category` may be changed.) 

```java
MDC.put("userid", "nhnent-userId");
MDC.put("userIp", "127.0.0.1");
...
MDC.clear();
```

<a id="3-user-defined-option-reserved-words-which-cannot-be-changed-with-mdc-no-difference-between-upper-and-lower-cases"></a>
#### Reserved words which cannot be changed with MDC (no difference between upper and lower cases)

 

| projectName | clientIp | projectVersion | url     | logSource | headers  |
| ----------- | -------- | -------------- | ------- | --------- | -------- |
| form        | logType  | cookie         | body    | agent     | logLevel |
| host        | referer  | sendTime       | dmpData | dmpFormat |          |

<a id="example-of-logncrash-sdk"></a>
## 4. Example of LogNCrash SDK { #example-of-logncrash-sdk }

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

<a id="faqs"></a>
## FAQs { #faqs }

<a id="q-can-i-send-logs-only-with-logncrashhttpappender-of-log-crash-logback-sdk"></a>
### Q. Can I send logs only with LogNCrashHttpAppender of Log & Crash Logback SDK? { #q-can-i-send-logs-only-with-logncrashhttpappender-of-log-crash-logback-sdk }

It is possible, since LogNCrashHttpAppender of Log & Crash Logback SDK sends logs to a collector server, by using the sync method. Nevertheless, although logs may be lost to the minimum, performance degradation may occur to Application when the Log & Crash Logback system fails, it is recommended to apply Async Appender. 

<a id="q-how-can-i-use-logncrash-client-in-a-batch-program-project"></a>
### Q. How can I use logncrash client in a batch program (project)? { #q-how-can-i-use-logncrash-client-in-a-batch-program-project }

Add a code that allows you to wait for seconds at the end of a batch program. 

```java
try {
    Thread.sleep(3000L);
} catch (InterruptedException ignore){}
```

In a Java batch program, the main thread is immediately closed, closing batch application before a demonstration thread of LogbackAsyncAppender is created to send logs. 
When there is no remaining general thread, regardless of demonstration threads, JVM is immediately closed. Therefore, a code needs to be added that allows waiting at the end of a program, so that a program can be closed after all logs are delivered. 

<a id="q-how-can-a-java-stack-trace-be-logged-to-a-logback-including-log-crash-search"></a>
### Q. How can a Java stack trace be logged to a logback (including Log & Crash Search)? { #q-how-can-a-java-stack-trace-be-logged-to-a-logback-including-log-crash-search }

To get an output of stack trace by using logback, use the log.error (e.getMessage(), e); type. SLF4J Logger, as a method parameter, does not support the logging method which receives Throwables only.  

```java
String[] aa = null;
try {
    ...
} catch (NullPointerException e) {
    log.error(e.getMessage(), e);
}
```

<a id="q-how-can-i-safely-close-was"></a>
### Q. How can I safely close WAS? { #q-how-can-i-safely-close-was }

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
