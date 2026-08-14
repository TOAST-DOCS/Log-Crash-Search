<!-- pre-align:aligned sig=45b1bfc767db -->

﻿## Analytics > Log & Crash Search > Unity WebGL SDK使用ガイド

Log & Crash Unity SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。  
Log & Crash Unity SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchで、転送されたログの照会および検索が可能です。

<a id="analytics-log-crash-search-unity-webgl-sdk-user-guide"></a>
## Analytics > Log & Crash Search > Unity WebGL SDK 使用ガイド { #analytics-log-crash-search-unity-webgl-sdk-user-guide }

<!-- TODO: translate body -->

<a id="supporting-environment"></a>
## サポート環境 { #supporting-environment }

- 共通
	\- Unity3D v4.0以上

<a id="download"></a>
## ダウンロード { #download }

[TOAST Document](http://docs.toast.com/ko/Download/)でUnity SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Unity SDK]
```

<a id="install"></a>
## インストール { #install }

 - ダウンロードしたtoast-logncrash-unity-sdk.unitypackageをダブルクリックしてImportします。

<a id="sample-description"></a>
### サンプル説明 { #sample-description }

サンプルを実行するには、Assets > LogNCrash > Sample > SampleSceneをダブルクリックします。
サンプルには初期化、ログ転送、エラー発生についての例が記述されています。

<a id="example"></a>
## 使用例 { #example }

1. LogNCrashSettingsによる初期化

UnityメニューバーからLogNCrash> Edit Settingsを選択して、LogNCrashSettingsを作成します。LogNCrashSettingsはAssetDatabaseで、ユーザーアプリケーションキーとSDKの動作を定義します。

- Appkey：ユーザーアプリケーションキー
- URL：コレクターアドレス、https://api-logncrash.nhncloudservice.comを使用します。
- Version：ログバージョン
- Send Warning：Unityで発生したWarningログを収集するかどうか
- Send Error：Unityで発生したErrorログを収集するかどうか
- Send Debug Warning：UnityでユーザーがDebugオブジェクトを利用して発生させたWarningログを収集するかどうか
- Send Debug Error：UnityでユーザーがDebugオブジェクトを利用して発生させたErrorログを収集するかどうか
- PLCrashreporter Enable：PLCrashrepoterは、Native領域で発生したCrashを探知するために追加されたライブラリです。Native Crash探知を行いたい場合にのみ使用します。

LogNCrashSettingsに情報を入力してLogNCrashオブジェクトのパラメータがないInitialize関数を呼び出した場合、LogNCrashSettingsで情報を取得して初期化を試みます。

```
using Toast.LogNCrash;
namespace Toast.LogNCrash
{
	public class SampleScript : MonoBehaviour
	{
		void Start ()
		{
			LogNCrash.Initialize ();
		}
	}
}
```

2. Scriptによる初期化
LogNCrash.Initializeにパラメータを入力して初期化を試みます。パラメータはサーバーアドレス、アプリケーションキー、バージョン、ポート、Send Thread Lockを実行するかどうかについての情報を渡します。

```
using Toast.LogNCrash;
namespace Toast.LogNCrash
{
	public class SampleScript : MonoBehaviour
	{
		void Start ()
		{
			LogNCrash.Initialize ("https://api-logncrash.nhncloudservice.com", "appkey", "1.0.0", 443,  true);
			LogNCrash.StartSendThread ();
		}
	}
}
```

- Appkey：ユーザーアプリケーションキー
- URL：コレクターアドレス、http、httpsのコクレクター情報を設定
- Version：ログバージョン
- Port：443
- SendThreadLock：trueの場合、発生したログはStartSendThreadが呼び出されるまでサーバーに転送せず、キューに保存します。ただしNative Crashが発生した場合、ThreadLockを解除してログを転送します。

<a id="api-details"></a>
## 詳細API { #api-details }

<a id="specify-custom-fields"></a>
### カスタムフィールドの指定 { #specify-custom-fields }

```
public static void AddCustomField(string key, string val)
public static void RemoveCustomField(string key)
public static void RemoveAllCustomFields()
```

- Parameters
	- key: string
		- [in] custom fieldのkey、custom keyは“A～Z、a～z、0～9、- \_”の文字を含み、アルファベットまたは数字で始まる必要があります。
	- value: string
		- [in] custom fieldの値
- Note
	- 次のkeywordは、SDKで使用中のため使用できません。
		- projectName
        - projectVersion
        - host
        - body
        - logLevel
        - userID
        - Platform
        - DmpData
        - Unity3D
        - Locale
        - CountryCode
        - SessionID
        - ExceptionType
        - NeloSDK
        - NetworkType
        - DeviceModel
        - @logType
	- custom filedの値がNULLまたは空の場合、SDKsは該当フィールドをserverに転送しません。

<a id="manage-default-setting"></a>
### 基本設定管理 { #manage-default-setting }

```
public static void SetLogSource(string value)
public static string GetLogSource()
```

- ログソースの取得や新規指定を行います。

```
public static void SetLogType(string value)
public static string GetLogType()
```

- ログタイプの取得や新規指定を行います。

<a id="filter-levels"></a>
### LEVELフィルタ { #filter-levels }

- Unity SDKでは、Default設定でFATALレベルのログだけを転送します。 Error、Warningレベルのログには変数値(時間、パス、進行度など)の挿入により多くのログが発生することがあります。
	- Send Error：システムで発生したERRORレベルのログを転送します。
	- Send Warning：システムで発生したWARNレベルのログを転送します。
	- Send Debug Error：ユーザーが発生させたERRORレベルのログを転送します。
	- Send Debug Warning：ユーザーが発生させたWARNレベルのログを転送します。

<a id="example-of-api-use"></a>
### API使用例 { #example-of-api-use }

- html > index.htmlを参照してください。

<a id="collect-ip-address"></a>
### IP Address収集設定 { #collect-ip-address }

```
public static void SetEnableHost:(bool flag)
```

- trueの場合、ip addressを取得し、hostフィールドに保存します。
- falseの場合、hostフィールドに"-"を保存します。

<a id="send-logs"></a>
### ログ転送 { #send-logs }

```
//send info log message
public static void Info(string strMsg)

//send debug log message
public static void Debug(string strMsg)

//send warn log message
public static void Warn(string strMsg)

//send fatal log message
public static void Fatal(string strMsg)

//send error log message
public static void Error(string strMsg)
```

- Parameters
	- strMsg: string
		- [in]転送するlogメッセージ

<a id="crash-callbacks"></a>
### クラッシュコールバック { #crash-callbacks }

```
public void Crash_Send_Complete_Callback(string message) {
	Debug.Log("Crash_Send_Complete_Callback : " + message);
}

void Start() {
	LogNCrashCallBack.ExceptionDelegate += Crash_Send_Complete_Callback;
}

```

ExceptionDelegateは、Unity Csharpで発生したCrashをサーバーに転送した後に呼び出されるコールバックです。
ネイティブCrashの場合は呼び出されません。

<a id="set-user-ids"></a>
### ユーザーID設定 { #set-user-ids }

```
public static void SetUserId(string userID)
public static string GetUserID()
```

- ユーザーごとの統計資料を取得するには設定する必要があります。
- Parameter
	- userID: string
		- [in]各ユーザーを区分するuser id。

<a id="remove-duplicates"></a>
### 重複除去モード設定 { #remove-duplicates }
一般ログの場合、bodyとlogLevelが同じログが発生した場合、転送しません。
クラッシュログの場合、stackTraceとconditionの値が同じログが発生した場合、転送しません。
不要な場合は、初期化後に下記の関数を使って機能を無効化できます。

```
	public static void SetDeduplicate(bool flag)
```

 - true：(Default値)重複除去ロジックを有効にする
 - false：重複除去ロジックを無効にする

<a id="webgl-api"></a>
## WebGL API { #webgl-api }

<a id="unsupported-api"></a>
### サポートしないAPI { #unsupported-api }

- WebGL SDKでは、asm.jsがtry-catchをサポートしないため、Handled Exceptionをサポートしません。

<a id="webgl-only-api"></a>
### WebGL専用API { #webgl-only-api }

- 重複除去ログキューの最大サイズを指定します。

```
LogNCrash.setDuplciateQueueSize (100);
```

- BulkMessageサイズの最大サイズを指定します。

```
LogNCrash.setMaximumBulkMessageSize (1024 * 512); // 512 KB
```

- ログの転送に失敗した時、保存できる最大ファイル数を指定します。

```
LogNCrash.setMaximumFileCount (100);
```

- 転送ログキューの最大サイズを指定します。

```
LogNCrash.setMaximumSendCount (100);
```

<a id="settings-to-collect-crashes"></a>
### Crashを収集するための設定 { #settings-to-collect-crashes }

- WebGL SDKでCrashを収集するには、PlayerSettings > Publishing Settings > Enable ExceptionオプションがFullに設定されている必要があります。

<a id="caution"></a>
### 注意事項 { #caution }

- Log & Crashは、メモリを転送するプロセスで、最大2000個のSendQueueにログを保存します。
- Log & Crashは、ログの重複を除去するために、最大500個のDuplicateログを保存します。
- Log & Crashは、転送に失敗したログを再転送するために、500個の失敗ログを保存します。
- したがって、充分なメモリが必要です。
- サーバーのレスポンス速度を測定する場合、対象サーバーにCross-Domainが設定されている必要があります。

<a id="build"></a>
## WebGL Buildする { #build }

1.File->Build Settingsをクリック。

![](http://static.toastoven.net/prod_logncrash/gl_5.png)

 - WebGL Platformを選択した後、Player Settingsをクリックします。

![](http://static.toastoven.net/prod_logncrash/gl_11.png)

2.Build settingsでBuild And Runをクリックします。

<a id="use-external-crashhandler"></a>
## 外部CrashHandlerを使用する { #use-external-crashhandler }

- 既存SDKでは初期化段階でlogMessageReceivedなどを使用してUnityのCrashHandlerをLogNCrash専用Callback関数に登録して使用しました。
- 外部CrashHandlerのように使用する場合があり、一緒に適用できるように構造を修正しました。(MultihandlerSample参照)

<a id="applications"></a>
### 適用方法 { #applications }

- LogNCrash.SetCrashHandler関数にfalseをパラメータとして渡し、自動的にCrashHandlerが登録されることを防ぎます。
- Initialize関数の前に設定されている必要があります。

```
LogNCrash.SetCrashHanlder (false);
LogNCrash.Initialize ();
```

- 以降、LogNCrash.unity3dHandleException関数を使用してCrashHandlerのパラメータをLogNCrashオブジェクトに渡します。

```
void OnEnable()
{
		Application.logMessageReceived += HandleLog;
}

void HandleLog(string logString, string stackTrace, LogType type)
{
		if (LogNCrash.isInitialized) {
			LogNCrash.unity3dHandleException (logString, stackTrace, type);
		}
}
```
