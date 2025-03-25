## Analytics > Log & Crash Search > AndroidNDK SDK使用ガイド

Log & Crash AndroidNDK SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。  
Log & Crash AndroidNDK SDKの特徴・利点は次のとおりです。  

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchから転送されたログを照会および検索できます。
- マルチスレッド環境で動作します。

## サポート環境

- Android 2.3.3、API Level 10以上
- AndroidNDK最新バージョン推奨
- サポートABI：armeabi、armeabi-v7a、x86

## ダウンロード

[TOAST Document](http://docs.toast.com/ko/Download/)でAndroid SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [AndroidNDK SDK]をクリック
```

## インストール

### 構成

AndroidNDK SDKは、次のように構成されています。

```
docs/                       ; AndroidNDK SDK文書
include/toast/logncrash.h   ; C++ヘッダファイル
androidndk-sdk/
    obj/                    ; AndroidNDK SDK Staticライブラリ
    PrebuiltStaticLib.mk    ; Staticライブラリmkファイル
    libs/                   ; AndroidNDK SDK Sharedライブラリ
    PrebuiltSharedLib.mk    ; Sharedライブラリmkファイル
androidndk-sdk-sample/      ; Android JNIサンプル
```

### SDKサンプル

一緒に提供されるandroidndk-sdk-sample/について説明します。

1. androidndk-sdk-sample/に移動します。
2. <ndk_path>/ndk-buildでNDK部分をビルドします。
3. EclipseでAndroid projectを読み込みます。
4. AndroidNDKSample.javaを開いて、発行されたアプリケーションキーに修正します。
5. 実行します。

クラッシュログをLog & Crash Searchで参照するには、シンボルファイルをアップロードする必要があります。

1. obj/local/<abi>/liblogncrashjni_sample.soをzipに圧縮します。
2. Toast Cloud Consoleで、"Analytics - Log & Crash Search - Settings - シンボルファイル"に移動して、zipファイルをアップロードします。この時、androidndk-sdk-sampleのようなプロジェクトバージョンでアップロードする必要があります。
3. androidndk-sdk-sampleを実行して、クラッシュログを発生させます。
	- クラッシュを発生させるために、"Initialize"ボタンを押して"Test 2"ボタンを押します。
	- Toast Cloud ConsoleでAnalytics - Log & Crash Search - Log Searchにクラッシュログが届いたことを確認し、"DmpData"フィールドにある"参照"を押して確認します。
4. "DmpData"フィールド"参照"が動作しない場合、次の事項をチェックする必要があります。
	- クラッシュログとシンボルの間のプロジェクトバージョンが合っているか
	- 無効なシンボルファイルをアップロードしていないか

## 使用例

1.jni/Application.mkに次の内容を追加します。

```
 ...
 APP_ABI := armeabi armeabi-v7a x86
 APP_PLATFORM := android-9
 APP_STL := gnustl_static
 ...
```

2.jni/Android.mkに次の内容を追加します。

```
...
 LOCAL_STATIC_LIBRARIES := logncrash_androidndk_static
 ...
 include $(LOCAL_PATH)/<androidndk_sdk_path>/androidndk-sdk/PrebuiltStaticLib.mk
```

- Sharedライブラリを使用するには、次のように宣言します。

```
...
  LOCAL_SHARED_LIBRARIES := logncrash_androidndk
  ...
  include $(LOCAL_PATH)/<androidndk_sdk_path>/androidndk-sdk/PrebuiltSharedLib.mk
```

3.toast/logncrash.hをインクルードして、ToastLog classを使用します。

```
...
 #include "toast/logncrash.h"
 ...

     ToastLog* log = GetToastLog();

     if (LOGNCRASH_LOG_OK != log->initialize(APP_KEY, VERSION, COLLECTOR_ADDR, COLLECTOR_PORT)) {
         fprintf(stderr, "ERROR in initialize()\n");
         return -1;
     }

     if (!log->info("info() TEST 1")) {
         fprintf(stderr, "ERROR in info()\n");
     }
     ...

     DestroyToastLog();
```

4.<ndk_path>/ndk-buildで、NDK部分をビルドします。

5.AndroidNDK SDKが正常に動作するには、AndroidManifest.xmlに次のパーミッションを与える必要があります。

```
...
 <uses-permission android:name="android.permission.INTERNET"/>
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
 <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
 ...
```

6.JNIライブラリを読み込みます。

```
static {
     System.loadLibrary("logncrashjni_sample");
 }
```

- Sharedライブラリを使用する時は、あらかじめliblogncrash_androidndk.soを読み込む必要があります。

```
static {
      System.loadLibrary("logncrash_androidndk");
      System.loadLibrary("logncrashjni_sample");
  }
```

AndroidNDK SDKは、Java Exceptionを処理できません。AndroidNDK SDKは、C++ Native codeのために作られているためです。
androidndk-sdk-sample/jni/Android.mkファイルとAndroidNDKに含まれている文書(<ndk_path>/docs/)を参照してください。

## API List

toast::logncrash::ToastLog classで提供する機能を説明します。

### ToastLogインスタンスの割り当て/解除

```
toast::logncrash::ToastLog* GetToastLog();

void DestroyToastLog();
```

- ToastLog instanceを割り当て、解除します。
- Singleton方式で1つのインスタンスのみ返します。
- 返されたToastLog instanceに対してdeleteをしてはいけません。除去するにはDestroyToastLog()を呼び出す必要があります。

### 初期化/解除

```
#define LOGNCRASH_VERSION         "1.0.0"
#define LOGNCRASH_COLLECTOR_ADDR  "api-logncrash.nhncloudservice.com"
#define LOGNCRASH_COLLECTOR_PORT  443
#ifdef WIN32
#define LOGNCRASH_LOGSOURCE       "logncrash-windows"
#else   //#ifdef WIN32
#define LOGNCRASH_LOGSOURCE       "logncrash-linux"
#endif  //#ifdef WIN32
#define LOGNCRASH_LOGTYPE         "logncrash-log"

#define LOGNCRASH_LOG_OK            0
#define LOGNCRASH_LOG_ERROR         -1
#define LOGNCRASH_LOG_ERROR_APPKEY  -2
#define LOGNCRASH_LOG_ERROR_VERSION -3
#define LOGNCRASH_LOG_ERROR_ADDRESS -4
#define LOGNCRASH_LOG_ERROR_PORT    -5

int32_t initialize(
  const char* appKey,
  const char* version = LOGNCRASH_VERSION,
  const char* collectorAddr = LOGNCRASH_COLLECTOR_ADDR,
  const uint16_t collectorPort = LOGNCRASH_COLLECTOR_PORT,
  const char* logSource = LOGNCRASH_LOGSOURCE,
  const char* logType = LOGNCRASH_LOGTYPE);

void destroy();
```

- ToastLogを初期化して解除します。
- ToastLog機能が正常に動作するには、initialize()が呼び出される必要があります。
- パラメータ
	- appKey：アプリケーションキー
	- version：アプリバージョン
	- collectorAddr：収集サーバーアドレス
		- Log & Crash収集サーバー：api-logncrash.nhncloudservice.com
	- collectorPort：収集サーバーポート
	- logSource：ログソース
	- logType：ログタイプ
  - clientHost：Hostを取得する方式
		- true：ioctl方式を使用して、clientからhostを取得する
		- false：serverから渡された値でhostを取得する
	- asyncStart：SendThreadをLockさせた状態で開始。Lock状態でログが発生した場合、サーバーに転送せずキューに保存して待機。Crashが発生したり、StartSendThread関数を実行する場合はLock解除
- initialize()戻り値
	- LOGNCRASH_LOG_OK：0、初期化成功
	- LOGNCRASH_LOG_ERROR：-1、内部エラーコード
	- LOGNCRASH_LOG_ERROR_APPKEY：-2、アプリケーションキーが無効な場合
	- LOGNCRASH_LOG_ERROR_VERSION：-3、バージョンが無効な場合
	- LOGNCRASH_LOG_ERROR_ADDRESS：-4、収集サーバーアドレスが無効な場合
	- LOGNCRASH_LOG_ERROR_PORT：-5、収集サーバーポートが無効な場合

### SendThread Lock状態解除

```
  	void StartSendThread();
```

  - SendThreadを転送可能な状態に変更

### ログ転送

```
bool sendLog(
    const LogNCrashLogLevel logLevel,
    const char* message,
    const char* errorCode = NULL,
    const char* location = NULL);
```

- 指定されたlogLevelでログを転送します。
- パラメータ
	- logLevel：転送するlogLevel。 setLogLevel()で指定されたlogLevelより大きいlogLevelは転送されません。
	- message：転送するメッセージ
	- errorCode：エラーコード。NULLや""を使うと転送されません。
	- location：エラー位置。NULLや""を使うと転送されません。
- 戻り値
	- 成功時はtrue
	- logLevelが大きいか、messageが空の場合はfalse
- 参考
	- setLogLevel(), getLogLevel();

```
bool debug(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool info(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool warn(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool error(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool fatal(const char* message, const char* errorCode = NULL, const char* location = NULL);
```

- 決められたDEBUG、INFO、WARN、ERROR、FATALログを転送します。
- logLevelが固定されている点以外は、sendLog()と同じです。
- 戻り値
	- 成功時はtrue
	- logLevelより大きいか、messageが空の場合はfalse

### ログレベルを指定する

```
typedef enum {
    LOGNCRASH_FATAL   = 0,
    LOGNCRASH_ERROR   = 3,
    LOGNCRASH_WARN    = 4,
    LOGNCRASH_INFO    = 5,
    LOGNCRASH_DEBUG   = 7,
    LOGNCRASH_TRACE   = LOGNCRASH_DEBUG,
} LogNCrashLogLevel;

LogNCrashLogLevel getLogLevel();

void setLogLevel(const LogNCrashLogLevel logLevel);
```

- ToastLog instanceのlogLevelの取得や指定を行います。
- ToastLogデフォルト値はLOGNCRASH_INFOです。したがってdebug()関数を使用するには、setLogLevel(LOGNCRASH_DEBUG)に設定する必要があります。

### カスタムキーを指定する

```
bool addCustomKey(const char* key, const char* value);

void removeCustomKey(const char* key);

void clearCustomKeys();
```

- カスタムキーを追加、削除、全部削除機能を提供します。
- カスタムキーは、大文字/小文字で始まり、大文字/小文字、数字、'-'、'_'のみ使用できます。( [A-Za-z][A-Za-z0-9-_]* )
- カスタムキーは最大64文字です。
- カスタムキーには、大文字/小文字に関わらず次の名前は使用できません。
	- projectname, projectversion, host, body, logsource, logtype
	- logType, sendTime, logLevel, userId, platform
	- dmpdata, dmpreport
- addCustomKey()戻り値
	- 成功時はtrue
	- key形式が合っていない場合、追加失敗時はfalse

### ホストタイプを指定する

```
bool setHostMode(int mode);
```

- modeが0の場合：Private IPを取得してhostフィールドを埋めます。Private IPを取得するのに失敗した場合、Public IPでhostフィールドを埋めます。
- modeが1の場合：Public IPでhostフィールドを埋めます。

### クラッシュを処理する

```
typedef enum {
    LOGNCRASH_LANG_DEFAULT     = 0,
    LOGNCRASH_LANG_ENGLISH     = 1,
    LOGNCRASH_LANG_KOREAN      = 2,
    LOGNCRASH_LANG_JAPANESE    = 3,
    LOGNCRASH_LANG_CHINESE     = 4,
    LOGNCRASH_LANG_CHINESE_TRADITIONAL = 5,
    LOGNCRASH_LANG_CHINESE_SIMPLIFIED  = LOGNCRASH_LANG_CHINESE,
} LogNCrashLangType;

#ifdef WIN32
typedef void (__cdecl *LogNCrashCallbackType)(void *data);
#else   //#ifdef WIN32
typedef void (*LogNCrashCallbackType)(void *data);
#endif  //#ifdef WIN32

bool openCrashCatcher(const bool bBackground = false, const LogNCrashLangType langType = LOGNCRASH_LANG_DEFAULT);
void closeCrashCatcher();

void setCrashCallback(const LogNCrashCallbackType cb, void* cbData = NULL);
```

- クラッシュ処理の開始や終了を行います。
- openCrashCatcherパラメータ
	- bBackground：クラッシュレポート動作方式設定をします。
	- langType：クラッシュレポートGUI言語を設定します。
- openCrashCatcher戻り値
	- 設定成功時はtrue
	- 設定失敗時はfalse
	- AndroidNDK SDKは、端末の/sdcardディレクトリを使用します。端末に上記のディレクトリがない場合、正常に動作しないことがあります。

### 重複除去モード設定
  - 2.4.0以上のSDKから一般ログに重複除去ロジックが適用されました。
  - 重複ログ機能が有効になっている場合、bodyとlogLevelの内容が同じログが発生した時、転送しません。

```
public static void setDuplicate(bool enable)
```
  - true：(Default値)重複除去ロジックが有効

  - false：重複除去ロジックが無効

### その他設定

```
const char* getUserId();

void setUserId(const char* userId);
```

- ユーザーIDの取得や指定を行います。

```
void enableHostField();

void disableHostField();
```

- Hostフィールドの有効化、無効化を行います。

```
void enablePlatformField();

void disablePlatformField();
```

- Platformフィールドの有効化、無効化を行います。
