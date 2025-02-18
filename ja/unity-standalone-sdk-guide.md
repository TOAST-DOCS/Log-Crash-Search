## Analytics > Log & Crash Search > Unity Standalone SDK使用ガイド


Log & Crash Unity SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。  
Log & Crash Unity SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchで、転送されたログの照会および検索が可能です。

## サポート環境

- 共通
	\- Unity3D v4.0以上
- Android
	\- Andorid SDK 2.3.3 API以上

## ダウンロード

[TOAST Document](http://docs.toast.com/ko/Download/)でUnity SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Unity SDK]
```

## インストール

* ダウンロードしたtoast-logncrash-android-unity-sdk.unitypackageをダブルクリックしてImportします。


### サンプル説明

サンプルを実行するには、Assets > LogNCrash > Sample > SampleSceneをダブルクリックします。
サンプルには初期化、ログ転送、エラー発生についての例が記述されています。

## 使用例

1. LogNCrashSettingsによる初期化

Unityメニューバーから、LogNCrash> Edit Settingsを選択してLogNCrashSettingsを作成します。 LogNCrashSettingsはAssetDatabaseで、ユーザーアプリケーションキーとSDKの動作を定義します。

- Appkey：ユーザーアプリケーションキー
- URL：コレクターアドレス、https://api-logncrash.nhncloudservice.comを使用します。
- Version：ログバージョン
- Send Warning：Unityで発生したWarningログを収集するかどうか
- Send Error：Unityで発生したErrorログを収集するかどうか
- Send Debug Warning：UnityでユーザーがDebugオブジェクトを利用して発生させたWarningログを収集するかどうか
- Send Debug Error：UnityでユーザーがDebugオブジェクトを利用して発生させたErrorログを収集するかどうか

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

## 詳細API

### カスタムフィールドの指定

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
		- DeviceID
        - @logType
	- custom filedの値がNULLまたは空の場合、SDKは該当フィールドをserverに転送しません。

### 基本設定管理

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

### LEVELフィルタ
- Unity SDKでは、Default設定でFATALレベルのログのみを転送します。 Error、Warningレベルのログには、変数値(時間、パス、進行度など)の挿入により多くのログが発生することがあります。
	- Send Error：システムで発生したERRORレベルのログを転送します。
	- Send Warning：システムで発生したWARNレベルのログを転送します。
	- Send Debug Error：ユーザーが発生させたERRORレベルのログを転送します。
	- Send Debug Warning：ユーザーが発生させたWARNレベルのログを転送します。

### API使用例

	- html > index.htmlを参照してください。

### IP Address収集設定

```
public static void SetEnableHost:(bool flag)
```

- trueの場合、ip addressを取得し、hostフィールドに保存します。
- falseの場合、hostフィールドに"-"を保存します。

### ログ転送

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

### クラッシュコールバック 

```
public void Crash_Send_Complete_Callback(string message) {
	Debug.Log("Crash_Send_Complete_Callback : " + message);
}

void Start() {
	LogNCrashCallBack.ExceptionDelegate += Crash_Send_Complete_Callback;
}

```

- ExceptionDelegateは、Unity Csharpで発生したCrashをサーバーに転送した後に呼び出されるコールバックです。<br>
ネイティブCrashの場合は呼び出されません。

### ユーザーID設定

```
public static void SetUserId(string userID)
public static string GetUserID()
```
- ユーザーごとの統計資料を取得するには、設定する必要があります。
- Parameter
	- userID: string
		- [in]各ユーザーを区分するuser id

### 重複除去モード設定

2.4.0以上のSDKから、一般ログに重複除去ロジックが適用されました。初期化時に重複除去ロジックが有効になります。

一般ログの場合、bodyとlogLevelが同じログが発生した場合、転送しません。

クラッシュログの場合、stackTraceとconditionの値が同じログが発生した場合、転送しません。

不要な場合は、初期化後に下記の関数を使って機能を無効化できます。

```
public static void SetDeduplicate(bool flag)
```

true：(Default値)重複除去ロジックを有効にする<br>
false：重複除去ロジックを無効にする

## Buildする

1.File->Build Settingsをクリックします。

![](http://static.toastoven.net/prod_logncrash/csharp_5.png)

- PC, Mac & Linux Standalone Platformを選択した後、Player Settingsをクリックします。

![](http://static.toastoven.net/prod_logncrash/csharp_10.png)

2.Build settingsでBuild And Runをクリックします。

## 外部CrashHandlerを使用する

- 既存SDKでは初期化段階でlogMessageReceivedなどを使用してUnityのCrashHandlerをLogNCrash専用Callback関数に登録して使用しました。
- 外部CrashHandlerのように使用する場合があり、一緒に適用できるように構造を修正しました。(MultihandlerSample参照)

### 適用方法

- LogNCrash.SetCrashHandler関数にfalseをパラメータとして渡し、自動的にCrashHandlerが登録されることを防ぎます。
- Initialize関数の前に設定されている必要があります。

```
LogNCrash.SetCrashHanlder (false);
LogNCrash.Initialize ();
```

- 以降、LogNCrash.unity3dHandleException関数を使用して、CrashHandlerのパラメータをLogNCrashオブジェクトに渡します。

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

### AssetDataBaseを活用したビルド環境分岐

- メニューバーのLogNCrash > Edit Settingsをクリックすると、簡単なデータを保存できるAssetDataBaseが作成されます。
- BuildPipeline.BuildPlayerでBuildを行う場合、LogNCrashSettings.Setter_BuildTypeとLogNCrashSettings.Getter_BuildTypeを活用してビルド環境を分岐します。

```
using UnityEditor;
using UnityEngine;
using Toast.LogNCrash.Implementation;

public class lncAndroidBuildPipeline: MonoBehaviour
{
	[MenuItem("Build/Build Android (Alpha)")]
	public static void AndroidAlphaBuildScript()
	{
		BuildPlayerOptions buildPlayerOptions = new BuildPlayerOptions();
		buildPlayerOptions.scenes = new[] {"Assets/Toast/Sample/Scene/Command/commandScene.unity"};
		buildPlayerOptions.locationPathName = "AndroidBuild.apk";
		buildPlayerOptions.target = BuildTarget.Android;
		buildPlayerOptions.options = BuildOptions.AutoRunPlayer;

		LogNCrashSettings.Setter_BuildType = LogNCrashSettings.BuildType.alpha;

		BuildPipeline.BuildPlayer(buildPlayerOptions);
	}

	[MenuItem("Build/Build Android (Real)")]
	public static void AndroidRealBuildScript()
	{
		BuildPlayerOptions buildPlayerOptions = new BuildPlayerOptions();
		buildPlayerOptions.scenes = new[] {"Assets/Toast/Sample/Scene/Command/commandScene.unity"};
		buildPlayerOptions.locationPathName = "AndroidBuild.apk";
		buildPlayerOptions.target = BuildTarget.Android;
		buildPlayerOptions.options = BuildOptions.AutoRunPlayer;

		LogNCrashSettings.Setter_BuildType = LogNCrashSettings.BuildType.real;

		BuildPipeline.BuildPlayer(buildPlayerOptions);
	}
}
```

- コマンドに応じて、AssetDataBaseに保存された値でLogNCrashの動作を決定します。

```
using Toast.LogNCrash.Implementation;

void Start () {
		if (LogNCrashSettings.Getter_BuildType == LogNCrashSettings.BuildType.real) {
			SetReal ();
		} else if (LogNCrashSettings.Getter_BuildType == LogNCrashSettings.BuildType.alpha) {
			SetAlpha ();
		} else {
			UnityEngine.Debug.Log ("Default Type");
		}
}
```

- build typeは全部で5個で構成されています。

```
public enum BuildType{
		real, alpha, beta, development, test
	}
```
