## Analytics > Log & Crash Search > Unity WebGL SDK User Guide

Log & Crash Unity SDK sends logs to a Log & Crash Search collector server.

Below describe benefits and features of Log & Crash Unity SDK.
- Send logs to a collector server.
- Send crash logs occurred in an app to a collector server.
- Retrieve and search logs sent from Log & Crash Search.

## Supporting Environment

- Common
  \- Unity3D v4.0 or higher

## Download

Go to [TOAST Document](http://docs.toast.com/en/Download/) and download **Unity SDK**.

```
[DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [Unity SDK] 
```

## Install

* Double-click downloaded toast-logncrash-android-unity-sdk.unitypackage and import it to your project.


### Sample Description

To execute the sample, double-click **Assets > LogNCrash > Sample > SampleScene**.
The sample describes examples of initialization, log delivery, and error occurrence.

## Example

1. Initialize with LogNCrashSettings

Select **LogNCrash > Edit Settings** in the Unity menu to create LogNCrashSettings. Use AssetDatabase to define user’s Appkey and SDK operations of LogNCrashSettings.

- Appkey: User’s Appkey
- URL: Collector address: use https://api-logncrash.nhncloudservice.com.
- Version: Log version
- Send Warning: Whether to collect warning logs occurred in Unity
- Send Error: Whether to collect error logs occurred in Unity
- Send Debug Warning: Whether to collect warning logs occurred by user’s debug object use in Unity
- Send Debug Error: Whether to collect error logs occurred by user’s debug object use in Unity
- PLCrashreporter Enable: PLCrashrepoter refers to a library added to detect crashes occurred in the native area. To be applied only when you want to detect native crashes.

Enter information for LogNCrashSettings and call Initialize function that has no LogNCrash object parameter, in order to initialize by reading such information of LogNCrashSettings.

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

2. Initialize with Script
Enter parameters to LogNCrash.Initialize to initialize. The parameters provide information on server address, Appkey, version, port, and whether to execute Send Thread Lock.

```
using Toast.LogNCrash;
namespace Toast.LogNCrash
{
	public class SampleScript : MonoBehaviour
	{
		void Start ()
		{
			LogNCrash.Initialize ("https://api-logncrash.nhncloudservice.com", "appkey", "1.0.0", 443,  true);
			LogNCrash.StartSendThread ();
		}
	}
}
```

- Appkey: User’s Appkey
- URL: Collector address: set collector information of http and https
- Version: Log version
- Port：443
- SendThreadLock: Save logs, which occur when it is true, in a queue without sending to server before StartSendThread is called. Nevertheless, if a native crash occurs, unlock ThreadLock and send the logs.


## API Details

### Specify Custom Fields

```
public static void AddCustomField(string key, string val)
public static void RemoveCustomField(string key)
public static void RemoveAllCustomFields()
```

- Parameters
  - key: string
    - [in] key of custom field, custom key must start with an alphabet or a number, and include “A~Z, a~z, 0~9, - \_”.
  - value: string
    - [in] value of custom field
- Note
  - Following keywords are occupied by SDK and hence cannot be used:
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
        - @logType
  - When the value of a custom field is NULL or empty, SDKs do not send the field to a server.

### Manage Default Setting

```
public static void SetLogSource(string value)
public static string GetLogSource()
```

- Get or newly specify a log source.

```
public static void SetLogType(string value)
public static string GetLogType()
```

- Get or newly specify a log type.

### Filter Levels
- In Unity SDK, send logs of a FATAL level only by default setting. In ERROR or WARN levels, many logs may occur due to variables (such as time, route, and progress level.).
  - Send Error: Send ERROR-level logs occurred at a system.
  - Send Warning: Send WARN-level logs occurred at a system.
  - Send Debug Error: Send ERROR-level logs induced by a user.
  - Send Debug Warning: Send WARN-level logs induced by a user.


### Example of API Use

- Refer to **html > index.html**.


### Collect IP Address

```
public static void SetEnableHost:(bool flag)
```

-	true: Get an ip address and save in the host field.
		false: Save"-" in the host field.

### Send Logs

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
    - [in] Log messages to send


### Crash Callbacks  

```
public void Crash_Send_Complete_Callback(string message) {
	Debug.Log("Crash_Send_Complete_Callback : " + message);
}

void Start() {
	LogNCrashCallBack.ExceptionDelegate += Crash_Send_Complete_Callback;
}

```

- The ExceptionDelegate callback is called after crashes in Unity CSharp are sent to server: it is not called for native crashes.

### Set User IDs

```
public static void SetUserId(string userID)
public static string GetUserID()
```
- Must set the value to get statistics per user.
- Parameter
  - userID: string
    - [in] User ID to sort out users

### Remove Duplicates

In the case of general logs, do not send logs that have the same content in the body and logLevel.

For crash logs, do not send logs that have the same stackTrace and condition values.

The function may be disabled by using the function below, after initialization.

```
	public static void SetDeduplicate(bool flag)
```

- true: (Default) Remove duplicates is enabled <br>
- false: Remove duplicates is disabled

## WebGL API

### Unsupported API

- WebGL SDK does not support Handled Exception because asm.js doesn’t support try-catch.

### WebGL-only API

- Specify a maximum size of remove duplicates log queue.

```
LogNCrash.setDuplciateQueueSize (100);
```
- Specify a maximum size of BulkMessage.

```
LogNCrash.setMaximumBulkMessageSize (1024 * 512); // 512 KB
```

- Specify a maximum number of files to save, when a log delivery is failed.

```
LogNCrash.setMaximumFileCount (100);
```

- Specify a maximum size of delivery log queue.

```
LogNCrash.setMaximumSendCount (100);
```

### Settings to Collect Crashes

- To collect crashes in WebGL SDK, go to **PlayerSettings > Publishing Settings > Enable Exception** and set the options to the **Full**.

### Caution

- Log&Crash can save up to 200 logs in SendQueue in the memory delivery process.
- Log&Crash can save up to 500 duplicate logs in order to remove duplicates.
- Log&Crash can save up to 500 failed logs in order to resend failed deliveries.
- Therefore, a sufficient memory capacity is required.
- To measure server’s response speed, Cross-Domain setting is required in the server.

## Build

1. Click **File > Build Settings**.

![](http://static.toastoven.net/prod_logncrash/gl_5.png)

- Select **WebGL Platform** and click **Player Settings**.

![](http://static.toastoven.net/prod_logncrash/gl_11.png)

2. Click **Build And Run** in the **Build settings**.

## Use External CrashHandler

- Existing SDKs have deployed logMessageReceived during initialization to register CrashHandler of Unity for a LogNCrash callback function.
- The structure has been modified to allow applications to be made both for CrashHandler and external CrashHandler (refer to MultihandlerSample).

### Applications

- Send a false parameter to the LogNCrash.SetCrashHandler function to prevent CrashHandler from being automatically registered.
- Must set before the initialize function.

```
LogNCrash.SetCrashHanlder (false);
LogNCrash.Initialize ();
```

- Then, use the LogNCrash.unity3dHandleException function to deliver CrashHandler parameters to LogNCrash object.

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
