## Analytics > Log & Crash Search > Log4J SDK使用ガイド

Log & Crash Log4J SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。
Log & Crash Log4J SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- Log & Crash Searchで、転送されたログの照会および検索が可能です。
- マルチスレッド環境で動作します。

## サポート環境

- Log4J 1.2.x (1.2.14, 1.2.16, 1.2.17)

## ダウンロード

[TOAST Document](http://docs.toast.com/ko/Download/)でLog4J SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Log4J SDK]をクリック
```

## インストール

### 構成

Log4J SDKは、次のように構成されています。

```
docs/       ; Log4J SDK文書
lib/        ; Log4Jライブラリ
sample/     ; Log4Jサンプル
```

### SDKサンプル

一緒に提供されるsample/log4j/について説明します。

1.Eclipseを実行し、メニューからFile - Import - Maven - Existing Maven Projectsを実行してsample/log4j/を読み込みます。
2.src/test/resources/log4j.xmlファイルを開き、発行されたアプリケーションキーとバージョンを修正し、必要な場合は収集サーバーアドレスを変更します。

```
<param name="collectorUrl" value="https://api-logncrash.nhncloudservice.com" />
<param name="appKey" value="__app_key__" />
<param name="version" value="1.0.0" />
```

3.EclipseメニューからProject - Properties - Java Build Path - Librariesを選択して、toast-logncrash-log4j-sdk-<version>.jarを追加します。
4.Eclipseメニューから、Run - Run As - JUnit Testを選択して実行します。

## 使用例

1.Log4J SDKライブラリをProjectに追加します。
- 例えば、EclipseメニューProject - Properties - Java Build Path - Librariesを選択してtoast-logncrash-log4j-sdk-<version>.jarを追加します。

2.Mavenを使用する場合、 pom.xmlにdependencyを追加します。

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

- SLF4Jを使用する場合、次のdependencyを追加します。

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

3.Mavenを使用しない場合は、次のライブラリを別途ダウンロードしてclass pathに追加します。

```
log4j, 1.2.17
commons-lang, 2.5
httpclient, 4.2.6
httpcore, 4.2.4
servlet-api, 2.4
json, 20090211
```

4.Appenderの設定と構成のために、log4j.xmlを作成します。

 - 全体構成はsample/log4j/src/test/resources/log4j.xmlを参照してください。
 - collectorUrl、appKeyには**収集サーバーアドレス**、**発行されたアプリケーションキー**を使用する必要があります。

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

5.propertiesを設定するには、log4j.propertiesを作成します。

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

6.Javaで次のように使用します。

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

### log4j.xml設定項目

- collectorUrl：収集サーバーアドレス
	HTTP： https://api-logncrash.nhncloudservice.com
- appKey：プロジェクトアプリケーションキー。必須
- version：プロジェクトバージョン。デフォルト値"1.0.0"
- logSource：ログソース。デフォルト値"http-log4j"
- logType：ログタイプ。デフォルト値"log"
- Threshold：転送するログレベル指定。デフォルト値"ALL"
- enable： Appenderを使用するかどうか。デフォルト値"true"
- debug：デバッグを使用するかどうか。デフォルト値"false"
- errorCodeType：エラーコードタイプ設定。デフォルト値"default"
	default： Exception情報を使用
	mdc： Log4j MDCのerrorCode項目値を設定して使用する。

## 制約事項

- 現在、**log4j 2.0**バージョンでは動作しません。log4j 1.3はalpha8のみ動作しますが、log4j 1.2でマイグレーションすることを推奨します。推奨バージョンは、log4j 1.2.14、1.2.16、1.2.17です。
- エラーデータが一度に大量に発生する場合は、logncrash-async appenderのbufferSizeが小さいとlog4j自体で処理する時に遅延が発生することがあるため、bufferSizeの調節が必要です。

## FAQ

### blockingをfalseで使用するには？

log4j.xmlで、次のようにlogncrash-asyncのclass名を変更する。

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

### batch program(project)で、logncrash clientを使用するには？

Quartzなどを使用して、デーモン形式で動くbatch projectには適用されません。batchプログラムの最後に数秒間待機するコードを追加します。

```
try {
    Thread.sleep(3000L);
} catch (InterruptedException ignore){}
```

logncrash-async appenderの場合、org.apache.log4j.AsyncAppenderを使用しています。
AsyncAppender内でログを記録する別途のデーモンスレッドが作成され、非同期でログを伝達するようになっています。Java batch programではmain threadがすぐに終了するため、AsyncAppenderデーモンスレッドが作成されログを転送する前にbatchアプリケーションが終了します。
デーモンスレッドに関係なく、生きている一般スレッドがない場合、JVMはすぐに終了します。
次の方法は、下記のようにbatch用log4j.xmlを別途使用するものです。Loggerでappender logncrashをすぐ使用するようにlog4j.xmlを修正します。この場合、loggingが同期モードで動作するため、エラー発生時にエラー収集サーバー呼び出しのためにdelayが発生します。web projectではこの方法を使用しないようにします。

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

### Java stack traceをlog4j(Log & Crash Searchを含む)にロギングするには？

log4jを利用してstack traceを出力するには、log.error(e.getMessage(), e);形式を使用します。log.error(e);の場合はstack traceが出力されません。

```
String[] aa = null;
try {
    aa[0] = "111";
} catch (NullPointerException e) {
    log.error(e); //stacktraceが出力されない。
    log.error(e.getMessage(), e); ///stacktrace出力
}
```

### log4j(Log & Crash Search含む) loggingによる性能低下を最小化するには？

log4j.xmlのlogger設定で、nameとlevelを使用してfilteringを最大化します。
このようにlogger設定でcomやorgをDEBUG levelに設定すると、loggerで多くのLoggingEvent(log4j)が不必要に作成されます。AppenderでThresholdがERRORに設定されていて実際のログは転送されませんが、一旦loggerでLoggingEventが作成されappenderに伝達されます。

[性能が低下する設定(開発用にのみ使用)]

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

[性能が考慮された設定(運営用に使用)]

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

### WASで使用時に安定的に終了するには？

エラーログを転送中の状況でWAS(Tomcatなど)が終了する場合は、次のようなExceptionが発生し、WASが正常に終了しないことがあります。

```
Exception in thread "pool-12-thread-1" java.lang.NullPointerException
at_external.org.apache.mina.common.AbstractPollingIoProcessor$Worker.run(AbstractPollingIoProcessor.java:740)
at_external.org.apache.mina.util.NamePreservingRunnable.run(NamePreservingRunnable.java:51)
at_java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:886)
at_java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:908)
at_java.lang.Thread.run(Thread.java:619)
```
ログを転送中にWASが終了する場合に該当Exceptionが発生します。このような現象を防ぐには、WAS終了時にLogManager.shutdown()メソッドを呼び出してlogncrash appenderをcloseすると、安定的に終了できます。
org.springframework.web.util.Log4jConfigListenerを使用する場合は、WAS終了時にLog4jConfigListenerがLogManager.shutdown()メソッドを呼び出すため、追加の設定をしなくても安定的に終了できます。
Log4jConfigListenerを使用しない場合のためにlogncrash-appenderではcom.toast.java.logncrash.log4j.Log4jShutdownListenerを提供しています。web.xmlに次のような設定を追加すると、WAS終了時にエラーログの転送が発生しても安定的に終了できます。

```
<listener>
    <listener-class>
        com.toast.java.logncrash.log4j.Log4jShutdownListener
    </listener-class>
</listener>
```
