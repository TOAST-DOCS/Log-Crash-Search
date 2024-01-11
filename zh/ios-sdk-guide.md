## Analytics > Log & Crash Search > iOS SDK Guide

> [Deprecated] 
> Log & Crash iOS SDK is not supported any more. 
> Please use [TOAST SDK](http://docs.toast.com/en/TOAST/en/toast-sdk/overview/). 

> [Notice]
> Crash logs from new devices using the arm64e architecture (iPhone XS, XR, XS Max, and iPad Pros 3rd) can only count the number of occurrences, and analysis of crash content is not yet supported.
> We will provide analysis capabilities for new devices in the near future.

Log & Crash iOS SDK sends logs to a Log & Crash Search collector server.

Below describe benefits and features of Log & Crash iOS SDK.

- Send logs to a collector server.
- Send crash logs occurred in an app to a collector server.
- Retrieve and search logs sent from Log & Crash Search.
- Operate in a multi-threading environment.

## Supporting Environment
- iOS 8.0 or higher

## Download

Go to [TOAST Document](http://docs.toast.com/en/Download/) to download **iOS SDK(native)**.

```
Click [DOCUMENTS] > [Download] > [Analytics > Log & Crash Search] > [iOS SDK] 
```

## How to Use SDKs

### Add Header Files

Add #import <LogNCrashSDK/LogNCrashSDK.h>.

### Initialize

```
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion;
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion forUserId:(NSString*)userId;
(bool) init:(NSString *)server ofAppKey:(NSString*)appName withVersion:(NSString*)appVersion forUserId:(NSString*)userId enableSyncStart:(bool)flag;
```

- Initialize TLCLog.
- Call is required to operate TLCLog properly.
- Must enter userID to obtain statistics of each user.
- Parameters
  - server: Collector server address
  - appKey: Appkey
  - version: App version
  - userId: User ID
  - enableSyncStart: Save logs, which occur when it is true, in queue without sending to server before startSendThread is called. Nevertheless, if a crash occurs, unlock ThreadLock and send the logs.
- Return value
  - userId: User ID
  - False for failure

### Unlock SendThread

```
	(void) startSendThread;
```

- Unlock SendThread.

### Lock Host

```
	(void) enableHost;
```

- true: Get an ip address and save it in the host field.
- false: Do not get an ip address.

### Send Logs

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

- Send logs to a collector server at the specified log level.
- Parameters
  - errorCode: Error code
  - message: Log messages
  - location: Error location

### Specify Custom Keys

```
(void) setCustomField:(NSString*)value forKey:(NSString*)key;
(void) removeAllCustomFields;
(void) removeCustomFieldForKey:(NSString*)key;
```

- Add, delete, and delete all custom keys.
- When "nil" is set as custom key, ignore the set value; if "nil" is set as the value, set as "-".
- The custom key must start with an upper or lower-case alphabet; only alphabets, numbers, and \'-\', \'\' can be used. (\^\[a-zA-Z\]\[a-zA-Z0-9-\_\]\*\$)
- Following names cannot be used, irrespective of upper or lower case:
  - projectName, projectVersion, host, logType, logSource, sendTime, logTime, logLevel, UserID
  - Platform, DeviceModel, NetworkType, Carrier, CountryCode, DmpData, errorCode, Location, body, SessionID.

### Remove Duplicates

The Remove Duplicates logic has been applied to general logs from 2.4.0
or higher SDKs.

When duplicate logging is enabled, do not send logs that have the same
content in the body and logLevel.

```
public static void setLogDeduplicate(bool enable)
```

true: (Default) Remove duplicates is enabled<br>
false: Remove duplicates is disabled

### Manage Default Setting

```
(void) setUserId:(NSString*)userId;
```

- Set user ID.

```
(void) setLogType:(NSString*)logType;
```

- Set a log type.

```
(void) setLogSource:(NSString*) logSource;
```

- Set a log source.

## Automatically Collected Information

Below information is automatically collected by Log & Crash SDK and can be found in Log & Crash Search. In case data collection is not available at the time of log delivery, values may not be found.  

- iOS
  \- Platform: iOS version information
  \- DeviceModel: iPhone model
  \- Carrier: User's telecommunication service provider
  \- CountryCode: User's country code of user's telecommunication service provider
  \- NetworkType: Wi-Fi or Cellular("No Connection\" if network use is unavailable when log delivery event occurs)

## Interpret iOS Crashed
- For iOS crashes, symbol files are required to interpret crash information which is collected as an address value.

- Open Xcode and click **Windows \> Organizer**.

- Click a build result, right-click open, and click **Show in Finder**.
  ![](http://static.toastoven.net/prod_logncrash/13.png)

- Click the result and right click open **See Package**.
  ![](http://static.toastoven.net/prod_logncrash/14.png)

- Compress .dSYM to .zip and register it to Web Console> Analytic > Log & Crash Search > Settings > Symbol Files tab.

## Note for iOS Unity Crash

- Crash logs without registered symbol files are classified as general logs.
