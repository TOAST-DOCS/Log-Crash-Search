<!-- machine_translated: true -->

<!-- pre-align:aligned sig=99d57f88ede8 -->

<a id="data-analytics-log-crash-search-release-notes"></a>
## Data & Analytics > Log & Crash Search > リリースノート { #data-analytics-log-crash-search-release-notes }

<a id="february-27-2024"></a>
### 2024. 02. 27. { #february-27-2024 }
<a id="february-27-2024-feature-updates"></a>
#### 機能改善/変更
* [Console]検索フィールドページの変更
  * ログ保管期間に該当する検索フィールド照会
 
<a id="december-19-2023"></a>
### 2023. 12. 19. { #december-19-2023 }
<a id="december-19-2023-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ外部保管設定機能の変更
  * OBS / AWS S3各タイプごとに1つの設定が可能

<a id="august-29-2023"></a>
### 2023. 08. 29. { #august-29-2023 }
<a id="august-29-2023-feature-updates"></a>
#### 機能改善/変更
* [Console]ログアラーム設定時、一部フィールドの長さ制限を変更
  * アラームタイトルの長さ制限が20文字から80文字に変更
  * アラーム説明の長さ制限が80文字から255文字に変更

<a id="may-30-2023"></a>
### 2023. 05. 30. { #may-30-2023 }
<a id="may-30-2023-bug-fixes"></a>
#### バグ修正
* [Console]選択可能なフィールドでUserTxtDataフィールドが表示されないように変更
<a id="may-30-2023-feature-updates"></a>
#### 機能改善/変更
* [Console]検索フィールドメニューでUserTxtDataフィールドがカスタムフィールドではなく、基本フィールドとして表示されるように変更

<a id="april-26-2023"></a>
### 2023. 04. 26. { #april-26-2023 }
<a id="bug-fixes"></a>
### バグ修正 { #bug-fixes }
* ユーザーベースのアラームが動作しないバグを修正

<a id="december-27-2022"></a>
### 2022. 12. 27. { #december-27-2022 }
<a id="december-27-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索自動更新機能の追加
* [Console]ログ検索結果チャートをドラッグしてログ検索期間条件を設定できる機能を追加

<a id="november-29-2022"></a>
### 2022. 11. 29. { #november-29-2022 }
<a id="november-29-2022-bug-fixes"></a>
#### バグ修正
* [Console]ログ検索結果のログクリック検索機能を改善
  * txt*、bodyフィールドの単語をクリックして検索しても全フィールドの値が検索されるバグを修正
  * カスタムフィールドクリック検索を行った時、ハイライトが適用されないバグを修正

<a id="september-27-2022"></a>
### 2022. 09. 27. { #september-27-2022 }
<a id="september-27-2022-bug-fixes"></a>
#### バグ修正
* [Console]保存クエリリストメニューでクエリを連続削除 するとエラーページに移動するバグを修正

<a id="august-23-2022"></a>
### 2022. 08. 23. { #august-23-2022 }
<a id="august-23-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]グローバルユーティリティ関数呼び出し方式の変更

<a id="july-26-2022"></a>
### 2022. 07. 26. { #july-26-2022 }
<a id="july-26-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索結果画面のUI/UX改善
    * 列挙型で表示形式提供
<a id="july-26-2022-bug-fixes"></a>
#### バグ修正
* [Console]一部プラットフォームのクラッシュダンプデータがダウンロードできない問題を修正
* [Console]ログアラーム設定メニューで2ページ以降のアラーム状態変更ボタンが動作しない問題を修正

<a id="june-30-2022"></a>
### 2022. 06. 30. { #june-30-2022 }
<a id="june-30-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]選択されたフィールドダイアログUI/UX改善
    * フィールドダイアログ位置修正
    * ページ当たりのアイテム数を変更してもスクロールが最上段に移動しません。
    * ページ当たりのアイテム数変更後にページを移動してもページ当たりのアイテム数が初期化されません。
* [Console]バグ修正
    * クエリに特定文字が含まれたアラームの設定を修正すると、保存されたクエリとは異なるものが表示されるバグを修正

<a id="may-24-2022"></a>
### 2022. 05. 24. { #may-24-2022 }
<a id="may-24-2022-feature-updates"></a>
#### 機能改善/変更
* [Console] WebコンソールのUI/UX改善
    * Webコンソール内の時間を表示する方式を統一
    * Webコンソール内にローディングイメージを追加
* [Console]バグ修正
    * アプリクラッシュイシュー詳細情報のエラーインスタンスメニューで、データが特定の値のときにエラーインスタンスリストが表示されないエラーを修正

<a id="april-26-2022"></a>
### 2022. 04. 26. { #april-26-2022 }
<a id="april-26-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]ログアラーム設定ページUI/UXを改善
    * **アラーム設定追加**ダイアログボックスで**アラームタイプ**を **発生数**から**増減率**に変更すると画面が正常に表示されなかったバグを修正

<a id="march-29-2022"></a>
### 2022. 03. 29. { #march-29-2022 }
<a id="march-29-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索ページUI/UX改善
    * ログ検索「広げて表示」を行ったときのボタン位置を修正
    * ログ検索時、クエリオートコンプリート項目キーボードが選択できるように変更

<a id="february-22-2022"></a>
### 2022. 02. 22. { #february-22-2022 }
<a id="february-22-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索ページのUI/UXを改善
    * ログ検索結果「広げて表示」を行ったときの表示形式を変更
    * クラッシュログインの場合、ログ検索ページに**イシュー照会**ボタンを提供

<a id="january-25-2022"></a>
### 2022. 01. 25. { #january-25-2022 }
<a id="january-25-2022-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索ページのUX改善
  * ログ検索結果テーブルヘッダ固定
  * 「選択したフィールド」内のフィールド名をクリックしたときに表示されるポップアップウィンドウの形態を変更

<a id="november-23-2021"></a>
### 2021. 11. 23 { #november-23-2021 }
<a id="november-23-2021-feature-updates"></a>
#### 機能改善/変更
* [Console]新しいUI/UXが適用されたWebコンソールをデフォルトで適用し、既存Webコンソールに切り替え機能を削除
* [Console]ログ検索ページUXの改善
    * ログ検索結果テーブル内の展開表示カラムを左側(一番前に)移動
    * 「選択したフィールド」領域内のフィールド名右側に該当フィールドの値の種類数を表示
    * 「選択したフィールド」内のフィールド名をクリックした時に表示されるモーダルウィンドウで該当フィールドの値の種類数が100を超える場合、正確な数を表示

<a id="july-27-2021"></a>
### 2021. 07. 27. { #july-27-2021 }
<a id="july-27-2021-feature-updates"></a>
#### 機能改善/変更
* [Console] Webコンソールの全体的なUI/UXの改善および変更

<a id="april-27-2021"></a>
### 2021. 04. 27. { #april-27-2021 }
<a id="april-27-2021-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索APIを提供
* [Console]ログのダウンロード時、運営管理者がプロジェクトに設定したフィールドにマスキングを適用

<a id="april-27-2021-bug-fixes"></a>
#### バグ修正
* [Console]ログ検索ページ中の本文にhtmlが含まれている場合、htmlの内容が解析されて表示される現象を修正
* [Console]多数のクラッシュイシュー状態を同時に変更する場合に発生するエラーを修正

<a id="december-15-2020"></a>
### 2020. 12. 15. { #december-15-2020 }
<a id="december-15-2020-feature-updates"></a>
#### 機能改善/変更
* [Console]アラーム設定のうち、タイトルと説明の最大入力文字数を表示および制限
* [Console]クラッシュアラームのうち、しきい値を0に修正すると保存できないバグを修正
* [Console]ログ保管期間6か月、1年項目を削除、4か月を追加

<a id="october-27-2020"></a>
### 2020. 10. 27. { #october-27-2020 }
<a id="october-27-2020-feature-updates"></a>
#### 機能改善/変更
* [Console]セッションなしでアクセス可能なイベント詳細ページを新規サポート
    * ログアラーム発生時、従来のコンソールリンクの代わりにイベント詳細ページリンクをサポート
    * SMSにもイベント詳細ページリンクを追加

<a id="october-13-2020"></a>
### 2020. 10. 13. { #october-13-2020 }
<a id="october-13-2020-feature-updates"></a>
#### 機能改善/変更
* ログ(一般ログ、クラッシュログ)1件の容量制限を2MBから8MBへ変更

<a id="september-22-2020"></a>
### 2020. 09. 22. { #september-22-2020 }
<a id="september-22-2020-feature-updates"></a>
#### 機能改善/変更
* [Console]メール、Doorayアラームにおいて、添付されたTOAST Log & Crash SearchアクセスリンクでIAMコンソールをサポート

<a id="august-25-2020"></a>
### 2020. 08. 25 { #august-25-2020 }
<a id="august-25-2020-feature-updates"></a>
#### 機能改善/変更
* [Console]アラームコールバック/Webフック(webhook)から複数の対象を入力して実行できるように改善

<a id="july-28-2020"></a>
### 2020. 07. 28. { #july-28-2020 }
<a id="july-28-2020-feature-updates"></a>
#### 機能改善/変更
* [Console]外部保管ログのデータ完全性検証機能を追加
    * [Console使用ガイド参考](/Data%20&%20Analytics/Log%20&%20Crash%20Search/ja/console-guide/)

<a id="june-23-2020"></a>
### 2020. 06. 23. { #june-23-2020 }
<a id="june-23-2020-feature-updates"></a>
#### 機能改善/変更
* [Console] Object、Arrayタイプのクエリー方式を変更
    * 文字列検索と同じ方法でクエリーを伝達する必要があります。
    * [Luceneクエリーガイド参考](/Data%20&%20Analytics/Log%20&%20Crash%20Search/ja/lucene-query-guide/)
    
<a id="may-26-2020"></a>
### 2020. 05. 26. { #may-26-2020 }
<a id="may-26-2020-feature-updates"></a>
#### 機能改善/変更
* [Console] Bitcodeを適用したiOSアプリクラッシュ分析をサポート
    * 同じバージョン内でアーキテクチャごとにシンボルファイルをアップロードして分析できるように修正

<a id="april-28-2020"></a>
### 2020. 04. 28. { #april-28-2020 }
<a id="april-28-2020-feature-updates"></a>
#### 機能改善/変更
* [Console]ログ検索時、照会できる期間を最大3か月に制限
* [API] Android NDKクラッシュ識別方式を変更
<a id="april-28-2020-bug-fixes"></a>
#### バグ修正
* [Console]保存したクエリーの修正時、特定条件で保存されない問題を修正


<a id="march-24-2020"></a>
### 2020. 03. 24. { #march-24-2020 }
<a id="march-24-2020-feature-updates"></a>
#### 機能改善/変更
* [Console] 保存されたクエリーの削除に失敗した時のエラー文言を修正

<a id="february-25-2020"></a>
### 2020.02.25. { #february-25-2020 }
<a id="february-25-2020-feature-updates"></a>
#### 機能改善/変更
* [Console] SDKログ転送設定画面のツールチップ文言を修正
* [Console] クエリー保存機能にLuceneクエリーの有効性検証プロセスを追加
<a id="february-25-2020-bug-fixes"></a>
#### バグ修正
* [Console] シンボルファイルアップロード機能で特定Windowsシンボルファイルを処理できないバグを修正 
    * 複数回のビルドによりWindows PDBのage値が11を超過した場合、それ以降に抽出されたシンボルファイルはguidが34文字以上になる場合があります。guidフィールドの33文字制限を解除しました。
* [Console] 30日が経過したログを検索する時、保存されたフィールドが正常に表示されないバグを修正

<a id="january-21-2020"></a>
### 2020.01.21. { #january-21-2020 }
<a id="january-21-2020-feature-updates"></a>
#### 機能改善/変更
* [Console] iOSクラッシュ発生位置を探す方法の変更
* [Console] iOSクラッシュ追加情報表記 (TOAST SDK iOS 0.21.0以上のバージョンが必要です。)

<a id="october-29-2019"></a>
### 2019.10.29. { #october-29-2019 }
<a id="october-29-2019-feature-updates"></a>
#### 機能改善/変更
* [Console] S3アップロード機能を追加
* [Console] 無効なアラームクエリーでアラームを有効にした時のエラー文言を変更

<a id="august-27-2019"></a>
### 2019.08.27. { #august-27-2019 }
<a id="august-27-2019-bug-fixes"></a>
#### バグ修正
- [Console]クエリーリストで、特定のクエリーが検索されない現象を修正

<a id="june-25-2019"></a>
### 2019.06.25. { #june-25-2019 }
<a id="june-25-2019-feature-updates"></a>
#### 機能改善 / 変更
* 一部iOSのクラッシュログのシンボリケーション結果が(null) ((null))になるイシューを修正
* ログ(一般ログ、クラッシュログ)単件の容量制限を1MBから2MBに増加
* アラーム受信者リストで、実際の電話番号が表示されるように修正

<a id="june-25-2019-bug-fixes"></a>
#### バグ修正
* アラームの最終修正者がプロジェクトメンバーから除外された場合、修正されたアラームがリストで照会できない現象を修正

<a id="may-28-2019"></a>
### 2019.05.28. { #may-28-2019 }
<a id="may-28-2019-bug-fixes"></a>
#### バグ修正
* [Console] Windows 10、Internet Explorer 11環境で一部のページが正常に動作しない現象を修正
* [Console]シンボルファイルをアップロードした時、一部のZIPファイルを解析できないイシューを修正
* [Console] Webコンソールで言語を変更した時、特定部分が即時に反映されない現象を修正
* [Console]アラームを設定した時、http/httpsコールバックのうち、httpsコールバックを設定できない現象を修正

<a id="march-27-2019"></a>
### 2019.03.27 { #march-27-2019 }
<a id="march-27-2019-feature-updates"></a>
#### 기능 개선 / 변경
* [Console] iOS arm64eデバイスで発生したクラッシュ分析をサポート(互換性のあるSDKが必要です。)
* [Console] Android NDKで発生したクラッシュ分析をサポート(互換性のあるSDKが必要です。)
* [Console] 国際化対応 (日本語)

<a id="january-15-2019"></a>
### 2019.01.15 { #january-15-2019 }
<a id="january-15-2019-feature-updates"></a>
#### 기능 개선 / 변경
* [Console] Userコンソールの適用
* [Console] Unityで発生したクラッシュを指標から除外
    * アプリクラッシュ検索 > アプリクラッシュ指標に集計されない
* [Console]イシュートラッカー登録後、登録前の状態に戻す機能を追加
* [Console]シンボルファイル管理画面で、シンボリケーションに使用される実際のシンボル名を強調表示

<a id="november-27-2018"></a>
### 2018.11.27 { #november-27-2018 }
<a id="november-27-2018-feature-updates"></a>
#### 기능 개선 / 변경
* [Console] Androidのクラッシュのうち、クラッシュが発生した箇所がunityで、クラッシュタイプが'java.lang.Error'として記録された場合、例外の種類を正常に判断できないイシューを修正
    * Webコンソール > クラッシュ > イシュー照会から詳細画面に進めないイシューを修正
    * Webコンソール > クラッシュ > イシュー統計 バージョン別イシュー件数が表示されないイシューを修正
* [Console]アラーム関連機能のUX/UI改善
    * アラームの登録
    * アラーム履歴
    * クラッシュアラームの登録
    * クラッシュアラーム履歴
    * ユーザーベースのアラーム登録
    * ユーザーベースのアラーム履歴
* [Console] Webコンソール > 設定 > シンボルファイルアップロード画面でAndroid unityシンボルファイルガイドを追加
* [Console] Network insight URLの有効性検証を変更

<a id="october-23-2018"></a>
### 2018.10.23 { #october-23-2018 }
<a id="october-23-2018-feature-updates"></a>
#### 기능 개선/ 변경
* [Console] (旧) ログアラームの終了
    * [関連お知らせ](https://www.nhncloud.com/kr/support/notice/1453435858K00594)

<a id="september-18-2018"></a>
### 2018.09.18 { #september-18-2018 }
<a id="september-18-2018-feature-updates"></a>
#### 기능 개선/ 변경
* [Console] 保存済みクエリの共有

<a id="september-4-2018"></a>
### 2018.09.04 { #september-4-2018 }
<a id="september-4-2018-feature-updates"></a>
#### 기능 개선/변경
* [SDK][[logback-3.0.2](/Download/#data-analytics-log-crash-search)]
    * Logncrash Appenderの予約語のうち、デフォルト値がない項目がemptyまたはnullの場合、予約語の項目が追加されないよう改善
    * Logncrash REST APIタイムアウト設定
    * LogbackのAsyncAppenderの使用

<a id="september-4-2018-bug-fixes"></a>
#### 버그 수정
* [SDK][[logback-3.0.2](/Download/#data-analytics-log-crash-search)]
    * 一部のempty、nullの予約語項目が追加されたバグ修正

<a id="july-24-2018"></a>
### 2018.07.24 { #july-24-2018 }

<a id="july-24-2018-feature-updates"></a>
#### 기능 개선/변경
* [Console] 設定ページのUI変更

<a id="june-26-2018"></a>
### 2018.06.26 { #june-26-2018 }

<a id="june-26-2018-bug-fixes"></a>
#### 버그 수정
* [SDK][[iOS-2.7.1](/Download/#data-analytics-log-crash-search)]
    * 重複して初期化を実行した際にクラッシュが発生するバグ修正

<a id="june-5-2018"></a>
### 2018.06.05 { #june-5-2018 }

<a id="june-5-2018-feature-updates"></a>
#### 기능 개선/변경
* [SDK][[Android-2.6.7](/Download/#data-analytics-log-crash-search)]
    * 保存されたログの場合、フィルターを経由せずに送信するように動作を変更

<a id="june-5-2018-bug-fixes"></a>
#### 버그 수정
* [SDK][[iOS-2.7.0](/Download/#data-analytics-log-crash-search)]
    * SDK内部ロジックの改善
    
* [SDK][[Android-2.6.7](/Download/#data-analytics-log-crash-search)]
    * Android 4.1.2以下のバージョンでログが送信されないバグを修正（Android-2.6.6 SDKのバグ）

* [SDK][[Unity-2.8.6](/Download/#data-analytics-log-crash-search)]
    * iOSでUnity Crash Logが発生した際、LogLevelがすべてFATALに設定される問題を修正

<a id="may-29-2018"></a>
### 2018.05.29 { #may-29-2018 }

* [Console] iOSクラッシュシンボリケーション中に重複した名前のBundle(例: Framework.UIKit、Accessibility.UIKit)が存在する場合、シンボリケーションが正常に行われない問題を修正

<a id="may-09-2018"></a>
### 2018.05.09 { #may-09-2018 }

<a id="may-09-2018-bug-fixes"></a>
#### 버그 수정

* [SDK][[Unity-2.8.5](/Download/#data-analytics-log-crash-search)]
    * Unity Scriptで発生したCrash logTypeのロールバック
        * Unity Scriptで発生したCrashをHANDLEDとして処理するロジックが適用されていたため、ロールバックします。

<a id="may-2-2018"></a>
### 2018.05.02 { #may-2-2018 }

<a id="may-2-2018-feature-updates"></a>
#### 기능 개선/변경

* [SDK][[AOS-2.6.6](/Download/#data-analytics-log-crash-search)]
    * IP Addressの収集フィールドを削除
        * コンソールに表示される"host"の値は、サーバーから収集した情報を使用します。

* [SDK][[Unity-2.8.4](/Download/#data-analytics-log-crash-search)]
    * Android Native SDK呼び出しAPIの改善

<a id="may-2-2018-bug-fixes"></a>
#### 버그 수정

* [SDK][[AOS-2.6.6](/Download/#data-analytics-log-crash-search)] 
    * 重複除去フィルターのエラーを修正

* [SDK][[iOS-2.6.10](/Download/#data-analytics-log-crash-search)]
    * 初期化処理中にUserIDの値がnilの場合にCrashが発生する問題を修正
    * 初期化処理中にenableSyncStartの値がYESの場合、CPU使用率が100%まで上昇する問題を修正

<a id="april-24-2018"></a>
### 2018.04.24 { #april-24-2018 }

<a id="april-24-2018-bug-fixes"></a>
#### 버그수정
* [Console] アプリクラッシュのGitlabイシュー連携時、イシュー番号が正しく採番されない問題を修正
* [Console] アプリクラッシュ用のシンボルファイルを削除後、同じバージョンでシンボルファイルをアップロードした際に、正常にシンボリケーションされない問題を修正
* [Console] SMSアラームで特定の文字がアラーム内容に含まれている場合にアラームが送信されない問題を修正
* [Console] アプリクラッシュのアラーム受信者に特定の国コードが含まれているため、アプリクラッシュSMSアラームが送信されない問題を修正

<a id="january-22-2018"></a>
### 2018.01.22 { #january-22-2018 }
<a id="january-22-2018-feature-updates"></a>
#### 기능 개선/변경
* [Console] Network Insights新機能のリリース

<a id="december-21-2017"></a>
### 2017.12.21 { #december-21-2017 }
<a id="december-21-2017-feature-updates"></a>
#### 기능 개선/변경
* [Console] クエリベースの新規アラーム機能追加

<a id="october-26-2017"></a>
### 2017.10.26 { #october-26-2017 }
<a id="october-26-2017-feature-updates"></a>
#### 기능 개선/변경
* [Console] 新しいクラッシュ発生時のアラーム設定機能追加

<a id="october-26-2017-bug-fixes"></a>
#### 버그수정
* [console] セッション有効期限切れ時にエラーメッセージを表示するように修正

<a id="september-21-2017"></a>
### 2017.09.21 { #september-21-2017 }
<a id="september-21-2017-feature-updates"></a>
#### 기능 개선/변경
* [SDK] 初期化過程でCrashHandlerを自動的に登録しない関数を追加 (MultihandlerSampleを参考)
* [SDK] 外部で登録したCrashHandlerを通じてUnity Crashを送信できるように変更 (MultihandlerSampleを参考)
* [SDK] Optimizationスクリプトによる不要なSDKの削除 ( Docドキュメント参考 )
* [SDK] ユーザーがSettingsオブジェクトを任意のタイミングで保存できるように変更
    * 修正バージョン: [toast-logncrash-unity-2.8.3](/Download/#data-analytics-log-crash-search)
* [Console] ログ検索の時間表示方式を変更 (ms単位の削除およびタイムゾーンの明示)
* [Console] 特定フィールドのdistinct countが100を超えた場合、TOTAL countを出力
* [Console] トレンドページでcrashを経験したユーザーのUI削除
* [Console] クラッシュアラームメール形式の変更(dmpData削除およびDmpReportフォーマット変更)
* [Console] Unityクラッシュ分類基準の変更
    * UnityでERRORレベルのログが発生した場合、クラッシュとして分類されないように修正されました
        * Log Search画面で検索および照会できます

<a id="september-21-2017-bug-fixes"></a>
#### 버그수정
* [SDK] initializeを複数回呼び出した場合にSessionIDが更新される問題を修正
* [SDK] BackKeyでActivityを終了した場合、SDKが最後のActivityの状態を保存しているためActivityがメモリから解放されない問題を修正
    * 修正バージョン: [toast-logncrash-android-2.6.4](/Download/#data-analytics-log-crash-search) / [toast-logncrash-unity-2.8.3](/Download/#data-analytics-log-crash-search)
* [SDK] PLCrashReporterがCrash Fileを生成できない場合、'EMPTY CRASH FILE'をDmpDataに入れて送信するように修正
    * 修正バージョン: [toast-logncrash-ios-mac-sdk-2.6.7](/Download/#data-analytics-log-crash-search) [toast-logncrash-unity-2.8.3](/Download/#data-analytics-log-crash-search)
* [SDK] iOS SDKでNative Crashが発生した際、CrashStyle、SymMethodが誤って表記される問題を修正
* [SDK] WebGLでUserIDが設定されない問題を修正
* [SDK] unity ios wrapper classでhttpsプロトコルが指定されない問題を修正
    * 修正バージョン: [toast-logncrash-unity-2.8.3](/Download/#data-analytics-log-crash-search)

<a id="july-20-2017"></a>
### 2017.07.20 { #july-20-2017 }
<a id="july-20-2017-feature-updates"></a>
#### 기능 개선/변경
* [SDK] WebGLプラットフォームのサポート
    * 修正バージョン: [toast-logncrash-unity-2.7.4](/Download/#data-analytics-log-crash-search)
* [Console] クラッシュ一覧画面のsofting optionからユーザー数を削除

<a id="july-20-2017-bug-fixes"></a>
#### 버그수정
* [Console] クラッシュユーザーレイアウトバグ修正

<a id="june-22-2017"></a>
### 2017.06.22 { #june-22-2017 }
<a id="june-22-2017-bug-fixes"></a>
#### 버그수정
* [SDK] 重複制御キューが最大サイズを超えた場合、LFU動作のDeleteバグによりCrashが発生する現象を修正
    * 修正バージョン: [toast-logncrash-cpp-windows-sdk-2.5.4](/Download/#data-analytics-log-crash-search) / [toast-logncrash-csharp-windows-sdk-2.5.4](/Download/#data-analytics-log-crash-search)/ [toast-logncrash-androidndk-sdk-2.6.2](/Download/#data-analytics-log-crash-search)
* [SDK] ReSendログ保存時に2MBずつ合計20MBを保存する方式から、2MBのみ保存するように変更
* [SDK] Send Queueのサイズを500件から2000件に変更
* [SDK] インターネットに接続されていない場合、送信動作を行わない方式からファイルに保存する方式に変更
* [SDK]インターネット接続が切断された後に再接続された場合、ファイルに保存していたログを再送信するように変更
    * 修正バージョン: [toast-logncrash-cpp-windows-sdk-2.5.4](/Download/#data-analytics-log-crash-search) / [toast-logncrash-csharp-windows-sdk-2.5.4](/Download/#data-analytics-log-crash-search)
* [SDK] 一部のフィールド(国コード、プラットフォーム情報など)が欠落する現象を修正
    * 修正バージョン: [toast-logncrash-android-2.6.2](/Download/#data-analytics-log-crash-search)
* [SDK] エラー内容をerrorCodeとtxterrorCodeフィールドに格納して送信するように変更
    * 修正バージョン: [toast-logncrash-logback-sdk-2.2.7](/Download/#data-analytics-log-crash-search) / [toast-logncrash-log4j-sdk-2.2.7](/Download/#data-analytics-log-crash-search)

<a id="june-19-2017"></a>
### 2017.06.19 { #june-19-2017 }
<a id="june-19-2017-bug-fixes"></a>
#### 버그수정
* [SDK] SendThreadにSleepがなく、CPU使用率が99%になる現象を修正
* [SDK] 1秒間に100件のログを送信する場合、メモリが正常に解放されない現象を修正
    * 修正バージョン: [toast-logncrash-ios-unity-mac-sdk-2.6.6.1](/Download/#data-analytics-log-crash-search)

<a id="may-25-2017"></a>
### 2017.05.25 { #may-25-2017 }
<a id="may-25-2017-feature-updates"></a>
#### 기능 개선/변경
* [Console] ログサーチフィールド名オートコンプリート機能追加
* [Console] Crashes > アプリクラッシュ指標ページ下部テーブルのUserID Column表示順変更およびGray処理
* [Console] セッションログ画面の表示・非表示をユーザーがon/offできる機能を追加
* [SDK] Unity Android / Android 統合
    * 修正バージョン: [toast-logncrash-android-2.6.1](/Download/#data-analytics-log-crash-search)
* [SDK] hotfield Enable/Disableを追加
    * 修正バージョン: [toast-logncrash-android-2.6.1](/Download/#data-analytics-log-crash-search) / [toast-logncrash-androidndk-sdk-2.6.1](/Download/#data-analytics-log-crash-search)

<a id="may-25-2017-bug-fixes"></a>
#### 버그 수정
* [SDK] Unity Crashの再送信時、Sessionログが1回追加で送信される動作を修正
* [SDK] DevicID フィールドが欠落するバグ修正
    * 修正バージョン: [toast-logncrash-ios-unity-mac-sdk-2.6.5.1](/Download/#data-analytics-log-crash-search)

<a id="april-20-2017"></a>
### 2017.04.20 { #april-20-2017 }
<a id="april-20-2017-feature-updates"></a>
#### 기능 개선/변경
* [Console] アプリクラッシュ指標画面のレイアウト変更
    * アプリクラッシュ指標画面にSDKバージョンを表示
    * ユーザー / デバイス情報テーブルに一緒に表示
    * テーブルヘッダーの変更「バージョン」→「アプリバージョン (SDKバージョン)」
* [Console] クラッシュマップ画面のレイアウト変更およびマップへのデバイス数追加
* [Console] 新しいウィンドウで開く機能を有効化
* [Console] アラーム送信フォーマットの変更(プロジェクト名の代わりにアラーム名を表示
* [SDK] 機能追加
    * Init関数とLog送信の間のAddCustomFieldを保証するため、SendThread Lock機能追加
        * 修正バージョン: [toast-logncrash-ios-unity-mac-sdk-2.6.0](/Download/#data-analytics-log-crash-search) / [toast-logncrash-android-unity-sdk-2.6.0](/Download/#data-analytics-log-crash-search) / [toast-logncrash-android-2.6.0](/Download/#data-analytics-log-crash-search)
* [SDK] 機能変更
    * Exception、errorCode、RequestHeaderフィールドをコンソールで分析可能な形式で送信
		* txtException、txterrorCode、txtRequestHeaderフィールド名に変更されました
		* 修正バージョン: [toast-logncrash-log4j-sdk-2.2.6](/Download/#data-analytics-log-crash-search) / [toast-logncrash-log4j2-sdk-2.2.6](/Download/#data-analytics-log-crash-search)/ [toast-logncrash-logback-sdk-2.2.6](/Download/#data-analytics-log-crash-search)
		* Exception、errorCode、RequestHeaderフィールドにアラーム設定がされている場合、2.6適用後にtxt\*フィールドへの修正が必要です
    * 最大2MBまでログをまとめて送信するようにSend方式を変更
        * 修正バージョン: [toast-logncrash-ios-unity-mac-sdk-2.6.0](/Download/#data-analytics-log-crash-search) / [toast-logncrash-android-unity-sdk-2.6.0](/Download/#data-analytics-log-crash-search) / [toast-logncrash-android-2.6.0](/Download/#data-analytics-log-crash-search)
* [SDK] Unity-ios / ios SDKの統合
    * 変更内容はSDKファイル内のREADME.mdファイルを参照してください
    * [Toast-logncrash-ios-unity-mac-sdk-2.6.0](/Download/#data-analytics-log-crash-search)
    *  Toast-logncrash-unity-ios-sdk / toast-logncrash-ios-mac-sdkを削除

<a id="april-20-2017-bug-fixes"></a>
#### 버그 수정
* アラーム周期が1分でない場合にsnoozeが動作しない問題を修正
<a id="march-23-2017"></a>
### 2017.03.23 { #march-23-2017 }
<a id="march-23-2017-feature-updates"></a>
#### 기능 개선/변경
* [Console] ダンプ解析に失敗したクラッシュもUNKNOWNクラッシュ形式で統計情報を提供するように機能改善
* [Console] スタックトレースを表示できない場合、スタックトレース画面に案内文言を表示
    * シンボルファイルを登録していないためスタックトレースを表示できない場合(エラータイプがUNKNOWNの場合)、スタックトレース画面に案内文言を表示

<a id="march-23-2017-bug-fixes"></a>
#### 버그 수정
* [Console] 英語のクラッシュサーチ画面でReal Time MonitoringタブがＵ崩れる現象を修正しました

<a id="february-23-2017"></a>
### 2017.02.23 { #february-23-2017 }
<a id="february-23-2017-feature-updates"></a>
#### 기능 개선/변경
* [API] [log Bulk upload](/Data%20&%20Analytics/Log%20&%20Crash%20Search/ja/api-guide/) 機能追加
    * REST APIでログを送信する際、JSON array形式でログを送信できます。
* [API] long,doubleオプション追加
    * REST APIでログ送信時にlong、doubleで始まるフィールドを使用した場合、long、double型として保存されます
    * ログ検索画面で、longおよびdoubleタイプのRange検索が可能です。
* [SDK] CrashCallback機能追加
    * [Windwos csharp SDK 2.5.2.1](/Download/#data-analytics-log-crash-search) / [Windows cpp SDK 2.5.2.1](/Download/#data-analytics-log-crash-search)

<a id="february-23-2017-bug-fixes"></a>
#### 버그 수정
* [WEB] 保存されたクエリ表示ページでクエリを削除できない問題を修正
* [WEB] イシューリストの詳細で戻るボタンをクリックしても1ページ目に移動しないようにpaginationを改善
* [SDK] スレッド間の競合現象を修正
    * [unity-android-sdk 2.5.6.0](/Download/#data-analytics-log-crash-search)
* [SDK] 外部ライブラリとlogncrashを同時にビルドする際のbinaryimagesort duplicate symbolエラーを修正
    * [unity-ios-sdk-2.5.2.6](/Download/#data-analytics-log-crash-search)
* [SDK] 一部のデバイスでbreakpadの処理が完了する前にアプリケーションが強制終了される現象を修正
    * [androidndk-sdk 2.4.7.0](/Download/#data-analytics-log-crash-search)
* [SDK] Asyncモードでcustomfieldが追加されない現象を修正
    * [Log4j-sdk-2.2.5](/Download/#data-analytics-log-crash-search)/ [Logback-sdk-2.2.5](/Download/#data-analytics-log-crash-search)

<a id="january-19-2017"></a>
### 2017.01.19 { #january-19-2017 }
<a id="january-19-2017-feature-updates"></a>
#### 기능 개선/변경
* アプリクラッシュ指標のバージョン表示基準の変更
    * アプリクラッシュ指標 > クラッシュが発生しなかったものの、実行数が存在するバージョンも表示されるように修正
<a id="january-19-2017-bug-fixes"></a>
#### 버그 수정
* Log Search画面のログをすべて表示/非表示にする機能を修正

<a id="december-22-2016"></a>
### 2016.12.22 { #december-22-2016 }
<a id="december-22-2016-feature-updates"></a>
#### 기능 개선/변경
* Web画面からログファイルをダウンロードする際、最大10万件に制限
    * 10万件以上試行時にポップアップアラーム
* [SDK] iOS/Androidのネイティブレベルの例外を収集できるように機能追加
    * 修正バージョン: unity-android-sdk-2.5.1, unity-ios-sdk-2.5.1
* [SDK] Log Duplicate Queue Sizeが最大1,000個に制限されました
    * 修正バージョン: Android-2.4.3、Android-NDK-2.4.5、iOS-2.4.1、unity-android-2.5.1、unity-ios-2.5.1

<a id="december-8-2016"></a>
### 2016.12.08 { #december-8-2016 }
<a id="december-8-2016-feature-updates"></a>
#### 기능 개선/변경
* イシュー照会 > イシュー詳細 > コメント、ヒストリータブで内容を登録したユーザーがプロジェクトメンバーから削除された場合
  メールの公開部分に「[削除されたメンバー]」と表示

<a id="december-8-2016-bug-fixes"></a>
#### 버그 수정
* ログアラーム設定時、フィルタリングルールの(非)包含文字列に「\」が含まれる場合にログアラーム一覧が照会されないバグ修正
* クラッシュアラームの新規保存時にメンバーリストがない場合、失敗アラームが表示されるように修正

<a id="november-24-2016"></a>
### 2016.11.24 { #november-24-2016 }
* [SDK] 一部のデバイスでhost値の取得に使用されるgetaddrinfo関数がhang現象を引き起こすため、host値を内部スレッドで取得するよう変更
  * 修正バージョン: Android-NDK 2.4.4

<a id="november-4-2016"></a>
### 2016.11.04 { #november-4-2016 }
<a id="november-4-2016-bug-fixes"></a>
#### 버그 수정
* [SDK] Android 2.4.1バージョンでAsyncTaskがCancelされないバグがあり、該当ロジックをThreadに変更
  * 修正バージョン: Android 2.4.2

<a id="october-20-2016"></a>
### 2016.10.20 { #october-20-2016 }
<a id="october-20-2016-feature-updates"></a>
#### 기능 개선/변경
* デバイスの固有ID値であるDeviceIDの収集
    * 新規SDKを通じたCrash Log送信時にDeviceIDが収集され、Console > Log & Crash Search > Crashes > アプリクラッシュ指標画面で
      DeviceIDを基準に指標を確認できます。
* [SDK] ログ送信ができない場合にファイルへ保存し、その後通信が正常に回復した場合にログファイルを送信するよう機能変更されました。
* [SDK] すべてのログに対して重複ログ処理ルーティンを適用
    * 重複ログ機能が有効になっている場合、BodyとlogLevelの内容が同じログが発生しても送信しません。
    * 必要な場合、ユーザーが別のAPIで制御できるよう機能が提供されます。
    * 詳細については、Developer's Guideを参照してください。
* [Console] アプリクラッシュ指標画面 > 「セッション」、「ユーザー数」タイトルが「実行数」「クラッシュを経験したユーザー」に変更されました。

<a id="september-29-2016"></a>
### 2016.09.29 { #september-29-2016 }
<a id="september-29-2016-feature-updates"></a>
#### 기능 개선/변경
* アラームしきい値設定およびhttp Callback機能追加
    * アラーム閾値の比較演算子をサポート(>、>=、=、<=、<)
    * n時間前比較の以上/以下機能追加
    * Snooze機能追加
* Log Search画面でダウンロード可能なUserTxtDataフィールドを追加
    * 「UserTxtData」フィールドはLog Search画面で[ダウンロード|表示]を表示し、フィールドの内容をすぐに確認できます

<a id="september-29-2016-bug-fixes"></a>
#### 버그 수정
* [SDK] Exceptionが発生した場合、ログ送信オブジェクトを初期化できずに繰り返し初期化を再試行していたバグ修正
    * 修正されたSDK: logback、log4j、log4j2
* [SDK] init関数にUserIDを設定した場合、ログに値が正常に追加されないバグ修正
    * 修正されたSDK: iOS

<a id="september-12-2016"></a>
### 2016.09.12 { #september-12-2016 }
<a id="september-12-2016-bug-fixes"></a>
#### 버그 수정
* [SDK] CarrierおよびCarrier値がnullでreturnされるケースに対する例外処理コードを追加
    * 修正されたSDK: Unity(v.2.3.4)

<a id="august-22-2016"></a>
### 2016.08.22 { #august-22-2016 }
<a id="august-22-2016-feature-updates"></a>
#### 기능 개선/변경
* Custom Field Defaultオプションおよび長さ制限の変更
    * Custom フィールド作成時にanalyzed(分析可否)をfalseに変更
    * 送信可能なログ長128byte -> 2Kbyteに変更
    * 2Kbyte以上のログ送信が必要な場合は、Field名にtxt prefixを指定してCustom fieldを作成します
    * txt*; string, オプション [in] フィールド名がtxtで始まるフィールド(txt_descriptionなど)は、分析済み(analyzed)フィールドとして保存<br>
      ログ検索画面でフィールド値の一部の文字列で検索が可能


<a id="august-18-2016"></a>
### 2016.08.18 { #august-18-2016 }
<a id="august-18-2016-feature-updates"></a>
#### 기능 개선/변경
* ログ送信ON / OFF機能追加
    * Log & Crash Searchに送信されるログ(一般ログ/クラッシュログ/セッションログ)について、ユーザーがコンソールで
      On/Offの設定で、収集するかどうかを決定できます。
    * 収集されなかったログは画面に表示されず、APIおよびStorage料金にも含まれません。
    * Log on/offの機能追加によりSDKが更新されました。
        * 対象SDK(v.2.3.0): Unity、Android、Windows、iOS

* [API] UserBinaryDataフィールド追加
    * ログファイルやバイナリファイルを上記フィールドで送信する場合、ログ検索画面からダウンロード可能

<a id="august-18-2016-bug-fixes"></a>
#### 버그 수정
* [Console] Crashの詳細ページ読み込み速度の問題を修正


<a id="august-4-2016"></a>
### 2016.08.04 { #august-4-2016 }
<a id="august-4-2016-feature-updates"></a>
#### 기능 개선/변경
* [SDK][Unity] 2.2.6アップデート
    * SaveToFileの保存フォーマットを変更
    * Regular Expression、JSONライブラリを使用して変換するように修正
    * ファイルの最大保存数100件制限
    * 重複排除キュー100個の制限

<a id="august-4-2016-bug-fixes"></a>
#### 버그 수정
* [API] 特定のフィールドにjson arrayまたはobjectを送信した場合にstringに変換される現象を修正
