<!-- pre-align:aligned sig=ecb9adf37221 -->

﻿## Analytics > Log & Crash Search > iOS SDK使用ガイド

> [Deprecated]
> Log & Crash iOS SDKバージョンは、今後サポートされません。
> [TOAST SDK](http://docs.toast.com/ko/TOAST/ko/toast-sdk/overview/)を利用してください。

> [告知]
> arm64eアーキテクチャを使用する新規端末(iPhone XS、XR、XS Max、iPad Pros 3rd)で発生したクラッシュログは、発生件数の集計のみ可能で、クラッシュ内容の分析はまだサポートされていません。
> 近日中に新規端末向けの分析機能を提供したいと思います。

Log & Crash iOS SDKは、Log & Crash Search収集サーバーにログを転送する機能を提供します。  
Log & Crash iOS SDKの特徴・利点は次のとおりです。  

- ログを収集サーバーに転送します。
- アプリで発生したクラッシュログを収集サーバーに転送します。
- Log & Crash Searchで、転送されたログを照会および検索ができます。
- マルチスレッド環境で動作します。

<a id="analytics-log-crash-search-ios-sdk-guide"></a>
## Analytics > Log & Crash Search > iOS SDK 使用ガイド { #analytics-log-crash-search-ios-sdk-guide }

<!-- TODO: translate body -->

<a id="supporting-environment"></a>
## サポート環境 { #supporting-environment }
- iOS 8.0以上

<a id="download"></a>
## ダウンロード { #download }

[TOAST Document](http://docs.toast.com/ko/Download/)でiOS SDK(native)をダウンロードできます。

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [iOS SDK]をクリック
```

<a id="how-to-use-sdks"></a>
## SDKの使用方法 { #how-to-use-sdks }

<a id="add-header-files"></a>
### ヘッダファイルの追加 { #add-header-files }

\#import <LogNCrashSDK/LogNCrashSDK.h\> 追加します。

<a id="initialize"></a>
### 初期化 { #initialize }

```
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion;
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion forUserId:(NSString*)userId;
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion forUserId:(NSString*)userId enableSyncStart:(bool)flag;
```

- TLCLogを初期化します。
- TLCLog機能が正常に動作するには、必ず呼び出す必要があります。
- ユーザー別統計を希望する場合、userIdを入力する必要があります。
- パラメータ
	- server：収集サーバーアドレス
	- appKey：アプリケーションキー
	- version：アプリバージョン
	- userId：ユーザーID
	- enableSyncStart： trueの場合、発生したログはstartSendThreadが呼び出されるまでサーバーに転送せず、キューに保存します。ただしCrashが発生した場合は、ThreadLockを解除してログを転送します。
- 戻り値
	- userId：ユーザーID
	- 失敗時はfalse

<a id="unlock-sendthread"></a>
### SendThreadのロック解除 { #unlock-sendthread }

```
	(void) startSendThread;
```

- SendThreadのロック状態を解除します。

<a id="lock-host"></a>
### Hostのロック設定 { #lock-host }

```
	(void) enableHost;
```

- true ： ip addressを取得して、hostフィールドに保存します。
- false： ip addressを取得しません。

<a id="send-logs"></a>
### ログを転送 { #send-logs }

```
(void) debug:(NSString*)errorCode withMessage:(NSString*)message;
(void) error:(NSString*)errorCode withMessage:(NSString*)message;
(void) fatal:(NSString*)errorCode withMessage:(NSString*)message;
(void) info:(NSString*)errorCode withMessage:(NSString*)message;
(void) warn:(NSString*)errorCode withMessage:(NSString*)message;



(void) debug:(NSString*)errorCode withMessage:(NSString*)message atLocation:(NSString*)location;
(void) error:(NSString*)errorCode withMessage:(NSString*)message atLocation:(NSString*)location;
(void) fatal:(NSString*)errorCode withMessage:(NSString*)message atLocation:(NSString*)location;
(void) info:(NSString*)errorCode withMessage:(NSString*)message atLocation:(NSString*)location;
(void) warn:(NSString*)errorCode withMessage:(NSString*)message atLocation:(NSString*)location;
```

- 指定されたログレベルで収集サーバーにログを転送します。
- パラメータ
	- errorCode：エラーコード
	- message：ログメッセージ
	- location：エラー位置

<a id="specify-custom-keys"></a>
### カスタムキーを指定する { #specify-custom-keys }

```
(void) setCustomField:(NSString*)value forKey:(NSString*)key;
(void) removeAllCustomFields;
(void) removeCustomFieldForKey:(NSString*)key;
```

- カスタムキーを追加、削除、全部削除機能を提供します。
- カスタムキーに"nil"を設定すると設定値を無視し、値に"nil"を設定すると"-"に設定します。
- カスタムキーは大文字/小文字で始まり、大文字/小文字、数字、'-'、'\_'のみ使用できます。(^[a-zA-Z][a-zA-Z0-9-_]\*$)
- カスタムキーは大文字/小文字に関わらず、次の名前は使用できません。
	- projectName, projectVersion, host, logType, logSource, sendTime, logTime, logLevel, UserID
	- Platform, DeviceModel, NetworkType, Carrier, CountryCode, DmpData, errorCode, Location, body, SessionID

<a id="remove-duplicates"></a>
### 重複除去モード設定 { #remove-duplicates }

2.4.0以上のSDKから一般ログに重複除去ロジックが適用されました。

重複ログ機能が有効になっている場合、bodyとlogLevelの内容が同じログが発生した時は転送しません。

```
public static void setLogDeduplicate(bool enable)
```

true：(Default値)重複除去ロジック有効化<br>
false：重複除去ロジックを無効にする

<a id="manage-default-setting"></a>
### 基本設定管理 { #manage-default-setting }

```
(void) setUserId:(NSString*)userId;
```

- ユーザーIDを設定します。

```
(void) setLogType:(NSString*)logType;
```

- ログタイプを設定します。

```
(void) setLogSource:(NSString*) logSource;
```

- ログソースを設定します。

<a id="automatically-collected-information"></a>
## 自動収集される情報 { #automatically-collected-information }

下記の情報は、Log & Crash SDKにより自動的に収集され、Log & Crash Searchで確認できます。ログ転送時点で情報収集ができない場合や、値を表示できない場合に発生することがあります。

- iOS
	\- Platform：iOSバージョン情報
	\- DeviceModel：iPhoneモデル情報
	\- Carrier：ユーザーのサービスプロバイダー
	\- CountryCode：ユーザーのサービスプロバイダーのISO国コード
	\- NetworkType： Wi-FiまたはCellular (ログ転送イベント発生時点でネットワーク使用ができない場合は"No Connection")

<a id="interpret-ios-crashed"></a>
## iOS Crashを解析する { #interpret-ios-crashed }
- iOSで発生したCrashの場合、Crash情報がアドレス値に収集されるため、これを解析するためのSymbolファイルが必要です。

- Xcodeを実行してWindows > Organizerをクリックします。

- ビルドした結果をクリックして、右クリックしてShow in Finderをクリックします。
![](http://static.toastoven.net/prod_logncrash/13.png)

- 結果物をクリックし、右クリックして'パッケージ内容表示'をクリックします。
![](http://static.toastoven.net/prod_logncrash/14.png)

- .dSYMを .zipに圧縮し、Webコンソール > Analytic > Log & Crash Search > Settings > シンボルファイルタブに登録します。

<a id="note-for-ios-unity-crash"></a>
## iOS Unity Crash注意事項 { #note-for-ios-unity-crash }

- シンボルがなくて解析されなかったCrashログは、一般ログとして扱われます。
