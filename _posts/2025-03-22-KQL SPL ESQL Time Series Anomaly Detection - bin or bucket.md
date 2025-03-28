---
title: KQL Time Series Anomaly Detection - bin orbucket
date: 2025-03-22 12:00:00 -500
categories: [Detection,Engineering ]
tags: [Detection ,KQL,Bin,Bucket]

---

> [!quote] A true friend is the most precious of all possessions and the one we take the least thought about acquiring.
> — Francois de La Rochefoucauld

---

## KQL Time Series Anomaly Detection - bin or bucket

Certainly! Let's delve into the concepts of aggregating data, anomaly detection, and forecasting, and explore how each is implemented across three query languages: Kusto Query Language (KQL), Splunk's Search Processing Language (SPL), and Elasticsearch Query Language (ESQL).

**1. Aggregating Data**

_Aggregating data_ involves summarizing detailed data to provide insights, such as computing averages, counts, or sums over specified intervals.

**KQL**

- **Function:** `summarize` with `bin()`
    
    The `summarize` operator aggregates data based on specified criteria, and the `bin()` function groups data into fixed time intervals.​
    
    _Example: Counting Events per Hour_
    

  `T   | summarize Count = count() by bin(Timestamp, 1h)`

This query counts the number of events occurring each hour.​

**Splunk**

- **Function:** `timechart`
    
    The `timechart` command creates a time-based chart by aggregating data over specified time intervals.​
    
    _Example: Counting Events per Hour_
    


```JSON
index=your_index   | timechart span=1h count
```

This command produces a time-based chart showing event counts per hour.​

**ESQL**

- **Function:** `STATS` with `BUCKET`
    
    The `STATS` command performs aggregations, and the `BUCKET` function groups data into specified time intervals.​
    
    _Example: Counting Events per Hour_
    

```JSON
FROM logs   
| STATS event_count = COUNT(*) BY hour = BUCKET(@timestamp, 1h)
```

This query counts events grouped by each hour.​

**2. Anomaly Detection**

_Anomaly detection_ identifies data points that deviate significantly from the norm, which can indicate potential issues or unusual activities.

**KQL**

- **Function:** `series_decompose_anomalies()`
    
    This function decomposes a time series and detects anomalies based on deviations from the expected pattern.​
    
    _Example: Detecting Anomalies in Web Traffic_
    

```JSON
let min_t = datetime(2025-03-01);   
let max_t = datetime(2025-03-22);   
let dt = 1h;   
demo_make_series2   
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid   
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')   
| render anomalychart with(anomalycolumns=anomalies)`
```

This query detects anomalies in web traffic data over a specified period.​

**Splunk**

- **Function:** `anomalydetection`
    
    The `anomalydetection` command identifies anomalies in a dataset based on statistical deviations.​
    
    _Example: Detecting Anomalies in CPU Usage_
    

```JSON
index=your_index   
| timechart span=1h avg(cpu_usage) as avg_cpu   
| anomalydetection
```

This command detects anomalies in average CPU usage over hourly intervals.​

**ESQL**

- **Function:** `MOVING_AVG` and `ABS`
    
    ESQL can detect anomalies by calculating moving averages and identifying significant deviations.​
    
    _Example: Detecting Anomalies in Event Counts_
    

```JSON
FROM logs   
| STATS event_count = COUNT(*) BY hour = BUCKET(@timestamp, 1h)   
| EVAL moving_avg = MOVING_AVG(event_count, 5)   
| EVAL deviation = ABS(event_count - moving_avg)   
| WHERE deviation > threshold
```

This query identifies hours where the event count deviates significantly from the moving average, indicating potential anomalies.​

**3. Forecasting**

_Forecasting_ predicts future data points based on historical patterns.

**KQL**

- **Function:** `series_decompose_forecast()`
    
    This function forecasts future values of a time series by decomposing it into seasonal and trend components.​[Azure 文档](https://docs.azure.cn/en-us/data-explorer/kusto/query/anomaly-detection?view=microsoft-fabric&utm_source=chatgpt.com)
    
    _Example: Forecasting Web Traffic_
    

```JSON
let min_t = datetime(2025-03-01);   
let max_t = datetime(2025-03-22);   
let dt = 1h;   
let horizon = 7d;   
demo_make_series2   
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid | extend forecast = series_decompose_forecast(num, toint(horizon/dt))   
| render timechart
```

This query forecasts web traffic for the next seven days based on historical data.​

**Splunk**

- **Function:** `predict`
    
    The `predict` command forecasts future values of a time series using various algorithms.​
    
    _Example: Forecasting CPU Usage_
    


```JSON
  index=your_index   
  | timechart span=1h avg(cpu_usage) as avg_cpu   
  | predict avg_cpu algorithm=LL
```

This command forecasts future CPU usage using the Local Level (LL) algorithm.​

**ESQL**

- **Function:** `HOLT`
    
    The `HOLT` function applies Holt's linear trend method for forecasting.​
    
    _Example: Forecasting Event Counts_
    

```JSON
FROM logs   
| STATS event_count = COUNT(*) BY hour = BUCKET(@timestamp, 1h)   
| EVAL forecast = HOLT(event_count, 0.3, 0.1)
```

This query forecasts future event counts using Holt's linear trend method.​[Azure 文档](https://docs.azure.cn/en-us/data-explorer/kusto/query/anomaly-detection?view=microsoft-fabric&utm_source=chatgpt.com)

These examples demonstrate how each query language implements functions for aggregating data, detecting anomalies, and forecasting, providing powerful tools for time series analysis in various SIEM platforms.

---

## Time Series Data with bucket/binning

​Binning is the process of grouping continuous data into discrete intervals, which is particularly useful in time-series analysis for identifying patterns and anomalies. Here's how binning is implemented in Splunk's Search Processing Language (SPL), Kusto Query Language (KQL), and Elasticsearch Query Language (ES|QL):​

**1. Splunk (SPL)**

In Splunk, the `bin` command is used to group data into bins based on specified intervals.​

**Syntax:**

`bin [options] <field>`

- **`<field>`**: The field to be binned.​
    

**Example 1: Hourly Process Creation Count for a Specific Host**


```JSON
index=your_index host=corpgenws02.otrflabs.com 
| bin span=1h _time 
| stats count as total_process_count by _time 
| sort _time
```

This query bins events into 1-hour intervals based on the `_time` field and counts the number of events in each bin for the specified host.​

**Example 2: Daily Process Creation Count per Host**

```JSON
index=your_index 
| bin span=1d _time 
| stats count as total_process_count by host, _time 
| sort _time
```

This query bins events into daily intervals and counts the number of events per host for each day.​

**2. Kusto Query Language (KQL)**

In KQL, the `bin()` function is used to group data into specified time intervals.​

**Syntax:**

`bin(ColumnName, BinSize)`

- **`ColumnName`**: The column containing the timestamp information.​
    
- **`BinSize`**: The timespan used for binning, such as `1h`, `1d`, etc.​
    

**Example 1: Hourly Process Creation Count for a Specific Device**

```JSON
DeviceProcessEvents 
| where DeviceName == "corpgenws02.otrflabs.com" 
| summarize total_process_count = count() by bin(Timestamp, 1h) 
| sort by Timestamp asc
```

This query filters events for the specified device, bins them into 1-hour intervals, and counts the number of events in each bin.​

**Example 2: Daily Process Creation Count per Device**

```JSON
DeviceProcessEvents 
| summarize total_process_count = count() by DeviceName, bin(Timestamp, 1d) 
| sort by Timestamp asc
```

This query bins events into daily intervals and counts the number of events per device for each day.​

**3. Elasticsearch Query Language (ES|QL)**

In ES|QL, the `date_histogram` aggregation is used to group data into specified time intervals.​

**Example 1: Hourly Process Creation Count for a Specific Host**


```JSON
FROM logs 
| WHERE host.name == "corpgenws02.otrflabs.com" 
| EVAL hour = DATE_FORMAT(@timestamp, "yyyy-MM-dd'T'HH") 
| STATS total_process_count = COUNT(*) BY hour 
| SORT hour
```

This query filters events for the specified host, formats the `@timestamp` field to the hour level, and counts the number of events in each hourly bin.​

**Example 2: Daily Process Creation Count per Host**

```JSON
FROM logs 
| EVAL day = DATE_FORMAT(@timestamp, "yyyy-MM-dd") 
| STATS total_process_count = COUNT(*) BY host.name, day 
| SORT day
```


This query formats the `@timestamp` field to the day level and counts the number of events per host for each day.​

By utilizing these binning techniques in SPL, KQL, and ES|QL, you can effectively aggregate and analyze time-series data to identify trends and anomalies.

---

## Time Series Anomaly Detection Using Bin/Bucket

I apologize for the earlier interruptions in my responses. Let's continue by translating the Kusto Query Language (KQL) concepts you've provided into equivalent functionalities in Splunk's Search Processing Language (SPL) and Elasticsearch Query Language (ESQL).

**Anomaly Detection with Moving Averages**

_Concept:_ Utilizing moving averages to identify anomalies in time-series data involves calculating the average value over a specific time window and comparing it to current data points. Significant deviations from this moving average may indicate anomalous behavior.​

**Splunk (SPL):**

_Function:_ `movingavg`

_Example:_ Detecting users whose daily login count is at least twice their average login count over the preceding week.​

```JSON
index=security_event EventID=4624
| eval day=strftime(_time, "%Y-%m-%d")
| timechart span=1d count by TargetUserName
| streamstats window=7 avg(count) as avg_login_count by TargetUserName
| where count > 2 * avg_login_count
| table TargetUserName, avg_login_count, count
```

_Explanation:_ This SPL query calculates the daily login count for each user and computes a 7-day moving average. It then filters for instances where the current day's login count exceeds twice the moving average, indicating potential anomalies.​

**Elasticsearch (ESQL):**

_Function:_ `moving_avg`

_Example:_ Detecting users whose daily login count is at least twice their average login count over the preceding week.​


```JSON
FROM security_event
| WHERE EventID == 4624
| EVAL day = DATE_FORMAT(Timestamp, "yyyy-MM-dd")
| STATS daily_logins = COUNT() BY TargetUserName, day
| SORT BY TargetUserName, day
| EVAL avg_login_count = MOVING_AVG(daily_logins, 7)
| WHERE daily_logins > 2 * avg_login_count
| KEEP TargetUserName, avg_login_count, daily_logins
```

_Explanation:_ This ESQL query calculates the daily login counts per user and computes a 7-day moving average. It then identifies days where the login count is more than twice the moving average, signaling potential anomalies.​

**Detecting Concurrent Activities Within Defined Time Intervals**

_Concept:_ Identifying multiple specific activities occurring within a short time frame can indicate suspicious behavior. By aggregating these activities and analyzing their frequency within defined intervals, potential security threats can be detected.​

**Splunk (SPL):**

_Function:_ `mvappend`, `stats`, `mvcount`

_Example:_ Detecting instances where at least three specific discovery commands are executed within a 15-minute window.​

```JSON
index=device_process_events
| search FileName IN ("hostname", "systeminfo", "whoami", "netstat", "ping", "ipconfig")
| bin _time span=15m
| stats values(FileName) as executed_commands by DeviceName, _time
| eval command_count = mvcount(executed_commands)
| where command_count >= 3
| table DeviceName, _time, executed_commands
```

_Explanation:_ This SPL query searches for specific discovery commands, groups them into 15-minute intervals per device, and counts the unique commands executed. It then filters for intervals where at least three different commands were executed, indicating potential reconnaissance activity.​

**Elasticsearch (ESQL):**

_Function:_ `terms`, `date_histogram`, `cardinality`

_Example:_ Detecting instances where at least three specific discovery commands are executed within a 15-minute window.​

```JSON
FROM device_process_events
| WHERE FileName IN ("hostname", "systeminfo", "whoami", "netstat", "ping", "ipconfig")
| EVAL interval = DATE_TRUNC('15m', Timestamp)
| STATS executed_commands = TERMS(FileName) BY DeviceName, interval
| EVAL command_count = LENGTH(executed_commands)
| WHERE command_count >= 3
| KEEP DeviceName, interval, executed_commands
```

_Explanation:_ This ESQL query filters for specific discovery commands, groups them into 15-minute intervals per device, and counts the number of unique commands executed. It then identifies intervals where at least three different commands were executed, which could indicate suspicious activity.​

_Tips for Improving Detection Logic:_

- **Grouping by Initiating Process:** Consider grouping by both the device and the initiating process to pinpoint specific sources of suspicious activity.​
    
- **Categorizing Discovery Commands:** Create separate lists for different types of discovery commands (e.g., host discovery vs. process discovery) to refine detection strategies.​
    
- **Adjusting Rule Schedules:** Set the rule's schedule interval shorter than the analysis window to create a moving analysis window, capturing a broader range of command executions and reducing the likelihood of false negatives. For instance, running the rule every 5 minutes can be effective for a 15-minute analysis window.​
    

By translating these KQL concepts into SPL and ESQL, security analysts can implement similar anomaly detection strategies across different Security Information and Event Management (SIEM) platforms, enhancing their ability to detect and respond to potential threats effectively.

---

## Generating Continuous Time Series Data

# Continuous Time Series with make-series in KQL, Splunk, and ESQL

## KQL: `make-series`

Unlike the `bin()` function, the `make-series` operator in KQL is designed for more than just grouping data into time intervals. It excels at creating **continuous** time series data, even in cases where certain intervals have no data points. This is essential for complete and consistent trend analysis and anomaly detection, ensuring no missing data skews the results.

### Syntax

```kql
T
| make-series <Aggregations> [default = DefaultValue>] 
  on <TimestampColumn> 
  from <StartTime> to <EndTime> 
  step <Interval> 
  [by <GroupingColumns>]
```

- `<Aggregations>`: Aggregation functions to apply (e.g., `count()`, `sum()`, `avg()`).
- `<DefaultValue>`: Optional. Value to fill in for missing intervals (default is `0`).
- `<TimestampColumn>`: Field with time data.
- `<StartTime>` and `<EndTime>`: Range for the series.
- `<Interval>`: Time interval (e.g., `1d`, `1h`).
- `<GroupingColumns>`: Optional. Grouping dimensions.

### Example

Generate a time series for the total bytes (`SentBytes + ReceivedBytes`) per `SourceIP` over the past 3 days in daily intervals.


```kusto
set query_datetimescope_column = "TimeGenerated";
set query_datetimescope_to = datetime(2023-09-18 09:10:00);
set query_now = datetime(2023-09-18 09:10:00);
//Actual Query
let _start = ago(3d);
let _end = now();
WebProxy
| where TimeGenerated > _start
| where isnotempty(SourceIP)
| where SourceIP != '-'
| where isnotempty(DestinationIP)
| project TimeGenerated, SourceIP, DestinationIP, SentBytes, ReceivedBytes
| make-series TotalBytes = sum(SentBytes + ReceivedBytes) on TimeGenerated from _start to _end step 1d by SourceIP
```

---

## Splunk Equivalent

Splunk does not have an exact equivalent to `make-series`, but you can simulate continuous time series using `timechart` with `fillnull` to backfill missing data points.

### Example

```spl
index=webproxy sourcetype=proxy_logs
| eval TotalBytes = SentBytes + ReceivedBytes
| timechart span=1d sum(TotalBytes) by SourceIP
| fillnull value=0
```

- `timechart`: Performs time-based aggregation.
- `span=1d`: Creates 1-day buckets.
- `fillnull`: Ensures gaps are filled with `0`.

---

## ESQL Equivalent

Elasticsearch ES|QL supports continuous series building using date truncation and filling techniques.

### Example

```esql
LET start = NOW() - INTERVAL 3 DAYS;
FROM webproxy
| WHERE Timestamp >= start AND SourceIP IS NOT NULL AND DestinationIP IS NOT NULL AND SourceIP != "-"
| EVAL TotalBytes = SentBytes + ReceivedBytes
| EVAL day = DATE_TRUNC('1d', Timestamp)
| STATS TotalBytes = SUM(TotalBytes) BY SourceIP, day
| KEEP SourceIP, day, TotalBytes
```

- `DATE_TRUNC('1d', Timestamp)`: Groups data by day.
- Missing time buckets must be handled during visualization or with scripted completion logic.

---

## Summary

| Feature           | KQL (`make-series`)        | Splunk (`timechart + fillnull`)         | ES|QL (`date_trunc + stats`)               |
|------------------|----------------------------|------------------------------------------|--------------------------------------------|
| Auto Time Fill   | ✅ Yes                     | ❌ Needs `fillnull`                      | ❌ Must be handled manually                |
| Aggregation      | ✅ Built-in                | ✅ Built-in                              | ✅ Built-in                                 |
| Granularity      | ✅ Flexible steps          | ✅ Flexible `span`                       | ✅ Flexible with `DATE_TRUNC()`            |
| Grouping Support | ✅ Yes (`by`)              | ✅ Yes (`by` in `timechart`)             | ✅ Yes (`STATS BY`)                        |
## Annotated KQL Query with Inline Comments

```JSON
// Set the time column used in queries
set query_datetimescope_column = "TimeGenerated";

// Set the upper bound for the time range (current context)
set query_datetimescope_to = datetime(2023-09-18 09:10:00);

// Set the logical "now" timestamp
set query_now = datetime(2023-09-18 09:10:00);

// Define the range for time series creation: past 3 days up to now
let _start = ago(3d);
let _end = now();

WebProxy
// Filter logs within the 3-day time window
| where TimeGenerated > _start

// Clean the data: remove rows with empty SourceIP
| where isnotempty(SourceIP)

// Filter out placeholder values (e.g., '-' used in logs)
| where SourceIP != '-'

// Clean the data: remove rows with empty DestinationIP
| where isnotempty(DestinationIP)

// Select only relevant columns
| project TimeGenerated, SourceIP, DestinationIP, SentBytes, ReceivedBytes

// Create a continuous time series for each SourceIP in 1-day intervals
// Filling missing intervals with 0 by default (make-series behavior)
// This produces two arrays: one for TotalBytes and one for matching timestamps
| make-series TotalBytes = sum(SentBytes + ReceivedBytes) 
    on TimeGenerated 
    from _start to _end 
    step 1d 
    by SourceIP
```

This query produces a continuous time series of total bytes (sent + received) per `SourceIP` across three days with 1-day intervals.  
The `make-series` operator ensures that each day within the range is represented, inserting `0` where data is missing — useful for trend analysis and anomaly detection.

---

# Time Series Anomaly Detection Using KQL, Splunk, and ESQL

## Overview

Time series anomaly detection is crucial in detecting abnormal patterns—such as cyberattacks or system misconfigurations—especially when monitoring consistent telemetry like logs or metrics. This guide explores how KQL’s `series_decompose_anomalies()` works, and how similar functionality can be implemented using **Splunk SPL** and **Elasticsearch ESQL**.

---

## KQL: Using `series_decompose_anomalies()`

### Function Summary

```kql
series_decompose_anomalies(
  <Series>,
  [<Threshold>, <Seasonality>, <Trend>, <TestPoints>, <ADMethod>, <SeasonalityThreshold>]
)
```

- **Input:** Numeric series (from `make-series` or `make_list`)
- **Output:**  
  - `ad_flag`: 1 (positive anomaly), -1 (negative anomaly), 0 (none)  
  - `ad_score`: numeric score  
  - `baseline`: predicted value

---

## Step-by-Step: Exfiltration Detection via KQL

### Step 1: Create the time series

```kql
let _start = ago(14d);
let _end = now();
WebProxy
| where TimeGenerated > _start
| where isnotempty(SourceIP) and SourceIP != '-' and isnotempty(DestinationIP)
| project TimeGenerated, SourceIP, DestinationIP, SentBytes, ReceivedBytes
| make-series TotalBytes = sum(SentBytes + ReceivedBytes) 
    on TimeGenerated from _start to _end step 1d 
    by SourceIP
```

### Step 2: Apply `series_decompose_anomalies()`

```kql
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(TotalBytes)
```

### Step 3: Expand the time series

```kql
| mv-expand TimeGenerated to typeof(datetime), TotalBytes to typeof(long), 
             ad_flag to typeof(int), ad_score to typeof(real), baseline to typeof(real)
| sort by SourceIP, TimeGenerated
```

### Step 4: Filter on flagged anomalies

```kql
| where ad_flag == 1
| extend difference_from_baseline = round((TotalBytes - baseline) / (1024*1024), 2)
| where difference_from_baseline > 500
| where TimeGenerated >= ago(1d)
```

---

## Equivalent in Splunk (SPL)

Splunk lacks a native decomposition function, but anomaly detection can be modeled via **moving averages + threshold filters**.

```spl
index=webproxy sourcetype=proxy_logs
| eval TotalBytes = SentBytes + ReceivedBytes
| timechart span=1d sum(TotalBytes) as TotalBytes by SourceIP
| foreach * [ eval ma_<<FIELD>>=movingavg(<<FIELD>>, 7) ]
| foreach * [ eval score_<<FIELD>> = abs('<<FIELD>>' - ma_<<FIELD>>) ]
| foreach * [ where 'score_<<FIELD>>' > threshold ]
```

---

## Equivalent in ESQL

Elasticsearch doesn't support decomposition natively, but can detect anomalies using **moving averages** and **differences**:

```esql
LET start = NOW() - INTERVAL 14 DAYS;
LET threshold = 500;
FROM webproxy
| WHERE @timestamp >= start AND SourceIP IS NOT NULL AND SourceIP != '-' AND DestinationIP IS NOT NULL
| EVAL TotalBytes = SentBytes + ReceivedBytes
| EVAL interval = DATE_TRUNC('1d', @timestamp)
| STATS TotalBytes = SUM(TotalBytes) BY SourceIP, interval
| SORT BY SourceIP, interval
| EVAL ma_TotalBytes = MOVING_AVG(TotalBytes, 7)
| EVAL anomaly_score = ABS(TotalBytes - ma_TotalBytes)
| WHERE anomaly_score > threshold
| KEEP SourceIP, interval, TotalBytes, anomaly_score
```

---

## Detection Strategy Recommendations

| Parameter      | Purpose                                              | Recommended |
|----------------|------------------------------------------------------|-------------|
| Time Range     | Capture multiple seasonal cycles                     | 14 days     |
| Interval       | Detect granularity (e.g., login = 1h, exfil = 4h)    | 1h–6h       |
| Threshold      | Adjust for false positives/negatives                 | ~1.5 (score)|
| Output Filter  | Combine anomaly flag, score, and volume change       | yes         |

---

## Prioritization Example (KQL)

```kql
let _interval = 4h;
let diff_threshold = 500;
WebProxy
| where TimeGenerated > ago(14d)
| where isnotempty(SourceIP) and SourceIP != '-' and isnotempty(DestinationIP)
| project TimeGenerated, SourceIP, DestinationIP, SentBytes, ReceivedBytes
| make-series TotalBytes = sum(SentBytes + ReceivedBytes) 
    on TimeGenerated from ago(14d) to now() step _interval 
    by SourceIP
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(TotalBytes)
| mv-expand TimeGenerated to typeof(datetime), TotalBytes to typeof(long), 
             ad_flag to typeof(int), ad_score to typeof(real), baseline to typeof(real)
| where ad_flag == 1
| extend difference_from_baseline = round((TotalBytes - baseline) / (1024*1024), 2)
| where difference_from_baseline > diff_threshold
| where TimeGenerated >= ago(1d)
| top 10 by ad_score
```

---

## Resources

- 📖 [KQL Time Series Analysis – Microsoft Docs](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/time-series-analysis)

---

# Time Series Anomaly Detection in KQL, Splunk, and ESQL (With Context)

This guide walks through the step-by-step approach to detect anomalies in time series data using KQL’s `series_decompose_anomalies()` and equivalent logic in Splunk SPL and Elasticsearch ES|QL.

---

## Why It Matters

Time series anomaly detection is essential for identifying:
- Unusual spikes or drops in metrics (e.g., bandwidth usage, login counts)
- Behavioral outliers across endpoints or users
- Exfiltration, abuse, or misconfigurations

Detecting anomalies in a reliable and tunable way helps prioritize investigations and reduce alert fatigue.

---

## Step-by-Step Walkthrough (KQL Focused)

### Step 1: Generate a Continuous Time Series

```kql
make-series TotalBytes = sum(SentBytes + ReceivedBytes) 
    on TimeGenerated from _start to _end step 1d by SourceIP
```

- **Why**: Normal aggregations like `summarize by bin()` won’t fill missing intervals.
- **This**: Fills all intervals from start to end, even if some SourceIPs didn’t send data on a day.
- **Result**: A table where each row has SourceIP, and an array of 14 values (bytes per day).

---

### Step 2: Apply `series_decompose_anomalies()`

```kql
extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(TotalBytes)
```

- **What it does**:
  - Breaks down `TotalBytes` into:
    - `baseline` (predicted trend)
    - `ad_score` (how far off the data is)
    - `ad_flag` (+1 = spike, -1 = drop, 0 = no anomaly)

---

### Step 3: Flatten for Human Readability

```kql
mv-expand TimeGenerated, TotalBytes, ad_flag, ad_score, baseline
```

- **Why**: Each row from make-series is an array. Analysts need readable row-by-row data.
- **This**: Expands each time slice into individual rows to analyze by timestamp.

---

### Step 4: Filter Significant Anomalies

```kql
| where ad_flag == 1
| where difference_from_baseline > 500  // MB
| where TimeGenerated >= ago(1d)
```

- **Focus**: Filter to only positive anomalies that occurred in the last 24 hours and exceed a 500 MB deviation from expected values.

---

## Splunk Version Breakdown

@Splunk

```spl
streamstats window=7 avg(TotalBytes) as baseline by SourceIP
| eval ad_score = abs(TotalBytes - baseline)
```

- `streamstats` simulates rolling average.
- `abs()` + threshold replicates `series_decompose_anomalies()`.
- You manually define the anomaly boundary (e.g., > 500MB deviation).

---

## ES|QL Version Breakdown

@ESQL

```esql
| EVAL baseline = MOVING_AVG(TotalBytes, 7)
| EVAL ad_score = ABS(TotalBytes - baseline)
| WHERE ad_score > threshold
```

- `MOVING_AVG` fills the role of the `baseline`.
- `ABS()` is used to detect distance from baseline.
- Manual filtering logic gives you complete control.

---

## Choosing Parameters Effectively

| Parameter     | Guidance |
|---------------|----------|
| Time Range    | At least 14 days to observe weekly patterns |
| Interval      | Match your use case (1h = login anomaly, 6h = exfil) |
| Threshold     | Tune based on bandwidth, log volume, or bytes transferred |
| Score Logic   | Filter top anomalies by `ad_score` or volume deviation |

---

## Prioritization Strategy

You can enhance triage by ranking results:

```kql
| top 10 by ad_score
```

In SPL:

```spl
| sort - ad_score
| head 10
```

In ESQL:

```esql
| SORT BY ad_score DESC
| LIMIT 10
```

---


Time series anomaly detection helps identify abnormal behavior in system telemetry. In cybersecurity, this could indicate data exfiltration, login spikes, or unusual host behavior. Below is a multi-SIEM guide using:

- ✅ **KQL:** `series_decompose_anomalies()`
- ✅ **Splunk SPL:** `movingavg()`, `streamstats`
- ✅ **Elasticsearch ES|QL:** `MOVING_AVG`, `ABS`, and difference thresholds

---

## 📘 KQL: Using `series_decompose_anomalies()`

```kql
let _start = ago(14d);
let _end = now();
WebProxy
| where TimeGenerated > _start
| where isnotempty(SourceIP) and SourceIP != '-' and isnotempty(DestinationIP)
| project TimeGenerated, SourceIP, DestinationIP, SentBytes, ReceivedBytes
| make-series TotalBytes = sum(SentBytes + ReceivedBytes) 
    on TimeGenerated from _start to _end step 1d 
    by SourceIP
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(TotalBytes)
| mv-expand TimeGenerated to typeof(datetime), TotalBytes to typeof(long), 
             ad_flag to typeof(int), ad_score to typeof(real), baseline to typeof(real)
| where ad_flag == 1
| extend difference_from_baseline = round((TotalBytes - baseline) / (1024*1024), 2)
| where difference_from_baseline > 500
| where TimeGenerated >= ago(1d)
```

---

## 🟡 Splunk Equivalent

@Splunk: Since Splunk doesn't have decomposition, use `movingavg()` and custom scoring.

```spl
index=webproxy sourcetype=proxy_logs
| eval TotalBytes = SentBytes + ReceivedBytes
| bin span=1d _time
| stats sum(TotalBytes) as TotalBytes by SourceIP, _time
| sort 0 SourceIP _time
| streamstats window=7 avg(TotalBytes) as baseline by SourceIP
| eval ad_score = abs(TotalBytes - baseline)
| where ad_score > 524288000  // 500MB in bytes
| where _time >= relative_time(now(), "-1d@d")
| table SourceIP, _time, TotalBytes, baseline, ad_score
```

---

## 🔵 ESQL Equivalent

@ESQL: Use `MOVING_AVG`, `ABS`, and interval grouping for detection logic.

```esql
LET start = NOW() - INTERVAL 14 DAYS;
LET threshold = 500 * 1024 * 1024;  // 500 MB
FROM webproxy
| WHERE @timestamp >= start AND SourceIP IS NOT NULL AND SourceIP != '-' AND DestinationIP IS NOT NULL
| EVAL TotalBytes = SentBytes + ReceivedBytes
| EVAL day = DATE_TRUNC('1d', @timestamp)
| STATS TotalBytes = SUM(TotalBytes) BY SourceIP, day
| SORT BY SourceIP, day
| EVAL baseline = MOVING_AVG(TotalBytes, 7)
| EVAL ad_score = ABS(TotalBytes - baseline)
| WHERE ad_score > threshold AND day >= DATE_TRUNC('1d', NOW() - INTERVAL 1 DAY)
| KEEP SourceIP, day, TotalBytes, baseline, ad_score
```

---

## Summary Table

| Feature                  | KQL                              | Splunk SPL                          | ES|QL                                  |
|--------------------------|----------------------------------|--------------------------------------|----------------------------------------|
| Function                 | `series_decompose_anomalies()`   | `streamstats`, `movingavg()`         | `MOVING_AVG()`, `ABS()`                |
| Time Binning             | `make-series ... step`           | `bin span=...` + `stats`             | `DATE_TRUNC()` + `STATS`               |
| Baseline Support         | built-in                         | via `streamstats`                    | via `MOVING_AVG`                       |
| Scoring Logic            | built-in                         | manual score with `eval`             | manual score with `EVAL`               |
| Filtering on Anomalies   | `ad_flag == 1` + score thresholds| score-based `where` filtering        | score-based `WHERE` filtering          |

---

## Notes

- Adjust thresholds (`500 MB`) as needed
- Use higher intervals (4h or 1d) for exfil, and smaller (15m–1h) for login spikes
- Prioritize top anomalies by score to manage investigation load

---


# Enhanced Anomaly Detection with Minimum Data Point Thresholds in KQL, Splunk, and ESQL

## 📘 KQL Original (Reference Query)

```kql
set query_datetimescope_column = "TimeGenerated";
set query_datetimescope_to = datetime(2023-09-18 09:10:00);
set query_now = datetime(2023-09-18 09:10:00);

let _start = ago(14d);
let _end = now();
WebProxy
| where TimeGenerated > _start
| where isnotempty(SourceIP) and SourceIP != '-' and isnotempty(DestinationIP)
| project TimeGenerated, SourceIP, DestinationIP, SentBytes, ReceivedBytes
| make-series TotalBytes = sum(SentBytes + ReceivedBytes)
    on TimeGenerated from _start to _end step 1d by SourceIP
| where array_index_of(TotalBytes, 0 , 0 , -1 , 7) == -1
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(TotalBytes)
| mv-expand TimeGenerated to typeof(datetime), TotalBytes to typeof(long),
            ad_flag to typeof(int), ad_score to typeof(real), baseline to typeof(real)
| where ad_flag == 1
| extend difference_from_baseline = round((TotalBytes - baseline) / (1024*1024), 2)
| where difference_from_baseline > 500
| where TimeGenerated >= ago(1d)
```

---

## 🟡 @Splunk Equivalent

> Splunk does not have native support for array search like `array_index_of`, but we can simulate this logic by counting the number of 0-byte days using `eventstats` and `streamstats`.

```spl
index=webproxy sourcetype=proxy_logs
| eval TotalBytes = SentBytes + ReceivedBytes
| bin _time span=1d
| stats sum(TotalBytes) as TotalBytes by SourceIP, _time
| eventstats count(eval(TotalBytes==0)) as ZeroCount by SourceIP
| where ZeroCount < 7
| streamstats window=7 avg(TotalBytes) as baseline by SourceIP
| eval ad_score = abs(TotalBytes - baseline)
| eval difference_from_baseline = round((TotalBytes - baseline) / 1024 / 1024, 2)
| where ad_score > 524288000
| where _time >= relative_time(now(), "-1d@d")
| table SourceIP, _time, TotalBytes, baseline, ad_score, difference_from_baseline
```

---

## 🔵 @ESQL Equivalent

> ES|QL also doesn’t support indexed search inside arrays like `array_index_of`, so we use conditional filtering after aggregation.

```esql
LET start = NOW() - INTERVAL 14 DAYS;
LET threshold = 500 * 1024 * 1024;
FROM webproxy
| WHERE @timestamp >= start AND SourceIP IS NOT NULL AND SourceIP != '-' AND DestinationIP IS NOT NULL
| EVAL TotalBytes = SentBytes + ReceivedBytes
| EVAL interval = DATE_TRUNC('1d', @timestamp)
| STATS TotalBytes = SUM(TotalBytes) BY SourceIP, interval
| SORT BY SourceIP, interval
| EVAL ZeroFlag = IF(TotalBytes == 0, 1, 0)
| STATS zero_days = SUM(ZeroFlag) BY SourceIP
| JOIN (
    FROM webproxy
    | WHERE @timestamp >= start AND SourceIP IS NOT NULL AND SourceIP != '-' AND DestinationIP IS NOT NULL
    | EVAL TotalBytes = SentBytes + ReceivedBytes
    | EVAL interval = DATE_TRUNC('1d', @timestamp)
    | STATS TotalBytes = SUM(TotalBytes) BY SourceIP, interval
    | SORT BY SourceIP, interval
    | EVAL baseline = MOVING_AVG(TotalBytes, 7)
    | EVAL ad_score = ABS(TotalBytes - baseline)
    | EVAL difference_from_baseline = ROUND((TotalBytes - baseline)/1024/1024, 2)
    | WHERE ad_score > threshold AND interval >= DATE_TRUNC('1d', NOW() - INTERVAL 1 DAY)
) ON SourceIP
| WHERE zero_days < 7
| KEEP SourceIP, interval, TotalBytes, baseline, ad_score, difference_from_baseline
```

---

## ✅ Summary

| Feature                        | KQL                                      | Splunk SPL                               | ESQL                                        |
|-------------------------------|------------------------------------------|-------------------------------------------|---------------------------------------------|
| Minimum Data Points Filter     | `array_index_of(..., 0, ..., 7)`         | `eventstats count where TotalBytes==0`    | `STATS SUM(ZeroFlag)` + `JOIN`              |
| Time Binning                  | `make-series ... step 1d`                | `bin span=1d` + `stats sum()`             | `DATE_TRUNC('1d', @timestamp)`              |
| Anomaly Detection             | `series_decompose_anomalies()`           | `streamstats avg()` + threshold filter    | `MOVING_AVG` + `ABS` + threshold            |

---

## 📌 Takeaway

Introducing a **minimum activity threshold** (like requiring >7 non-zero days in 14) increases anomaly detection accuracy by excluding noisy or inactive endpoints. This helps reduce **false positives**, improving alert quality.

---


# Enhanced Anomaly Detection with Minimum Data Points – KQL, Splunk, and ESQL (With Explanations)

This guide walks through anomaly detection with a **data completeness filter**, ensuring reliable results by ignoring noisy/inactive data. We'll examine how this logic is applied in KQL, Splunk SPL, and Elasticsearch ES|QL.

---

## 🔎 Why Filter by Data Point Count?

Anomaly detection works best with **consistent activity**. If a host only sends logs on a few days within the time range, it may:
- Cause false positives due to lack of context
- Skew anomaly scores
- Waste investigative resources

### Strategy

Filter out hosts with **7 or more zero-activity days** in a 14-day window before running anomaly detection.

---

## ✅ Step-by-Step (KQL)

### Step 1: Define Time Range & Clean Data

```kql
let _start = ago(14d);
let _end = now();
WebProxy
| where TimeGenerated > _start
| where isnotempty(SourceIP) and SourceIP != '-' and isnotempty(DestinationIP)
```

**Purpose**: Pull 14 days of logs, filter out noise (empty or placeholder IPs).

---

### Step 2: Create Daily Time Series

```kql
| make-series TotalBytes = sum(SentBytes + ReceivedBytes)
    on TimeGenerated from _start to _end step 1d by SourceIP
```

**Purpose**: Create a complete daily log for each `SourceIP`, with `0` for missing days.

---

### Step 3: Filter Series with Low Activity

```kql
| where array_index_of(TotalBytes, 0 , 0 , -1 , 7) == -1
```

**What It Does**: Checks if there are 7 or more zero-byte days. If so, filter out that `SourceIP`.

- `array_index_of(..., 7)` looks for the 7th `0` in the `TotalBytes` array.
- Returns -1 if there are fewer than 7, which means "good data".

---

### Step 4: Detect Anomalies

```kql
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(TotalBytes)
```

**Decomposes** the cleaned time series into:
- `baseline`: expected trend
- `ad_score`: deviation
- `ad_flag`: +1 spike, -1 drop, 0 normal

---

### Step 5: Flatten & Filter for Real Threats

```kql
| mv-expand TimeGenerated, TotalBytes, ad_flag, ad_score, baseline
| where ad_flag == 1
| extend difference_from_baseline = round((TotalBytes - baseline) / (1024*1024), 2)
| where difference_from_baseline > 500
| where TimeGenerated >= ago(1d)
```

- Filters to real spikes (positive flag)
- Requires spike to be >500 MB
- Limits to past day

---

## 🟡 Splunk Logic (Explained)

```spl
| stats sum(TotalBytes) by SourceIP, _time
| eventstats count(eval(TotalBytes==0)) as ZeroCount by SourceIP
| where ZeroCount < 7
```

- `eventstats`: Counts zero-byte days per `SourceIP`
- `where ZeroCount < 7`: Only continue with good data

```spl
| streamstats window=7 avg(TotalBytes) as baseline by SourceIP
| eval ad_score = abs(TotalBytes - baseline)
```

- Rolling baseline (like 7-day average)
- Calculate anomaly score

---

## 🔵 ESQL Logic (Explained)

```esql
| EVAL ZeroFlag = IF(TotalBytes == 0, 1, 0)
| STATS zero_days = SUM(ZeroFlag) BY SourceIP
```

- Flag each zero-day
- Sum to count low-activity hosts

```esql
| JOIN ... WHERE zero_days < 7
```

- Exclude bad data before detection

```esql
| EVAL baseline = MOVING_AVG(TotalBytes, 7)
| EVAL ad_score = ABS(TotalBytes - baseline)
| WHERE ad_score > threshold
```

- Manual trend + score + filtering

---

## 📌 Summary of Benefits

| Step                     | Goal                                 |
|--------------------------|--------------------------------------|
| Time Series (1d)         | Smooth out inconsistencies           |
| Zero-Day Filter          | Exclude underactive sources          |
| Decomposition / Scoring | Identify spikes or volume drops      |
| Threshold Filtering      | Remove noisy or false positive results|

---

## ✅ Final Advice

- Use `array_index_of()` in KQL for accurate data health filtering
- In SPL/ESQL, simulate this using counting + `where` clauses
- Tailor the zero-day threshold to your environment (e.g. 5 of 14)

---


# Using Optional Parameters in Time Series Anomaly Detection — KQL, Splunk, and ESQL

## Overview

Optional parameters in anomaly detection functions can fine-tune how trends are interpreted and how sensitive the detection logic is. This lesson focuses on KQL’s `series_decompose_anomalies()` with different parameter options, and shows how to replicate similar logic in **Splunk** and **Elasticsearch ES|QL** using available statistical tools.

---

## 📘 KQL Reference (with Explanations)

### Baseline Example with `linefit` (not recommended for 2–4 week datasets)

```kql
let FileShareAccess = datatable (TimeGenerated:datetime, SubjectUserName:string, UniqueFileShareCount: int) [ 
datetime(2023-09-12), 'miriam.graham', 1,
datetime(2023-09-13), 'miriam.graham', 3,
datetime(2023-09-14), 'miriam.graham', 5,
datetime(2023-09-17), 'miriam.graham', 6,
datetime(2023-09-18), 'miriam.graham', 10,
datetime(2023-09-20), 'miriam.graham', 5,
datetime(2023-09-21), 'miriam.graham', 2,
datetime(2023-09-22), 'miriam.graham', 4,
datetime(2023-09-24), 'miriam.graham', 19,
datetime(2023-09-25), 'miriam.graham', 3,
];
let _start = ago(14d);
let _end = now();
FileShareAccess
| make-series UniqueFileShareCountSeries = take_any(UniqueFileShareCount) default = 0 on TimeGenerated from _start to _end step 1d by SubjectUserName
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(UniqueFileShareCountSeries, 2.5, -1, 'linefit')
```

**Explanation:**
- `2.5`: Increases anomaly threshold sensitivity.
- `'linefit'`: Uses linear regression as a trend model — may underperform on volatile short-term data.

---

### Recommended: Use Default `'avg'` Trend Parameter

```kql
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(UniqueFileShareCountSeries, 2.5, -1)
```

**Why**: `avg` is more stable for small datasets and produces better baseline prediction for typical 14-day activity.

---

### Filling Missing Values for Better Trends

```kql
| make-series UniqueFileShareCountSeries = take_any(UniqueFileShareCount) default = int(null)
| extend UniqueFileShareCountSeries = series_fill_linear(UniqueFileShareCountSeries)
| extend (ad_flag, ad_score, baseline) = series_decompose_anomalies(UniqueFileShareCountSeries, 2.5, -1)
```

**Context**:
- Use `int(null)` to preserve empty buckets.
- `series_fill_linear()` interpolates missing values for better trend continuity.

---

## 🟡 Splunk Equivalent

**Simulate baseline trends and anomaly scoring using moving averages and regression.**

```spl
| makeresults
| eval TimeGenerated=strptime("2023-09-12", "%Y-%m-%d") + (86400 * random() % 14)
| eval SubjectUserName="miriam.graham"
| eval UniqueFileShareCount=case(TimeGenerated==strptime("2023-09-24", "%Y-%m-%d"), 19,
                                 TimeGenerated==strptime("2023-09-18", "%Y-%m-%d"), 10,
                                 true(), random() % 6 + 1)
| bin span=1d _time
| stats max(UniqueFileShareCount) as UniqueFileShareCount by SubjectUserName, _time
| eventstats avg(UniqueFileShareCount) as baseline by SubjectUserName
| eval ad_score = abs(UniqueFileShareCount - baseline)
| where ad_score > 2.5
```

**Note**: Splunk doesn't support native `linefit` trend smoothing inside `stats`, but you can simulate smoothing using `streamstats` or ML Toolkit.

---

## 🔵 ESQL Equivalent

**Use regression or moving averages for trend baselines**

```esql
FROM file_share_access
| EVAL UniqueFileShareCount = IF(IS_NULL(UniqueFileShareCount), NULL, UniqueFileShareCount)
| EVAL date = DATE_TRUNC('1d', TimeGenerated)
| STATS UniqueFileShareCount = MAX(UniqueFileShareCount) BY SubjectUserName, date
| SORT BY SubjectUserName, date
| EVAL UniqueFileShareCount = IF(IS_NULL(UniqueFileShareCount), LINEAR_FILL(UniqueFileShareCount), UniqueFileShareCount)
| EVAL baseline = MOVING_AVG(UniqueFileShareCount, 7)
| EVAL ad_score = ABS(UniqueFileShareCount - baseline)
| WHERE ad_score > 2.5
```

**Notes**:
- `LINEAR_FILL()` can be applied if missing value filler is supported.
- No native regression modeling, but moving averages can simulate local trends.
- Simulate `baseline` + `deviation score` using EVALs.

---

## 🎯 Final Recommendations

| Consideration            | Best Practice                                 |
|--------------------------|-----------------------------------------------|
| Short time range         | Use default trend (avg) for KQL               |
| Missing values           | Fill with `series_fill_linear` or similar     |
| Strong outliers          | Use higher thresholds (e.g., 2.5)             |
| Long-term trends         | Test `linefit` vs `avg` to compare detection  |

---

## References

- [KQL series_decompose_anomalies() Docs](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction)

This document shows how optional parameters impact trend modeling and anomaly scoring across SIEM platforms.

