<!-- pre-align:aligned sig=ecb9adf37221 -->

<a id="analytics-log-crash-search-ios-sdk-guide"></a>
## Analytics > Log & Crash Search > iOS SDK 사용 가이드 { #analytics-log-crash-search-ios-sdk-guide }

> [Deprecated]
> Log & Crash iOS SDK 버전은 더 이상 지원되지 않습니다.
> [TOAST SDK](http://docs.toast.com/ko/TOAST/ko/toast-sdk/overview/)를 이용해 주시기 바랍니다.

> [공지]
> arm64e 아키텍처를 사용하는 신규기기(iPhone XS, XR, XS Max, iPad Pros 3rd)에서 발생한 크래시 로그는 발생 건수의 집계만 가능하며, 크래시 내용의 분석은 아직 지원되지 않고 있습니다.
> 이른 시일 내에 신규기기에 대한 분석 기능을 제공하도록 하겠습니다.

Log & Crash iOS SDK는 Log & Crash Search 수집 서버에 로그를 보내는 기능을 제공합니다.  
Log & Crash iOS SDK 특·장점은 다음과 같습니다.  

- 로그를 수집 서버로 보냅니다.
- 앱에서 발생한 크래시 로그를 수집 서버로 보냅니다.
- Log & Crash Search 에서 전송된 로그를 조회 및 검색이 가능합니다.
- 멀티 쓰레딩 환경에서 동작합니다.

<a id="supporting-environment"></a>
## 지원 환경 { #supporting-environment }
- iOS 8.0 이상

<a id="download"></a>
## 다운로드 { #download }

[TOAST Document](http://docs.toast.com/ko/Download/)에서 iOS SDK(native)를 받을 수 있습니다.

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [iOS SDK] 클릭
```

<a id="how-to-use-sdks"></a>
## SDK 사용방법 { #how-to-use-sdks }

<a id="add-header-files"></a>
### 헤더 파일 추가 { #add-header-files }

\#import <LogNCrashSDK/LogNCrashSDK.h\> 추가 합니다.

<a id="initialize"></a>
### 초기화 { #initialize }

```
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion;
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion forUserId:(NSString*)userId;
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion forUserId:(NSString*)userId enableSyncStart:(bool)flag;
```

- TLCLog를 초기화 합니다.
- TLCLog 기능이 제대로 동작하기 위해서는 반드시 호출되어야 합니다.
- 사용자 별 통계를 원하는 경우 userId는 반드시 입력해야 합니다.
- 파라미터
	- server: 수집서버 주소
	- appKey: 앱키
	- version: 앱 버전
	- userId: 사용자 아이디
	- enableSyncStart: true인 경우 발생한 로그들은 startSendThread가 호출되기 전까지 서버에 전송하지 않고, 큐에 저장합니다. 단 Crash가 발생한 경우 ThreadLock을 해제하고 로그를 전송합니다.
- 반환값
	- userId: 사용자 아이디
	- 실패시 false

<a id="unlock-sendthread"></a>
### SendThread 잠금 해제 { #unlock-sendthread }

```
	(void) startSendThread;
```

- SendThread의 잠금 상태를 해제합니다.

<a id="lock-host"></a>
### Host 잠금 설정 { #lock-host }

```
	(void) enableHost;
```

- true : ip address를 구하여 host 필드에 저장합니다.
- false: ip address를 구하지 않습니다.

<a id="send-logs"></a>
### 로그 보내기 { #send-logs }

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

- 지정된 로그 레벨로 수집서버에 로그를 보냅니다.
- 파라미터
	- errorCode: 에러 코드
	- message: 로그 메세지
	- location: 에러 위치

<a id="specify-custom-keys"></a>
### 커스텀 키 지정하기 { #specify-custom-keys }

```
(void) setCustomField:(NSString*)value forKey:(NSString*)key;
(void) removeAllCustomFields;
(void) removeCustomFieldForKey:(NSString*)key;
```

- 커스텀 키를 추가, 삭제, 전부 삭제 기능을 제공합니다.
- 커스텀 키로 "nil"을 설정하면 설정 값을 무시하고, 값에 "nil"을 설정하면 "-"로 설정합니다.
- 커스텀 키는 대소문자로 시작하고 대소문자, 숫자, '-', '\_'만 사용하실수 있습니다. (^[a-zA-Z][a-zA-Z0-9-_]\*$)
- 커스텀 키는 대소문자 관계없이 다음 이름은 사용하실 수 없습니다.
	- projectName, projectVersion, host, logType, logSource, sendTime, logTime, logLevel, UserID
	- Platform, DeviceModel, NetworkType, Carrier, CountryCode, DmpData, errorCode, Location, body, SessionID

<a id="remove-duplicates"></a>
### 중복 제거 모드 설정 { #remove-duplicates }

2.4.0 이상 SDK 부터 일반 로그에 중복 제거 로직이 적용되었습니다.

중복 로그 기능이 켜져있는 경우 body와 logLevel의 내용이 같은 로그가 발생하면 전송하지 않습니다.

```
public static void setLogDeduplicate(bool enable)
```

true: (Default 값) 중복 제거 로직 활성화<br>
false: 중복 제거 로직 비활성화

<a id="manage-default-setting"></a>
### 기본 설정 관리 { #manage-default-setting }

```
(void) setUserId:(NSString*)userId;
```

- 사용자 아이디를 설정 합니다.

```
(void) setLogType:(NSString*)logType;
```

- 로그 타입을 설정 합니다.

```
(void) setLogSource:(NSString*) logSource;
```

- 로그 소스를 설정 합니다.

<a id="automatically-collected-information"></a>
## 자동수집되는 정보들 { #automatically-collected-information }

아래 정보들은 Log & Crash SDK에 의해 자동으로 수집되며, Log & Crash Search 에서 확인할 수 있습니다. 로그전송 시점에 정보수집이 불가능한 경우 값을 볼 수 없는 경우가 발생할 수 있습니다.  

- iOS
	\- Platform: iOS 버전 정보
	\- DeviceModel: 아이폰 모델 정보
	\- Carrier: 사용자의 텔레콤 서비스 제공자
	\- CountryCode: 사용자의 텔레콤 서비스 제공자의 ISO 국가 코드
	\- NetworkType: Wi-Fi 또는 Cellular (로그 전송이벤트 발생 시점에 네트워크 사용이 불가한 경우 "No Connection")

<a id="interpret-ios-crashed"></a>
## iOS Crash 해석하기 { #interpret-ios-crashed }
- iOS에서 발생한 Crash의 경우 Crash 정보가 주소 값으로 수집되기 때문에 이를 해석하기 위한 Symbol 파일이 필요합니다.

- Xcode를 실행하고 Windows > Organizer를 클릭합니다.

- 빌드한 결과물을 클릭한 뒤, 오른쪽 버튼을 클릭하여 Show in Finder로 클릭합니다.
![](http://static.toastoven.net/prod_logncrash/13.png)

- 결과물을 클릭하고 오른쪽 버튼을 눌러 '패키지 내용보기'를 클릭합니다.
![](http://static.toastoven.net/prod_logncrash/14.png)

- .dSYM을 .zip으로 압축하여 웹 콘솔 > Analytic > Log & Crash Search > Settings > 심볼 파일 탭에 등록합니다.

<a id="note-for-ios-unity-crash"></a>
## iOS Unity Crash 주의 사항 { #note-for-ios-unity-crash }

- 심볼이 없어 해석되지 않은 Crash 로그는 일반 로그로 취급됩니다.
