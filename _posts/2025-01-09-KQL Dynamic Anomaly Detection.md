---
title: KQL Dynamic Anomaly Detection
date: 2025-01-09 12:00:00 -500
categories: [Queries,Detection]
tags: [Queries,Engineering,Detection Engineering,Detection]

---

> [!quote] The secret of success is constancy to purpose.
> — Benjamin Disraeli

---

## KQL Dynamic Anomaly Detection

Below is a more streamlined Splunk approach that uses a single search (rather than multiple `append` calls) to compute the 5-day average baseline and compare it to the current 1-day counts. It excludes weekends only for the baseline calculation, then compares the final values in one pass:

```JSON
**index=windows source="WinEventLog:Security" EventCode=4624 earliest=-8d
| eval DayOfWeek=strftime(_time, "%w")
| eval isBaseline=if(_time < relative_time(now(), "-1d"), 1, 0)
| bucket span=1d _time
| stats count AS daily_count BY TargetUserName DayOfWeek isBaseline _time
| eventstats avg(eval(if(isBaseline=1 AND DayOfWeek!="0" AND DayOfWeek!="6", daily_count, null()))) AS baseline_avg
           stdev(eval(if(isBaseline=1 AND DayOfWeek!="0" AND DayOfWeek!="6", daily_count, null()))) AS baseline_stdev
           BY TargetUserName
| where isBaseline=0  -- This now filters us down to "current" rows
| stats sum(daily_count) AS current_logins max(baseline_avg) AS baseline_avg max(baseline_stdev) AS baseline_stdev BY TargetUserName
| eval anomaly_threshold = baseline_avg * 2
| where current_logins > anomaly_threshold
| eval deviation_factor = round(current_logins / baseline_avg, 2), 
      baseline_avg     = round(baseline_avg, 2)
| table TargetUserName current_logins baseline_avg deviation_factor
| sort - deviation_factor
**
```

- **Baseline**: Collects events from the last 8 days but before “today” (`isBaseline=1`), excludes weekends for the average by checking `DayOfWeek!="0" AND DayOfWeek!="6"`.
- **Current**: Same 8-day window, but includes only “today” (`isBaseline=0`) for the final comparison.
- **Comparison**: Compares `current_logins` to `baseline_avg` and alerts if `current_logins` is more than twice the baseline average.

---

Below is the Splunk query again, followed by a line-by-line explanation. The goal is to replicate the KQL logic:

1. Identify Windows 10 devices (from the last 30 days).
2. Create a baseline of devices that have run `netsh.exe` in the last 30 days but not in the most recent hour.
3. Look at the last 1 hour of data for the same Windows 10 devices to see if `netsh.exe` has been executed for the first time (i.e., any device that has never executed `netsh.exe` in the baseline).

---

## Splunk Query


```JSON
"Index_and_Sourcetype_for_DeviceInfo"
| search OSPlatform="Windows10" earliest=-30d
| stats values(DeviceName) AS win10_devices

| appendpipe [
    search "Index_and_Sourcetype_for_DeviceProcessEvents" FileName="netsh.exe" earliest=-30d latest=-1h
        [ | search OSPlatform="Windows10" earliest=-30d
          | stats values(DeviceName) AS win10_devices_sub
          | format ]
    | stats values(DeviceId) AS baselineDeviceIds
    | eval tag="baseline"
]

| eventstats values(win10_devices) AS all_win10_devices values(baselineDeviceIds) AS all_baseline_ids
| where isnull(tag) AND FileName="netsh.exe" earliest=-1h
| search DeviceName IN (all_win10_devices)
| where NOT (DeviceId IN (all_baseline_ids))
| table _time DeviceName DeviceId FileName
```

---

## Step-by-Step Explanation

### 1. Identify Windows 10 Devices

`"Index_and_Sourcetype_for_DeviceInfo" | search OSPlatform="Windows10" earliest=-30d | stats values(DeviceName) AS win10_devices`

- **"Index_and_Sourcetype_for_DeviceInfo"**: Replace this with the actual index (and optionally sourcetype) containing your device inventory or device information logs.
- **`search OSPlatform="Windows10" earliest=-30d`**: We look back 30 days for devices that run Windows 10.
- **`stats values(DeviceName) AS win10_devices`**: We collect all unique `DeviceName` values into a single multivalue field, `win10_devices`.

At the end of this part, you have a list of all Windows 10 devices that have appeared in the logs in the last 30 days.

### 2. Append a Pipe to Create the Baseline

`| appendpipe [     search "Index_and_Sourcetype_for_DeviceProcessEvents" FileName="netsh.exe" earliest=-30d latest=-1h         [ | search OSPlatform="Windows10" earliest=-30d           | stats values(DeviceName) AS win10_devices_sub           | format ]     | stats values(DeviceId) AS baselineDeviceIds     | eval tag="baseline" ]`

- **`appendpipe []`**: Runs an additional search in the same pipeline. The results from this bracketed search block get appended to the main search results.
- **`search "Index_and_Sourcetype_for_DeviceProcessEvents" FileName="netsh.exe" earliest=-30d latest=-1h`**:
    - We look back 30 days (`earliest=-30d`) and up to 1 hour ago (`latest=-1h`) for events where `FileName="netsh.exe"`.
    - This effectively creates the baseline window.
- **Nested search**:

    `[ | search OSPlatform="Windows10" earliest=-30d   | stats values(DeviceName) AS win10_devices_sub   | format ]`
    
    - This nested subsearch is an inline check for Windows 10 devices again, ensuring we only look at events from that same set of Windows 10 devices.
    - The subsearch returns a formatted query constraint used to filter only events where `DeviceName` is in the set of Windows 10 devices.
- **`stats values(DeviceId) AS baselineDeviceIds`**: We then gather all `DeviceId` values from the baseline period (last 30 days except for the last hour) where `netsh.exe` was executed.
- **`eval tag="baseline"`**: We tag all these events with `tag="baseline"` so we can identify them after the append merges back into the main result set.

When the `appendpipe` finishes, our main pipeline will contain two types of events:

1. The original list of Windows 10 device names (without a `tag`).
2. The appended baseline results (with `tag="baseline"` and `baselineDeviceIds`).

### 3. Combine and Filter Events

`| eventstats values(win10_devices) AS all_win10_devices values(baselineDeviceIds) AS all_baseline_ids`

- **`eventstats`**: Combines data across all events in the pipeline.
- **`values(win10_devices)`**: Pulls the aggregated Windows 10 device names from the non-`baseline` portion of the pipeline and makes them available in all events as `all_win10_devices`.
- **`values(baselineDeviceIds)`**: Pulls the aggregated baseline device IDs from the `baseline` portion and makes them available as `all_baseline_ids`.

This way, each event in the pipeline can “see” the full list of Windows 10 devices as well as the baseline device IDs that have run `netsh.exe`.

### 4. Identify `netsh.exe` Executions in the Last Hour That Are Not in the Baseline

`| where isnull(tag) AND FileName="netsh.exe" earliest=-1h | search DeviceName IN (all_win10_devices) | where NOT (DeviceId IN (all_baseline_ids))`

- **`where isnull(tag)`**: Filters out the `baseline` events we appended. We only want to look at “new” or “current” data in the pipeline.
- **`AND FileName="netsh.exe" earliest=-1h`**: Ensure we only look at `netsh.exe` events in the last hour.
- **`search DeviceName IN (all_win10_devices)`**: Confirms we only see devices that are in our Windows 10 device list.
- **`where NOT (DeviceId IN (all_baseline_ids))`**: Finally, we check if the `DeviceId` for the current event does **not** exist in the baseline.
    - This is the Splunk equivalent of KQL’s `join kind=leftantisemi`: we want to see “new” events that **did not** appear in the baseline set.
### 5. Final Output

`| table _time DeviceName DeviceId FileName`

- The **`table`** command just organizes the final columns. We show the timestamp of the event, the device name, the device ID, and the filename (`netsh.exe`).
- The result is a list of devices that ran `netsh.exe` in the last hour for the **first** time (i.e., they never ran it during the baseline period).
---
## Summary

1. **Gather Windows 10 Devices:** We first retrieve a list of all Windows 10 devices active in the last 30 days.
2. **Create Baseline:** We append data from the same timeframe (minus the last hour) to see which devices have executed `netsh.exe`.
3. **Combine Data:** With `eventstats`, we unify the list of Windows 10 devices and baseline device IDs.
4. **Filter and Compare:** In the last hour, list any device that runs `netsh.exe` for the first time by excluding those already found in the baseline.
5. **Output:** We show only relevant columns in a tidy table.

This approach mimics the KQL logic of using a “leftanti” (or “leftantisemi”) join by excluding the baseline IDs and only returning truly “new” `netsh.exe` executions in the last hour.

ENHANCED 
```JSON
############ 1. Threat Hunting Components ############
# Base threat hunting macro
| loadjob subsearch_id [
    # Advanced credential hunting
    index=windows source="WinEventLog:Security" earliest=-24h
    | eval 
        authentication_type=case(
            EventCode=4624, "interactive",
            EventCode=4625, "failed",
            EventCode=4648, "explicit",
            EventCode=4768, "kerberos_tgt",
            EventCode=4769, "kerberos_service",
            1=1, "other"
        )
    | stats 
        dc(src_ip) as unique_sources,
        dc(LogonType) as logon_types,
        values(LogonType) as logon_type_list,
        dc(WorkstationName) as workstations
        by TargetUserName authentication_type

    # Lateral movement detection
    | append [
        | search index=windows EventCode IN (4688, 4689, 5140) earliest=-24h
        | stats count as process_count by host CommandLine
        | where process_count > 10
        | rename host as src_host
    ]

    # Living off the land detection
    | append [
        | search index=windows EventCode=4688 earliest=-24h
        | regex CommandLine="(?i)(certutil\.exe|bitsadmin\.exe|mshta\.exe)"
        | stats count as suspicious_cmd_count by host CommandLine
    ]
]

############ 2. Machine Learning Components ############
# Apply ML model for anomaly detection
| fit XGBoost total_risk_score from 
    current_logins baseline_hourly_avg deviation_factor 
    current_sources privilege_changes time_risk location_risk 
    into my_security_model

# Calculate anomaly scores
| score my_security_model 
| eval 
    ml_anomaly_score=case(
        predicted_value > 0.8, "Critical",
        predicted_value > 0.6, "High",
        predicted_value > 0.4, "Medium",
        1=1, "Low"
    )

############ 3. Performance Optimization ############
# Data model acceleration
| tstats 
    count as event_count 
    from datamodel=Authentication 
    where Authentication.action=success 
    by Authentication.user Authentication.src Authentication.dest 
    prestats=true

# Optimized lookup strategy
| lookup local=true asset_criticality_mv host as dest_hostname OUTPUT criticality
| lookup local=true threat_intel_ip_mv ip as src_ip OUTPUT threat_score category

# Cached results for common queries
| cache ttl=300 
    stats count as cache_hit by user

############ 4. Alert Response Playbook ############
| eval playbook_steps=case(
    ml_anomaly_score=="Critical", mvappend(
        "1. Immediately disable user account",
        "2. Notify SOC and Incident Response team",
        "3. Collect system memory dump from affected hosts",
        "4. Initialize incident response procedure",
        "5. Begin forensic timeline creation"
    ),
    ml_anomaly_score=="High", mvappend(
        "1. Review user activity logs for past 24 hours",
        "2. Check for associated credential changes",
        "3. Monitor user activity in real-time",
        "4. Prepare incident timeline",
        "5. Contact user's manager for verification"
    ),
    ml_anomaly_score=="Medium", mvappend(
        "1. Monitor user activity",
        "2. Review recent password changes",
        "3. Check access patterns",
        "4. Document findings"
    ),
    1=1, mvappend(
        "1. Log event for review",
        "2. Update baseline metrics",
        "3. Document false positive if confirmed"
    )
)

# MITRE ATT&CK correlation
| eval attack_techniques=case(
    like(CommandLine, "%mimikatz%"), "T1003 - Credential Dumping",
    like(CommandLine, "%psexec%"), "T1021 - Remote Services",
    like(CommandLine, "%reg%save%"), "T1012 - Query Registry",
    1=1, null
)

############ 5. Automated Response Actions ############
| sendalert security_response_workflow param.severity=$ml_anomaly_score$ 
    param.user=$TargetUserName$ param.evidence=$attack_techniques$

# Output final analysis with all contexts
| table 
    _time,
    TargetUserName,
    ml_anomaly_score,
    attack_techniques,
    playbook_steps,
    total_risk_score,
    current_logins,
    unique_sources,
    logon_type_list,
    suspicious_cmd_count,
    login_cities,
    privilege_changes,
    authentication_type
| sort - ml_anomaly_score
```



---
---
## Anomaly Detection using Frequency Analysis


Let me break down the key translations from KQL to Splunk SPL:

1. **Time Range Functions**:
    - KQL: `ago(4d)` and `ago(3d)`
    - Splunk: `relative_time(now(), "-4d")` and `relative_time(now(), "-3d")`
2. **Aggregation Functions**:
    - KQL: `dcount(DeviceId)`
    - Splunk: `dc(ComputerName)` (distinct count)
3. **FirstSeen/LastSeen logic**:
    - KQL: `min(Timestamp)` and `arg_max(Timestamp,*)`
    - Splunk: `min(_time)` and `latest(_time)`, `latest(*)`
4. **Key Differences**:
    - Splunk uses `stats` instead of `summarize`
    - Time fields in Splunk use `_time` instead of `Timestamp`
    - Device identification typically uses `ComputerName` in Windows events
    - Time windowing requires explicit `eval` statements in Splunk

Some considerations for implementation:

1. **Data Source Adaptation**:
    - The sourcetype and index need to match your Splunk environment
    - Windows Process Creation events (4688) are used as an example
2. **Performance Optimization**:
    - Consider adding relevant index restrictions
    - Use accelerated datamodels if available
    - Add specific fields to the `stats` command instead of `latest(*)`
3. **Additional Enhancements**:
    - Add process command line analysis: `| rex field=CommandLine ...`
    - Include parent process correlation
    - Add lookup tables for known good processes
      

# Set time variables - in Splunk these would typically be handled by the time picker
# But we can use eval and strftime to create specific times
| eval _start = relative_time(now(), "-4d")
| eval _end = relative_time(now(), "-3d")

# Main query starts here
index=* sourcetype=windows EventCode=4688 
# Looking back 15 days - adjust index/sourcetype based on your Splunk environment
| where _time >= relative_time(now(), "-15d")

# Group and calculate prevalence
| stats 
    dc(ComputerName) as Prevalence 
    min(_time) as FirstSeen 
    latest(_time) as LastSeen 
    latest(*) as * 
    by SHA1

# Filter for rare processes
| where Prevalence < 3

# Time window filtering
| eval inTimeWindow = if(
    FirstSeen >= _start AND FirstSeen <= _end AND 
    LastSeen >= _start AND LastSeen <= _end,
    1, 0)
| where inTimeWindow=1

# Optional: Format timestamps for readability
| eval FirstSeen=strftime(FirstSeen, "%Y-%m-%d %H:%M:%S")
| eval LastSeen=strftime(LastSeen, "%Y-%m-%d %H:%M:%S")


```JSON
# Set time variables - in Splunk these would typically be handled by the time picker
# But we can use eval and strftime to create specific times
| eval _start = relative_time(now(), "-4d")
| eval _end = relative_time(now(), "-3d")

# Main query starts here
index=* sourcetype=windows EventCode=4688 
# Looking back 15 days - adjust index/sourcetype based on your Splunk environment
| where _time >= relative_time(now(), "-15d")

# Group and calculate prevalence
| stats 
    dc(ComputerName) as Prevalence 
    min(_time) as FirstSeen 
    latest(_time) as LastSeen 
    latest(*) as * 
    by SHA1

# Filter for rare processes
| where Prevalence < 3

# Time window filtering
| eval inTimeWindow = if(
    FirstSeen >= _start AND FirstSeen <= _end AND 
    LastSeen >= _start AND LastSeen <= _end,
    1, 0)
| where inTimeWindow=1

# Optional: Format timestamps for readability
| eval FirstSeen=strftime(FirstSeen, "%Y-%m-%d %H:%M:%S")
| eval LastSeen=strftime(LastSeen, "%Y-%m-%d %H:%M:%S")
```


```JSON

```