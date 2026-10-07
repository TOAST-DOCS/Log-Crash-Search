## Data & Analytics > Log & Crash Search > Lucene 쿼리 가이드

## 기본 주의 사항

큰따옴표(")와 물결표(~)의 위치 또는 유무에 따라 연산자로 사용될지, 아닐지가 결정됩니다.
* ex) 근접 검색과 유사항목 검색
  또한 기본적으로 검색어가 큰따옴표(")로 감싸지면 감싸진 부분 전체를 하나의 구문으로 검색합니다.
* ex) body:normal AND performance
* -> body 필드의 값에 "normal"와 "performance" 단어가 있는 로그를 검색

![lcs_lucene_guide_01](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_01.png)

* ex) body:"normal AND performance"
* -> body 필드의 값에 "normal AND performance" 단어가 있는 로그를 검색

![lcs_lucene_guide_02](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_02.png)
* "normal AND performance" 에 해당하는 텍스트가 없어서 로그 결과가 0

검색 시 특수문자를 사용할 때는 문자 앞에 백슬래시 또는 \를 입력하여 이스케이프 처리합니다.
* 대상 특수문자 : ```+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /```

URL에서의 특수문자(예약문자 포함)들은 인코딩하여 처리하여야합니다.
* ex) '<' 특수문자는 %3C로 입력

## 기본 검색

filedname:search word
* 원하는 단일 필드(fieldname)에서 단일 용어(search word)를 검색합니다.

필드명이 log.type, log.time 또는 log.version같은 형태라면 아래와 같이 일괄 검색이 가능합니다.
* ex) log.*:alpha

\_missing\_:fieldname
* 해당 fieldname에 값이 없거나(null) 해당 fieldname을 지니지 않은 로그를 검색합니다.

\_exists\_:fieldname
* 해당 fieldname에 non-null인 값을 가진 로그를 검색합니다.

## 범위 검색

| 문법 | 동작 |
| --- | --- |
| [min TO max] | min, max 조건 포함 검색 |
| {min TO max} | min, max 조건 제외 검색 |
| {min TO max], [min TO max} | 조건 포함 또는 제외 혼용 |
| {* TO max}, {min TO *} | min 또는 max 조건만 사용 |

* 아래와 같이 좀 더 심플하게 조건을 넣을 수 있습니다.
  * ex) fieldname:>10

## 부울 연산자

| 연산자 | 의미 |
| --- | --- |
| AND, &&, + | AND 연산자 |
| OR\, \|\| | OR 연산자 |
| NOT, !, - | NOT 연산자 |

NOT 연산자인 `-`의 경우 AND NOT의 의미로도 사용됩니다.
* ex) logType:bulk -api
* -> logType:bulk AND NOT api 와 동일

## 와일드카드 검색

*는 하나 이상의 임의의 문자들에 대응하여 검색합니다.
* ex) body:performance\*
* performance로 시작하는 body가 있는 로그를 검색합니다.

?는 하나의 임의의 문자만 대응하여 검색합니다.
* ex) body:performance?
* performance로 시작하며 임의의 한 글자까지만 대응하는 body필드가 있는 로그를 검색합니다.

*와 ? 두 와일드카드는 글자 가운데에 적용하는 것이 가능합니다.

## 근접 검색(Proximity search)

fiedname:"검색어A 검색어B"~n
* 검색어A와 검색어B 사이에 최대 n개의 단어가 들어간 로그를 찾습니다.
* ex) body:"normal cron"~2

![lcs_lucene_guide_03](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_03.png)

### 우선순위 부여 검색(Boosting)

fieldname:검색어A^n 검색어B
* 일부 검색 키워드에 가중치를 두어 더 높은 순위로 결과를 가져올 수 있습니다.
* ex) body:normal^2 cron
* 가중치는 0보다 커야하며 1보다 작은 숫자도 가능합니다.

default 가중치는 1입니다.

## 정규식 검색

일반적으로 알려진 정규식 검색이 가능합니다.
* ex) dress 또는 press을 포함하는 문서를 찾으려면 /[dp]ress/ 로 작성

## 유사항목 검색(Fuzzy search)

fieldname:검색어~n
* 검색어와 유사한, n개의 글자까지 다른 결과까지 검색합니다.(최대 2개)
* default 글자수는 2입니다.
* ex) logSource:logncrash-logS**ur**rce~2

![lcs_lucene_guide_04](https://static.toastoven.net/prod_logncrash/lcs_lucene_guide_04.png)

## Object/Array 검색

각 필드의 타입이 Object와 Array일 경우 모두 문자열로 치환하여 저장합니다.
다음의 예제 로그 중 Object와 Array 필드로 검색하기 위해 와일드카드 검색 기능을 사용합니다.

```json
// 예제 로그
{
  "arrayTypedField": ["elem1", "elem2"],
  "objectTypedField": {
    "key": "value"
  }
}
```
* Object 검색: `objectTypedField:*\"key\"\:\"value\"*`
* Array 검색: `arrayTypedField:*\"elem1\"*`
