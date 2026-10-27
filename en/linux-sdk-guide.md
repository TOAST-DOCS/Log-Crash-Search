<!-- pre-align:aligned sig=91e5466c6a88 -->

<a id="analytics-log-crash-search-linux-sdk-guide"></a>
## Analytics > Log & Crash Search > Linux SDK Guide { #analytics-log-crash-search-linux-sdk-guide }

Log & Crash C++ Linux SDK provides a feature to send logs to the Log & Crash Search collector server.
The features and advantages of Log & Crash C++ Linux SDK are as follows.

- Sends logs to the collector server.
- The C++ Linux SDK does not collect crash logs.
- You can search and query logs sent from Log & Crash Search.
- Operates in a multi-threading environment.

<a id="section-1"></a>
## Supported Environment { #section-1 }

- Linux, glibc >= 2.0.0
- 32bit/64bit
- openssl >= 1.0.1e
- libcurl >= 7.19.7

<a id="section-2"></a>
## Download { #section-2 }

You can download the C++ Linux SDK from Toast Cloud.

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Linux SDK] 클릭
```

<a id="section-3"></a>
## Installation { #section-3 }

<a id="section-3-1"></a>
### Configuration { #section-3-1 }

The C++ Linux SDK consists of the following:

```
...
docs/					; C++ Linux SDK 문서
include/toast/logncrash.h	; C++ 해더 파일
linux-sdk/lib32/			; C++ Linux 32bit 라이브러리
linux-sdk/lib64/			; C++ Linux 64bit 라이브러리
linux-sdk-sample/			; C++ Linux SDK 샘플
...
```

<a id="sdk"></a>
### SDK Sample { #sdk }

This document describes the linux-sdk-sample/ that is provided with the SDK.

1. Go to the linux-sdk-sample/ directory.
2. Open sample.cpp and replace the app key with the one you issued.
3. Run make to generate the sample-x86 and sample-x86_64 executables.
	- To build individually, use the `make sample-x86` or `make sample-x86_64` commands.
	- For x86 and x86_64 build environments, see the documentation for the Linux distribution you are using.
4. Execute the built executable file.

<a id="section-4"></a>
## Example of Use { #section-4 }

1. Add include/toast/ to the include path.
2. Link lib32/liblogncrash_linux.a or lib64/liblogncrash_linux.a depending on the target.
	- liblogncrash_linux.a uses pthread internally. Therefore, the -lpthread link option is required.
3. Include toast/logncrash.h and use the ToastLog class.

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

<a id="api-list"></a>
## API List { #api-list }

Describes the features provided by the toast::logncrash::ToastLog class.

<a id="toastlog"></a>
### Allocate/Deallocate ToastLog Instance { #toastlog }

```
toast::logncrash::ToastLog* GetToastLog();

void DestroyToastLog();
```

- ToastLog instance를 할당하고 해제합니다.
- Only a single instance is returned in the singleton manner.
- Do not call delete on the returned ToastLog instance. To remove it, you must call DestroyToastLog().

<a id="api-list-1"></a>
### Initialize/Release { #api-list-1 }

```
#define LOGNCRASH_VERSION         "1.0.0"
#define LOGNCRASH_COLLECTOR_ADDR  "https://api-logncrash.nhncloudservice.com"
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

- Initializes and releases ToastLog.
- To operate the ToastLog feature normally, initialize() must be called.
- Parameter
	- appKey: Appkey
	- version: App Version
	- collectorAddr: Collector server address
		- Log & Crash collection server: https://api-logncrash.nhncloudservice.com
	- collectorPort: Collector server port
	- logSource: Log Source
	- logType: Log type
- initialize() return values
	- LOGNCRASH_LOG_OK: 0, initialization successful
	- LOGNCRASH_LOG_ERROR: -1, internal error code
	- LOGNCRASH_LOG_ERROR_APPKEY: -2, Invalid appkey
	- LOGNCRASH_LOG_ERROR_VERSION: -3, When the version is invalid
	- LOGNCRASH_LOG_ERROR_ADDRESS: -4, when the collector server address is invalid
	- LOGNCRASH_LOG_ERROR_PORT: -5, when the collector server port is invalid

<a id="api-list-2"></a>
### Send Logs { #api-list-2 }

```
bool sendLog(
    const LogNCrashLogLevel logLevel,
    const char* message,
    const char* errorCode = NULL,
    const char* location = NULL);
```

- Sends logs at the specified logLevel.
- Parameter
	- logLevel: The logLevel to send. A logLevel greater than the logLevel set by setLogLevel() is not sent.
	- message: Message to be sent
	- errorCode: Error code. If NULL or "" is used, it is not sent.
	- location: Error location. If NULL or "" is used, it will not be sent.
- Return values
	- true on success
	- Returns false if logLevel is too large or message is empty
- Note
	- setLogLevel(), getLogLevel();

```
bool debug(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool info(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool warn(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool error(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool fatal(const char* message, const char* errorCode = NULL, const char* location = NULL);
```

- Sends logs at the defined DEBUG, INFO, WARN, ERROR, and FATAL levels.
- Same as sendLog() except that the logLevel is fixed.
- Return values
	- true on success
	- false if logLevel is too large or message is empty

<a id="api-list-3"></a>
### Set Log Level { #api-list-3 }

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

- Retrieves or sets the logLevel of the ToastLog instance.
- The default value of ToastLog is LOGNCRASH_INFO. Therefore, to use the debug() function, you must set setLogLevel(LOGNCRASH_DEBUG).

<a id="api-list-4"></a>
### Set Custom Keys { #api-list-4 }

```
bool addCustomKey(const char* key, const char* value);

void removeCustomKey(const char* key);

void clearCustomKeys();
```

- Provides features to add, delete, and delete all custom keys.
- Custom keys must start with an uppercase or lowercase letter, and can only contain uppercase and lowercase letters, numbers, '-', and '\_'. ( [A-Za-z][A-Za-z0-9-_]* )
- Custom keys can be up to 64 characters.
- The following names cannot be used for custom keys, regardless of case.
	- projectname, projectversion, host, body, logsource, logtype
	- logType, sendTime, logLevel, userId, platform
	- dmpdata, dmpreport
- Return value of addCustomKey()
	- true on success
	- false if the key format is invalid and the addition fails

<a id="api-list-5"></a>
### Process Crashes { #api-list-5 }

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

bool openCrashCatcher(const bool bBackground = false, const LogNCrashLangType langType = LOGNCRASH_LANG_DEFAULT);
void closeCrashCatcher();

void setCrashCallback(const LogNCrashCallbackType cb, void* cbData = NULL);
```

- Linux SDK does not collect crash logs.
- Return value of openCrashCatcher
	- Always false

<a id="api-list-6"></a>
### Other Settings { #api-list-6 }

```
const char* getUserId();

void setUserId(const char* userId);
```

- Gets or sets the user ID.

```
void enableHostField();

void disableHostField();
```

- Enable or disable the Host field.

```
void enablePlatformField();

void disablePlatformField();
```

- Enable or disable the Platform field.
