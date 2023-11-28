## Luceneクエリガイド

## 基本注意事項

ダブルクォーテーション(")とチルダ(~)の位置や有無によって、演算子として使用するかどうかが決定されます。
* 例)近似検索と類似項目検索
また、基本的に検索語がダブルクォーテーション(")で囲まれている場合、囲まれている部分全体を1つの構文として検索します。
* 例) body:normal AND performance
* → bodyフィールドの値に"normal"と"performance"があるログを検索

![lcs_lucene_guide_01](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_01.png)

* 例) body:"normal AND performance"
* → bodyフィールドの値に"normal AND performanceがあるログを検索

![lcs_lucene_guide_02](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_02.png)
* "normal AND performance"に該当するテキストがないため、ログ結果が0

検索時、特殊文字を使用する時に文字の前にバックスラッシュまたは、\を入力してエスケープ処理します。
* 対象特殊文字: ```+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /```

URL内の特殊文字(予約文字含む)は、エンコードする必要があります。
* 例) '<'特殊文字は%3Cと入力

## 基本検索

filedname:search word
* 任意の単一フィールド(fieldname)で、単一の用語(search word)を検索します。

フィールド名がlog.type、log.timeまたはlog.versionなどの形式の場合は、下記のように一括検索が可能です。
* 例) log.*:alpha

!\_exists\_:fieldname
* 該当fieldnameに値がないか(null)、該当fieldnameを持たないログを検索します。

\_exists\_:fieldname
* 該当fieldnameにnon-nullの値を持たないログを検索します。

## 範囲検索

| 文法 | 動作 |
| --- | --- |
| [min TO max] | min以上、max以下を検索 |
| {min TO max} | minより大きい、max未満を検索 |
| {min TO max], [min TO max} | minより大きいmax以下、min以上max未満 |
| {* TO max}, {min TO *} | max未満、minより大きい |

* 下記のように、もう少しシンプルに条件を入れることができます。
    * 例) fieldname:>10

## 論理演算子

| 演算子 | 意味 |
| --- | --- |
| AND, &&, + | AND演算子 |
| OR\, \|\| | OR演算子 |
| NOT, !, - | NOT演算子 |

NOT演算子`-`の場合、AND NOTの意味で使います。
* 例) logType:bulk -api
* → logType:bulk AND NOT apiと同じ

## ワイルドカード検索

*は、任意の0文字以上の文字列を検索します。

* 例) body:performance\*
* performanceで始まるbodyがあるログを検索します。

?は、任意の1文字を検索します。
* 例) body:performance?
* performanceで始まり、任意の1文字に対応するbodyフィールドがあるログを検索します。

ワイルドカードの*と?は、文字の間にも適用することができます。

## 近似検索(proximity search)

fiedname:"検索語A 検索語B"~n
* 検索語Aと検索語Bの間に最大n個の単語があるログを探します。
* 例) body:"normal cron"~2

![lcs_lucene_guide_03](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_03.png)

### 重み付け検索(Boosting)

fieldname:検索語A^n検索語B
* 一部検索キーワードに重みを用いて、結果を取得できます。
* 例) body:normal^2 cron
* 重みは、0より大きい実数のみ可能です。

デフォルトの重みは1です。

## 正規表現検索

正規表現検索が可能です。
* 例) dressまたはpressを含む文書を検索する場合は、/[dp]ress/と入力

## あいまい項目検索(fuzzy search)

fieldname:検索語~n
* 検索語と類似した、n個の文字まで他の結果を検索します。(最大2個)
* デフォルトの文字数は2です。
* 例) logSource:logncrash-logS**ur**rce~2

![lcs_lucene_guide_04](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_04.png)

## Object/Array検索

各フィールドのタイプがObjectとArrayの場合、全て文字列に置換して保存します。
次のサンプルログでは、ObjectとArrayフィールドで検索するためにワイルドカード検索機能を使用します。

```json
// サンプルログ
{
  "arrayTypedField": ["elem1", "elem2"],
  "objectTypedField": {
    "key": "value"
  }
}
```
* Object検索：`objectTypedField:*\"key\"\:\"value\"*`
* Array検索：`arrayTypedField:*\"elem1\"*`
