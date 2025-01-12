---
title: Dynamic Anomaly Detection
date: 2024-12-28 12:00:00 -500
categories: [Engineering,Detection]
tags: [BLUE,DE,Engineering,Detection]

---

> [!quote] I believe that every person is born with talent.
> — Maya Angelou

---

## Dynamic Anomaly Detection


KQL


```kusto
set query_datetimescope_column = "TimeGenerated";
set query_datetimescope_to = datetime(2023-09-19 13:10:00);
set query_now = datetime(2023-09-19 13:10:00);
// Actual Query
let lookback = 8d;
let query_timeframe = 1d;
let baseline = 
    SecurityEvent
    | where TimeGenerated between (ago(lookback) .. ago(query_timeframe))
    | where EventID == 4624
    | where toint(format_timespan(dayofweek(TimeGenerated), 'd')) !in (6,7)
    | summarize avg_login_count = (count() / 5.0) by TargetUserName
    ;
SecurityEvent
| where TimeGenerated > ago(query_timeframe)
| where EventID == 4624
| summarize current_login_count = count() by TargetUserName
| join kind=inner baseline on TargetUserName
| where current_login_count > avg_login_count * 2
| project TargetUserName, avg_login_count, current_login_count
```

Splunk

```json
index=SecurityEvent EventID=4624 earliest=-8d@d latest=now
| eval dow = tonumber(strftime(_time, "%w"))
| eval period = if(_time >= relative_time(now(), "-1d@d"), "current", "baseline")
| where (period="baseline" AND dow >=1 AND dow <=5) OR period="current"
| stats count AS count BY TargetUserName period
| eval avg_login_count = if(period=="baseline", count / 5.0, null())
| eval current_login_count = if(period=="current", count, null())
| stats values(avg_login_count) AS avg_login_count, values(current_login_count) AS current_login_count BY TargetUserName
| where current_login_count > 2 * avg_login_count
| table TargetUserName avg_login_count current_login_count
```






KQL

```kusto
set query_datetimescope_column = "Timestamp";
set query_datetimescope_to = datetime(2023-09-18 09:10:00);
set query_now = datetime(2023-09-18 09:10:00);
// Actual query
let lookback = 30d;
let query_timeframe = 1h;
// Windows 10 devices in the last 30d
let win10_devices = 
    DeviceInfo
    | where Timestamp > ago(lookback)
    | where OSPlatform == 'Windows10'
    | summarize make_set(DeviceName)
    ;
// Create baseline
let baseline = 
    DeviceProcessEvents
    | where Timestamp between (ago(lookback) .. ago(query_timeframe))
    | where DeviceName in (win10_devices)
    | where FileName =~ 'netsh.exe'
    ;
// Compare last 1h of data with the baseline using leftantisemi join
DeviceProcessEvents
| where Timestamp > ago(query_timeframe)
| where DeviceName in (win10_devices)
| where FileName =~ 'netsh.exe'
| join kind=leftantisemi baseline on DeviceId
```


SPL

```JSON
<!-- Step 1: Identify Windows 10 Devices in the Last 30 Days -->
| tstats `security` count where index=DeviceInfo OSPlatform="Windows10" earliest=-30d latest=now by DeviceName
| table DeviceName
| format "(" "(" "DeviceName" "=" "" ")" "OR")

<!-- Step 2: Create Baseline from DeviceProcessEvents -->
| tstats `security` count where index=DeviceProcessEvents FileName="netsh.exe" earliest=-30d@h latest=-1h@h by DeviceId DeviceName
| table DeviceId

<!-- Step 3: Compare Last 1 Hour of Data with Baseline -->
| search index=DeviceProcessEvents FileName="netsh.exe" earliest=-1h@h latest=now
| search [ 
    | tstats `security` values(DeviceId) where index=DeviceProcessEvents FileName="netsh.exe" earliest=-30d@h latest=-1h@h by DeviceId 
    | fields DeviceId 
    | format NOT "DeviceId" "=" 
]
```
