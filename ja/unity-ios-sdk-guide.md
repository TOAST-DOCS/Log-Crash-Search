## Analytics > Log & Crash Search > Unity iOS SDK使用ガイド

> [Deprecated]
> Log & Crash Unity iOS SDKバージョンは、今後サポートされません。
> [TOAST SDK](http://docs.toast.com/ko/TOAST/ko/toast-sdk/overview/)をご利用ください。

Log & Crash Unity SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。  
Log & Crash Unity SDKの特徴・利点は次のとおりです。

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchで、転送されたログの照会および検索が可能です。

## サポート環境

- 共通
	\- Unity3D v4.0以上
- iOS
	\- An Intel-based Mac
	\- Xcode 6.0 or later

## ダウンロード

[TOAST Document](http://docs.toast.com/ko/Download/)でUnity SDKをダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Unity SDK]
```

## インストール

 - ダウンロードしたtoast-logncrash-ios-unity-sdk.unitypackageをダブルクリックして、該当プロジェクトにImportします。


### サンプル説明

サンプルを実行するには、Assets > LogNCrash > Sample > SampleSceneをダブルクリックします。
サンプルには初期化、ログ転送、エラー発生についての例が記述されています。

### ヘッダファイル追加

iOS Unity環境で使用するには、#import <LogNCrashSDK/LogNCrashSDK.h\>を追加します。

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

LogNCrash.Initializeにパラメータを入力して初期化を試行します。パラメータはサーバーアドレス、アプリケーションキー、バージョン、ポート、 PLCrashreporter Enable、Send Thread Lockを実行するかどうかについての情報を渡します。

```
using Toast.LogNCrash;
namespace Toast.LogNCrash
{
	public class SampleScript : MonoBehaviour
	{
		void Start ()
		{
			LogNCrash.Initialize ("https://api-logncrash.nhncloudservice.com", "appkey", "1.0.0", 443, true, true);
			LogNCrash.StartSendThread ();
		}
	}
}
```

- Appkey：ユーザーアプリケーションキー
- URL：コレクターアドレス、http、httpsのコクレクター情報を設定
- Version：ログバージョン
- Port：443
- PLCrashreporter Enable：PLCrashrepoterを使用するかどうかを決定します。
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
	- value:string
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
	- custom filedの値がNULLまたは空の場合、SDKsは該当フィールドをserverに転送しません。

### Hostロック設定

```
		public static SetEnableHost(bool flag)
```

	- true：ip addressを取得し、hostフィールドに保存します。
	- false：ip addressを取得しません。

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

## iOS buildする

1.File->Build Settingsをクリック。

![](http://static.toastoven.net/prod_logncrash/image023.png)

![](http://static.toastoven.net/prod_logncrash/image024.png)

- iOS Platformを選択し、Player Settingsをクリックします。

![](http://static.toastoven.net/prod_logncrash/image025.png)

- Target iOS Versionを設定し、Simulatorを使用する場合はSDK VersionでSimulator SDKを、 deviceを使用する場合はDevice SDKを選択してBuild settingsのBuildボタンをクリックします。

2.Buildされたプロジェクトが保存されるパスを選択し、Saveを選択したら、UnityでXcode projectを作成します。

![](http://static.toastoven.net/prod_logncrash/image026.png)

![](http://static.toastoven.net/prod_logncrash/image027.png)

3.作成されたXcode projectをXcodeで開きます。


## iOSでATS(App transport Security)を追加する
- ATSは、iOS9、OS X 10.11で導入されたアプリとネットワーク間の安全な通信を保障するための機能で、安全に暗号化されたhttps通信のみを許可し、安全ではない水準のhttps/http通信を遮断する機能です。Log & Crash Searchではhttpプロトコルを使用して通信を試行する際、info.plistに下記のような設定を追加する必要があります。

詳細な設定は、下記のリンクを参照してください。
- https://developer.apple.com/library/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html


1.全てのhttpを許可する方法

```
<key> NSAppTransportSecurity </key>
<dict>
    <key> NSAllowsArbitraryLoads </key>
   <true />
</dict>
```

2.特定ドメインを許可する方法

```
<key> NSAppTransportSecurity </key>
<dict>
    <key> NSExceptionDomains </key>
    <dict>
           <key> api-logncrash.nhncloudservice.com </key>
            <dict>
		 <key>NSTemporaryExceptionAllowsInsecureHttpLoads </key>
		 <true />
	    </dict>

	   <key> setting-logncrash.cloud.toast.com </key>
            <dict>
		 <key>NSTemporaryExceptionAllowsInsecureHttpLoads </key>
		 <true />
	    </dict>

    </dict>
</dict>
```

3.ATS自動設定機能

- Assets > Toast > LogNCrash > Editor > post_process.pyファイルには、iOSビルド時、info.plistにapi-logncrash.nhncloudservice.comとsetting-logncrash.cloud.toast.comを自動的に追加するコードが挿入されています。

## iOS Native Crashを解析する
- Unity iOSのCrashは、Unity Engineで発生するCrashと、iOS Naitveで発生するCrashに区分されます。
- Unityで発生したCrashの場合、Crash情報がStringで収集されるため、Symbolファイルが必要ありません。
- iOSで発生したCrashの場合、Crash情報がアドレス値で収集されるため、これを解析するためのSymbolファイルが必要です。
- Xcodeを実行し、Windows > Organizerをクリックします。
![](http://static.toastoven.net/prod_logncrash/ios_12.png)
- ビルドした結果物をクリックした後、右クリックしてShow in Finderをクリックします。
![](http://static.toastoven.net/prod_logncrash/ios_13.png)
- 結果物をクリックし、右クリックして'パッケージ内容参照'をクリックします。
![](http://static.toastoven.net/prod_logncrash/ios_14.png)
- .dSYMを.zipに圧縮して、Webコンソール > Analytic > Log & Crash Search > Settings > シンボルファイルタブに登録します。
![](http://static.toastoven.net/prod_logncrash/ios_15.png)

## iOS Unity Crash注意事項

- シンボルがなく、解析されていないCrashログは一般ログとして扱われます。

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
