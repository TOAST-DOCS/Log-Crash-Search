<!-- pre-align:aligned sig=91e5466c6a88 -->

<a id="analytics-log-crash-search-linux-sdk-guide"></a>
## Analytics > Log & Crash Search > Linux SDK Guide { #analytics-log-crash-search-linux-sdk-guide }

Log & Crash C++ Linux SDKは、Log & Crash Searchの収集サーバーにログを送信する機能を提供します。
Log & Crash C++ Linux SDKの特長は次のとおりです。

- ログを収集サーバーに送信します。
- C++ Linux SDKはクラッシュログを収集しません。
- Log & Crash Searchで送信されたログの照会および検索が可能です。
- マルチスレッド環境で動作します。

<a id="section-1"></a>
## サポート環境 { #section-1 }

- Linux, glibc >= 2.0.0
- 32bit/64bit
- openssl >= 1.0.1e
- libcurl >= 7.19.7

<a id="section-2"></a>
## ダウンロード { #section-2 }

Toast CloudでC++ Linux SDKを入手できます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Linux SDK] 클릭
```

<a id="section-3"></a>
## インストール { #section-3 }

<a id="section-3-1"></a>
### 構成 { #section-3-1 }

C++ Linux SDKは次のように構成されています。

```
...
docs/					; C++ Linux SDK 문서
include/toast/logncrash.h	; C++ 해더 파일
linux-sdk/lib32/			; C++ Linux 32bit 라이브러리
linux-sdk/lib64/			; C++ Linux 64bit 라이브러리
linux-sdk-sample/			; C++ Linux SDK 샘플
...
```

<a id="sdk"></a>
### SDK サンプル { #sdk }

同梱のlinux-sdk-sample/について説明します。

1. linux-sdk-sample/ 디렉토리로 이동합니다.
2. sample.cppを開き、発行されたアプリキーに修正します。
3. makeを実行して、sample-x86、sample-x86_64実行ファイルを生成します。
	- 個別にビルドするには、make sample-x86、make sample-x86_64コマンドを使用してください。
	- x86、x86_64のビルド環境については、使用しているLinuxディストリビューションのドキュメントを参照してください。
4. ビルドされた実行ファイルを実行します。

<a id="section-4"></a>
## 使用例 { #section-4 }

1. include/toast/をインクルードパスに追加します。
2. ターゲットに応じて、lib32/liblogncrash_linux.aまたはlib64/liblogncrash_linux.aをリンクします。
	- liblogncrash_linux.aは内部的にpthreadを使用しています。そのため、-lpthread link optionが必要です。
3. toast/logncrash.hをインクルードし、ToastLog classを使用します。

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

<a id="api-list"></a>
## API List { #api-list }

toast::logncrash::ToastLog classで提供する機能について説明します。

<a id="toastlog"></a>
### ToastLogインスタンスの割り当て/解放 { #toastlog }

```
toast::logncrash::ToastLog* GetToastLog();

void DestroyToastLog();
```

- ToastLog instanceを割り当て、解放します。
- シングルトン方式で1つのインスタンスのみが返されます。
- 返されたToastLog instanceに対してdeleteを行わないでください。削除するには、必ずDestroyToastLog()を呼び出す必要があります。

<a id="api-list-1"></a>
### 初期化/解除 { #api-list-1 }

```
#define LOGNCRASH_VERSION         "1.0.0"
#define LOGNCRASH_COLLECTOR_ADDR  "https://api-logncrash.nhncloudservice.com"
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

- ToastLogを初期化および解放します。
- ToastLog機能が正常に動作するには、必ずinitialize()を呼び出す必要があります。
- パラメータ
	- appKey: アプリキー
	- version: アプリバージョン
	- collectorAddr: 収集サーバーアドレス
		- Log & Crashの収集サーバー: https://api-logncrash.nhncloudservice.com
	- collectorPort: 収集サーバーポート
	- logSource: ログソース
	- logType: ログタイプ
- initialize()の戻り値
	- LOGNCRASH_LOG_OK: 0、初期化成功
	- LOGNCRASH_LOG_ERROR: -1, 内部エラーコード
	- LOGNCRASH_LOG_ERROR_APPKEY: -2、アプリケーションキーが無効な場合
	- LOGNCRASH_LOG_ERROR_VERSION: -3, バージョンが正しくない場合
	- LOGNCRASH_LOG_ERROR_ADDRESS: -4、収集サーバーのアドレスが誤っている場合
	- LOGNCRASH_LOG_ERROR_PORT: -5、収集サーバーのポートが正しくない場合

<a id="api-list-2"></a>
### ログ送信 { #api-list-2 }

```
bool sendLog(
    const LogNCrashLogLevel logLevel,
    const char* message,
    const char* errorCode = NULL,
    const char* location = NULL);
```

- 指定されたlogLevelでログを送信します。
- パラメータ
	- logLevel: 送信するlogLevel。setLogLevel()で指定したlogLevelより大きいlogLevelは送信されません。
	- message: 送信するメッセージ
	- errorCode: エラーコード。NULLまたは""を指定した場合、送信されません。
	- location: エラーの位置。NULLまたは""を指定すると送信されません。
- 戻り値
	- 成功した場合はtrue
	- logLevelが大きい場合、またはmessageが空の場合はfalse
- 参考
	- setLogLevel(), getLogLevel();

```
bool debug(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool info(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool warn(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool error(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool fatal(const char* message, const char* errorCode = NULL, const char* location = NULL);
```

- DEBUG、INFO、WARN、ERROR、FATALのログを送信します。
- logLevelが固定されている点以外は、sendLog()と同じです。
- 戻り値
	- 成功した場合はtrue
	- logLevelが大きい場合、またはmessageが空の場合はfalse

<a id="api-list-3"></a>
### ログレベルの指定 { #api-list-3 }

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

- ToastLog instanceのlogLevelを取得または設定します。
- ToastLogのデフォルト値はLOGNCRASH_INFOです。したがって、debug()関数を使用するには、setLogLevel(LOGNCRASH_DEBUG)で設定する必要があります。

<a id="api-list-4"></a>
### カスタムキーを指定する { #api-list-4 }

```
bool addCustomKey(const char* key, const char* value);

void removeCustomKey(const char* key);

void clearCustomKeys();
```

- カスタムキーの追加、削除、全削除機能を提供します。
- カスタムキーは大文字または小文字で始まり、大文字、小文字、数字、「-」、「\_」のみ使用できます。( [A-Za-z][A-Za-z0-9-_]* )
- カスタムキーは最大64文字です。
- カスタムキーには大文字・小文字に関係なく、次の名前は使用できません。
	- projectname, projectversion, host, body, logsource, logtype
	- logType, sendTime, logLevel, userId, platform
	- dmpdata, dmpreport
- addCustomKey()の戻り値
	- 成功した場合はtrue
	- keyの形式が合わない場合、追加失敗時はfalse

<a id="api-list-5"></a>
### クラッシュ処理 { #api-list-5 }

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

- Linux SDKはクラッシュログの収集を行いません。
- openCrashCatcherの戻り値
	- 常にfalse

<a id="api-list-6"></a>
### その他の設定 { #api-list-6 }

```
const char* getUserId();

void setUserId(const char* userId);
```

- ユーザーIDを取得または設定します。

```
void enableHostField();

void disableHostField();
```

- Host フィールドを有効化または無効化します。

```
void enablePlatformField();

void disablePlatformField();
```

- Platform フィールドを有効または無効にします。