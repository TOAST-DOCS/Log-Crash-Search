## Analytics > Log & Crash Search > Console User Guide

Log & Crash Search can be applied in the following order:

- Enable Service
Create a project on the console and enable Log & Crash Search.
- Send Logs
Start sending logs via Log & Crash Search SDK.
- Check Logs
  Go to **Log Search** or **App Crash Search** to find logs in many ways, like a chart or search function.

## Select Projects

Access console and select an organization and project from menu on the left. When there is no organization or project available, create one.
- Select **ORGANIZATION > PROJECT** from the menu on the left.

See the [NHN Cloud Console Guide](https://docs.toast.com/zh/TOAST/zh/console-guide/) for how to create an organization and a project

## Enable Services

While a project is selected, click **Service** on top of the screen and go to **Log & Crash Search** under **Analytics** and enable it. When a service is enabled, (v) is displayed by the service name.

> [Note]
> Delivery becomes available in about five minutes after service is enabled.

When Log & Crash Search is enabled, **Analytics > Log & Crash Search** shows on the menu, with the Appkey created.

## Check Appkey

To send logs, it is required to check Appkey.

1. Click **Analytics > Log & Crash Search > Log Search** on the left navigation menu.
2. Click **URL & Appkey** on top of the screen and check Appkey.

## Send Logs

To send a log, Log & Crash Search SDK is required.
You can download an SDK from **Analytics > Log & Crash Search** of [NHN Cloud Downloads](https://docs.toast.com/zh/Download/)

> [Note]  
> When the log volume is small, the result can be applied in about five minutes.  

## Search Logs

Logs can be searched by using Log Search.

1. Click **Analytics > Log & Crash Search > Log Search**.
2. **Log Search** provides around-the-clock log-volume graphs with details.

![lcs_01_201812_en](https://static.toastoven.net/prod_logncrash/lcs_01_201812_en.png)

Details of the **Log Search** screen are as below.
![lcs_02_201812_en](https://static.toastoven.net/prod_logncrash/lcs_02_201812_en.png)

| Item | Description |
|---|---|
| Search Query Input | Can apply Lucene grammar for the search of query field. <br/> (note: http://lucene.apache.org/core/2_9_4/queryparsersyntax.html)|
| Time Conditions for Search Query | Configure time conditions for search queries. |
| Log Search Result > Chart View | Print log search results in a bar graph; with a click, it shows the period to fit the unit on the right (e.g. 1Bar=1hour). |
| Field Search Conditions | - host: Apply query to select one or more hosts when selecting a host which belongs to a project. <br/> - logLevel: Classified by log level (FATAL / ERROR / WARN / INFO / DEBUG) <br/> - logSource: Classified by user-defined log source. <br/> - logType: Classified by user-defined project name. <br/> - projectVersion: Classified by user-defined project version. <br/> - Click Show All Fields to apply other fields as search conditions |
| Log Search Result > Log Records | Show details of a searched log. Click each item of a log output to change search queries. |

Search conditions of user configuration are reflected on the search query window, and you may save this query ([Save Queries]), or search by saved queries ([Select Saved Queries]).

## Retrieve App Crash

Crash information of Android and iOS devices can be retrieved through **Analytics > Log & Crash Search > App Crash Search**.

### Retrieve Issues

Select **Crashes** on **Error Type** of **Query Issues**, to check issues.

![lcs_03_201812_en](https://static.toastoven.net/prod_logncrash/lcs_03_201812_en.png)

Select **Handled** on **Error Type** of **Query Issues**, to check handled issues.

![lcs_04_201812_en](https://static.toastoven.net/prod_logncrash/lcs_04_201812_en.png)

|Item |	Description|
|---|---|
|Error Type| Classified into Crash occurred at a system and user-induced Handled at an exception block.|
| Filter Conditions|	Status –Processing status of crash issues <br/> Recent – Retrieving filter of crash logs collected for the recent N days <br/> App Version – App version <br/> Tag – Tags <br/> Minimum Number of Occurrences – Retrieving filter of crashes with at least more than N times of occurrences <br/> Exception Type – Recent filter for each exception type <br/> Location – Retrieving filter for each location of crash occurrence |
|Chart| Display the number of crash occurrences on a timeline chart.|
|List of Issues| Print the list of issues. The order can be changed by using the list box in the upper right; the status can be changed by clicking the checkbox on the left.|

Click an issue on the list to find its details.

![lcs_05_201812_en](https://static.toastoven.net/prod_logncrash/lcs_05_201812_en.png)

|Item|	Description|
|---|---|
|Status|	Set issue status as unresolved, resolved, re-open, or completed.|
|Issue Tracker Setup|	Register issues by setting up external issue trackers, such as GitHub or GitLab.|
|Tag|	Add tags, or modify/delete them afterwards.|
|ETC - Show Search Page|	A click can move to a log search page.|
|Stack Trace|	Show the type and cause of exception for crash occurrence as well as the stack trace.|
|Error Instance|	Show the list of error instances.|
|User| Show the list of users who have occurred issues.|
|Comment|	Write comments of an issue and show the list of comments written by project members.|
|History|	Show management history of an issue (status change, tags added, comments written, and etc.) on a timeline schedule.|
|History Graph| Trace the number of issue occurrences on a timeline chart and a world map.|
|Matrix Data| Display information such as network, OS version, device, operator, and nation.|

### Real-time Monitoring

![lcs_06_201812_en](https://static.toastoven.net/prod_logncrash/lcs_06_201812_en.png)

Monitors real-time occurrences of app crashes for about five minutes.

### Trend

![lcs_07_201812_en](https://static.toastoven.net/prod_logncrash/lcs_07_201812_en.png)

|Item|	Description|
|---|---|
|Time Condition| Set time conditions for statistics. (e.g. 7 days, 2 weeks, or 30 days)|
|Trend Graph| Show various trend graphs. <br/> - Crash Counts <br/> - Device Experiencing Crash <br/> - Statistics of All Errors <br/> - Statistics of Unresolved Errors <br/> - Issue Statistics by App Version <br/> - Issue Statistics by OS version <br/> - Issue Statistics by Device <br/> - Issue Statistics by Country <br/> |

### App Crash Indicator

![lcs_08_201812_en](https://static.toastoven.net/prod_logncrash/lcs_08_201812_en.png)

App Crash Indicator provides session/crash files/occurrence rate (%)/crashes of previous period/increase rate (%)
1.	Click the link for app crash indicator to switch into an app crash trend screen.
2.	Choose a platform (iOS, Android or Windows) to search filtering.
3.	Adjust the period and number of search.
4.	Check results of each version by table.

![lcs_09_201812_en](https://static.toastoven.net/prod_logncrash/lcs_09_201812_en.png)

App Crash Trend provides the number of occurrences during user-defined period

![lcs_10_201812_en](https://static.toastoven.net/prod_logncrash/lcs_10_201812_en.png)

Crash Map displays crash counts during user-defined period on a map


### Crash User

Provide crash occurrence information per user.
1.	Available when user_id is defined in an SDK: if not saved, all users are viewed as ‘-‘.
2.	A user is identified by the combination of a user ID and a device name: users of a same user ID but on different devices are considered different users.

![lcs_11_201812_en](https://static.toastoven.net/prod_logncrash/lcs_11_201812_en.png)


|Item|	Description|
|---|---|
|Error Type|Classified into Crash statistics occurred at a system and user-induced Handled at an exception block.|
|User|Set user ID, specified as user_id in an SDK, as a search condition.  |
|Device|Set a device name as a search condition. |
|Time Condition|Set time conditions for user-specific crash retrieval.|
|App Version|Filtered by app version|

### Issue Statistics

![lcs_12_201812_en](https://static.toastoven.net/prod_logncrash/lcs_12_201812_en.png)

|Item|Description|
|---|---|
|Error Type|Classified into crash statistics occurred at a system and user-induced Handled at an exception block.|
|Time Condition|Set time conditions for statistics. |
|App Version|Filtered by app version.|
|Pie Chart for Frequency of Crash Occurrence|Show frequency of each crash occurrence.|
|Ranking Table for Frequency of Crash Occurrence |Show the ranks of frequency of crash occurrence.|

## Alarm

Set alarms of log and crash, and check history of alarm delivery.
Click **Analytics > Log & Crash Search > Alarms**.

### Set Log Alarms

![lcs_13_201812_en](https://static.toastoven.net/prod_logncrash/lcs_13_201812_en.png)

Provide all log alarm functions:  

- Alarms are classified by the number of occurrences and by the rate of increase/decrease.
- Register a log type (Lucene query) to receive alarms and send alarms depending on the conditions when a log occurs.
- Alarms exist in two types: **Number of Occurrences**, and **Rate of Increase/Decrease**.
- Click **Add Alarms** to register an alarm.

### Alarm by Number of Occurrences

![lcs_14_201812_en](https://static.toastoven.net/prod_logncrash/lcs_14_201812_en.png)

- Here is how to set an alarm:
	- Alarm Title: Enter name to show on the list of alarm setting.
	- Alarm Query: Enter a log type to receive alarms for in Lucene Query.
	- Description: Enter a description of the alarm.
	- Alarm Type: Set it as Number of Occurrences type.
	- Alarm Rule: Select a sign of inequality to be applied for **threshold**
	- Threshold: Send an alarm when logs of ‘search conditions’ occur as much as ‘threshold value’, according to the ‘alarm rule’ inequality sign.
	- Occurrence Cycle: Enter by the minute, and send alarms when a log occurs as much as ‘threshold’ within time input.
	- Snooze: Enter by the minute, between 1 and 1,440 (24 hours); off for 0.  
	- Recipient: Enter recipients to send an alarm to; choose email or SMS for each recipient.
	- SMS Alarm Messages: Enter SMS messages to send alarms with.
	- Callback URL: Enter URL to call when an alarm is sent. http(s)://, Email, and Dooray hook are supported.

### Alarm by Rate of Increase/Decrease

![lcs_15_201812_en](https://static.toastoven.net/prod_logncrash/lcs_15_201812_en.png)

Send alarms by the increase/decrease rate of occurrences of a log type you choose (Lucene Query).  
- Here is how to set an alarm:
	- Alarm Title: Enter a name to show on the list of alarm setting.
	- Alarm Query: Enter a log type to receive alarms for in Lucene Query.
	- Description: Enter a description of the alarm.
	- Alarm Type: Set is as Rate of Increase/Decrease type.
	- Threshold: Increase/decrease rate of logs of ‘search conditions’: positive number means an increase; 0 is for no log changes; negative number means a decrease.
	- Comparison Time: Enter by the minute; compare the log volume between time input and previous interval with threshold. If the increase/decrease rate during ‘comparison time’ satisfies ‘threshold’, an alarm is sent.  
	- Snooze: Enter by the minute between 1 and 1,440 (off for o).
	- Recipient: Enter recipients to send alarms to; choose email or SMS for each recipient.
	- Alarm messages: Enter SMS messages to send alarms with  
	- Callback URL: Enter URL to call when an alarm is sent. http(s)://, Email, and Dooray hook are supported.

### Retrieve Log Alarm History

![lcs_16_201812_en](https://static.toastoven.net/prod_logncrash/lcs_16_201812_en.png)

- Retrieve logs (Lucene Query) for an alarm.  
- Check history of alarm occurrences.

### Set Crash Alarms

![lcs_17_201812_en](https://static.toastoven.net/prod_logncrash/lcs_17_201812_en.png)

Set alarm for each crash log: one for each platform (iOS, Android, Windows, and WebGL).

To set an alarm:

- Platform: Specify a platform to send alarms among iOS, Android, Windows, or WebGL.
- New Crash Alarm Setting: Set On/Off for alarm occurrences.
- Threshold-based Crash Alarm Setting: Select frequency of crash occurrence or average frequency to enter threshold. The detection cycle is 10 minutes.
	- Alarm Type: There are two types: Number of Crash Occurrences and Rate of Increase/Decrease.
	- Threshold: Set number of logs for 'Number of Crash Occurrences' type and percentage for 'Rate of Increase/Decrease'.
- Use Alternative SMS Message: When enabled, custom alarm messages will be delivered via SMS, in place of default error messages,
- Alarm Recipients: Select email or SMS of users who decide to receive alarms from the list of project members.


### Retrieve Crash Alarm History

![lcs_18_201812_en](https://static.toastoven.net/prod_logncrash/lcs_18_201812_en.png)

Retrieves the history of crash alarm occurrences.

- View by platform
- Set time condition to control viewing range
- Information on alarm time, platform, crash type, threshold, event count, and delivery method/status is provided.

### Set User-defined Alarms

![lcs_19_201812_en](https://static.toastoven.net/prod_logncrash/lcs_19_201812_en.png)

When the rate of users experiencing crash exceeds threshold (%), alarm is sent to specified phone or email of the users.

To set an alarm:

- Platform: Specify a platform to send alarms among iOS, Android, Windows, or WebGL.
- Affected User based Alarm SettingAlarm Setting: Set On/Off for alarm occurrences.
- Threshold(Rate of Users Experienced Crash): Set the rate of users who experienced crash..
- Use Alternative SMS Message: When enabled, custom alarm messages will be delivered via SMS, in place of default error messages,
- Alarm Recipients: Select email or SMS of users who decide to receive alarms from the list of project members.

### Retrieve Crash Alarm History

![lcs_20_201812_en](https://static.toastoven.net/prod_logncrash/lcs_20_201812_en.png)

Retrieves the history of user-defined alarm occurrences.

- View by platform
- Set time condition to control viewing range
- Information on alarm time, platform, crash type, threshold, event count, and delivery method/status is provided.

## Setting Up

Manage required service setting, such as search field, issue tracker, and symbol file.

Click **Analytics > Log & Crash Search > Setting**.

### Search Field
Can retrieve search fields for log search: customized fields can be added, to default system fields.

![lcs_21_201812_en](https://static.toastoven.net/prod_logncrash/lcs_21_201812_en.png)

1. If a field name starts with txt for log delivery, it is set true for whether to analyze or not, while other cases are set false. If it is false, it can be registered as a search field of log search.
2. In case you want to deliver a log or binary file and use the **Download > View** link on the log search page, include base 64-encoded values to UserBinaryData or UserTxtData fields.

### Issue Tracking

By setting up an issue tracker, errors can be registered and managed on the **Error Detail** page with the click of issue list of **App Crash Search > Retrieve Issues**.

![lcs_22_201812_en](https://static.toastoven.net/prod_logncrash/lcs_22_201812_en.png)

- Platform: Choose a platform among iOS, Android, and Unity. Can set an issue tracker for each platform.
- Issue Tracker: Choose GitHub or GitLab.
- GitHub Project URL: If GitHub is selected as an issue tracker, enter the URL in the form of https://github.com/{user}/{project}.
- Access Token: If GitHub is selected as an issue tracker, enter an access token created by GitHub (refer to: https://help.github.com/articles/creating-an-access-token-for-command-line-use).
- GitLab Project URL: If GitLb is selected, enter the URL in the form of http://{baseUrl}/{namespace}/{project}.
- Private Token: If GitLab is selected, enter token created at My profile-Account of the GitLab site.
- Issue Title Format: Decide whether to include version and location information to the issue title.
- Test: Check if the setting works fine.

### Symbol File

It is required to register a symbol file to check a crash log. This menu helps upload/download/delete symbol files.

![lcs_23_201812_en](https://static.toastoven.net/prod_logncrash/lcs_23_201812_en.png)

Click **[Select Files]** to upload a symbol file.

- iOS: When uploading an iOS symbol file, it is necessary to compress your BundleName.app.dSYM file using ZIP compression method.
- Android: When uploading an Android symbol file, it is necessary to upload your mapping.txt.file.    
- Android-NDK: When uploading an Android NDK symbol file, it is necessary to compress your lib.so file using ZIP compression method.
- Windows: When uploading a Windows symbol file, it is necessary to compress your BundleName.sym file using - ZIP compression method.
A symbol file must be at most 200MB in size.
- In case of Android NDK, when a symbol file size exceeds the maximum size permitted, it is possible to upload a ZIP file which contains a single ‘lib.so.sym’ file that contains the text-format symbols of the original ‘lib.so’ binary file.

### Log Retention Period

![lcs_24_201812_en](https://static.toastoven.net/prod_logncrash/lcs_24_201812_en.png)

Set the period of log retention.  
- Select the period among 1month, 2months, 3months, 6months, or 1year, which can be modified once every month.
- Expired data are to be deleted early next morning.  

### Set Log Delivery
Set whether to send logs per service.

![lcs_25_201812_en](https://static.toastoven.net/prod_logncrash/lcs_25_201812_en.png)

- Set delivery for each of the general logs, session logs, and crash logs.
- Save and restart app, so as to apply the setting.

## Network Insights

Show latency and error rate delivered by Log & Crash Search SDK, on a timeline chart, URL list, or map.

Click **Analytics > Log & Crash Search > Network Insights**.

- SDK delivers latency and status of a client request from screen of URL setting to URL, to Log & Crash Search
- Set current platform and filter on the screen of monitoring and index, and check latency and error rate.

### Monitoring

- Display latency and error rate on a timeline chart and URL list.

![lcs_26_201812_en](https://static.toastoven.net/prod_logncrash/lcs_26_201812_en.png)

|Item|	Description|
|---|---|
|Filter Conditions | - Recent: Retrieving filter of each time, such as recent 15 minutes, 60 minutes, 24 hours, or 48 hours. For user-defined conditions, retrieve by selecting start/end dates (up to 48 hours). <br/>- App Version: Retrieving filter per app version <br/>- OS Version: Retrieving filter per OS version <br/>- Device: Enter device name <br/> - Telecom: Enter telecom name <br/>- Country: Filter by countries <br/>- URL: Filter by URLs|
| Chart| Latency and error rate is shown on timeline graph. <br/> You can select iOS, Android, Windows, or WebGL from **Current Platform** dropdown menu.|
|URL| Latency and error rate per URL is shown in the table.|

### Map

- Display latency and error rate on a map.

![lcs_27_201812_en](https://static.toastoven.net/prod_logncrash/lcs_27_201812_en.png)

|Item|	Description|
|---|---|
|Map Types | There are two types of map: latency and error rate types.|
|Filter Conditions | - Recent: Retrieving filter of each time, such as recent 15 minutes, 60 minutes, 24 hours, or 48 hours. For user-defined conditions, retrieve by selecting start/end dates (up to 48 hours). <br/>- App Version: Retrieving filter per app version <br/>- OS Version: Retrieving filter per OS version <br/>- Device: Enter device name <br/>|
| Map | Latency and error rate is shown on the map. <br/>You can select iOS, Android, Windows, or WebGL from **Current Platform** dropdown menu. |

### URL Setting

- Add or remove URLs for latency and error rate monitoring. 

![lcs_28_201812_en](https://static.toastoven.net/prod_logncrash/lcs_28_201812_en.png)

| Item|Description|
|---|---|
| Date Modified | Date of most recent URL list modification.|
| URL Table| Shows the list of URLs for latency and error rate monitoring.<br/>You can add an URL from the top-right corner. <br/>To remove from the list, check the checkbox and click **[Delete Selected Items]**. |