## Analytics > Log & Crash Search > C++ Windows SDK Guide

> [Deprecated]
> Log & Crash C++ Windows SDK is not supported any more.
> Please use [TOAST SDK](http://docs.toast.com/en/TOAST/en/toast-sdk/overview/). 

Log & Crash C++Windows SDK sends logs to a Log & Crash Search collector server.
Below describe benefits and features of Log & Crash C++ Windows SDK.

  - Send logs to a collector server.
  - Send crash logs occurred in an app to a collector server.
  - Retrieve and search logs sent from Log & Crash Search.
  - Operate under a multi-threading environment.

## Supporting Environment

- Windows 2000, Windows Vista, Windows XP, Windows 2003, Windows 2008, Windows 7, Windows 8
- 32bit/64bit

## Download

Go to [TOAST Document](http://docs.toast.com/en/Download/) and download **C++ Windows SDK**.

```
Click [DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Windows SDK]
``` 

## Install

### Configuration

C# Windows SDK is configured as below.

```
docs/						; C++ Windows SDK Document
include/					; C++ Windows SDK Example
include/toast/logncrash.h	; C++ Header File
windows-sdk/lib32/			; C++ Windows 32bit Library
windows-sdk/lib64/			; C++ Windows 64bit Library
windows-sdk-sample/			; Sample Project for VS 2010
```

### SDK Sample

Below describes sample/ provided along with SDK.

1. Run Microsoft Visual Studio 2010 and open windows-sdk-sample/Sample.sln.
2. Open Sample.cpp and update with issued Appkey.
3. Copy *.dll required for app execution to an execution file directory, depending on 32bit/64bit and Debug/Release.
4. Execute.

## Example

1. Add include/toast/ to the include path.
2. Specify an import library as below, depending on 32bit/64bit and Debug/Release.
	- 32bit, Debug: lib32/liblogncrashD.lib
	- 32bit, Release: lib32/liblogncrash.lib
	- 64bit, Debug: lib64/liblogncrashD.lib
	- 64bit, Release: lib64/liblogncrash.lib
3. Copy *.dll to the execution file directory for app execution.
4. Include toast/logncrash.h and use ToastLog class.

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

## API List

Below describe functions provided by toast::logncrash::ToastLog class.

### Assign/Destroy

```
toast::logncrash::ToastLog* GetToastLog();

void DestroyToastLog();
```

- Assign ToastLog instance, then destroy it.
- With a single-tone method, only one instance is returned.
- Do not delete a returned ToastLog instance; it is required to call DestroyToastLog() to delete it.


### Initialize/Destroy

```
#define LOGNCRASH_VERSION         "1.0.0"
#define LOGNCRASH_COLLECTOR_ADDR  "api-logncrash.nhncloudservice.com"
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

- Initialize ToastLog, then destroy it.
- Requires a call of Initialize() to operate ToastLog.
- Parameters
  - appKey: Appkey
  - version: App version
  - collectorAddr: Collector server address
    - Log&Crash collector server: api-logncrash.nhncloudservice.com
  - collectorPort: Collector server port
  - logSource: Log source
  - logType: Log type
- Return value of initialize()
  - LOGNCRASH\_LOG\_OK: 0, Succeeded to initialize
  - LOGNCRASH\_LOG\_ERROR: -1, Internal error code
  - LOGNCRASH\_LOG\_ERROR\_APPKEY: -2, Error in Appkey
  - LOGNCRASH\_LOG\_ERROR\_VERSION: -3, Error in version
  - LOGNCRASH\_LOG\_ERROR\_ADDRESS: -4, Error in collector server address
  - LOGNCRASH\_LOG\_ERROR\_PORT: -5, Error in collector server port

### Send Logs

```
bool sendLog(
    const LogNCrashLogLevel logLevel,
    const char* message,
    const char* errorCode = NULL,
    const char* location = NULL);
```

- Send logs to a specified logLevel.
- Parameters
  - logLevel: A logLevel to send. Cannot send a logLevel that is higher than what has been specified as setLogLevel().
  - message: Messages to send
  - errorCode: Error code. Cannot send with NULL or \"\".
  - location: Error location. Cannot send with NULL or \"\".
- Return value
  - True if successful
  - False if a logLevel is high or message is empty
- Note
  - setLogLevel(), getLogLevel();

```
bool debug(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool info(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool warn(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool error(const char* message, const char* errorCode = NULL, const char* location = NULL);

bool fatal(const char* message, const char* errorCode = NULL, const char* location = NULL);
```

- Send specified DEBUG, INFO, WARN, ERROR, or FATAL logs.
- Same as sendLog(), except that it has a fixed logLevel.
- Return value
  - true: if successful
  - false: if logLevel is high or message is empty

### Specify Log Levels

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

- Get or specify a logLevel of ToastLog instance.

- The default of ToastLog is LOGNCRASH\_INFO. Therefore, to use debug() function, it should be set to setLogLevel(LOGNCRASH\_DEBUG).

### Specify Custom Keys

```
bool addCustomKey(const char* key, const char* value);

void removeCustomKey(const char* key);

void clearCustomKeys();
```

- Provide functions such as to Add, Delete, or Delete All custom keys.
- A custom key starts with an upper or lower-case alphabet: only alphabets, numbers, \'-\', and \'\' can be used (\[A-Za-z\]\[A-Za-z0-9-\]\*).
- A custom key cannot have more than 64 characters.
- Following names cannot be used, irrespective of upper or lower cases:
  - projectname, projectversion, host, body, logsource, and logtype
  - logType, sendTime, logLevel, userId, and platform
  - dmpdata and dmpreport
- Return value of addCustomKey()
  - true: if successful
  - false: if the key format is wrong

### Process Crashed

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

bool openCrashCatcher();
void closeCrashCatcher();

void setCrashCallback(const LogNCrashCallbackType cb, void* cbData = NULL);
```

- Start or end processing crashes.

### Remove Duplicates

- When duplicate logging is enabled, do not send any same logs that occur in the body and logLevel.

```
public static void setDuplicate(bool enable)
```
- true: (Default) Remove duplicates is enabled.
- false: Remove duplicates is disabled.

### Other Settings

```
const char* getUserId();

void setUserId(const char* userId);
```

- Get or specify a user ID.

## Guide to Create Symbol Files

- To interpret crashes occurred in Log & Crash Windows SDK, symbol files need to be created and uploaded to Console.

### Requirements

- Use dump\_syms which fits for the VS. ( VC\_1500 = 2008, VC\_1600 = 2010 )
- Download for [VS 2008 or lower](https://github.com/zpao/v8monkey/blob/master/toolkit/crashreporter/tools/win32/dump_syms_vc1500.exe)
- Download for [VS 2010 or higher](http://hg.mozilla.org/mozilla-central/file/tip/toolkit/crashreporter/tools/win32)
- [minidump\_stackwalk.exe](http://hg.mozilla.org/build/tools/raw-file/755e58ebc9d4/breakpad/win32/minidump_stackwalk.exe)

### Create Symbol Files
- Get debugging information for windows crash dumps, by converting .pdb files to .sym symbol files.

- How to convert .pdb files to .sym files:

    - Create.pdb files (to be created with a project build)
    - Download dump\_syms.exe.
    - Execute dump.syms as below, to create symbol files (it is deemed successful if no error has occurred).
      - If an error has occurred, like CoCreateInstance CLSID\_DiaSource failed (msdia\*.dll unregistered?), copy its dll to c:\\Program Files\\Common Files\\Microsoft Shared\\VC.
      - Register dll by order of regsvr32.

       ```
      regsvr32 c:\Program Files\Common Files\Microsoft Shared\VC\msdia80.dll.
       ```

        - If 0x80004005 has occurred, retry at manager's authority.
        ```
      'dump_syms {.pdb file} > {File ouput}'
      'dump_syms Sample.pdb > Sample.sym'
        ```

        - Upload symbol files that are created to the Web Console.
