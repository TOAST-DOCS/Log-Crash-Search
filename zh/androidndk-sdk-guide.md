## Analytics > Log & Crash Search > AndroidNDK SDK Guide

Log & Crash Android SDK sends logs to a Log & Crash Search collector server.
Below describe benefits and features of Log & Crash Android SDK.

- Send logs to a collector server.
- Send crash logs occurred in an app to a collector server.
- Retrieve and search logs sent from Log & Crash Search.
- Operate in a multi-threading environment.

## Supporting Environment

- Android 2.3.3. API Level 10 or higher
- Most updated AndroidNDK version recommended
- Supportive ABI : armeabi, armeabi-v7a, x86

## Download

Go to [TOAST Document](http://docs.toast.com/en/Download/) to download **AndroidNDK SDK**.

```
Click [DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [AndroidNDK SDK]
``` 

## Install

### Configuration

Android SDK is configured as follows:

```
docs/                       ; AndroidNDK SDK Document
include/toast/logncrash.h   ; C++ Header File
androidndk-sdk/
    obj/                    ; AndroidNDK SDK Static Library
    PrebuiltStaticLib.mk    ; Static Library .mk file
    libs/                   ; AndroidNDK SDK Shared Library
    PrebuiltSharedLib.mk    ; Shared Library .mk file
androidndk-sdk-sample/      ; Android JNI Sample
```

### SDK Sample

Below describe androidndk-sdk-sample/ provided along with SDK.

1. Go to androidndk-sdk-sample/.
2. Build NDK with <ndk_path>/ndk-build.
3. Import an Android project into Eclipse.
4. Open AndroidNDKSample.java and update Appkey information.
5. Execute.

To view crash logs at Log & Crash Search, symbol files must be uploaded.

1. Compress obj/local//liblogncrashjni_sample.so into .zip.
2. Upload the Zip file to **Analytics > Log & Crash Search > Settings > Symbol Files** in the TOAST Cloud console. Note the project version should be same as androidndk-sdk-sample.
3. Execute androidndk-sdk-sample to create crash logs.
   - Click **Initialize** and **Test2** to occur crashes.
   - Check if crash logs have arrived at **Analytics > Log & Crash Search > Log Search** in the TOAST Cloud console and click **View** in the **DmpData** field to confirm.
4. If **View** of **DmpData** doesn’t open:
   - Check if the project version is different between a crash log and a symbol.
   - Check if there is an error in the symbol file.

## Example

1.Add below to jni/Application.mk.

```
 ...
 APP_ABI := armeabi armeabi-v7a x86
 APP_PLATFORM := android-9
 APP_STL := gnustl_static
 ...
```

2.Add below to jni/Android.mk.

```
...
 LOCAL_STATIC_LIBRARIES := logncrash_androidndk_static
 ...
 include $(LOCAL_PATH)/<androidndk_sdk_path>/androidndk-sdk/PrebuiltStaticLib.mk
```

- To use shared library, declare as below.

```
...
  LOCAL_SHARED_LIBRARIES := logncrash_androidndk
  ...
  include $(LOCAL_PATH)/<androidndk_sdk_path>/androidndk-sdk/PrebuiltSharedLib.mk
```

3.Include toast/logncrash.h to use ToastLog class.

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

4.Build NDK with <ndk_path>/ndk-build.

5.To operate AndroidNDK SDK normally, add permissions as below to AndroidManifest.xml.

```
...
 <uses-permission android:name="android.permission.INTERNET"/>
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
 <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
 ...
```

6.Import JNI library.

```
static {
     System.loadLibrary("logncrashjni_sample");
 }
```

- To use shared library, liblogncrash_androidndk.so should be imported in advance.

```
static {
      System.loadLibrary("logncrash_androidndk");
      System.loadLibrary("logncrashjni_sample");
  }
```

Java Exception cannot be processed in AndroidNDK SDK because it has been made for C++ Native Code.
Please refer to documents (/docs/) included to the androidndk-sdk-sample/jni/Android.mk file and AndroidNDK.

## API List

Below describe functions provided by toast::logncrash::ToastLog class.

### Assign/Destroy ToastLog Instances

```
toast::logncrash::ToastLog* GetToastLog();

void DestroyToastLog();
```

- Assign ToastLog instance, then destroy it.
- With a single-tone method, only one instance is returned.  
- Do not delete a returned ToastLog instance: it is required to call DestroyToastLog() to delete it.

### Initialize/Destroy

```
#define LOGNCRASH_VERSION         "1.0.0"
#define LOGNCRASH_COLLECTOR_ADDR  "api-logncrash.cloud.toast.com"
#define LOGNCRASH_COLLECTOR_PORT  80
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

- Initialize ToastLog and destroy it.
- Requires a call of initialize() to operate ToastLog.
- Parameters
  - appKey: Appkey
  - version: App version
  - collectorAddr: Collector server address
    - Log & Crash collector server: api-logncrash.cloud.toast.com
  - collectorPort: Collector server port
  - logSource: Log source
  - logType: Log type
  - clientHost: Get a host
    - true: getting a host from client by using ioctl function
    - false: for getting a host with a value delivered from a server.
  - asyncStart: Start when SendThread is locked. If a log occurs while locked, save in queue and wait, without sending to server. Unlock, if a crash occurs or the StartSendThread function is executed.
- Return Value of initialize()
  - LOGNCRASH_LOG_OK: 0, Succeeded to initialize ment
  - LOGNCRASH_LOG_ERROR: -1, Internal error code
  - LOGNCRASH_LOG_ERROR_APPKEY: -2, Error in Appkey
  - LOGNCRASH_LOG_ERROR_VERSION: -3, Error in version
  - LOGNCRASH_LOG_ERROR_ADDRESS: -4, Error in collector server address
  - LOGNCRASH_LOG_ERROR_PORT: -5, Error in collector server port

### Unlock SendThread

```
  	void StartSendThread();
```

  - Change the status of SendThread to deliverable.

### Send Logs

```
bool sendLog(
    const LogNCrashLogLevel logLevel,
    const char* message,
    const char* errorCode = NULL,
    const char* location = NULL);
```

- Send logs to a specified loglevel.
- Parameters
  - logLevel: A logLevel to send. A logLevel that is higher than what has been specified as setLogLevel() cannot be sent.
  - message: Messages to send
  - errorCode: Error code. Cannot send with NULL or "".
  - location: Error location. Cannot send with NULL or "".
- Return Value
  - true: if successful
  - false: if a logLevel is high or message is empty
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
- Return Value
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

- Get or newly specify a logLevel of a ToastLog instance.
- The default of ToastLog is LOGNCRASH_INFO. Therefore, to use debug () function, it should be set as setLogLevel(LOGNCRASH_DEBUG).

### Specify Custom Keys

```
bool addCustomKey(const char* key, const char* value);

void removeCustomKey(const char* key);

void clearCustomKeys();
```

- Provide Add, Delete, or Delete All custom keys.
- The custom key must start with an upper or lower-case alphabet: only alphabets, numbers, and '-', '_' can be used. (A-Z a-z 0-9_-* )
- The custom key cannot have more than 64 characters.
- Following names cannot be used, irrespective of upper or lower case:
  - projectname, projectversion, host, body, logsource, or logtype
  - logType, sendTime, logLevel, userId, or platform
  - dmpdata or dmpreport
- Return Value of addCustomKey()
  - true: if successful
  - false: if a wrong key format is adde

### Specify Host Types

```
bool setHostMode(int mode);
```

- For Mode 0: Get a private IP to fill up the host field. If it fails, fill the host field with public IP.
- For Mode 1: Fill the host field with public IP.

### Process Crashes

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

- Start or end processing crashes.
- openCrashCatcher Parameter
  - bBackground : Set an operating method of a crash reporter
  - langType : Set the GUI language of a crash reporter
- Return Value of openCrashCatcher
  - True if setting is successful
  - False if setting fails
  - AndroidNDK SDK needs /sdcard directory on devices. If the directory is not available on a device, it may not operate properly.

### Remove Duplicates
  - Remove Duplicates has been applied to general logs for 2.4.0 or higher SDKs.
  - When duplicate logging is enabled, do not send logs that have the same content in the body and logLevel.

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

```
void enableHostField();

void disableHostField();
```

- Enable or disable the host field.

```
void enablePlatformField();

void disablePlatformField();
```

- Enable or disable the platform field.
