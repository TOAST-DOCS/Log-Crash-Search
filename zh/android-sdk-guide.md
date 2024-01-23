## Analytics > Log & Crash Search > Android SDK Guide

> [Deprecated] 
> Log & Crash Android SDK is not supported any more. 
> Please use [TOAST SDK](http://docs.toast.com/en/TOAST/en/toast-sdk/overview/). 

Log & Crash Android SDK sends logs to a Log & Crash Search collector server.
Below describe benefits and features of Log & Crash Android SDK.

- Send logs to a collector server.
- Send crash logs occurred in an app to a collector server.
- Retrieve and search logs sent from Log & Crash Search.
- Operate in a multi-threading environment.

## Supporting Environment

- Android 2.3.3, API Level 10 or higher

## Download

Go to [TOAST Document](http://docs.toast.com/en/Download/) and download **Android SDK**.

```
Click [DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Android SDK]
``` 

## Install

### Configuration

Android SDK is configured as follows:

```
docs/       ; Android SDK Document
libs/       ; Android SDK Library
sample/     ; Android SDK Sample
```

### SDK Sample

Below describes sample/ provided along with SDK.

1. Copy libs/ to sample/libs/.
2. Run Eclipse and open sample/ to File > New > Android > Android Project from Existing Code.
    In AndroidStudio click File > New > Import Project.
3. Open ToastLogSample.java to modify Appkey and collector server address in onCreate (). Modifying version, log source, and log type is useful for a better search.
4. Execute.
5. Click Initialize.
6. Click debug, info, warn, error, fatal to send logs.
7. Click send crash, crash to send crash logs. send crash button sends crash logs only. crash button occurs crash by force, both to close an app and sends crash logs.

## Example

1. Copy libs/ of Android SDK to a project libs/.
2. Add authority to the AndroidManifest.xml file.
- Authority is required to bring information on network status, device, and operator.

```
<!-- Internet access authority to send logs (required) -->
<uses-permission android:name="android.permission.INTERNET" />

<!-- Authority to access device information, such as Platform and Carrier (required) -->
<uses-permission android:name="android.permission.READ_PHONE_STATE" />

<!-- Authority to access network status (required) -->
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

<!-- Authority to save logs in files when network access is unavailable (optional) -->
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<application
      ......
```

3. Send logs via ToastLog class which is provided.

- Execute ToastLog.initialize () to initialize.
- Deliver logLevel log using debug()/info()/warn()/error()/fatal() function to a collector server.
- When app crash occurs, crash logs are delivered to a collector server.

```
.....
public class MainActivity extends Activity {
  .....
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    .....
    if (ToastLog.initialize(getApplication(), "__collectorserver_address__", 0, "__appkey__", "__version__") {
      // initialization succeeds
      ToastLog.info("Init Success")
    } else {
      // initialization fails
    }
    .....
  }
}
.....
```

## API List

Describes functions provided by com.toast.android.logncrash.ToastLog class.

### Initialize

```
public static final String DEFAULT_APP_KEY = "__app_key__";
public static final String DEFAULT_VERSION = "1.0.0";
public static final String DEFAULT_COLLECTOR_ADDR = "https://api-logncrash.cloud.toast.com";
public static final int DEFAULT_COLLECTOR_PORT = 0;
public static final String DEFAULT_LOG_SOURCE = "logncrash-logSource";
public static final String DEFAULT_LOG_TYPE = "logncrash-logType";

public static boolean initialize(Application application, String collectorAddr, int collectorPort, String appKey, String version, String userId);

public static boolean initialize(Application application, String collectorAddr, int collectorPort, String appKey, String version);

public static boolean initialize(Application application, String collectorAddr, int collectorPort, String appKey, String version, boolean syncStart);
```

- Initialize ToastLog.
- Requires a call to operate ToastLog.
- Parameters
  - application: Android Application information. Apply return value of getApplication().
  - collectorAddr: Collector server address
    - HTTP collector server: https://api-logncrash.cloud.toast.com
  - collectorPort: Port information of a collector server. When it is set to 0, default port of each protocol is applied.
    - HTTP: 80
  - appKey: Appkey
  - version: App version
  - userId: User ID
  - syncStart: Save logs, which occur when it is true, in a queue, without sending them to a server, before startSendThread is called. Nevertheless, if a crash occurs, unlock ThreadLock and send the logs
- Return Value
  - true: for successful initialization
  - false: for failure

### Unlock SendThread
```
  	(void) startSendThread;
```
  - Unlock SendThread.

### Caution for Initialization
  - To execute initialization from Application onCreate
     - Calls may be unintentionally made when a receiver, a service, or an activity is created from Application onCreate.

```
public static boolean isInitialized()
```

- Return result of Initialize ToastLog
- Return value
  - true for successful initialization
  - false for failure

### Send Logs

```
public static void fatal(String message, Throwable t)
public static void error(String message, Throwable t)
public static void warn(String message, Throwable t)
public static void info(String message, Throwable t)
public static void debug(String message, Throwable t)

public static void fatal(String message)
public static void error(String message)
public static void warn(String message)
public static void info(String message)
public static void debug(String message)
```

- Send logs to collector server by specified log level.
- Parameters
  - message: Log messages. With null or "", default messages are sent.
  - Throwable t: Send collected errors altogether. Can use null.
    - Classified as handled logs, when errors are sent altogether

```
public static void crash(String message, Throwable t)
```

- Send exception logs to a collector server during execution.
- Parameters
  - message: Log messages. With null or "", default messages are sent.
  - Throwable t: Sends collected errors altogether.
    - Classified as crash log, when errors are sent altogether.

With enhanced functions to investigate information such as errorCode and location in Throwable, following methods **have been deprecated**.

```
public static void fatal(String errorCode, String message, String location)
public static void error(String errorCode, String message, String location)
public static void warn(String errorCode, String message, String location)
public static void info(String errorCode, String message, String location)
public static void debug(String errorCode, String message, String location)

public static void fatal(String errorCode, String message)
public static void error(String errorCode, String message)
public static void warn(String errorCode, String message)
public static void info(String errorCode, String message)
public static void debug(String errorCode, String message)

public static void crash(Throwable throwable, String errorCode, String message, String location)
public static void crash(Throwable throwable, String errorCode, String message)
```

### Specify Custom Keys

```
public static void addCustomField(String key, String value)

public static void removeCustomField(String key)

public static void clearCustomFields()
```
- Provides Add, Delete, or Delete All custom keys.
- The custom key must start with an upper or lower-case alphabet: only alphabets, numbers, and '-', '_' can be used. ( [A-Za-z][A-Za-z0-9*-]* )
- Following names cannot be used, irrespective of upper or lower case:
  - "projectName", "projectVersion", "body"
  - "logSource", "logType", "host", "sendTime", "logLevel"
  - "DmpData", "Platform", "NeloSDK", "Exception", "Location", "Cause"
  - "SessionID", "UserID"
  - "Carrier", "CountyCode", "DeviceModel", "Locale", "NetworkType", "Rooted"

### Manage Default Setting

```
public static String getAppKey()
public static String getVersion()
public static String getCollectorAddr()
public static int getCollectorPort()
```

- Get Appkey, version, collector server address, and collector server port, which are used for initialization

```
public static String getLogSource()
public static void setLogSource(String logSource)
```

- Get or newly specify a log source.

```
public static String getLogType()
public static void setLogType(String logType)
```

- Get or newly specify a log type.

### Remove Duplicates

The Remove Duplicates logic has been applied to general logs for 2.4.0 or higher SDKs.

When duplicate logging is enabled, do not send logs that have the same content in the body and logLevel.

```
public static void setDuplicate(bool enable)
```

true: (Default) Remove duplicates is enabled <br>
false: Remove duplicates is disabled

## Test Proguard Using SDK Samples

Describes how to test code obfuscation using Proguard which Android provides.
To apply Proguard, a project needs to be created by Release. Sample/ has required keystore and Proguard configuration.

### Test Proguard with Eclipse

1. Copy libs/ to sample/libs/.
2. Run Eclipse to select a project, and select File - Export... .
3. In Export select Android - Export Android Application.
4. Check the project name, click Next and select mykey.keystore of Keystore: password of Keystore is set 'abcdefg', Alias ID is 'mykey' and Alias password is 'abcdefg'. For details, refer to [Link](http://developer.android.com/tools/publishing/app-signing.html).
5. Specify a path to save.
6. Install the saved .apk file on the device as 'adb install <file name>'.
7. Execute, click Initialize, click crash to occur crash logs. This my not properly work in sample because ToastLogSample.clickCrash() is obfuscated.

Under normal operations of Proguard, you will find proguard/mapping.txt created.

1. In the left navigation menu click **Analytics > Log & Crash > Settings** then click **Select File** on the **Symbol File** tab to upload mapping.txt. Note that the project version should be consistent.
2. Send crash logs from app. You can check logs unleashed with Proguard in View of the DmpData field.
3. See if ToastLogSample.clickCrash() is named correctly.


### Test Proguard with Ant Build
1. Copy libs/ to sample/libs/.
2. In case build.xml is not available, create one with android update project -p . -n AndroidSDKSample.
3. Build by ant clean release. The result will be saved as AndroidSDKSampe-release.apk in bin/.

In case of Release builds using Ant, unlike Eclipse Release builds, mapping.txt is located in bin/proguard/mapping.txt.  

### Test Proguard with AndroidStudio

1. Copy libs/ to sample/libs/.
2. Run AndroidStudio and execute File - New - Import Project..., so that an AndroidStudio project can be created at a new location.
3. Copy mykey.keystore from an existing source to a new project.
4. Execute **Build > Generate Signed APK**. On page 1, check the module and click **Next**. On Page 2, specify mykey.keystore for the keystore path; set ‘abcdefg’ as the **Keystore password**, ‘mykey’ as **Alias ID**, and ‘abcdefg’ as **Alias password**, and click **Next**. On page 3, check **APK Destination Folder** and click **Finish**.
5. Install .apk which has been created.  

With Release build using AndroidStudio, the mapping.txt shall be located in app/build/outputs/mapping/release/mapping.txt.

## Guide for JNI Application

Here is how to use Android SDK by using [JNI](http://en.wikipedia.org/wiki/Java_Native_Interface) of [Android NDK](http://developer.android.com/tools/sdk/ndk/index.html). Log & Crash Android SDK can normally operate only when catch-exception is available in Java. To that end, a class is required in Native Code to deliver errors when they occur. For instance, if an error occurs in the process of executing getString function, catch an error by using the try/catch phrase, create an exception with Java code, and throw it. Then, Java will catch and send the error to log.

**JNI code**

```
.....
// Native Code
void throwArithmeticError(JNIEnv *env, char *message) {
  jclass exClass;
  char *className = "java/lang/ArithmeticException: / by zero";
  exClass = (*env)->FindClass(env, className);
  if (exClass == NULL) {
    return throwNoClassDefError(env, className);
  }
  (*env)->ThrowNew(env, exClass, message);
}

JNIEXPORT JNICALL jstring Java_com_example_jnitest_HelloJNI_getString(JNIEnv *env, jobject object) {
  if (...) {
    return str;
  } else {
    //On Error
    throwArithmeticError(env, "THIS IS JNI EXCEPTION");
  }
}
.....

**Java Code**

.....
// Java Code
try{
  mTextView.setText( new HelloJNI().getString() );
} catch(Throwable e){
  ToastLog.crash(e, "JNI ERROR", "JNI ERROR MESSAGE - Throw Error");
}
.....
```

In order to catch a system crash, like Segment Fault, add a phrase to catch signal in the Native Code.
Refer to [Link](http://stackoverflow.com/questions/1083154/how-can-i-catch-sigsegv-segmentation-fault-and-get-a-stack-trace-under-jni-on) to create an example as below.

**Native Code**

```
.....
//Native Code
JNIEXPORT jint android_sigaction(int signal, siginfo_t *info, void *reserved) {
  old_sa[signal].sa_handler(signal);
  return throwNoClassDefError(env, "THIS android_sigaction ACTION");
}

JNIEXPORT jint JNICALL JNI_OnLoad(JavaVM *jvm, void *reserved) {
  jclass cls, vcls;
  if ((*jvm)->GetEnv(jvm, (void **) &env, JNI_VERSION_1_2))
    return JNI_ERR;

  // Try to catch crashes...
  struct sigaction handler;
  memset(&handler, 0, sizeof(sigaction));
  handler.sa_sigaction = android_sigaction;
  handler.sa_flags = SA_RESETHAND;

  #define CATCHSIG(X) sigaction(X, &handler, &old_sa[X])
  CATCHSIG(SIGILL);
  CATCHSIG(SIGABRT);
  CATCHSIG(SIGBUS);
  CATCHSIG(SIGFPE);
  CATCHSIG(SIGSEGV);
  CATCHSIG(SIGSTKFLT);
  CATCHSIG(SIGPIPE);

  return JNI_VERSION_1_2;
}

jint throwNoClassDefError(JNIEnv *env, char *message) {
  jclass exClass;
  char *className = "java/lang/NoClassDefFoundError";

  exClass = (*env)->FindClass(env, className);
  if (exClass == NULL) {
    return throwNoClassDefError(env, className);
  }
  (*env)->ThrowNew(env, exClass, message);
}
.....
```
