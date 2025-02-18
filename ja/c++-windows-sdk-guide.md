## Analytics > Log & Crash Search > C++ Windows SDK使用ガイド

> [Deprecated]
> Log & Crash C++ Windows SDKバージョンは今後はサポートされません。
> [TOAST SDK](http://docs.toast.com/ko/TOAST/ko/toast-sdk/overview/)をご利用ください。

Log & Crash C++ Windows SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。  
Log & Crash C++ Windows SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchで転送されたログを照会および検索できます。
- マルチスレッド環境で動作します。

## サポート環境

- Windows 2000、Windows Vista、Windows XP、Windows 2003、Windows 2008、Windows 7、Windows 8
- 32bit/64bit

## ダウンロード

Toast CloudでC++ Windows SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Windows SDK]をクリック
```

## インストール

### 構成

C++ Windows SDKは、次のように構成されています。

```
docs/						; C++ Windows SDK文書
include/					; C++ Windows SDK使用例
include/toast/logncrash.h	; C++ ヘッダファイル
windows-sdk/lib32/			; C++ Windows 32bitライブラリ
windows-sdk/lib64/			; C++ Windows 64bitライブラリ
windows-sdk-sample/			; VS 2010用サンプルプロジェクト
```

### SDKサンプル

一緒に提供されるwindows-sdk-sample/について説明します。

1. Microsoft Visual Studio 2010を実行してwindows-sdk-sample/Sample.slnを開きます。
2. Sample.cppを開き、発行されたアプリケーションキーに修正します。
3. 32bit/64bitとDebug/Releaseに応じて、アプリ実行に必要な\*.dllを実行ファイルディレクトリにコピーします。
4. 実行します。

## 使用例


1. include/toast/をインクルードパスに入れます。
2. 32bit/64bitとDebug/Releaseに応じて、次のようにImportライブラリを指定します。
	- 32bit, Debug   ; lib32/liblogncrashD.lib
	- 32bit, Release ; lib32/liblogncrash.lib
	- 64bit, Debug   ; lib64/liblogncrashD.lib
	- 64bit, Release ; lib64/liblogncrash.lib
3. アプリを実行するには、\*.dllを実行ファイルディレクトリにコピーします。
4. toast/logncrash.hをインクルードして、ToastLog classを使用します。

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

## API List

toast::logncrash::ToastLog classで提供する機能を説明します。

### ToastLogインスタンスの割り当て/解除

```
toast::logncrash::ToastLog* GetToastLog();

void DestroyToastLog();
```

- ToastLog instanceを割り当て、解除します。
- Singleton方式で1つのインスタンスのみ返されます。
- 返されたToastLog instanceに対してdeleteをしてはいけません。除去するには、DestroyToastLog()を呼び出す必要があります。

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
		- Log & Crash収集サーバー： api-logncrash.nhncloudservice.com
	- collectorPort：収集サーバーポート
	- logSource：ログソース
	- logType：ログタイプ
- initialize()戻り値
	- LOGNCRASH_LOG_OK： 0、初期化成功
	- LOGNCRASH_LOG_ERROR： -1、内部エラーコード
	- LOGNCRASH_LOG_ERROR_APPKEY： -2、アプリケーションキーが無効な場合
	- LOGNCRASH_LOG_ERROR_VERSION： -3、バージョンが無効な場合
	- LOGNCRASH_LOG_ERROR_ADDRESS： -4、収集サーバーアドレスが無効な場合
	- LOGNCRASH_LOG_ERROR_PORT： -5、収集サーバーポートが無効な場合

### ログを転送する

```
bool sendLog(
    const LogNCrashLogLevel logLevel,
    const char* message,
    const char* errorCode = NULL,
    const char* location = NULL);
```

- 指定されたlogLevelでログを転送します。
- パラメータ
	- logLevel：転送するlogLevel。 setLogLevel()で指定されたlogLevelより大きいlogLevelは、転送してはいけません。
	- message：転送するメッセージ
	- errorCode：エラーコード。 NULLや""を使うと転送されません。
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
	- logLevelが大きい場合、messageが空の場合はfalse

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
- ToastLogデフォルト値は、LOGNCRASH_INFOです。したがってdebug()関数を使用するには、setLogLevel(LOGNCRASH_DEBUG)で設定する必要があります。

### カスタムキーを指定する

```
bool addCustomKey(const char* key, const char* value);

void removeCustomKey(const char* key);

void clearCustomKeys();
```

- カスタムキーを追加、削除、全部削除機能を提供します。
- カスタムキーは、大文字/小文字で始まり、大文字/小文字、数字、'-'、'_'のみ使用できます。( [A-Za-z][A-Za-z0-9-_]* )
- カスタムキーは最大64文字です。
- カスタムキーには、大文字/小文字に関わらず、次の名前は使用できません。
	- projectname, projectversion, host, body, logsource, logtype
	- logType, sendTime, logLevel, userId, platform
	- dmpdata, dmpreport
- addCustomKey()の戻り値
	- 成功時はtrue
	- key形式が合っていなければ、追加失敗時にfalse

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

bool openCrashCatcher();
void closeCrashCatcher();

void setCrashCallback(const LogNCrashCallbackType cb, void* cbData = NULL);
```

- クラッシュ処理の開始、終了を行います。

### 重複除去モード設定

- 重複ログ機能が有効になっている場合、bodyとLogLevelの内容が同じログが発生しても、転送しません。

```
public static void setDuplicate(bool enable)
```
- true：重複除去ロジックを有効にする(Default値)
- false：重複除去ロジックを無効にする

### その他の設定

```
const char* getUserId();

void setUserId(const char* userId);
```

- ユーザーIDの取得や指定を行います。

## シンボルファイル作成ガイド

### 概要
- Log & Crash Windows SDKで発生したCrashを解析するには、シンボルファイルを作成してWebコンソールにアップロードする必要があります。

### 必要ツール
- VSに合ったdump_symsを使用します( VC_1500 = 2008, VC_1600 = 2010 )
- [VS 2008以下のダウンロード](https://github.com/zpao/v8monkey/blob/master/toolkit/crashreporter/tools/win32/dump_syms_vc1500.exe)
- [VS 2010以上のダウンロード](http://hg.mozilla.org/mozilla-central/file/tip/toolkit/crashreporter/tools/win32)
- [minidump_stackwalk.exe](http://hg.mozilla.org/build/tools/raw-file/755e58ebc9d4/breakpad/win32/minidump_stackwalk.exe)

### シンボルファイルの作成
- windows crash dumpsは .pdbファイルを .symシンボルに変換させて、デバッグ情報を取得できます。
- .pdbファイルを .symファイルに変換させる：
    - .pdbファイルを作成します。 (プロジェクトビルド時に作成)

    - dump_syms.exeをダウンロードします。

    - 下記の例のようにdump_symsを実行して、シンボルファイルを作成します。
    (エラーが発生しなければ、シンボルファイルの作成に成功したということです。)
        - CoCreateInstance CLSID_DiaSource failed (msdia*.dll unregistered?)エラーが発生したら、c:\Program Files\Common Files\Microsoft Shared\VC\.に該当dllをコピーします。
        - regsvr32コマンドでdllを登録します。
        ```
        regsvr32 c:\Program Files\Common Files\Microsoft Shared\VC\msdia80.dll.
        ```

        - 0x80004005が発生する場合は、管理者権限で再試行します。
        ```
        'dump_syms {.pdbファイル} > {出力ファイル}'
        'dump_syms Sample.pdb > Sample.sym'
        ```

        - 作成したシンボルファイルをWebコンソールにアップロードします。
