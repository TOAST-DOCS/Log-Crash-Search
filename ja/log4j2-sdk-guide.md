## Analytics > Log & Crash Search > Log4J v2 SDK使用ガイド

Log & Crash Log4J 2 SDKはLog & Crash Search収集サーバーにログを転送する機能を提供します。
Log & Crash Log4J SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- Log & Crash Searchで、転送されたログの照会および検索が可能です。
- マルチスレッド環境で動作します。

## サポート環境

- Log4J 2.x

## ダウンロード

[TOAST Document](http://docs.toast.com/ko/Download/)でLog4J 2 SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Log4J.v2 SDK]をクリック
```

## インストール

### 構成

Log4J SDKは次のように構成されています。

```
docs/       ; Log4J 2 SDK文書
lib/        ; Log4J 2ライブラリ
sample/     ; Log4J 2サンプル
```

### SDKサンプル

一緒に提供されるsample/log4j2/について説明します。

1.Eclipseを実行し、メニューからFile - Import - Maven - Existing Maven Projectsを実行して、sample/log4j2/を読み込みます。
2.src/test/resources/log4j2.xmlファイルを開き、発行されたアプリケーションキーとバージョンを修正し、必要な場合は収集サーバーアドレスを変更します。

```
<collectorUrl>https://api-logncrash.nhncloudservice.com </collectorUrl>
<appkey>__app_key__</appkey>
<version>1.0.0</version>
```

3.EclipseメニューからProject - Properties - Java Build Path - Librariesを選択して、toast-logncrash-log4j2-sdk-<version>.jarを追加します。
4.EclipseメニューからRun - Run As - JUnit Testを選択して実行します。

## 使用例

1.Log4J SDKライブラリをProjectに追加します。
例えばEclipseメニューProject - Properties - Java Build Path - Librariesを選択して、toast-logncrash-log4j2-sdk-<version>.jarを追加します。
2.Mavenを使用する場合、 pom.xmlにdependencyを追加します。

```
<dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-api</artifactId>
      <version>2.3</version>
  </dependency>
  <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
      <version>2.3</version>
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

SLF4Jを使用する場合は、次のdependencyを追加します。

```
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.2</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.3</version>
</dependency>
```

3.Mavenを使用しない場合は、次のライブラリを別途ダウンロードしてclass pathに追加します。

```
log4j, 2.3
log4j-core, 2.3
commons-lang, 2.5
httpclient, 4.2.6
httpcore, 4.2.4
servlet-api, 2.4
json, 20090211
```

4.Appenderの設定と構成のために、log4j2.xmlを作成します。

- 全体構成はsample/log4j2/src/test/resources/log4j2.xmlを参照してください。
- collectorUrl、appKeyには収集サーバーアドレス、発行されたアプリケーションキーを使用する必要があります。

```
<Appenders>
	<LogNCrashHttpAppender name="HTTP">
	<collectorUrl>https://api-logncrash.nhncloudservice.com </collectorUrl>

	<appKey>__app_key__</appKey>
	<version>1.0.0</version>
	<logSource>http-log4j2</logSource>
	<logType>log</logType>
	<enable>true</enable>
	<debug>false</debug>
	</LogNCrashHttpAppender>
	</Appenders>
	...
	<Loggers>
	<Root level="debug">
	<AppenderRef ref="HTTP"/>
	</Root>
</Loggers>
```

5.Javaで次のように使用します。

```
...
private static Logger log4j2Logger = LogManager.getLogger(LogNCrashLog4j2Sample.class);
...
// CustomField Test
ThreadContext.put("custom", "custom log test");
log4j2Logger.debug("LogNCrash Log4J2 Test");
...
try {
	String npe = null;
	npe.toString();
} catch (Exception e) {
	log4j2Logger.error(e.toString(), e);
}
```

## API List

### log4j.xml設定項目

- collectorUrl：収集サーバーアドレス
	HTTP： https://api-logncrash.nhncloudservice.com
- appKey：プロジェクトアプリケーションキー。必須
- version：プロジェクトバージョン。デフォルト値"1.0.0"
- logSource：ログソース。デフォルト値"http-log4j2"
- logType：ログタイプ。デフォルト値"log"
- enable： Appenderを使用するかどうか。デフォルト値"true"
- debug：デバッグを使用するかどうか。デフォルト値"false"

## 制約事項

- **log4j 1.2**バージョンでは動作しません。

## FAQ

### 性能向上のためにAsynchronous Loggerを使用するには

Asynchronous Loggers for Low-Latency Loggingを参照してください。

### Java stack traceをLog4j 2(Log & Crash Search含む)にロギングするには

Log4j 2を利用してstack traceを出力するには、log.error(e.toString(),e);形式を使用します。log.error(e);の場合にはstack traceが出力されません。

```
String[] aa= null;
try {
	aa[0] = "111";
} catch(NullPointerexception e) {
		log.error(e); // stacktrace出力されない。
		log.error(e.toString(), e); // stacktrace出力
}
```
