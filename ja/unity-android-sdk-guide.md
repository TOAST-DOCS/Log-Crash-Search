## Analytics > Log & Crash Search > Unity Android SDK使用ガイド

> [Deprecated]
> Log & Crash Unity Android SDKバージョンは、今後サポートされません。
> [TOAST SDK](http://docs.toast.com/ko/TOAST/ko/toast-sdk/overview/)を利用してください。

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

UnityメニューバーからLogNCrash> Edit Settingsを選択して、LogNCrashSettingsを作成します。 LogNCrashSettingsはAssetDatabaseで、ユーザーアプリケーションキーとSDKの動作を定義します。

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
		- [in] custom fieldのkey、custom keyは“A～Z、a～z、0～9、-\_”の文字を含み、アルファベットか数字で始まる必要があります。
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
- Unity SDKでは、Default設定でFATALレベルのログのみを転送します。Error、Warningレベルのログには、変数値(時間、パス、進行度など)の挿入により多くのログが発生することがあります。
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

### Handled Exception

```
//send Handled info log message
public static void Info(string strMsg, Exception e)

//send Handled debug log message
public static void Debug(string strMsg, Exception e)

//send Handled warn log message
public static void Warn(string strMsg, Exception e)

//send Handled fatal log message
public static void Fatal(string strMsg, Exception e)

//send Handled error log message
public static void Error(string strMsg, Exception e)
```

```
try{
	// Exception code
}catch(Exception e){
	LogNCrash.Info("handled exception message", e)
}
```

- try&catchで発生したExceptionを転送します。

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

## Android Buildする

1.File->Build Settingsをクリックします。

![](http://static.toastoven.net/prod_logncrash/image023.png)

![](http://static.toastoven.net/prod_logncrash/image028.png)

- Android Platformを選択した後、Player Settingsをクリックします。

![](http://static.toastoven.net/prod_logncrash/image029.png)

- Internet AccessはRequire、Write AccessはExternal(SDCard)に設定します。

2.Build settingsでBuild And Runをクリックします。

## Android Unity Crashを解析する

- UnityのCrashは、Unity Engineで発生するCrashとAndroid Naitveで発生するCrashに分けられます。

- Proguardが適用されていない場合、別途Symbol登録プロセスが必要ありません。

- Proguardが適用されている場合、NativeレベルのCrash解析のためにはmapping.txtファイルをWebコンソール > Analytic > Log & Crash Search > Settings > シンボルファイルタブに登録する必要があります。

- mapping.txtファイルはproguardフォルダの下に作成されます。
<br><br>
![](http://static.toastoven.net/prod_logncrash/12.png)

## Android Unity Crashの注意事項

- シンボルがなく、解析されていないCrashログは一般ログとして扱われます。

## 外部CrashHandlerを使用する

- 既存SDKでは初期化段階でlogMessageReceivedなどを使用して、UnityのCrashHandlerをLogNCrash専用Callback関数に登録して使用しました。
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
