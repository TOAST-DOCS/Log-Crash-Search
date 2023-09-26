## Data & Analytics > Log & Crash Search > Logback SDK使用ガイド

Log & Crash Logback SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。Log & Crash Searchで、転送されたログの照会および検索が可能で、マルチスレッド環境で動作します。

## 1. Log & Crash Logback SDK追加

logncrash-java-sdk3-3.0.5.jarを依存性に追加します。
[NHN Cloud Document](http://docs.toast.com/ko/Download/)でLog & Crash Logback SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Data & Analytics > Log & Crash Search] > [Logback SDK]クリック
```


- Log & Crash Logback SDKは、`logback-classic 1.2.3+, apache httpclient 4.5+, json 20171018+`のライブラリに依存性を持っています。
- 参照するlibraryが重複する場合、問題が発生することがあるため、上位バージョンの使用を推奨します。 

## 2. Log & Crash Logback SDKに必要な依存性を追加

### 2.1 Mavenインストール

pom.xmlにdependencyを追加します。 

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
### 2.2 Gradleインストール

```gradle
dependencies {
    compile 'org.json:json:20171018'
    compile 'org.apache.httpcomponents:httpclient:4.5'
    compile 'ch.qos.logback:logback-classic:1.2.3'
}
```

## 3. Logger設定およびオプション

logback.xmlを基準に説明します。

- LogbackのAsyncAppenderをデフォルトで使用し、ログを非同期で収集してログ収集サーバーに転送します。
- Log & Crash Logback SDKのLogNCrashHttpAppenderで、転送ログの詳細項目を設定できます。

```xml
<!-- LogNCrashHttpAppender宣言 -->
<appender name="logNCrashHttp" class="com.toast.java.logncrash.logback.LogNCrashHttpAppender">
    <appKey value="アプリケーションキー"/>
    <logSource value="運営"/>
    <version value="1.0.0"/>
    <logType value="監査ログ"/>
    <debug value="true"/>
    <category value="ログサービス"/>
    <errorCodeType value="action"/>
</appender>
<!-- LogNCrashHttpAppenderを含むAsyncAppender宣言 -->
<appender name="LNCS-APPENDER" class="ch.qos.logback.classic.AsyncAppender">
    <!-- LogbackのAsyncAppenderオプション -->
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

### 3.1 LogbackのAsyncAppenderオプション

- 設定値の詳細な情報は、[公式文書](https://logback.qos.ch/manual/appenders.html#AsyncAppender)を参照してください。

| キー | 説明 |
|---|---|
| includeCallerData | 送信者の情報(class名、行番号など)が追加され、収集サーバーに転送するかどうかを決定します。<br>trueに設定すると、性能が低下することがあります。
| queueSize | blocking queueの最大収容個数で、デフォルト値は256です。 |
| neverBlock | falseに設定した場合、キューがいっぱいの状況でappenderはメッセージの消失を防ぐためにapplicationをblockします。 <br>trueに設定した場合、applicationを止めないようにメッセージを捨てます。|
| maxFlushTime | LoggerContextが停止すると、AsyncAppenderのstopメソッドは作業スレッドがtimeoutするまで待機します。<br>maxFlushTimeを使用すると、timeout時間をミリ秒で設定できます。<br>該当時間内に処理できなかったイベントは削除されます。 |

### 3.2 Log & Crash Logback SDKのLogNCrashHttpAppenderオプション

appKeyを除いた残りの値は、任意項目です。paramを記入しない場合、デフォルト値が入力されます。

| キー | 説明 | デフォルト値
|---|---|---|
| appKey | logncrahコンソールで有効にしたprojectKeyです。 | `必須値` | 
| logSource | alpha、beta、realなど、実行される環境設定情報です。<br>Spring Profileを使用する場合、 ${spring.profiles.active}を使用します。 | http-logback |
| version | 送信者のプロジェクトバージョンを明示します。 | 1.0.0 |
| logType | 収集するlogのtypeを明示します。 | log |
| debug | boolean値。consoleにsdkログ情報の出力が必要かどうかを決定します。 | true |
| category | 収集するlogのcategoryを明示します。 |  |
| errorCodeType | エラー発生時に収集されるエラー情報のタイプを設定します。default、action、message、mdcタイプが存在します。<br>- default： Throwable情報を使用します。<br>- action： URL pathの情報も含めてエラーを返します。<br>- message： loggerに設定したメッセージのみ返します。<br>- mdc： MDCのerrorCode項目値を設定して使用します。 | default |

### 3.3ユーザー定義オプション

slf4jのMDCを使用して、Log & CrashのLogNCrashHttpAppenderで定義されていない項目を追加できます。
(ただし`category`は変更できます。)

```java
MDC.put("userid", "nhnent-userId");
MDC.put("userIp", "127.0.0.1");
...
MDC.clear();
```

#### MDCで変更できない予約語(大文字/小文字を区別しません。)

|projectName|clientIp|projectVersion|url|logSource|headers|
|---|---|---|---|---|---|
|form|logType|cookie|body|agent|logLevel|
|host|referer|sendTime|dmpData|dmpFormat| |

## 4. LogNCrash SDK使用例

Javaで次のように使用します。

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;

public class LogNCrash {
    private static final Logger USER_LOG = LoggerFactory.getLogger("user-logger");

    public void logging() {
        logger.debug("LogNCrash Debug.") ;

        // Log & Crashで予約された項目以外のユーザー定義項目を使用する時にMDCを活用
        MDC.put("userid", "nhnent-userId");
        logger.warn("Customize items...") ;
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

## 5. FAQ

### Q. Log & Crash Logback SDKのLogNCrashHttpAppenderだけでログを転送することはできませんか？

Log & Crash Logback SDKのLogNCrashHttpAppenderは、sync方式で収集サーバーにログを転送するので、可能です。
ログの消失が最小化されますが、Log & Crash Logbackシステム障害の時は、Applicationの性能低下が発生することがあるため、Async Appenderの使用を推奨します。

### Q. batch program(project)でlogncrash clientを使用するには？

batchプログラムの最後に数秒間、待機するコードを追加します。

```java
try {
    Thread.sleep(3000L);
} catch (InterruptedException ignore){}
```

Java batch programでは、main threadがすぐに終了するため、LogbackのAsyncAppenderのデーモンスレッドが作成され、ログを転送する前にbatchアプリケーションが終了します。
デーモンスレッドに関係なく、生きている一般スレッドがない場合、JVMはすぐに終了します。したがって、上記のようにプログラムの最後に待機するコードを追加し、すべてのログを転送してから終了するようにします。

### Q. Java stack traceをlogback(Log & Crash Search含む)にロギングするには？

logback利用してstack traceを出力するには、log.error(e.getMessage(), e);形式を使用します。SLF4J Loggerはメソッドの引数にThrowableのみ受け取るロギングメソッドはサポートしません。

```java
try {
    ...
} catch (Exception e){
    logger.error(e.getMessage(), e);
}
```

### Q. WASで使用する時に安定的に終了するには？

エラーログを転送中の状況でWAS(Tomcatなど)が終了する場合は、次のようなExceptionが発生し、WASが正常に終了しないことがあります。

このような現象を防ぐためには、WAS終了時にLoggerContextインスタンスに対してstop()メソッドを呼び出し、LogNCrashHttpAppendetをcloseすると安定的に終了できます。

Springでは、Log4Jに対してはorg.springframework.web.util.Log4jConfigListenerを提供しますが、Logbackに対しては安定的な終了をサポートするListenerを提供していません。

Log & Crash Logback SDKでは、独自にLogbackShutdownListenerを提供します。web.xmlに次のような設定を追加すると、WAS終了時にエラーログの転送が発生しても安定的に終了できます。

```xml
<listener>
    <listener-class>
        com.toast.java.logncrash.logback.LogbackShutdownListener
    </listener-class>
</listener>
```
