## Analytics > Log & Crash Search > Android SDK使用ガイド

> [Deprecated]
> Log & Crash Android SDKバージョンは、今後はサポートされません。
> [TOAST SDK](http://docs.toast.com/ko/TOAST/ko/toast-sdk/overview/)をご利用ください。

Log & Crash Android SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。
Log & Crash Android SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchから送られたログの照会および検索ができます。
- マルチスレッド環境で動作します。

## サポート環境

- Android 2.3.3、API Level 10以上

## ダウンロード

[TOAST Document](http://docs.toast.com/ko/Download/)でAndroid SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Android SDK]をクリック
```

## インストール

### 構成

Android SDKは、次のように構成されています。

```
docs/       ; Android SDKドキュメント
libs/       ; Android SDKライブラリ
sample/     ; Android SDKサンプル
```

### SDKサンプル

一緒に提供されるsample/について説明します。

1. libs/をsample/libs/にコピーします。
2. Eclipseを開いて、File - New - Android - Android Project from Existing Codeでsample/を開きます。
     - AndroidStudioでは、File - New - Import Project…を使用します。
3. ToastLogSample.javaを開いて、onCreate()でアプリケーションキー、収集サーバーアドレスを修正します。バージョン、ログソース、ログタイプなどを修正すると、検索に役立ちます。
4. 実行します。
5. Initializeボタンを押して開始します。
6. debug、info、warn、error、fatalボタンを押してログを転送します。
7. send crash、crashボタンを押してクラッシュログを転送します。send crashボタンはクラッシュログのみを転送する機能です。crashボタンは、強制的にcrashを発生させ、アプリ終了と同時にクラッシュログを転送します。

### 使用例

1.Android SDKのlibs/を該当プロジェクトlibs/にコピーします。
2.AndroidManifest.xmlファイルに権限を追加します。

- ネットワーク状態、デバイス情報、通信社などの情報をインポートするには、権限が必要です。

```
<!-- ログを転送するためのインターネットアクセス権限(必須) -->
<uses-permission android:name="android.permission.INTERNET" />

<!-- Platform、Carrierなど、端末の情報にアクセスするための権限(必須) -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />

<!-- ネットワーク状態にアクセスするための権限(必須) -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<!-- ネットワークに接続できない場合、ファイルにログを保存するための権限(オプション) -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<application
      ......
```

3.提供されるToastLog classを使用して、ログを転送します。

- ToastLog.initialize()を実行して、初期化します。
- debug()/info()/warn()/error()/fatal()関数を使用して、該当logLevelのログを収集サーバーに転送します。
- アプリクラッシュが発生すると、クラッシュログが収集サーバーに転送されます。

```
.....
public class MainActivity extends Activity {
  .....
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    .....
    if (ToastLog.initialize(getApplication(), "__収集サーバー_アドレス__", 0, "__アプリケーションキー__", "__バージョン__") {
      // 初期化成功
      ToastLog.info("Init Success")
    } else {
      // 初期化失敗
    }
    .....
  }
}
.....
```

## API List

com.toast.android.logncrash.ToastLog classで、提供する機能を説明します。

### 初期化

```
public static final String DEFAULT_APP_KEY = "__app_key__";
public static final String DEFAULT_VERSION = "1.0.0";
public static final String DEFAULT_COLLECTOR_ADDR = "https://api-logncrash.nhncloudservice.com";
public static final int DEFAULT_COLLECTOR_PORT = 0;
public static final String DEFAULT_LOG_SOURCE = "logncrash-logSource";
public static final String DEFAULT_LOG_TYPE = "logncrash-logType";

public static boolean initialize(Application application, String collectorAddr, int collectorPort, String appKey, String version, String userId);

public static boolean initialize(Application application, String collectorAddr, int collectorPort, String appKey, String version);

public static boolean initialize(Application application, String collectorAddr, int collectorPort, String appKey, String version, boolean syncStart);
```

- ToastLogを初期化します。
- ToastLog機能が正常に動作するには、必ず呼び出される必要があります。
- パラメータ
	- application：Android Application情報。getApplication()の戻り値を入れます。
	- collectorAddr：収集サーバーアドレス
		- HTTP収集サーバー：https://api-logncrash.nhncloudservice.com
	- collectorPort：収集サーバーのポート情報、0に指定すると、各protocolの基本ポートが使用されます。
		- HTTP：443
	- appKey：アプリケーションキー
	- version：アプリバージョン
	- userId：ユーザーID
  - syncStart： trueの場合、発生したログはstartSendThreadが呼び出されるまでサーバーに転送せず、キューに保存します。ただしCrashが発生した場合は、ThreadLockを解除してログを転送します。
- 戻り値
	- 初期化成功時はtrue
	- 失敗時はfalse

### SendThreadのロック解除
```
  	(void) startSendThread;
```
  - SendThreadのロック状態を解除します。

### 初期化の注意事項
  - Application onCreateで初期化を行う場合
  	- Application onCreateはレシーバー、サービス、アクティビティが作成される時に呼び出されるので、意図しない呼び出しが発生することがあります。

```
public static boolean isInitialized()
```

- ToastLogが初期化されたかを返します。
- 戻り値
	- 初期化されていればtrue
	- 初期化されていなければfalse

### ログの転送

```
public static void fatal(String message, Throwable t)
public static void error(String message, Throwable t)
public static void warn(String message, Throwable t)
public static void info(String message, Throwable t)
public static void debug(String message, Throwable t)

public static void fatal(String message)
public static void error(String message)
public static void warn(String message)
public static void info(String message)
public static void debug(String message)
```

- 指定されたログレベルで、収集サーバーにログを転送します。
- パラメータ
	- message：ログメッセージ。 Nullや""を使うと、基本メッセージが転送されます。
	- Throwable t：収集されたエラーを一緒に転送します。 nullを使用できます。
		- エラーを一緒に転送すると、ハンドルドログに分類されます。

```
public static void crash(String message, Throwable t)
```

- 実行すると、発生したExceptionを収集サーバーに転送します。
- パラメータ
	- message：ログメッセージ。 Nullや""を使うと、基本メッセージが転送されます。
	- Throwable t：収集されたエラーを一緒に転送します。
		- エラーを一緒に転送すると、クラッシュログに分類されます。

errorCode、locationなどの情報をThrowableで調査するように向上させつつ、次のメソッドが**Deprecated**されました。

```
public static void fatal(String errorCode, String message, String location)
public static void error(String errorCode, String message, String location)
public static void warn(String errorCode, String message, String location)
public static void info(String errorCode, String message, String location)
public static void debug(String errorCode, String message, String location)

public static void fatal(String errorCode, String message)
public static void error(String errorCode, String message)
public static void warn(String errorCode, String message)
public static void info(String errorCode, String message)
public static void debug(String errorCode, String message)

public static void crash(Throwable throwable, String errorCode, String message, String location)
public static void crash(Throwable throwable, String errorCode, String message)
```

### カスタムキーを指定する

```
public static void addCustomField(String key, String value)

public static void removeCustomField(String key)

public static void clearCustomFields()
```

- カスタムキーを追加、削除、全部削除機能を提供します。
- カスタムキーは、大文字/小文字で始まり、大文字/小文字、数字、'-'、'_'のみ使用できます。( [A-Za-z][A-Za-z0-9_-]* )
- カスタムキーは、大文字/小文字に関わらず、次の名前は使用できません。
	- "projectName", "projectVersion", "body"
	- "logSource", "logType", "host", "sendTime", "logLevel"
	- "DmpData", "Platform", "NeloSDK", "Exception", "Location", "Cause"
	- "SessionID", "UserID"
	- "Carrier", "CountyCode", "DeviceModel", "Locale", "NetworkType", "Rooted"


### 基本設定管理

```
public static String getAppKey()
public static String getVersion()
public static String getCollectorAddr()
public static int getCollectorPort()
```

- 初期化に使用されたアプリケーションキー、バージョン、収集サーバーアドレス、収集サーバーポートを取得します。

```
public static String getLogSource()
public static void setLogSource(String logSource)
```

- ログソースの取得や新たな指定を行います。

```
public static String getLogType()
public static void setLogType(String logType)
```

- ログタイプの取得や新たな指定を行います。

### 重複除去モードの設定

2.4.0以上のSDKから、一般ログに重複除去ロジックが適用されました。

重複ログ機能が有効になっている場合、bodyとlogLevelの内容が同じログが発生した時に転送しません。

```
public static void setDuplicate(bool enable)
```

true：(Default値)重複除去ロジックを有効にする<br>
false：重複除去ロジックを無効にする

## SDKサンプルを利用したProguardテスト

Androidで提供するProguardで、コードの難読化をテストする方法を説明します。
Proguardを適用するには、Releaseでプロジェクトを作成する必要があります。これに必要なキーストア、Proguard設定などがsample/に含まれています。


### Eclipseを使用してProguardテスト

1. libs/をsample/libs/にコピーします。
2. Eclipseを起動して該当プロジェクトを選択し、メニューからFile - Export…を選択します。
3. Exportウィンドウが表示されたら、Android - Export Android Applicationを選択します。
4. プロジェクト名を確認してNextをクリックし、Keystore選択でmykey.keystoreを選します。Keystore暗号'abcdefg'、Alias名'mykey'、Alias暗号'abcdefg'に指定されています。詳細は[こちら](http://developer.android.com/tools/publishing/app-signing.html)を参照してください。
5. 保存するパスを指定します。
6. 保存された.apkファイルを'adb install <ファイル名>'で端末にインストールします。
7. 実行してInitializeボタンを押した後、 crashボタンを押してクラッシュログを発生させます。Sampleでは、ToastLogSample.clickCrash()のメンバーファンクションが難読化されているため、正常に発生しません。

正常にProguardが起動している状態では、proguard/mapping.txtが作成されます。

1. Console - Analytics - Log & Crash - Settings - シンボルファイルでmapping.txtをアップロードします。この時、同じプロジェクトバージョンを入力する必要があります。
2. アプリからクラッシュログを転送します。Proguardが解除されたログは"DmpData"フィールドの"参照"をクリックすると確認できます。
3. ToastLogSample.clickCrash()の名前が正常に表示されるか確認します。

### Antビルドを使用してProguardをテスト

1. libs/をsample/libs/にコピーします。
2. build.xmlがない場合、android update project -p . -n AndroidSDKSampleコマンドで作成します。
3. ant clean releaseでビルドします。結果物はbin/内のAndroidSDKSampe-release.apkに保存されます。

Antを利用してReleaseビルドをする場合、Eclipse Releaseビルドとは異なり、mapping.txtの位置がbin/proguard/mapping.txtです。

### AndroidStudioを使用してProguardテスト

1. libs/をsample/libs/にコピーします。
2. AndroidStudioを起動し、メニューからFile - New - Import Project…を実行して、新しい位置にAndroidStudioプロジェクトを作成します。
3. 既存ソースで、mykey.keystoreを新たに作成されたプロジェクトにコピーします。
4. Build - Generate Signed APK…を実行します。最初のページでModuleを確認した後、Nextをクリックします。2ページ目でKey store pathをmykey.keystoreに指定し、Keystore暗号'abcdefg'、Alias名'mykey'、Alias暗号'abcdefg'に指定して、Nextをクリックします。3ページ目でAPK Destination Folderを確認して、Finishをクリックします。
5. 作成された.apkをインストールします。

AndroidStudioを利用してReleaseビルドをすると、mapping.txtの位置がapp/build/outputs/mapping/release/mapping.txtです。

## JNI適用ガイド

[Android NDK](http://developer.android.com/tools/sdk/ndk/index.html)を利用して、[JNI](http://en.wikipedia.org/wiki/Java_Native_Interface)を使用する時に、Android SDKを活用する方法について説明します。
Log & Crash Android SDKでは、Java上でExceptionをCatchできる状況でのみ正常に動作します。そのために作成したNative Codeでエラーが発生した時に、エラーを渡すクラスを作成する必要があります。
例えば、下記のようにgetString関数が実行される過程でエラーが発生した場合、try/catch構文を利用してErrorをCatchした後、Javaコードで例外を作成してThrowすると、Javaから該当のErrorをCatchしてログに転送します。

**JNI code**

```
.....
// Native Code
void throwArithmeticError(JNIEnv *env, char *message) {
  jclass exClass;
  char *className = "java/lang/ArithmeticException: / by zero";
  exClass = (*env)->FindClass(env, className);
  if (exClass == NULL) {
    return throwNoClassDefError(env, className);
  }
  (*env)->ThrowNew(env, exClass, message);
}

JNIEXPORT JNICALL jstring Java_com_example_jnitest_HelloJNI_getString(JNIEnv *env, jobject object) {
  if (...) {
    return str;
  } else {
    //On Error
    throwArithmeticError(env, "THIS IS JNI EXCEPTION");
  }
}
.....

**Java Code**

.....
// Java Code
try{
  mTextView.setText( new HelloJNI().getString() );
} catch(Throwable e){
  ToastLog.crash(e, "JNI ERROR", "JNI ERROR MESSAGE - Throw Error");
}
.....
```

Segment falutなどのSystem Crashをキャッチするには、Native CodeでSignalをキャッチする構文を追加してください。
下記の例は[こちら](http://stackoverflow.com/questions/1083154/how-can-i-catch-sigsegv-segmentation-fault-and-get-a-stack-trace-under-jni-on)を参照してください。

**Native Code**

```
.....
//Native Code
JNIEXPORT jint android_sigaction(int signal, siginfo_t *info, void *reserved) {
  old_sa[signal].sa_handler(signal);
  return throwNoClassDefError(env, "THIS android_sigaction ACTION");
}

JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *jvm, void *reserved) {
  jclass cls, vcls;
  if ((*jvm)->GetEnv(jvm, (void **) &env, JNI_VERSION_1_2))
    return JNI_ERR;

  // Try to catch crashes...
  struct sigaction handler;
  memset(&handler, 0, sizeof(sigaction));
  handler.sa_sigaction = android_sigaction;
  handler.sa_flags = SA_RESETHAND;

  #define CATCHSIG(X) sigaction(X, &handler, &old_sa[X])
  CATCHSIG(SIGILL);
  CATCHSIG(SIGABRT);
  CATCHSIG(SIGBUS);
  CATCHSIG(SIGFPE);
  CATCHSIG(SIGSEGV);
  CATCHSIG(SIGSTKFLT);
  CATCHSIG(SIGPIPE);

  return JNI_VERSION_1_2;
}

jint throwNoClassDefError(JNIEnv *env, char *message) {
  jclass exClass;
  char *className = "java/lang/NoClassDefFoundError";

  exClass = (*env)->FindClass(env, className);
  if (exClass == NULL) {
    return throwNoClassDefError(env, className);
  }
  (*env)->ThrowNew(env, exClass, message);
}
.....
```
